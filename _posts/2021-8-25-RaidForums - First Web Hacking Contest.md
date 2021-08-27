---
layout: post
title: RaidForums - First Web Hacking Contest
---

I happened to come across this post on RaidForums, and decided to give it a go.

![](/assets/2021-08-24-13-17-15.png)

### Code Review

We were given a link to a Github repo, which turned out to be a fork of `flox`.

[https://github.com/Anas7asiaM00N/flox/](https://github.com/Anas7asiaM00N/flox/)

The first thing I noticed in the repo is that it's ahead 12 commits from the original project that it was forked off.  So Anas7asiaM00N did some code changes.

![](/assets/2021-08-24-13-19-16.png)

#### Commit review

### SQL Injection

Going through the commits, I come across this piece of code, which immediately indicates there is a SQL injection vulnerability here.

![](/assets/2021-08-24-13-20-46.png)

Knowing that the framework that was being used was Laravel, I was able to also search through the code to determine where SQL injection might occur.  SQL queries in Laravel, done right, are done using Eloquent models.  

You are however able to perform raw DB queries on the database as well, and this is where the human error normally comes in.

Search for it in Visual Code brings up the same finding.

![](/assets/2021-08-27-11-50-48.png)

#### How to get here 

Looking at the code, if I can control $type, $mediaType or $value, I'll be able to execute SQL queries making use of this statement.

The three variables are being password to the method call.

![](/assets/2021-08-27-11-53-19.png)

I start looking at all the places in the code that calls this method.  I come across one in FileParser.php that looks like a good candidate to target.

![](/assets/2021-08-27-11-56-35.png)

Tracing back to where the above is called from, we find the following.

![](/assets/2021-08-27-11-57-34.png)

The above seems to execute the validateStore() method when the status is equal to 'added'.

![](/assets/2021-08-27-11-58-14.png)

I then look for where handleStatus() is being called from.

![](/assets/2021-08-27-11-59-13.png)

Further searching and I finally find the entry point to this chain of calls.

![](/assets/2021-08-27-12-00-15.png)

Looking at the route.php file, I'm able to see how the HTTP routing works to get to the receive() method.

![](/assets/2021-08-27-12-00-46.png)

#### Creating the payload

You'll notice in my screenshot above, I had added some debug code in for myself while testing this locally.

The first we target is where we are setting the type value of the object.

![](/assets/2021-08-27-12-06-52.png)

In Laravel the `$request` will contain GET and POST.  So using `?type=evil_code` attached to the URL, will allow us to set the `$type` variable here.

This means we can successfully control the value being parsed to the updateDatabase) method, the subsequent updateDatabase() call too.

![](/assets/2021-08-27-12-08-34.png)

![](/assets/2021-08-27-12-09-16.png)

The next part would be to have the `$files` variable in such a structure that it would traverse through the array, and call the handleStatus() method.

![](/assets/2021-08-27-12-10-28.png)

It's basically expecting an array of items within an array of items.  If that makes sense?

I basically setup the following array, and converted it to JSON to use.

```php
$arr = [
        "type1" => ["type2" => [
          "status" => "added",
          "name" => "name1",
        ]]
      ];
```

The resulted in a JSON string:

```json
{"type1":{"type2":{"status":"added","name":"name1"}}}
```

Using all the above information, I knew I as now able to execute SQL queries, but adjusting my `?type=` value.

### API access

I finally got the following to take advantage of the end point `/api/updates-files` to execute the SQL.

![](/assets/2021-08-24-13-25-26.png)

This gave me the output I was looking for.

![](/assets/2021-08-24-13-25-59.png)

Great, I had the `api_key` to work with now.

### eval

The other finding I notice is that we have an `eval()` being used.  This could possibly be used to inject arbitrary PHP code.

![](/assets/2021-08-24-13-22-33.png)

This is also one of the vulnerabilities you would search for in a PHP code base when doing code review.

![](/assets/2021-08-27-12-16-47.png)

#### How to get here 

Looking through all the calls to methods of the name handle(), I can across an interesting one.

![](/assets/2021-08-27-12-18-31.png)

Searching in the `web.php` file this time for web routes, I find where this call is being triggered.

![](/assets/2021-08-27-12-19-35.png)

Going through the middleware's code, you see that you will need an api_key to access this endpoint.

![](/assets/2021-08-27-12-20-17.png)

Luckily we were able to find that with the SQL injection.

#### Creating the payload

The first thing we need to do is send a JSON payload in a 'payload' parameter, with a POST request.

![](/assets/2021-08-27-12-28-23.png)

This is then passed to the handle() method that we found.  You will notice that the Plex class doesn't have a handle() method, but because it is a child of the Api class, the Api's handle() is called.

The first hurdle to overcome was making sure our payload contained a `title` and `type`.

![](/assets/2021-08-27-12-33-44.png)

![](/assets/2021-08-27-12-34-07.png)

![](/assets/2021-08-27-12-34-19.png)

Another check to pass is to make sure the title can bt found on the TMDB website, and that it is not already added in our database.  For this you can just look up a random title on the TMDB website.

![](/assets/2021-08-27-12-36-04.png)

![](/assets/2021-08-27-12-36-47.png)

Finally we can get to the part that executes our PHP code.

![](/assets/2021-08-27-12-37-10.png)

### Code execution

Eventually able to get code execution with the following payload.

![](/assets/2021-08-24-13-27-24.png)

![](/assets/2021-08-24-13-27-38.png)

### The flag

Having code execution meant that I could begin with enumeration.

The very first thing I did was list the files in the current folder.

![](/assets/2021-08-24-13-28-25.png)

This file looked like the flag...

![](/assets/2021-08-24-13-28-40.png)
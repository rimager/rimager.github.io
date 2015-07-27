---
title: WordPress wp-admin Redirect Loop
layout: post
---

I recently encountered an issue with my WordPress Multisite installation that took me all weekend to figure out. In the hopes of helping someone stuck in the same boat, here's the fix.

# The Problem: WordPress wp-admin doesn't load because of a redirect loop

Quite suddenly, I could not longer access the wp-admin section of my Multisite installation. Chrome reported that "The webpage at http://irnetwork.us/wp-admin/ has resulted in too many redirects. Clearing your cookies for this site or allowing third-party cookies may fix the problem. If not, it is possibly a server configuration issue and not a problem with your computer."

![wp-admin redirect loop](http://i.imgur.com/jWJD7Wi.png)

What was going on? After poking through the Wordpress forums with no success, I checked [Redirect Detective](http://redirectdetective.com/), only to learn that it was 302 redirects causing the issue. 

![Redirect Detective](http://i.imgur.com/aYZOd7Ql.png)

Since I could access wp-login.php, but no stylesheets coming from within the wp-admin folder would load (in my case, http://domain.com/wp-admin/css/login.min.css?ver=4.2.3), it was becoming clear that it was an access issue with the wp-admin folder.

![wp-login.php redirect loop](http://i.imgur.com/rLkhMvCm.png)

# The solution: Missing permissions in .htaccess file in wp-admin folder

I investigated the wp-admin folder to see if there was a php.ini file located there as [Victor Font has documented](http://victorfont.com/fix-wp-multisite-wp-admin-redirect-loop/), and checked all permissions. All was good on that end so only one culprit remained: the .htaccess file.

And indeed, it turns out that the permissions for my IP address were missing:

```
AuthName "WP Admin"
AuthUserFile /dev/null
AuthGroupFile /dev/null
AuthName "WordPress Admin Access Control"
AuthType Basic
<LIMIT GET>
order deny,allow
deny from all
# whitelist office
allow from 161.38.221.135
</LIMIT>
```

The IP address I was working from was not being whitelisted, causing wp-admin folder to be denied access. After adding my IP address, I was once again up and running and able to access wp-admin like a charm.

Hope this helped if you were, like me, puzzled by this one :)




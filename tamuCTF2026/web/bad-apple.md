# CTF Writeup Template

## Challenge Information
- **Challenge Name:** bad-apple
- **Category:** 
- **Difficulty:** 

## Challenge Description
funny touhou reference

## Solution Approach
From the Dockerfile, we can see the flag location:
```sh
CMD ["sh", "-c", "HEX=$(openssl rand -hex 16) && mv /srv/http/uploads/admin/flag.gif /srv/http/uploads/admin/$HEX-flag.gif && echo $HEX > /srv/http/.flag_secret && httpd -DFOREGROUND"]
```

The httpd-append.conf has 
```
    Alias /browse /srv/http/uploads
```
which is good becuase that same config file also disables directory indexing for /uploads/.

That means we can go to `https://bad-apple.tamuctf.com/browse/` and see there is an admin folder. The file name is `318c8a8e8d58ce1a13309dada5be99e7-flag.gif` but it requires a password to access.

If we check wsgi_app.py, we can see we can use `/convert?user_id=admin&filename=318c8a8e8d58ce1a13309dada5be99e7-flag.gif` however this just redirects us back to the origal default gif.

Looking back in the python file, we can see the `/` route is expecting view_user_id, not user_id, so if we switch it to that, we can see the flag! `https://bad-apple.tamuctf.com/?view=318c8a8e8d58ce1a13309dada5be99e7-flag&view_user_id=admin`
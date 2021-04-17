# deploying a django app

- [ ] (Clone git repository)
- [ ] (Check if it runs properly on your server)
- [ ] (Run the project with HTTP Server like python WSGI (gunicorn) )
- [ ] (Create a script to do run it the way you did it)
- [ ] (Make a script able to run automatically preferably by a service manager like supervisor)

## Reverse Proxy in Nginx
Assuming your app is running on port 8000 and you wish to show use your app on domain name like `myapp.local`

-go to your hosts file 
`sudo nano /etc/hosts`

add your domain ( myapp.local ) to point to your IP for example I point myapp.local to 127.0.0.1
```
...
127.0.0.1 localhost myapp.local
...
```
<font color="red">*Note: We can't just point a specific port to a domain for example this is invalid*</font>
```diff
- ...
- 127.0.0.1 localhost 
- 127.0.0.1:8080 myapp.local
- ...
```

Instead this is done with in the nginx config file `sudo nano /etc/nginx/sites-enabled/myapp.local` and put code below and restart nginx
```
  server {
    listen 80;
    server_name myapp.local;
    access_log  /var/log/nginx/example.log;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
  }
```

Now when you visit `myapp.local` in your browser you should be able to get the same result as `localhost:8000`
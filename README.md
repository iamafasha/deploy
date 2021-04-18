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

## running a Python App using Gunicorn
> GUNICORN is a server that translates HTTP requests into Python. WSGI is one of the interfaces/implementations that does that (e.g., the text parts of http headers are transformed into key-value dicts).
>
>Django's built-in development web server (what you get when you run manage.py runserver) provides that functionality also, but it targets a development environment (e.g., restart when the code changes), whereas Gunicorn targets production.
>
>Gunicorn has many features that Django's built-in server is lacking:
>- gunicorn can spawn multiple worker processes to parallelize incoming requests to multiple CPU cores 
>- gunicorn has better logging
>- gunicorn is generally optimized for speed
>- gunicorn can be configured to fine grades depending on your setup
>- gunicorn is actively designed and maintained with security in mind

from <a href="https://stackoverflow.com/a/35660663/6842763">stackoveflow</a>

It's actually easy to serve a project using gunicorn 
if I have a file called my web-app.py as below 
```
def app(environ, start_response):
        data = b"<h2>Hello, World!</h2>"
        start_response("200 OK", [
            ("Content-Type", "text/html"),
            ("Content-Length", str(len(data)))
        ])
        return iter([data])
```
all you need is to install gunicorn and run `gunicorn -w 4 web-app:app`

To run  a django project using gunicorn, It comes with s a wsgi file (Web Server Gateway Interface) so all you need to do is run `python gunicorn path-to-wsgi-file` and then your server will be running
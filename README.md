# nginx2cgi

This project is a small Perl script you can include in your nginx config to enable running CGI scripts directly from nginx (no need to run a separate FastCGI or fcgiwrap or uWSGI process).

## How to use

1. Install the nginx Perl module.

```apt install nginx libnginx-mod-http-perl```

2. Include `cgi.nginx.conf` from this repo in your nginx config for whatever location you want CGI to be used, and set the variable `$cgi_script` to be called (and optionally `$cgi_headers` to specify which headers to pass to your CGI script).

```
location /mycgi {
    set $cgi_script "/path/to/cgi-bin/myscript.cgi
    set $cgi_headers "Host Content-Length Content-Type Cookie Authorization";
    include "/path/to/cgi.nginx.conf";
}
```

## How this works

The included config is a small Perl script that converts the incoming request into a CGI request, calls the designated `$cgi_script` path as a subprocess, and returns whatever the result is as the response.

NOTE: The Perl script uses `open2` to call the `$cgi_script ` for every request, which means this briefly forks and creates a new subprocess for every request. So this project is not nearly as efficient as running a dedicated FastCGI server. I made this project primarily for low volume dynamic websites where I didn't want to have to run an additional server besides nginx.


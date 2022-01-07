# ansible-nginx-letsencrypt

Simple role to install [nginx](https://www.nginx.org) with [let's encrypt](https://letsencrypt.org) on 
[debian](https://www.debian.org) and configuring a set of reverse proxies.

The roles allows the following configurations:

* multiple backends in a single server
* multiple backends in multiple servers

For the _let's encrypt_ certificate there are two possibilities:

* one combined cert that is used in all of the servers
* a single cert for each server

The role defaults to using one combined certificate for all the servers.
This behaviour can be changed by setting `nginx_single_cert: false`.
The nginx host marked `default: true` will get a 'default' flag.

Example set of variables:


```
  nginx_letsencrypt_email: "le@example.com"
  nginx_hosts: [
    {
      default: true,
      domain_name: "abcde.example.com",
      reverse_hosts: [ 
        {
          protocol: "http",
          path: "",
          host: "127.0.0.1",
          port: "8080"
        },
        {
          protocol: "http",
          path: "/foo",
          host: "127.0.0.1",
          port: "8090"
        }
      ]
    },
    {
      default: false,
      domain_name: "edcba.example.com",
      reverse_hosts: [ 
        {
          protocol: "http",
          path: "/foobar",
          host: "127.0.0.1",
          port: "9000"
        }
      ]
    }
  ]
```

If for a server there are no `reverse_hosts` defined, a regular nginx
server with the document root being in `/var/www/{{ domain_name}}` is 
configured.

### Basic Auth

If basic auth is wanted for the server, adding the `users` variable will
enable this:

```
 nginx_letsencrypt_email: "le@example.com"
  nginx_hosts: [
    {
      default: true,
      domain_name: "abcde.example.com",
	  users: [ "username:password", "seconduser:password" ],
      reverse_hosts: [ 
        {
          protocol: "http",
          path: "",
          host: "127.0.0.1",
          port: "8080"
        },
        {
          protocol: "http",
          path: "/foo",
          host: "127.0.0.1",
          port: "8090"
        }
      ]
    }
 ]
```


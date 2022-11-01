![This is an image](https://media.makeameme.org/created/if-you-could-436e34b63e.jpg)

# Issue summary
Access to server endpoint using curl 0:80 was returning `curl: (7) Failed to connect to 0 port 80: Connection refused`. The issue lasted for 2 hours after the last server update from a team member up to when it was resolved. The Airbnb clone website was completely inaccessible to half of the clients during the 2 hours downtime. This was caused by a wrong  port value set in nginx configuration files.

# Duration (East Africa Time GMT+3).
Monday, 31 October 2022


08:37 AM: Logged into the server and nginx server was not responding to requests on socket 0:80
08:38: Begun web debugging to locate issues. This involved the steps;
Check if nginx is running 
`sudo service nginx status`
showed nginx is not running.

Check if there are nginx processes running.
`pgrep -lf nginx`
Showed the program is active and running.

Tried to restart nginx
`sudo service nginx restart`

failed

Check log files
`cat /var/log/nginx/error.log | tail -10`
bind() to [::]:8080 failed (98: Address already in use)

Check processes using ports
`netstat -lpn`
nginx was listening on port 8080

Checked nginx configuration files;
`cat /etc/nginx/sites-available/default`
`cat /etc/nginx/sites-enabled/default`

The problem was with nginx configuration in the file `/etc/nginx/sites-enabled/default` . Nginx was listening on `8080` instead of `80`.

08:51: nginx was configured to listen on port 80 and restarted successfully.
# Root Cause.
Nginx was listening to port 8080 instead of port 80 used to access web server endpoint. The nginx server config file /etc/nginx/sites-enabled/default was set to listen to port 8080 instead of port 80. The issue was fixed by changing the default port 8080 to 80 using  a shell command:

`sudo sed -i 's/8080 default_server/80 default_server/g' /etc/nginx/sites-enabled/default`

and nginx was restarted by the command:

`sudo service nginx restart`

# Corrective and Preventative measures.
Nginx server management can be improved by using script automation. A server monitoring tool should be set up to enable quick response to problems.
Todos
1. Restrict users' access to the server to avoid unconfirmed server changes.
2. Create nginx automation script in shell or puppet.
3. Install a server monitoring system and a response team to handle server problems.

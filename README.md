 <mark>Installation steps only </mark>



# Install SftpGo (with pkg)
 
 # Download file from Github repository:
    sudo wget https://github.com/drakkan/sftpgo/releases/download/v2.6.4/sftpgo_2.6.4-1_amd64.deb

 # Install service:
    sudo dpkg -i sftpgo_2.6.4-1_amd64.deb

 # Create Service:
    sudo systemctl enable sftpgo
    sudo systemctl start sftpgo
    sudo systemctl status sftpgo
    
 # Visit the following address in your browser:
    http://<your_ip>:8080/
        
# Install pgadmin & postgress-sql 
# install the public key for the repository (if not done previously):  
    curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg

  # Create the repository configuration file:
    sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list 
    && apt update'
        
  # Install pgAdmin:
    sudo apt install pgadmin4

  # Configure the webserver, if you installed pgadmin4-web:
        
    sudo /usr/pgadmin4/bin/setup-web.sh

  # Visit the following address in your browser:
      http://<your_ip>/pgadmin4/
     
  # Install PostgreSQL:
    sudo apt -y install postgresql
    sudo systemctl enable postgresql
    sudo systemctl start postgresql
    systemctl status postgresql

  # Install Haproxy(LoadBalancer):
    sudo apt install haproxy

  # Create Service:
    sudo systemctl enable haproxy
    sudo systemctl start haproxy
    sudo systemctl status haproxy
    

  # Haproxy performance test:
    sudo haproxy -c -f /etc/haproxy/haproxy.cfg


    
# Visit the following address in your browser:
    http://<HAProxy_IP>:8800/

   

       
  <mark> Configuration sftpgo architecture  </mark>  

  # Haproxy configuration to set load balancer for nodes

  # Edit the haproxy configuration file:
    sudo vim /etc/haproxy/haproxy.cfg

# Add at the end of the file:
      defaults
          log     global
          mode    http
          option  httplog
          timeout connect 5000ms
          timeout client  50000ms
          timeout server  50000ms

      frontend sftpgo
          bind *:8080
          mode http
          default_backend sftpgo_servers

      backend sftpgo_servers
          mode http
          balance roundrobin
          option httpchk GET /healthz
          server sftpgo1 172.20.104.58:8080 check
          server sftpgo2 172.20.104.59:8080 check

      listen stats
          bind :8800
          mode http
          stats enable
          stats uri /
          stats hide-version
          stats auth admin:password

     

    
     

# Get Access to Your Remote PostgreSQL Database running on VPS in No Time: Here's How

Connecting to a PostgreSQL database running on an Ubuntu VPS from a local machine using VS Code involves the following steps:

1. Install the "PostgreSQL" extension in VS Code:
    
    * it should look like the below screenshot.👇
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672194168825/026e9f0a-cf4d-4274-b3d9-fa36ebd6d1fa.png align="center")
        
        Click "Install" to install the extension
        
2. Configure the remote PostgreSQL server to accept connections from your local machine. To do this, you will need to edit the `pg_hba.conf` file, which controls which clients are allowed to connect to the server. Follow these steps:
    
    * Connect to your Ubuntu VPS using SSH
        
    * Navigate to the `pg_hba.conf` file, which is usually located at `/etc/postgresql/10/main/pg_hba.conf` (*the exact path may vary depending on your PostgreSQL version*)
        
    * Edit the file using a text editor such as vi or nano:
        
    
    ```bash
    sudo vi pg_hba.conf
    ```
    
    * under `IPv4 local connections`, set it to your ipaddress or `0.0.0.0/0` if your IP is dynamic.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672194472641/b31e0ba7-b4fe-42d8-ad95-d2d3e82a4eae.png align="center")
        
3. Configure the remote PostgreSQL server to listen for connections on the network. By default, PostgreSQL only listens for connections on the local loopback interface (127.0.0.1). To allow connections from other hosts, you will need to edit the `postgresql.conf` file.
    
    * Navigate to the `postgresql.conf` file, which is usually located at `/etc/postgresql/10/main/postgresql.conf` (*the exact path may vary depending on your PostgreSQL version*)
        
    * Edit the file using a text editor such as vi or nano:
        
    
    ```bash
    sudo vi postgresql.conf
    ```
    
    * Find the line that reads `listen_addresses = '`[`localhost`](http://localhost)`'` and change it to `listen_addresses = 'your_server_ipaddress'` like below.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672195070894/91aa8a66-785f-42a0-b8ea-5a0bfae4cbb8.png align="center")
        
4. Restart the PostgreSQL server to apply the changes. You can do this by running the following command:
    
    ```bash
    sudo systemctl restart postgresql
    ```
    
5. Connect to the remote PostgreSQL server from VS Code. To do this, follow these steps:
    
    * Click on the Database icon in left of sidebar. it should like like below image.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672195360480/43bb3cba-6cee-48af-9a9d-2d2fe4c664c6.png align="center")
        
    * Click on create new connection and you will see the screen like below image.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672195451084/569bb529-6891-45b1-b3af-9b5f0c9430fc.png align="center")
        
    * fill all the parameters including ones in ssh tunnel too.
        
    * click connect after filling everything.
        
    * now you will see all the database tables in sidebar.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672195612889/c8e17909-1bd6-489f-8a72-9b848d428d06.png align="center")
        

# Is this method secure 🤔

The method described above for connecting to a PostgreSQL database running on an Ubuntu VPS from a local machine using vs code is generally secure, as it uses the md5 authentication method and requires a valid username and password to access the database.

However, still there can be lot of things to make it secure & I don't have much info on how to make it most secure. but there are a few additional steps you can take to further secure the connection:

* Use a strong password for the PostgreSQL user. Make sure to use a password that is difficult to guess and not shared with any other accounts.
    
* Enable SSL encryption for the connection. To do this, you will need to edit the `postgresql.conf` file on the remote server and set the `ssl` parameter to `on`. You will also need to generate SSL certificates and configure them on both the client and server sides. Checkout this [tutorial](https://www.howtoforge.com/how-to-enable-ssl-for-postgresql-connections/) for more info on this step.
    
* Use a firewall to limit access to the PostgreSQL server. You can use a firewall such as iptables or ufw to allow connections only from trusted IP addresses or networks.
    
* Regularly update and patch the PostgreSQL server to keep it secure. Make sure to keep the server up to date with the latest security updates and patches.
    

you can do more research on making it more secure.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672196321602/ffd94bf1-82a9-41ad-a4f9-846b03653de7.gif align="center")

In **conclusion**, connecting to a PostgreSQL database from a local machine using a GUI tool such as pgAdmin or a VS Code extension like **PostgreSQL** by weijan can be a convenient way to manage and query the database. To do this, you will need to install the appropriate tool, configure the remote PostgreSQL server to accept connections, and establish a connection to the database using the hostname, port, username, and password. By following the steps outlined in this answer, you should be able to successfully connect to a PostgreSQL database from a local machine.

It is important to keep in mind that security is an important aspect of any database connection. To ensure that your connection is secure, it is recommended to use a strong password, enable SSL encryption, use a firewall to limit access to the server, and regularly update and patch the PostgreSQL server.

I hope this tutorial helps you. if you have any questions or suggestions, pls drop them in the comments.💬

### If you want a Django developer who can bring your project to life, don't hesitate to contact me and let's discuss your needs! 😀
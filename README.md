P.S., future: 
- mongodb -> k8s
- swagger for api doc ([link](https://swagger.io/))

# nodejs-mysql-docker-backend

A learning-focused API backend server for performing CRUD operations to MySQL database. It simulates a user managing subscription plans offered by a company. `Managing` = creating new plans, retrieving individual plan details / all plan details, updating existing plan info, and deleting plans from the database through this custom API server running in a Docker container by Docker Compose.


# Demo Video

`Get All Plans` API demo,

==> (1) from starting the API server, (2) waiting it starts up, (3) using Postman to **GET HTTP** call the API server endpoint `http://{HOST_NAME}/item` to get info of all subscriptions from database, (4) to verifying API server can receive the API call from Postman

https://user-images.githubusercontent.com/71545537/129474116-aa757cb9-c6bc-4301-acc8-9a0360a72377.mp4

`Create new plan` API demo,

==> (1) from starting the API server, (2) waiting it starts up, (3) using Postman to **POST HTTP** call the API server endpoint `http://{HOST_NAME}/item` to create a new subscription plan to database, (4) to verifying API server can receive the API call from Postman

https://user-images.githubusercontent.com/71545537/129475524-af9a2e0a-afe8-446f-97b3-266818ca4d3d.mp4


## File usage
0. `db/testing-and-data-model.sql` - describe the data model in MySQL database
1. `app/app.js` - creating server and server configuration
2. `app/src/route/item.route.js` - handling routes
3. `app/src/controller/item.controller.js` - handling client request/response
4. `app/src/model/item.model.js` - dealing with database operations
5. `app/config/db.js` - for database configurations

`start.sh` - Bash Script to start the project at host startup. `cron` is used to enable `start after reboot`.
([Reference1](https://www.baeldung.com/linux/run-script-on-startup), [Reference2](https://linuxconfig.org/how-to-run-script-on-startup-on-ubuntu-20-04-focal-fossa-server-desktop))

## Instructions on starting the project

This program is tested under 
1. `macOS Big Sur` `version 11.4` and 
2. `Ubuntu 20.04` (in Virtualbox)

_______
This project locates exactly under `$HOME` path (i.e. `/home/$USERNAME/`) in Ubuntu by default to enable `start after host reboots`.

<img src="img\project location in ubuntu 20.04.png" style="zoom:50%;"/>

_______

To enable `docker starts on boot`

1.  Most Linux distributions (RHEL, CentOS, Fedora, Debian, Ubuntu 16.04 and higher) use `systemd` to manage which services start when the system boots.

    `On Debian and Ubuntu, the Docker service is configured to start on boot by default.` - By [docker docs](https://docs.docker.com/engine/install/linux-postinstall/#configure-docker-to-start-on-boot)

    a.  To ensure `docker.service` and To list all enabled services from `systemctl`:
    
    ==> https://askubuntu.com/questions/795226/how-to-list-all-enabled-services-from-systemctl
    
    ```sh
    # See the one currently running, you need 
    $ systemctl | grep running
    
    # To list all enabled services from `systemctl`
    $ systemctl list-unit-files | grep enabled
    proc-sys-fs-binfmt_misc.automount          static          enabled      
    -.mount                                    generated       enabled      
    boot-efi.mount                             generated       enabled      
    dev-hugepages.mount                        static          enabled
    ...
    docker.service                             disabled        enabled
    ...
    containerd.service                         disabled        enabled
    ...
    ```

    b.  To automatically start Docker and Containerd on boot, use commands:

    ==> https://docs.docker.com/engine/install/linux-postinstall/#configure-docker-to-start-on-boot

    ```sh
    $ sudo systemctl enable docker.service
    $ sudo systemctl enable containerd.service
    ```

2.  Move `start.sh` to `$HOME` directory (i.e. `/home/$USERNAME/`).

    Change the path of `cd` in `start.sh` if the project location is different.

    <img src="img\start.sh in HOME path.png" style="zoom:50%;"/>

2.  Command to deal with shell script `.sh`:
    ```sh
    $ cd /home/$USERNAME/nodejs-mysql-docker-backend
    # Sets access permissions for .sh
    $ sudo chmod 744 start.sh 
    # Execute the shell script to test
    $ ./start.sh
        OR
    $ sh start.sh
    ```

3.  Deal with `cron`:
    ```sh
    # Set crontab
    $ crontab -e

    # Add a line in crontab (i.e. config file of cron) using the @reboot expression
    ## Change the path if the project location is different
    @reboot sh /home/$USERNAME/nodejs-mysql-docker-backend/start.sh
    ```

Video shows containers are started after reboot.

https://youtu.be/8oQAn82D0aU

### With docker-compose

1.  In the terminal, go to the porject directory containing `docker-compose.yaml`, and input 
    ```sh
    $ cd nodejs-mysql-docker-backend/
    $ docker-compose up --build
        OR
    # Run in detached mode
    $ docker-compose up --build -d
    ```

    <img src="img\docker-compose up, success 1.png" style="zoom:50%;"/>

    Output of the successful execution:
    
    <img src="img\docker-compose up, success 2.png" style="zoom:45%;"/>

2.  Send HTTP request with Postman to containerized API server to retrieve all plan info,

        Method: GET
        URL:    http://localhost:4000/item

    a. Output from Postman,

    <img src="img\docker-compose, request, postman, get all plans.png" style="zoom:25%;"/>

    b. Output from API server,

    <img src="img\docker-compose, request, server, get all plans.png" style="zoom:50%;"/>

3.  Create a new plan,

        Method: POST
        URL:    http://localhost:4000/item
        Body:   [JSON]
                {
                    "plan_id" : 22,
                    "plan_name" : "test",
                    "general" : 0,
                    "specialist" : 0,
                    "physiotherapy" : 0,
                    "SYMPTOM_n" : 1,
                    "plan_desc" : "abd",
                    "plan_month_price" : 9999
                }
    
    a. Output after created,
    
    <img src="img\docker-compose, request, postman, create 1.png" style="zoom:50%;"/>

    b. Database stored the new plan info. We can see the new plan with `"plan_id": 3`.

    <img src="img\docker-compose, request, postman, create 2.png" style="zoom:50%;"/>
    
4.  Video
    _______
    `Get All Plans` API demo,

    https://user-images.githubusercontent.com/71545537/129474116-aa757cb9-c6bc-4301-acc8-9a0360a72377.mp4

    `Create new plan` API demo,

    https://user-images.githubusercontent.com/71545537/129475524-af9a2e0a-afe8-446f-97b3-266818ca4d3d.mp4
    _______

### WITHOUT docker-compose
Steps:

1. Start the API server,
    ```javascript
    node app.js
    ```
2. Send request to server!
    
    Test with Postman, request to Get All Plans:
    
        Method:  GET
        URL:     http://localhost:4000/item


## API 



_______
## Note

1.  Why use `POST` HTTP request to do the below code Read operation?

    Code in `item.route.js`:
    ```js
    // Retrieve a single item with id
    router.post('/name', controller.findByName);
    ```

    Ans: `GET` HTTP method is suggested NOT TO handle entity-body (i.e. JSON body) in a request. So, we use `POST` request instead.

    Ref,
    1. https://stackoverflow.com/questions/978061/http-get-with-request-body
    2. https://datatracker.ietf.org/doc/html/rfc2616#page-53

2.  Data structure in `item.model.js`:
    ```js
    // Item Object

    var Item = function(item){
        this.plan_id = item.plan_id;
        this.plan_name = item.plan_name;
        this.general = item.general;
        this.specialist = item.specialist;
        this.physiotherapy = item.physiotherapy;
        this.SYMPTOM_n = item.SYMPTOM_n;
        this.plan_desc = item.plan_desc;
        this.plan_month_price = item.plan_month_price;
    };
    ```

3.  Why need an extra shell script while `depends_on` exists in `docker-compose.yaml`?  

    Ans 1: https://docs.docker.com/compose/startup-order/

    Ans 2: [from bottom reference link]

    `depends_on` does not guarantee the execution of services will be kept in order.
    
    So it needs to use shell script (i.e. `wait.sh`) to control the execution of the services.

    ```yaml
    version: "3"

    services:
    nodejs:
        image: rest-app
        restart: on-failure # restart the app if app fails to start
        build:  # looking for Dockerfile in "./app" to build this "nodejs" image
        context: ./app
        dockerfile: Dockerfile
        depends_on: # indicate "db" service should be executed first 
        - db
        ports:
        - "4000:4000" # map host port 4000 to container port 4000
        entrypoint: ["/wait.sh"] # In case "depends_on" not work, use this shell script to control the service execution order
                                # "entrypoint" keyword: to execute the shell script file, i.e., wait.sh.
    
    ...
    ```

4.  Why `db` is ok to be the hostname (i.e. maps to IP address) to find the MySQL server in docker network?
    
    Ans: 
     
    `By default Compose sets up a single network for your app. Each container for a service joins the default network and is both reachable by other containers on that network, and discoverable by them at a hostname identical to the container name.`
    By https://docs.docker.com/compose/networking/

    _______
    When you run docker-compose up, the following happens:

    1. A network called `nodejs-mysql-docker-backend_default` is created.
    2. A container is created using `db`’s configuration. It joins the network `nodejs-mysql-docker-backend_default` under the name `db`.
    3. A container is created using `rest-app`’s configuration. It joins the network `nodejs-mysql-docker-backend_default` under the name `rest-app`.
    _______

    Docker has a default DNS server, it takes the service/container name in "docker-compose.yaml" as the hostname (which can be mapped to container's IP address).

    ``` js
    'use strict';

    const mysql = require('mysql');

    var con = mysql.createConnection({
        //Why `db` can be ok to be the IP address to find the MySQL server?
        //Ans: 
        //=> Each container for a service joins the default network and is both reachable by other containers on that network, and discoverable by them at a hostname identical to the container name.
        //=> docker has a default DNS server, it takes the service/container name in "docker-compose.yaml" as the hostname (which can be mapped to container's IP address).
        //ref: https://docs.docker.com/compose/networking/ 
        // host: 'localhost',
        host: 'db',	
        user: 'root',
        password: '00000000',
        database: 'plan',
        // port: 3300
    });

    ...
    ```

5.  To enable auto-start docker containers on boot, please ensure the below 2 items:

    a.  Enable `docker.service` in `systemd`:

    ==> https://docs.docker.com/engine/install/linux-postinstall/#configure-docker-to-start-on-boot

    ```sh
    $ sudo systemctl enable docker.service
    $ sudo systemctl enable containerd.service
    ```

    b.  Configure the restart policy for a container to `restart: always` in `docker-compose.yaml`:
    
    ==> (1) https://docs.docker.com/config/containers/start-containers-automatically/#use-a-restart-policy 
    
    ==> (2) https://docs.docker.com/compose/compose-file/compose-file-v3/#restart

    ```yaml
        db:
            image: mysql:8.0.25
            command: --default-authentication-plugin=mysql_native_password
            restart: always #on-failure # Ensure mysql container can be restarted after reboot
            ports:
                - "33000:3306"
            environment:
                MYSQL_ROOT_PASSWORD: "00000000"
            volumes:
                - ./db:/docker-entrypoint-initdb.d/:ro
    ```


##### Design steps

1. Design data model in MySQL ==> `testing-and-data-model.sql`
2. Desgin the API routing in `item.route.js` 
3. Design functions in `item.model.js` to interact with MySQL database
4. Design `


##### Reference

[link-api-server](https://roytuts.com/nodejs-express-mysql-rest-api-crud-example/)

[link-docker-compose](https://roytuts.com/docker-compose-dockerizing-nodejs-mysql-rest-api-crud-example/)

[link-react-nodejs-mysql](https://www.bezkoder.com/react-node-express-mysql/)

# Docker-Laravel_setup
### Laravel + Docker setup 

<ol>
Laravel is a free, open-source PHP web framework. To create a connection between laravel and docker we need to download docker (to install docker follow the given link "/") <br>
After downloading a docker into the system, we need to <strong>download laravel and install it's dependencies</strong>.</br>
start work from your home directory.<br>
<strong>$ cd</strong><br></br>

<li>Download laravel</li>
<strong>$ git clone https://github.com/laravel/laravel.git name_of_directory</strong></br>
<strong> git clone https://github.com/laravel/laravel.git lara_proj1</strong></br>  </br>

![1](https://user-images.githubusercontent.com/47202519/57177133-b8fc7c00-6e7e-11e9-893f-b2f961c1d975.png)</br></br>

<li>move into the lara_proj1 directory.</li>
<strong>$ cd lara_proj1</strong><br></br>

<li>Composer</li>
composer is a dependency manager that written in and for php.<br>
it allows you to declare the libraries your project depends on and it will manage (install/update) them.</br>

<strong>$ docker run --rm -v $(pwd):/app composer install</strong><br></br>

we use -v and -rm flages with docker run, it creates a temporary container that will be bind-mounted(replica) to your current directory before being removed. </br>
This will copy the content of your directory (Lara-proj1) to the container and by default, the vendor folder Composer created inside the current working directory.<br>  

![2](https://user-images.githubusercontent.com/47202519/57177135-bd289980-6e7e-11e9-8114-3277af99c764.png)</br></br>

during this command, these dependencies will take few-time  to get download.<br>
</ol>

### Creating the Docker Compose File

<ol>
<strong>Docker-compose.yml</strong> file is the main connection file, in which we make a connection between our used services.</br>
<strong>Laravel application</strong> runs using <strong>nginx</strong> (proxy and load balancer server) with <strong>SQL</strong> connectivity.</br></br>

<li><strong>version:</strong> docker compose version</li>

<strong>version: '3'</strong></br></br>

<li><strong>services:</strong> which is being used in the project</li>

```

services:
  # name of service :PHP
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: app
    restart: unless-stopped
    tty: true
    environment:
      SERVICE_NAME: app
      SERVICE_TAGS: dev
    working_dir: /var/www
    networks:
      - app-network
```

<li><strong>context:</strong> will define the path of the directory.</li>
<li><strong>dockerfile:</strong> will provide a path of dockerfile in this directory.</li>
<li><strong>container_name:</strong> which container we want to run we will define here like-laravel applictaion</li>
<li><strong>restart:</strong> will up your container unless we don't stop it.</li>
<li><strong>tty:</strong> tty is essentially a text input-output environment aka shell.</li>
<li><strong>environment:</strong> the given environment will run inside of Docker container.</li>
<li><strong>working_dir:</strong> sets the working directory of the container that is created.</li></br>

<li><strong>Persisting Data</strong></li>
we will use the <strong>volume and bindmounts</strong> for Persisting (existing) a data. volume will provide the backup of the existing data.</br>
app service contains the application code to the <strong>/var/www</strong> folder in the container. it will speed up the development process. if we make any changes in the local application directory those changes reflected inside the container.</br>
we are also binding our PHP configuration file,<strong> ~/project_directory/php/local.ini, to /usr/local/etc/php/conf.d/local.ini</strong> inside the container.</br></br>

```
  
  volumes:
       - ./:/var/www
       - .~/project_directory/php/local.ini:/usr/local/etc/php/conf.d/local.ini

```

------------------------------------------------------

```
  # Nginx Service

  webserver:
    image: nginx:alpine
    container_name: webserver
    restart: unless-stopped
    tty: true
    ports:
      - "8084:80"
    networks:
      - app-network

```

<li><strong>port:</strong> we need to take two ports to run nginx image.</br>
image build at "80" port and browse in "8084" port.</li>

```

    volumes:
      - ./:/var/www
      - ./nginx/conf.d/:/etc/nginx/conf.d/

```

bind the application code in the project directory to the <strong>/var/www</strong> directory inside the container.</br>
The configuration file that you will add to <strong>~/project_directory/nginx/conf.d/</strong> will also be mounted to <strong>/etc/nginx/conf.d/</strong> in the container, allowing you to add or modify the configuration directory's contents as needed.</br>

------------------------------------------------------
```
# MySQL Service
  db:
    image: mysql:5.7.22
    container_name: db
    restart: unless-stopped
    tty: true
    ports:
      - "13306:3306"
    environment:
      MYSQL_DATABASE: laravel
      MYSQL_ROOT_PASSWORD: passcode
      SERVICE_TAGS: dev
      SERVICE_NAME: mysql
    networks:
      - app-network

```

pulls the mysql:5.7.22 image from Docker and defines a few environmental variables, including a database and the root password for the database.This service definition also maps port 13306 on the host to port 3306 on the container.</br>

```

volumes:
      - dbdata:/var/lib/mysql
      - ./mysql/my.cnf:/etc/mysql/my.cnf

```

<li><strong>dbdata:</strong> persists the contents of the <strong>/var/lib/mysql</strong> folder present inside the container. This allows you to stop and restart the db service without losing data.
 bind mount binds <strong>/project_directory/mysql/my.cnf to /etc/mysql/my.cnf</strong> in the container.</li>

 -----------------------------------------------------
 <li><strong>app-network:</strong> provide the communication between the containers, the services are connected to a bridge network.</li>
 
```

 # Docker Networks
networks:
  app-network:
    driver: bridge

```


</ol>

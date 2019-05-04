# Docker-Laravel_setup
### Laravel + Docker setup steps one by one

<ol>
Laravel is a free, open-source PHP web framework. To create a connection between laravel and docker we need to download docker (to install docker follow the given link "/") <br>
After downloading a docker into the system, we need to <strong>download laravel and install it's dependencies</strong>.</br>
start work from your home directory.<br>
<strong>$ cd</strong><br>
<li>Download laravel</li>
<strong>$ git clone https://github.com/laravel/laravel.git name_of_directory</strong></br>
<strong> git clone https://github.com/laravel/laravel.git lara_proj1</strong></br>
1

<li>move into the lara_proj1 directory.</li>
<strong>$ cd lara_proj1</strong><br>

<li>Composer</li>
composer is a dependency manager that written in and for php.<br>
it allows you to declare the libraries your project depends on and it will manage (install/update) them.</br>

<strong>$ docker run --rm -v $(pwd):/app composer install</strong><br>

we use -v and -rm flages with docker run, it creates a temporary container that will be bind-mounted(replica) to your current directory before being removed. </br>
This will copy the content of your directory (Lara-proj1) to the container and by default, the vendor folder Composer created inside the current working directory.<br>
2
during this command, these dependencies will take few-time  to get download.<br>
</ol>

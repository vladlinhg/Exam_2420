# Exam_2420

## Establish Server Connection

### Author

+ **Nai Yen Lin**
  + *A01320713*


## Part 1

The command to update most of the software on your Ubuntu OS

```bash
sudo apt update
sudo apt upgrade
```


## Part 2

Using replace function in Vim

```bash
:s/1/0 # to replace first 1 to 0 in the script
:%s/V/C/g # to replace all upper letter V to upper letter C
:%s/eco/echo/g # to replace all "eco" to "echo" in this script
```

Then press "I" on the keyboard, goes into "insert mode".
Using "down arrow" to move the cursor down, delete "numb" and second last two row.
Replace it with ":digit:" (it gives me error when i try to edit with replace command) 

Screenshots of script are provided below.
![image_part2](Images/part2.png)


## Step 3

Get to the manual page of "journalctl" first.

```bash
man journalctl
/boot #search for option to print log of the current boot (lastest one)
/warning #search for option to prioritize the warning
/json #search for format to output pretty json

sudo journalctl -b -0 -p warning --output-field=json-pretty #The command I used to access
```

Screenshot of boot part.
![image_part3_boot](Images/part3_boot.png)

Screenshot of warning part. 
![image_part3_priority](Images/part3_priority.png)

Screenshot of json part. 
![image_part3_pretty_json](Images/part3_pretty_json.png)

Screenshot of output part. 
![image_part3_output](Images/part3_output.png)



## Step 4

Create website files to upload.

Create a new directory call **html**.
  ```bash
    mkdir html
  ```
Create a **html file** called **index.html**.
  ```bash
    vim html/index.html
  ```

Here is the content written in index.html.
  ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>My Website</title>
    </head>
    <body>
        <h1>This is [Author]'s Website on Server [one or two]</h1>
    </body>
    </html>
  ```

Create a new directory call **src**, and go to the newly created directory.
  ```bash
    mkdir src
    cd src
  ```

Create new **node** project inside **src**, and installation of **fastify** will then be performed here.
  ```bash
    npm init
    npm i fastify
  ```

Create a **javascript** file called **index.js** in **src**.
  ```bash
    vim index.js
  ```

Here is the content written in index.js, you will add host and change port.
  ```javascript
    // Require the framework and instantiate it
    const fastify = require('fastify')({ logger: true })

    // Declare a route
    fastify.get('/api', async (request, reply) => {
    return { hello: 'Server [one or two]' }
    })

    // Run the server!
    const start = async () => {
    try {
        await fastify.listen({ port: 5050, host: '127.0.0.1' })
    } catch (err) {
        fastify.log.error(err)
        process.exit(1)
    }
    }
    start()
  ```



## Step 5

Write the **Caddyfile** 

Codes are provided to access the file.
  ```bash
    sudo vim /etc/caddy/Caddyfile
  ```

Modify the contents in caddy file, indicates **its port** and **where html file is located**.
  ```bash 
    # The Caddyfile is an easy way to configure your Caddy web server.
    #
    # Unless the file starts with a global options block, the first
    # uncommented line is always the address of your site.
    #
    # To use your own domain name (with automatic HTTPS), first make
    # sure your domain's A/AAAA DNS records are properly pointed to
    # this machine's public IP, then replace ":80" below with your
    # domain name.

    http:// {
            # Set this path to your site's directory.
            root * /var/www/html

            reverse_proxy /api localhost:5050

            # Enable the static file server.
            file_server

            # Another common task is to set up a reverse proxy:
            # reverse_proxy localhost:8080

            # Or serve a PHP site through php-fpm:
            # php_fastcgi localhost:9000
    }

    # Refer to the Caddy docs for more information:
    # https://caddyserver.com/docs/caddyfile
  ```

Screenshot is avaliable.
![image_caddy_file](img/caddy_file.png)

Don't forget to give permission to execute Caddyfile.
  ```bash
    sudo chown caddy:caddy /etc/caddy/Caddyfile
    sudo chmod 660 /etc/caddy/Caddyfile
  ```


## Step 6

Install **node** and **npm** with **Volta**.

Codes are provided.
  ```bash
    curl https://get.volta.sh | bash
    source ~/.bashrc
    volta install node
    which npm
    volta install npm
  ```



## Step 7

Write a **service file** to start server.

Open the file to write.
  ```bash
  sudo vim /etc/systemd/system/hello_web.service
  ```

The contents are wriiten like this.
  ```bash
    [Unit]
    Description=It is a web app
    After=network-online.target
    Wants=network-online.target

    [Service]
    ExecStart=[directory of node installed by Volta] [direcotry of your index.js]
    User=nlin
    Restart=always
    RestartSec=15
    TimeoutStopSec=60
    SyslogIdentifier=hello_web

    [Install]
    WantedBy=multi-user.target
  ```

Start both **caddy and hello_web.service**.
  ```bash
    sudo systemctl daemon-reload
    sudo start caddy
    sudo start hello_web.service
  ```



## Step 8

Check the functionality.

Access the site inside terminal.
  ```bash
    curl [your host ip]
    curl [your host ip]:[port]/api
  ```

Screenshots are provided for both **server one and two**.

![image_server1_curl](img/server1_curl.png)

![image_server1_curl_api](img/server1_curl_api.png)

![image_server2_curl](img/server2_curl.png)

![image_server2_curl_api](img/server2_curl_api.png)


Access the webpage with browser with **ip of load balancer**.

![image_browser_1](img/browser_1.png)

![image_browser_api_1](img/browser_api_1.png)

![image_browser_2](img/browser_2.png)

![image_browser_api_2](img/browser_api_2.png)




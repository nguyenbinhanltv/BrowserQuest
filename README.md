BrowserQuest
============

It has three major parts:

* the server side, which runs using Node.js
* the client side, which runs using javascript in your browser
* the database side, which runs using Redis

Ubuntu / Windows
------

    $ sudo apt-get update
    $ sudo apt-get upgrade
    $ sudo apt-get install g++ make memcached libncurses5 redis-server git -y
    $ curl -sL https://deb.nodesource.com/setup | sudo bash -
    $ sudo apt-get install nodejs

Clone the git repo:

    $ git clone https://github.com/nguyenbinhanltv/BrowserQuest.git
    $ cd BrowserQuest

Then install the Node.js dependencies by running:

    $ npm config set registry http://registry.npmjs.org/
    $ npm install -d
    
Before starting the BrowserQuest server, you must start Redis. In Windows, you can simply run `redis-server.exe`.
Download Redis here: https://github.com/dmajkic/redis/downloads

Then start the server by running:

    $ node server/js/main.js

The BrowserQuest server should start, showing output like this:

    $ node server/js/main.js
    This server can be customized by creating a configuration file named: ./server/config_local.json
    [Thu Sep 13 2012 17:16:27 GMT-0400 (EDT)] INFO Starting BrowserQuest game server...
    [Thu Sep 13 2012 17:16:27 GMT-0400 (EDT)] INFO world1 created (capacity: 200 players).
    [Thu Sep 13 2012 17:16:27 GMT-0400 (EDT)] INFO world2 created (capacity: 200 players).
    [Thu Sep 13 2012 17:16:27 GMT-0400 (EDT)] INFO world3 created (capacity: 200 players).
    [Thu Sep 13 2012 17:16:27 GMT-0400 (EDT)] INFO world4 created (capacity: 200 players).
    [Thu Sep 13 2012 17:16:27 GMT-0400 (EDT)] INFO world5 created (capacity: 200 players).
    [Thu Sep 13 2012 17:16:27 GMT-0400 (EDT)] INFO Server (everything) is listening on port 8000

That means its working.  There should not be any warnings or errors.

Using a browser, connect to port 8000 of the server entered above.  The
BrowserQuest start page should appear, and the game should work.

Deploying BrowserQuest (Because requiment Nodejs 8 but server using Nodejs v 0.10. Should run at local!!!)
----------------------

Currently, BrowserQuest can run on the following PAAS (Platform as a Service) providers:
* [OpenShift](https://www.openshift.com)
* [Heroku](https://www.heroku.com)

### Instructions for OpenShift ###
1. Follow the instructions to get started with the OpenShift client tools [here](https://www.openshift.com/get-started).

2. Create a new application by running this command:

        $ rhc app create <app-name> nodejs-0.6
        $ cd <app-name>

   where \<app-name\> is the name of your app, e.g. browserquest.

3. Add the Redis cartridge (necessary for BrowserQuest to be able to store data) with the following command:

        $ rhc add-cartridge \
          http://cartreflect-claytondev.rhcloud.com/reflect?github=smarterclayton/openshift-redis-cart \
          --app <app-name>

4. Add the BrowserQuest repository, and pull its contents with the following commands:

        $ git remote add github https://github.com/nguyenbinhanltv/BrowserQuest.git
        $ git fetch github
        $ git reset --hard github/master
        
5. Copy the BrowserQuest config file with the following command:

        $ cp server/config.json server/config_local.json
    
6. Open `server/config_local.json` in a text editor such as Gedit (Linux), TextEdit (OS X), or Vim.
On the line that reads `"production": "heroku",`, change `"heroku"` to `"openshift"`.

7. Add this file to your repository by running the following commands:

        $ git add server/config_local.json
        $ git commit -m "Added config_local.json"

8. Now, deploy to OpenShift with one final command (this will take several minutes):

        $ git push -f

Congratulations! You have now deployed BrowserQuest to Openshift! You can see the url of your instance by running


    $ rhc app show <app-name>

Visit the url shown by the above command to see BrowserQuest running. You will need to add ":8000" to the end. Use the url below as a guide:

    http://your_openshift_browserquest_url.rhcloud.com:8000/
    
### Instructions for Heroku ###

1. Install the Heroku toolbelt from [here](https://toolbelt.herokuapp.com/).

2. Create a new application by running the following command:

        $ heroku create [NAME]
    
Where [NAME] is an optional name for your application (Heroku will automatically create one otherwise).

3. Sign up for a Redis provider, such as [Redis To Go](https://redistogo.com), or host a Redis instance yourself.

4. Run the following commands to allow BrowserQuest to run on Heroku:

        $ heroku config:add HEROKU=true
        $ heroku config:add HEROKU_REDIS_HOST=[REDIS_HOST]
        $ heroku config:add HEROKU_REDIS_PORT=[REDIS_PORT]
        $ heroku config:add HEROKU_REDIS_PASSWORD=[REDIS_PASSWORD]
    
Where [REDIS_HOST], [REDIS_PORT], and [REDIS_PASSOWRD] are your Redis hostname, port, and password, respectively.
If you Redis instance is configued without a password, omit the last command.

Note: If you use RedisToGo, you will be provided with a URL that looks something like this:

    redis://redistogo:12345678901234567890@something.redistogo.com:9023/
    
In this case, your REDIS_HOST is `something.redistogo.com`, your REDIS_PORT is `9023`, and your REDIS_PASSWORD is `12345678901234567890`.

5. Deploy to Heroku by running the following command:

        $ git push heroku master
    
6. Enable the Heroku WebSockets lab (needed for communication between the browser and the BrowserQuest server) with the following command:

        $ heroku labs:enable websockets
    

Congratulations! You have now deployed BrowserQuest to Heroku! To open BrowserQuest in your browser, run `heroku open`.

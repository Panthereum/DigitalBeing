# What is Digital Being? #

Digital Being (DB) is a software written in Typescript and Python used to deploy DL or ML agents easily in the world.

A platform for letting researchers connect an intelligent AI directly to real time communication networks and 3D worlds.

[In partnership with SuperReality, a free open source teaching platform for everyone.](https://superreality.com)

The Client is written in Typescript, which holds the Clients of most known social networks, as well XR-Engine which is a 3D World.

While the server is written in Python, as Python is the most used language for Deep and Machine Learning.

Currently the Client supports out of the Box Discord (Servers, Threads, Direct Messages), Twitter (Twits, Direct Messages), Instagram (Direct Messages), Messenger (Direct Messages - Facebook Pages), Reddit (Posts, Messages), Telegram (Group, Direct Messages), Twilio (SMS), WhatsAPP (Not tested), XR-Engine (Chat, also supports Commands and events from other users - proximity, emotions etc), Zoom (Not fully implemented, Supports Video Group Calls).

The Client is using either Puppeteer (xr-engine, zoom) or the API for each client (discord, messenger etc).

The Server supports out of the box DiaoloGPT, GPTNeo, Wizard of Wikipedia, OpenChat, GPTj, Droidlet, Rasa, GPT3 - Openai, Metaintelligence. What is more, the server can fetch chat history and transform it to a list.

More info about [Networking](https://docs.google.com/document/d/1fz4x1pZGGELPKzlTBgtApiY54oqfNdaCIBTFwRqRDvs/edit?usp=sharing)

The Client is connected with the Server using a TCP Socket, sending JSON encoded packets.

Right now there is sendMessage (chat packet message), sendSlashCommand (slash command from discord - can be implemented to other clients too), sendUserUpdateEvent (on discord join server, leave, on online, idle or offline, all events can be fed to agents), sendGetAgents (returns a list of the active agents), sendSetAgentsFields (this command can be used to update the context of an agent), sendPingSoloAgent (this command can be used to ping directly one agent - if many agents are active), sendMessageReaction (this command is sent when a user reacts to a message - current implemented in discord), sendMessageEdit (this command is sent when a message is edited, in order to update the agent’s response too), sendMetadata (this command is used to send the metadata of the current server - currently implemented in Discord and the xr-engine).

New packets can be implemented easily, the only thing to be noted is that the ID should be different from the old packets, also the packet should be sent as a JSON string, like the examples, to avoid any issues with the server.

How to select available agents:

Go to server/agent_params.py and add the agent name in SELECTED_AGENTS array, or delete/comment unwanted agents, like this.

![alt_text](https://github.com/XRFoundation/DigitalBeing/blob/main/readme_images/Screenshot_286.png)


How to select available clients:

Go to the src/initialize.ts and comment/uncomment the needed clients, like this.

![alt_text](https://github.com/XRFoundation/DigitalBeing/blob/main/readme_images/Screenshot_287.png)

How to port forward in docker:

In order to expose a port in the public inside docker go to docker-compose.yaml, find the needed image and add the needed port like this.

![alt_text](https://github.com/XRFoundation/DigitalBeing/blob/main/readme_images/Screenshot_288.png)
 \
Port forwarding is needed in order to enable public access to a service, like the database, the web server or the bot manager.

## Requirements ##

Digital Being requires the following to run:



* Nodejs - version 16 or more
* Npm - will be installed through nodejs
* Python - version 3.8 or more
* Docker - for easy setup in containers (build scripts are included)

# Setup #

## Windows 10: ##



* Install the requirements (Nodejs, npm, Python)
* Install Rust - required for the python requirements - Rust is needed in order to installed the requirements.txt, the tokenizers library, also if you get errors with installing the `psycopg2 `library, just comment it out and uncomment `psycopg2-binary `library (postgres connector library), the second is already built and will be installed easier without any dependencies
* Install c++ build tools (can be installed through Visual Studio Community)
* Clone the repository and go in the folder
* Run npm install
* Run pip install -r requirements.txt
* Install postgres, setup the user and the database and run the init.sql (root folder)
* Rename the .env.default to .env (or make a new copy) and update the variables
* Run the bot in using npm run in the command line

## Docker (WSL/Linux): ##



* Install docker and docker-compose - [detailed installation instructions](https://docs.docker.com/desktop/windows/wsl/)
* Clone the repository and go in the folder
* Run docker-compose build to build the docker image
* In order to run the image you can use docker-compose up (if in Putty you can use docker-compose up -d to keep the image open after closing)
* In order to close the image you can either use docker-compose down or CTRL+ALT+C

Docker - Options 2 (WSL/Linux):



*  Docker build -t digital_being
* Docker run -d digital_being

## Without Docker (WSL/Linux): ##



* Install the requirements (Nodejs, npm and Python)
* Run npm install
* Run pip install -r requirements.txt
* Install postgres, setup the user and the database and run the init.sql (root folder)
* Rename the .env.default to .env (or make a new copy) and update the variables
* Run the bot in using npm run in the command line

M## isc Information/Troubleshooting: ##

How to change active agents (from the installed): Go to server/agent_params.py and add/remove the SELECT_AGENTS list using the agent name string.

How to change active clients (from the installed): Go to src/initialize.ts and comment/uncomment the clients you need, if you need a client that uses a web server (twilio, messenger), go to client/webserver.ts and uncomment line 17 otherwise you can leave it commented.

How to setup Twilio: In order to run twilio, you will need to have your ports forwarded, this can be done easier though using ngrok, which opens a tunnel. For ngrok you should use the web server port (65535 - ngrok http 65535), ngrok can be used both in linux and windows.

Puppeteer error, that it cant find Chrome: WSL can cause an error with puppeteer that it cant find chromium driver, in order to fix it you will need to install chrome manually, running the following commands:



* wget[ https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb](https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb)
* sudo apt -y install ./google-chrome-stable_current_amd64.deb

## XR-Engine Support: ##

DigitalBeing (DB) can run the xr-engine runs using Puppeteer ([https://github.com/puppeteer/puppeteer](https://github.com/puppeteer/puppeteer)). It connects as a virtual headful client in the engine, it updates the model randomly and to a set name for the bot, it can interact as a person and access everything. Currently it has built in support with the chat which can help it talk with other users and use chat commands, also it reads the client-logs which have info that are about the bot, like scene metadata etc.

**Available Commands - all commands start with a slash (/):**



* /move x,y,z -> moves to x,y,z position using navmesh
* /metadata scene -> gets the scene metadata
* /metadata world,max_distance (from bot) -> gets the world metadata (objects) around the bot
* /listAllusers -> sends a client log with all users
* /goTo landmark (based on world metadata) -> moves towards a landmark
* /emote emote_name -> players an emote
* /getPosition user -> get the position of a user
* /getRotation user -> get the rotation of a user
* /getScale user -> get the scale of a user
* /getTransform user -> get the transform of a user (position, rotation, scale)
* /subscribe system -> subscribe to chat system
* /unsubscribe system -> unsubscribe from a chat system
* /getSubscribed -> get the current subscribed chat systems
* /follow user -> follow a user
* /follow stop -> stop following a user
* /getChatHistory -> get the recent chat history as client log
* /getLocalUserId -> get the local user id as client log
* The xrengine-client.ts has all the functions needed to send commands or generally a chat message

**Scripts Structure:**

The xr-engine engine is one of the DB’s clients, it can handle multiple clients at once though.

There are multiple scripts for the xr-engine:



* chatHistory.ts -> has functions to handle the chat history
* messageHandler.ts -> handles the chat messages and sends to the server side the needed messages
* Stt.ts  -> handles the speech to text engine
* Tts.ts -> handles the text to speech engine
* UsersInRange.ts -> holds lists of users around the bot
* Xrengine-client.ts -> is the main script for the xr-engine, it holds all the functions that use the puppeteer
* xrEnginePacketHandler.ts -> handles the received packets from the python server

**.Env Config File:**

The .env config file holds all the variables that are needed for configuration, they are added externally to support easy changes and avoid rebuilding docker if used.

Some values are the api keys, chat history length, editor values, ports used etc.

Values Explanation:



* Twitter:
    * In order to get the Twitter API tokens a request will be needed to be made for a Twitter Developer Account, then the keys can be fetched this way after creating the Application inside the dev Portal

![alt_text](https://github.com/XRFoundation/DigitalBeing/blob/main/readme_images/Screenshot_283.png)

    * TWITTER_BEARER_TOKEN
    * TWITTER_APP_TOKEN
    * TWITTER_APP_TOKEN_SECRET
    * TWITTER_ACCESS_TOKEN
    * TWITTER_Access_TOKEN_SECRET
    * TWITTER_ID -> The name of the twitter account, e.x. DigitalBeing
    * TWITTER_TWEET_RULES -> Rules (words) that are used to fetch Tweets, they are separated using semicolon (;), for example: digital being;digital;being
* Discord:
    * In order to fetch discord Tokens go to the [Dev Portal](https://discord.com/developers/applications) and click New Application, select a name for the Application and click Create, select the Bot panel and create bot, Client_API_Token can be fetched from the Bot Tab and Client and Client_Secret can be fetched from the OAuth2 tab. In the dev portal you in the bot tab you will need to enable these 2 options in order to make the bot crash.

![alt_text](https://github.com/XRFoundation/DigitalBeing/blob/main/readme_images/Screenshot_285.png)

    * DISCORD_API_TOKEN
    * DISCORD_CLIENT_ID
    * DISCORD_CLIENT_SECRET
    * LOAD_DISCORD_LOGGER -> whether or not to load the external Logger, which sends DMs with logs to a selected user
    * LOG_DM_USER_ID -> The user that the Logs are sent to (can be fetched by enabled the Developer Mode in discord, right click to the user and click Copy Id and will be copied to clipboard)                                  

![alt_text](https://github.com/XRFoundation/DigitalBeing/blob/main/readme_images/Screenshot_284.png)

    * DIGITAL_BEINGS_ONLY -> This variable is a boolean, if set to true, the bot ignores channels that don't have Digital Being in their metadata (Channel Description)
* Twilio:
    * In order to use twilio, you will need to create an account and create a new phone number (Twilio doesn’t receive SMS abroad, so the number should be better from your Region). 
    * In order to run Twilio you will need to install NGROK or an alternative.
    * Run ngrok and with the same HTTP port set for the TWILIO_PORT in the .env -> ngrok http 65535
    * Update Twilio WebHooks for Messaging (SMS) with the new ngrok link x.x.x.x.ngrok.io/sms for the active number
    * Though NGROK can be ignored if you have port forwarded, then you can just use your local ip:port/sms
    * TWILIO_ACCOUNT_SID
    * TWILIO_AUTH_TOKEN
    * TWILIO_PHONE_NUMBER
* Telegram:
    * In order to create a bot in telegram, you will need to install the APP in an Android or iOS device and login/register, then search for the BotFather bot (Should have a blue checkmark beside his name), click Start conversation to activate it and then follow his instructions.
    * TELEGRAM_BOT_TOKEN
* XR-Engine:
    * In order to use the xr-engine you will need to [install ](https://github.com/XRFoundation/XREngine/blob/dev/tutorial/01-installation.md)the engine, then for the URL variable just apply the URL for the engine using the location you want the bot to enter, for example [https://localhost:3000/location/test](https://localhost:3000/location/test) - this url will make the bot to connect to the XR-engine that is setup locally on Port 3000 in the location test
    * XRENGINE_URL
* Whatsapp:
    * WHATSAPP_BOT_NAME
* Harmony:
    * Harmony runs the same way with the xr-engine, though the url is [https://localhost:3000/harmony](https://localhost:3000/harmony) for a local test
    * HARMONY_URL
* Messenger (Facebook)
    * Messenger is using the default Facebook API, it supports Direct Messages through a Facebook Page. In order to set up the bot for facebook, you will need to go to the Facebook Dev, then create a new Application, set it up with your Page and add Messenger (it can work with Instagram too), then you will need to set a verify token to the .env (something like a password), run the bot, let it load, then go to the Messenger Dev Portal and in the Messenger APP go to callbacks, set your ip:port/webhook and for the next text field type the verify token you used to the .env, click verify and wait to load, then get the app token from the dev portal and update the .env
    * MESSENGER_TOKEN
    * MESSENGER_VERIFY_TOKEN
* Zoom:
    * To use zoom, you will need to create a room and get the invitation link (for web), like this [https://us04web.zoom.us/wc/join](https://us04web.zoom.us/wc/join)/id if the room needs a password, you will need to add it in the zoom_password variable or in the url
    * ZOOM_INVITATION_LINK
    * ZOOM_PASSWORD
* Reddit:
    * For reddit, you will need to create an app in reddit [dev portal](https://www.reddit.com/prefs/apps) and create an app, then for redirect_url you will need to use this URL [https://not-an-aardvark.github.io/reddit-oauth-helper/](https://not-an-aardvark.github.io/reddit-oauth-helper/) finally go inside the same url and follow the steps to generate an OATH token and set up the variables in the .env with the needed values.
    * REDDIT_APP_ID
    * REDDIT_APP_SECRED_ID
    * REDDIT_USERNAME
    * REDDIT_PASSWORD
    * REDDIT_OATH_TOKEN
* Instagram:
    * Instagram is the simplest to setup, there is no need of a dev account or dev application, just the account’s credentials for the bot to login in the account.
    * IG_USERNAME
    * IG_PASSWORD
* Postgres (Database):
    * Postgres is the database that the bot uses locally to save chat history, user data etc, it can be setup locally or in a different server, here are some more detailed [instructions](https://harshityadav95.medium.com/postgresql-in-windows-subsystem-for-linux-wsl-6dc751ac1ff3) on how to set it up
    * PGUSER
    * PGHOST
    * PGPASSWORD
    * PGDATABASE
    * PGPORT
* Manager:
    * The Manager is a web interface to edit variables from the database for the bot
    * BOT_MANAGER_IP
    * BOT_MANAGER_PORT
    * MANAGER_USERNAME -> in order to use it there is a login screen on the start, to avoid others editing the bot
    * MANAGER_PASSWORD
    * BOT_MANAGER_SECRET_KEY -> a custom secret key
    * AVAILABLE_AIS -> Available agents in the bot, for easier editing, they are separated using semicolons (;), e.x.: gpt3;gpt2;test agent
* Misc:
    * EDIT_MESSAGES_MAX_COUNT -> max message to be sent to agent if edited (older messages will be ignored)
    * OPENAI_API_KEY -> open ai key that is needed for the GPT3 agent
    * BOT_NAME_REGEX -> the regex for the bot name, which is used for the bot to understand that someone is starting a conversation with it
    * CHAT_HISTORY_MESSAGES_COUNT -> the count of chat history fetched in-case the agent needs them
    * BOT_NAME -> the name of the Bot that is used globally in the chat history, so the bot  understands easily if it sent by it
    * TCP_PORT -> the TCP port that is used to connect the typescript client with the python server
    * WEBSERVER_PORT -> the webserver port that is used for the webhooks (twilio, messenger)
    * METAINTELLIGENCE_CONNECTION_STRING -> the connection string for the meta intelligence agent if used
    * THALES_CONNECTION_STRING -> the connection string for the [Thales agent](https://github.com/DavinciDreams/Thales) if used

# pi-node-linux-port

## This is a project to port the existing Pi-Network Node Application to Linux (Ubuntu 24.04)
- As of now, I was able to run the container on Docker within Ubuntu.
- At the moment I try to debug the UI-App on Windows to learn about the communication between the app and docker and how the container gets authenticated, how the synchronization works and so on.
---
### What I have done so far:
#### Running the container on Ubuntu:
- Commands (DISCLAIMER: MIGHT BE INCOMPLETE OR HAVE UNNECESSARY COMMANDS):
```
2 2025-01-12 17:41:36 sudo apt update && sudo apt upgrade -y
4 2025-01-12 17:41:36 sudo apt install nano -y
7 2025-01-12 17:42:00 cd /tmp/
8 2025-01-12 17:42:03 wget https://downloads.minepi.com/Pi%20Network%20Setup%200.5.0.exe
14 2025-01-12 17:43:51 sudo apt install p7zip-full
15 2025-01-12 17:44:09 7z x Pi\ Network\ Setup\ 0.5.0.exe
17 2025-01-12 17:44:31 mkdir Pi-Node
22 2025-01-12 17:47:17 7z x "Pi Network Setup 0.5.0.exe" -o/tmp/Pi-Node
25 2025-01-12 17:47:24 cd Pi-Node/
26 2025-01-12 17:47:32 cp "/tmp/Pi-Node/$PLUGINSDIR/app-64.7z" /tmp/Pi-Node/
27 2025-01-12 17:48:38 7z x app-64.7z
36 2025-01-12 17:59:41 sudo apt install npm
38 2025-01-12 18:01:22 npx asar extract resources/app.asar extracted_asar
40 2025-01-12 18:02:15 cd extracted_asar/
45 2025-01-12 18:05:16 sudo apt install libnss3
47 2025-01-12 18:05:39 sudo apt install libatk1.0-0
50 2025-01-12 18:06:09 sudo apt install libatk-bridge2.0-0
56 2025-01-12 18:09:00 sudo apt install libcups2
61 2025-01-12 18:10:56 npm install electron
65 2025-01-12 18:11:53 sudo apt install -y libgtk-3-0 libpango-1.0-0 libcairo2 libxdamage1
68 2025-01-12 18:12:13 sudo add-apt-repository universe
69 2025-01-12 18:12:18 sudo add-apt-repository multiverse
70 2025-01-12 18:12:22 sudo apt update
71 2025-01-12 18:12:25 sudo apt upgrade
80 2025-01-12 18:14:26 sudo apt install libasound2t64
86 2025-01-12 18:15:15 cd /tmp/Pi-Node/extracted_asar/node_modules/electron/dist
87 2025-01-12 18:15:18 sudo chown root:root chrome-sandbox
88 2025-01-12 18:15:20 sudo chmod 4755 chrome-sandbox
89 2025-01-12 18:15:29 cd /tmp/Pi-Node/extracted_asar/
92 2025-01-12 18:15:52 sudo setcap cap_sys_admin+ep ./node_modules/electron/dist/electron
98 2025-01-12 18:17:22 sudo apt install xvfb
99 2025-01-12 18:17:29 Xvfb :99 -screen 0 1920x1080x24 &
100 2025-01-12 18:17:36 export DISPLAY=:99
110 2025-01-12 18:19:52 sudo apt install docker-compose
112 2025-01-12 18:20:12 cd /tmp/Pi-Node/extracted_asar/
113 2025-01-12 18:20:16 npx electron . --no-sandbox
130 2025-01-12 18:26:29 sudo docker run -d --name testnet2 --env-file "/home/master/.config/Pi Network/testnet2.env" -v "/home/master/.config/Pi Network/docker_volumes/testnet_2/stellar:/opt/stellar" -v "/home/master/.config/Pi Network/docker_volumes/testnet_2/supervisor_logs:/var/log/supervisor" -v "/home/master/.config/Pi Network/docker_volumes/testnet_2/history:/history" -p 31401:8000 -p 31402:31402 -p 31403:1570 pinetwork/pi-node-docker:protocol18.4 --testnet2
```
- Log of the testnet2 container:
```
Starting Stellar Quickstart
mode: persistent
network: testnet2 (Pi Testnet)
postgres user: stellar
using POSTGRES_PASSWORD
finalize-pgpass: ok
init-postgres: ok
postgres: up
create-horizon-db: ok
create-core-db: ok
stellar-postgres-user: ok
chown-core: ok
finalize-core-config-pgpass: ok
finalize-core-config-run-standalone: ok
finalize-core-config-manual-close: ok
set-private-key: ok
init-core-db: ok
chown-horizon: ok
init-horizon-db: ok
postgres: down
starting supervisor
2025-01-12 18:27:01,562 INFO Set uid to user 0 succeeded
2025-01-12 18:27:01,567 INFO RPC interface 'supervisor' initialized
2025-01-12 18:27:01,567 CRIT Server 'unix_http_server' running without any HTTP authentication checking
2025-01-12 18:27:01,567 INFO supervisord started with pid 1
2025-01-12 18:27:02,570 INFO spawned: 'postgresql' with pid 133
2025-01-12 18:27:02,573 INFO spawned: 'stellar-core' with pid 134
2025-01-12 18:27:02,574 INFO reaped unknown pid 131
2025-01-12 18:27:03,592 INFO success: postgresql entered RUNNING state, process has stayed up for  than 1 seconds (startsecs)
2025-01-12 18:27:03,593 INFO success: stellar-core entered RUNNING state, process has stayed up for  than 1 seconds (startsecs)
```
- Container status:
```
 CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS
NAMES
4672671e5c3a pinetwork/pi-node-docker:protocol18.4 "/start --testnet2" 5 minutes ago Up 5 minutes 5432/tcp, > 0.0.0.0:31402->31402/tcp, :::31402->31402/tcp, 0.0.0.0:31403->1570/tcp, :::31403->1570/tcp, 0.0.0.0:31401->8000/tcp, :::31401->8000/tcp testnet2
```
#### Debugging of the Windows app:
- Copied the extraced_apar folder to my windows machine
- Installed NVM on Windows
- Opened CMD in the apar folder
- Run: "npm install" to install the necessary dependencies
- Installed js-beautify to view the main.js: npm install -g js-beautify
- Run: js-beautify dist\main\main.js -o readable_main.js
- Replaced the main.js in /dist/main with the readable_main.js and renamed to main.js
- Edited the path of the preload.js to match ./preload.js
- Run: npx electron . --enable-logging --remote-debugging-port=9222 --remote-allow-origins=http://localhost:9222
- UI-App starts and can be used as normal with debugging in the CMD Window

# Home Server Infrastructure

## Why did I develop Home Server?
From my internship, I used to set up a CI/CD pipeline for a team project on GitHub Actions for deploying the frontend on **Azure Blob Storage** and the backend on **Azure App Service**. 
I saw how easy it was to deploy and auto-scale docker containerized apps written in any language and run 24/7 without downtime. This experience made me want to deploy my personal project on the cloud.

However, I saw this video about how much the cloud costs and how it can make you bankrupt.

[![FireshipCloudOverflow](https://i.ytimg.com/vi/N6lYcXjd4pg/hq720.jpg?sqp=-oaymwEpCOgCEMoBSFryq4qpAxsIARUAAIhCGAHYAQHiAQwIGhACGAYgATgBQAE=&rs=AOn4CLDB4wyMIWMDZVtRBIs7SEtco4Y4NQ)](https://www.youtube.com/watch?v=N6lYcXjd4pg&t=48sv)

That’s why I needed to check how much each cloud provider costs. 
For my projects, I usually dockerize the apps into containers and deploy them, so I need to find services that allow me to deploy and run containerized apps on them. I also want them to run 24/7 without downtime, so it will be 730 hours/month on average. The basic spec that I want is 1 CPU and 1-2 GB of RAM.

The first one is **Azure App Services** and the price is around 460 baht/month.
!["AzureAppService"](https://i.imgur.com/MZ2ldaK.png)

The second one is **AWS Fargate** and the price is around 1250 baht/month.
!["Fargate"](https://i.imgur.com/wM1drkV.png)

The last one is **Google Cloud Run** and the price is around 2000 baht/month.
!["CloudRun"](https://i.imgur.com/8CkxGM5.png)

You can see that the cheapest service, which is Azure App Service, costs me around 460 baht/month or **5520 baht/year**. Moreover, this price does not include Database service.

From the results, the cost is too high for me to just deploy a personal project. I need to find other solutions that allow me to run many personal projects 24/7 at the lowest cost possible.

Then I found this video about **deploying a Node.js project on Raspberry Pi4** and port forwarding to the public internet, so technically I can deploy my personal project like this as well.

[![FireshipCloudOverflow](https://i.ytimg.com/vi/QdHvS0D1zAI/hq720.jpg?sqp=-oaymwEpCOgCEMoBSFryq4qpAxsIARUAAIhCGAHYAQHiAQwIGhACGAYgATgBQAE=&rs=AOn4CLBjlkVafPtDQOt5qLmePuNXUWqmvw)](https://www.youtube.com/watch?v=QdHvS0D1zAI)

First, I tried to run Node.js on localhost:3000 and set up port forwarding on my router to expose my Node.js to the public network. I tried to find my public IP and then access it on a browser with port 3000, but I was disappointed because I could not access it. 
I thought the issue was with the Dynamic IP, so I used the **no-ip dynamic DNS service** to map my IP with a domain. However, it still did not work.

Next, I found out that **my ISP does NAT on my public IP**, so this public IP is shared among my neighborhood. A solution that my ISP gave me is the **True DDNS service**, which I can use with the provided domain name and port to allow public network access to my local application on a specific port. However, my ISP only gave me 10 ports, so it’s not good for the long term if I have more applications running.

Luckily, while I was scrolling through my Facebook feed, I found **Supadej Suthiphongkanasai** writing about **Cloudflare Tunnel**. Traditionally, port forwarding can cause security threats to my home network. Moreover, if I want to use the HTTPS protocol, I must set up and buy an SSL certificate by myself. Whereas Cloudflare Tunnel allows exposing local applications to the public internet with the HTTPS protocol and without firewall or NAT issues.

Therefore, Cloudflare Tunnel is the solution for the Home Server Infrastructure. You can follow this YouTube tutorial for setting up Cloudflare Tunnel.
[![FireshipCloudOverflow](https://i.ytimg.com/vi/ey4u7OUAF3c/hq720.jpg?sqp=-oaymwEpCOgCEMoBSFryq4qpAxsIARUAAIhCGAHYAQHiAQwIGhACGAYgATgBQAE=&rs=AOn4CLBQ2yL7uH9TgEiga0suP0rhQD9XEg)](https://www.youtube.com/watch?v=ey4u7OUAF3c&t=53s)


## WebChat Infrastructure Diagram
!["Infrastructure Diagram"](https://i.imgur.com/0TjD3yp.png)
This diagram shows how my **WebChat** application connects with the public internet.

When users go to https://webchat.xaiphersk.com, their browser will go to an IP of a remote server (CloudFlare), then the remote server will forward the request to the Cloudflare Tunnel docker container that is running on my machine. The Cloudflare Tunnel container will forward it to a local application with the IP and Port that I set on this page. 
In this case, I set up a Docker network with the name: webchat and IP 172.2.0.0/24 and deployed the backend on 172.2.0.251:8080, so https://webchat-backend.xaiphersk.com is bound with 172.2.0.251:8080 on the local machine.

!["SetupIP"](https://i.imgur.com/CuqvOMr.png)

**In the Frontend,** when users go to https://webchat.xaiphersk.com, Nginx will return web content, which has already been built, back to the client. (You can see my nginx.conf on my webchat frontend repo)

**In the Backend,** when the client fetches to https://webchat-backend.xaiphersk.com, Nginx acts as a reverse proxy and it will select an instance of Node.js among 3 of them with Round-robin.
(I needed to do extra Nginx config so that it would preserve connections that are using WebSocket. You can check out the config on the webchat backend repo)

**In the PostgreSQL Database,** I did not expose it to the public network and only used it inside a private network, which is the best practice to avoid any security threats.

Moreover, I set up a **self-hosted runner** on my local machine so that I can do CI/CD pipeline using **GitHub Actions**. The runners run on WSL2 and then I configured Docker Desktop to connect with WSL2 Ubuntu, so the runners can connect and use docker commands. 
(Every secret key such as Cloudflare token is stored in GitHub Secret Environment) 
https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions

Lastly, this is my mini PC spec and price. I bought a **BMAX B2 Pro Mini PC**. 
CPU: Intel Celeron J4105 (4 cores) 
RAM: 8GB 
Storage: 256GB 
OS: Windows 11 
Price: 4500 baht

I thought the price was appropriate for me and it was a one-time payment and I can deploy many docker containers without worrying about cost.

**Thank you for taking the time to read my article.**
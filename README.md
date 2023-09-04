# Home Server Infrastructure

## Why did I develop Home Server?
From my internship, I used to set up CI/CD pipeline for a team project on GitHub Actions for deploying frontend on **Azure Blob Storage** and backend on **Azure App Service**. 
I saw it easy to deploy and auto scale docker containerized apps written in any language and run 24/7 without a downtime. This experience make me want to deploy my personal project on cloud.

However, I saw this video about how much does cloud cost and how it can make you bandkrupt.

[![FireshipCloudOverflow](https://i.ytimg.com/vi/N6lYcXjd4pg/hq720.jpg?sqp=-oaymwEpCOgCEMoBSFryq4qpAxsIARUAAIhCGAHYAQHiAQwIGhACGAYgATgBQAE=&rs=AOn4CLDB4wyMIWMDZVtRBIs7SEtco4Y4NQ)](https://www.youtube.com/watch?v=N6lYcXjd4pg&t=48sv)

That why, I need to check how much each cloud provider cost. 
For my projects, I usually use dockerize the apps into containers and deploy them, so I need to find services that allow me to deploy and run containerized apps on them. I also want them to run 24/7 without a downtime, so it will be 730 hours/month in avg. The basic spec that I want is 1 CPU and 1-2 GB of Ram.

The first one is **Azure App Services** and the price is around 460 baht/month
!["AzureAppService"](https://i.imgur.com/MZ2ldaK.png)

The second one is **AWS Fargate** and the price is around 1250 baht/month
!["Fargate"](https://i.imgur.com/wM1drkV.png)

The last one is **Google Cloud Run** and the price is around 2000 baht/month
!["CloudRun"](https://i.imgur.com/8CkxGM5.png)

You can see that the cheapest service, which is **Azure App Service**, cost me around 460 baht/month or **5520 bath/year**. Moreover, this price did not include Database service.

From the results, the cost is too pain for me to just deploy a personal project. I need to find other solutions that allow me to run many personal projects 24/7 at the cheapest cost as possible.

Then I found this video about **deploying Node.js project on Raspberry Pi4** and port forwarding to public internet, so technically I can deploy my personal project like this as well.

[![FireshipCloudOverflow](https://i.ytimg.com/vi/QdHvS0D1zAI/hq720.jpg?sqp=-oaymwEpCOgCEMoBSFryq4qpAxsIARUAAIhCGAHYAQHiAQwIGhACGAYgATgBQAE=&rs=AOn4CLBjlkVafPtDQOt5qLmePuNXUWqmvw)](https://www.youtube.com/watch?v=QdHvS0D1zAI)

First, I tried to run Node.js on Localhost:3000 and set up port forwarding on my router to expose my Node.js to public network. I tried to find my public IP and then access it on browser with port 3000, but I was disappointed because I could not access it.
I thought the issue was with the Dynamic IP, so I used the **no-ip dynamic DNS service** to map my IP with a domain. However, it still did not work.

Next, I found out that **my ISP does NAT on my public IP**, so this public IP is shared among my neighborhood. A solution that my ISP gave me is the **True DDNS service**, which I can use with the provided domain name and port to allow public network access to my local application on a specific port. However, my ISP only gave me 10 ports, so it’s not good for the long term if I have more applications running.

Luckily, while I was scrolling through my Facebook feed, I found **Supadej Suthiphongkanasai** writing about **Cloudflare Tunnel**. Traditionally, port forwarding can cause Security Threats to my home network. Moreover, If I want to use HTTPS protocal, I must set up and buy ssl certificate by myself. 
Whereas, Cloudflare Tunnel allow exposing local applications to public internet with HTTPS protocal and without firewall or NAT issue.

Therefore, Cloudflare Tunnel is the solution of the Home Server Infrastructure.
You can follow this youtube tutorial for setting up Cloudflare Tunnel.
[![FireshipCloudOverflow](https://i.ytimg.com/vi/ey4u7OUAF3c/hq720.jpg?sqp=-oaymwEpCOgCEMoBSFryq4qpAxsIARUAAIhCGAHYAQHiAQwIGhACGAYgATgBQAE=&rs=AOn4CLBQ2yL7uH9TgEiga0suP0rhQD9XEg)](https://www.youtube.com/watch?v=ey4u7OUAF3c&t=53s)


## WebChat Infrastructure Diagram
!["Infrastructure Diagram"](https://i.imgur.com/0TjD3yp.png)

This diagram show how my **WebChat** application connect with the public internet.

When users go to https://webchat.xaiphersk.com, their browser will go to an IP of remoted server (CloudFlare), then remoted server will forward request to the Cloudflare Tunnel docker container that is running on my machine. The Cloudflare Tunnel container will forward to an local application with IP and Port that I set on this page. 
In this case, I set up Docker network with name: webchat and IP 172.2.0.0/24 and deploy backend on 172.2.0.251:8080, so https://webchat-backend.xaiphersk.com is binded with 172.2.0.251:8080 on the local machine.

!["SetupIP"](https://i.imgur.com/CuqvOMr.png)

**In Frontend,** when users go to https://webchat.xaiphersk.com, Nginx will return web content, which has already built, back to the client. 
(You can see my nginx.conf on my webchat frontend repo)

**In Backend,** when client fetch to https://webchat-backend.xaiphersk.com, Nginx that act as reverse proxy and it will select an instance of Node.js among 3 of them with Round-robin. 
(I need to do extra Nginx config so that it will preserve connection that are using WebSocket. You can checkout the config on webchat backend repo)

**In PostgreSQL Database,** I did not expose it to public network and only use inside private network, which is the best practice to avoid any security threats.

Moreover, I setup **self-hosted runner** on my local machine so that I can do CI/CD pipeline using **GitHub Actions**. The runners are run on WSL2 and then I configed Docker Desktop to connection with WSL2 Ubuntu, so the runners can connect and use docker command. 
(Every secret key such as cloudflare token is stored in GitHub Secret Environment)
https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions

Lastly, this is my mini pc spec and price. I bought **BMAX B2 Pro Mini PC**. 
CPU: Intel Celeron J4105 (4 cores)
RAM: 8GB
Storage: 256GB
OS: Windows 11
Price: 4500 baht

I thought the price is appropriate for me and it on-time paid and I can deploy many docker container without worrying about cost.

**Thank you for taking the time to read my articles.**
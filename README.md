# Home Server Infrastructure

## Why did I develop Home Server?
From my internship, I used to set up CI/CD pipeline for a team project on GitHub Actions for deploying frontend on Azure Blob Storage and backend on Azure App Service. 
I saw it easy to deploy and auto scale docker containerized apps written in any language and run 24/7 without a downtime. This experience make me want to deploy my personal project on cloud.

However, I saw this video about how much does cloud cost and how it can make you bandkrupt.

[![FireshipCloudOverflow](https://i.ytimg.com/vi/N6lYcXjd4pg/hq720.jpg?sqp=-oaymwEpCOgCEMoBSFryq4qpAxsIARUAAIhCGAHYAQHiAQwIGhACGAYgATgBQAE=&rs=AOn4CLDB4wyMIWMDZVtRBIs7SEtco4Y4NQ)](https://www.youtube.com/watch?v=N6lYcXjd4pg&t=48sv)

That why, I need to check how much each cloud provider cost. 
For my projects, I usually use dockerize the apps into containers and deploy them, so I need to find services that allow me to deploy and run containerized apps on them. I also want them to run 24/7 without a downtime, so it will be 730 hours/month in avg. The basic spec that I want is 1 CPU and 1-2 GB of Ram.

The first one is Azure App Services and the price is around 460 baht/month
!["AzureAppService"](https://i.imgur.com/MZ2ldaK.png)

The second one is AWS Fargate and the price is around 1250 baht/month
!["Fargate"](https://i.imgur.com/wM1drkV.png)

The last one is Google Cloud Run and the price is around 2000 baht/month
!["CloudRun"](https://i.imgur.com/8CkxGM5.png)

You can see that the cheapest service, which is Azure App Service, cost me around 460 baht/month or 5520 bath/year. Moreover, this price did not include Database service.

From the results, the cost is too pain for me to just deploy a personal project. I need to find other solutions that allow me to run many personal projects 24/7 at the cheapest cost as possible.

Then I found this video about deploy Node.js project on Raspberry Pi4 and port forwarding to public internet, so technically I can deploy my personal project like this as well.

[![FireshipCloudOverflow](https://i.ytimg.com/vi/QdHvS0D1zAI/hq720.jpg?sqp=-oaymwEpCOgCEMoBSFryq4qpAxsIARUAAIhCGAHYAQHiAQwIGhACGAYgATgBQAE=&rs=AOn4CLBjlkVafPtDQOt5qLmePuNXUWqmvw)](https://www.youtube.com/watch?v=QdHvS0D1zAI)

First, I tried to run Node.js on Localhost:3000 and set up port forwarding on my router to expose my Node.js to public network.

!["Infrastructure"](https://i.imgur.com/0TjD3yp.png)
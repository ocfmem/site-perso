+++
title = "The positive impact of serverless architectures"
description = "Each application architecture requires a solution adapted to the execution context and business issues. serverless, any more than more traditional architectures, is not the single answer to all..."
date = 2025-02-19T09:00:00+02:00
draft = false
author = "Meddy Menzikoff"
tags = ["serverless"]
[params]
  source = "LinkedIn"
  source_url = "https://fr.linkedin.com/pulse/limpact-positif-des-architectures-serverless-meddy-menzikoff-ihi2e"
+++

> Article originally published on LinkedIn on **2025-02-19**.  
> Source: [https://fr.linkedin.com/pulse/limpact-positif-des-architectures-serverless-meddy-menzikoff-ihi2e](https://fr.linkedin.com/pulse/limpact-positif-des-architectures-serverless-meddy-menzikoff-ihi2e)

Each application architecture requires a solution adapted to the execution context and business issues. serverless, any more than more traditional architectures, is not the single answer to all infrastructure problems.

In this article, however, I have decided to only focus on a few advantages offered by these technologies.

### Scalability, high SLA and energy savings for IT decision-makers

Companies today are looking for solutions capable of responding quickly to traffic peaks while reducing costs and environmental impact. IT decision-makers often find themselves faced with a complex choice: continue to host their applications internally (on-premises) or move towards cloud solutions. Among cloud options, serverless architecture is increasingly emerging as an optimal response.

In this article, we will discuss the positive impact of the generalization of serverless architectures such as those offered by AWS (Amazon Web Services). We will see how this approach revolutionizes scalability, ensures a very high SLA and contributes to better energy efficiency in IT. To fully understand these issues, we will take the concrete example of an online training SaaS.

Let's imagine e-learning software that allows users to follow video courses, download materials and take quizzes. Far from being a linear use, this platform experiences predictable peaks of activity (early morning, early afternoon) and significant troughs (particularly at night). We'll see how serverless on AWS handles these fluctuations efficiently and cost-effectively, while ensuring high availability, smooth streaming, and unlimited large storage.

### 1. Scalability: performance on demand

### 1.1. The essence of serverless

serverless offers “continuous” operation where the underlying infrastructure is entirely managed by the cloud provider. The goal is to allow IT teams and developers to focus solely on code and business logic. The provider automatically allocates the resources needed for execution and releases them as soon as the load decreases. Thus, no physical or virtual machine needs to be provisioned or maintained on the client side.

Scalability – that is to say the ability of the system to quickly adapt to variations in load – then becomes a major asset. When more users connect to the platform, the provider opens more resources and automatically manages load balancing. On the contrary, when there is little traffic (at night, for example), resources go down to the strict minimum. Result: you only pay for what you consume, and you avoid underutilized servers.### 1.2. The example of an online training SaaS

Let's come back to our training SaaS which offers both streaming videos and PDF materials to download. Let's imagine a platform on which thousands of students and professionals can take courses at the same time, or on the contrary no connection for hours. In a classic scheme (on-premises or self-hosting), it would be necessary to plan a significant hardware dimensioning to absorb peaks of activity, even if it means heavily underutilizing these servers during off-peak periods. It is costly in terms of investments (CAPEX), maintenance and energy.

With an AWS serverless architecture, for example, it becomes trivial to:

- Handle traffic peaks (for example, every day at 9 a.m. or 2 p.m.) by automatically switching from a single execution to hundreds or thousands of parallel executions.

    - Reduce consumption and costs at night or during vacations, when no one is online.

In addition, because the platform needs to store large video and audio files, it can take advantage of services like Amazon S3 (Simple Storage Service), which offers almost infinite scalability and 99.999999999% durability. AWS Lambda functions or Fargate containers can be triggered on demand to manipulate, transcode, or serve these media.

For their part, structured data (for example, user profiles, progress, quiz scores) can be housed in Amazon DynamoDB, a fully managed NoSQL database capable of scaling without sacrificing performance. For relational data (for example, payment history or managing course catalogs that require complex joins), AWS Aurora serverless comes into play. This serverless variant of Amazon Aurora automatically adjusts capacity based on activity, combining the power of MySQL or PostgreSQL with the elasticity of the cloud.

### 1.3. Why serverless is ideal for traffic variations

Serverless solutions mainly bill by the number of requests and execution time, rather than by the capacity reserved 24 hours a day. For a training SaaS, you benefit from automatic horizontal scalability at a lower cost. You can imagine that at the start of the morning, 5,000 learners connect simultaneously to follow their courses: in an on-premises model, this could saturate the servers if you had not planned enough power. In serverless, AWS Lambda can instantiate as many functions as needed (one for each video stream, for example), DynamoDB can support a sudden spike in reads/writes, and Aurora serverless can add nodes or increase allocated memory to handle more intense queries.

Once the traffic peak passes, everything goes back down. You do not keep any “unnecessary machines” during off-peak periods. Billing follows this logic: financial and IT teams do not find themselves paying for servers that are inactive at night. This advantage makes serverless the perfect solution for web traffic variability. This flexibility is all the more interesting for a SaaS whose activity depends on time slots or seasons (example: massive training in the morning, almost none at night).

### 1.4. Comparison with On-Premises and Self-Hosting

On-premises, to absorb these peaks, you should purchase, install and maintain enough servers for the maximum expected peak. This is often wasteful (oversizing), not to mention the software, hardware and security maintenance to be managed internally. In self-hosting on VMs, you can set up manual or semi-automatic auto-scaling, but this involves monitoring, monitoring, and management of scaling scripts that is your responsibility. Resource allocation is not instantaneous and may require VM startup time or even commands to provision more capacity.

With AWS Lambda coupled with Amazon API Gateway or AWS App Runner, granular and almost instantaneous auto-scaling takes place. The software architecture can consist of separate microservices: one microservice managing course registration, another managing video streaming, another managing quizzes, etc. Everyone evolves at their own pace based on demand.

Thus, serverless is a champion of scalability: it is a “one-size-fits-all” strategy to absorb peaks and not waste outside peaks.

### 2. Very high SLA: built-in reliability and resilience

### 2.1. High availability by default

Another key argument for IT decision-makers is the guaranteed SLA. AWS serverless architecture often offers an SLA of 99.95% or more, which corresponds to less than 22 minutes of downtime per month. Concretely, AWS hosts its services in geographically distinct Availability Zones within the same region. If one of them encounters a problem, requests are automatically redirected to another zone.

Additionally, AWS handles maintenance, hardware and security updates. In Serverless solutions, this maintenance is completely invisible: you do not have to plan or endure service outages. This secures the availability of your SaaS.

### 2.2. Resilience and fault tolerance

For a training SaaS that wants to offer constant access to videos and courses, the loss of service is critical: each minute of interruption can generate frustration, complaints or loss of revenue. An On-Premises architecture would require expensive redundancy devices (clustered servers, geographically remote sites). In self-hosting, you would have to carefully configure several zones, load-balancing, and monitor the health of the VMs. In serverless, the cloud provider takes care of this automatically.

For example, AWS Lambda will replicate your function code across multiple Availability Zones, and you can configure Amazon S3 to store your videos in multi-AZ mode. Amazon Aurora serverless also offers storage distributed across multiple zones, ensuring redundancy for your relational databases. So, even in the event of a hardware failure in a data center, your SaaS remains operational thanks to these fault tolerance mechanisms.

### 2.3. Impact on user trust

This high availability guarantees a smooth experience for learners. Whether they connect at 9 a.m. or 9 p.m., whether they play an HD video or download a large PDF, SaaS delivers consistent performance. This reliability strengthens the reputation of the platform, consolidates customer satisfaction and reduces costs linked to support and incidents.

Ultimately, serverless proves to be valuable SLA insurance for IT decision-makers: less risk, less stress, and more time to focus on innovation and continuous improvement of the product.

### 3. Energy saving: an asset for sustainability

### 3.1. Eco-responsible architecture

Beyond performance, environmental impact has become a major issue for companies, particularly in the context of CSR (Corporate Social Responsibility) strategies. Data centers represent a significant portion of global electricity consumption, but cloud platforms like AWS have drastically improved their energy efficiency. According to some estimates, managed cloud infrastructures can be up to 88% more energy efficient than traditional data centers.

In serverless, dynamic resource allocation plays a key role. Rather than powering servers 24 hours a day, AWS only uses CPUs, memory and computing power during the execution of functions. At night, when there is little demand for SaaS training, consumption drops. The physical servers continue to run, of course, but they are shared and optimized on a large scale by AWS to meet all customers. This pooling increases the overall hardware utilization rate, which translates into better efficiency in terms of watts consumed per transaction.

### 3.2. Limit material waste

In an on-premises or self-hosting mode, you must maintain your own servers, potentially in overcapacity. Even when they're not processing any requests, these servers consume power to stay powered on, cooled, and secure. In addition, their renewal generates electronic waste. The use of shared serverless computing minimizes this waste, because AWS operates latest generation data centers, optimized for performance and energy consumption.

### 3.3. Communicate on a green approach

More and more customers and investors are giving importance to the environmental approach. For a training SaaS, being able to say that your platform has a reduced impact on the carbon footprint can be a strong marketing argument. You participate in digital sobriety, while offering an efficient service. Opting for serverless therefore positions you as a committed and responsible player, in line with current sustainable development issues.

### 4. Comparison of serverless vs On-Premises vs Self-Hosting

To give an overview to IT decision-makers, here is a summary comparative table, taking into account the three main axes: scalability, SLA and energy efficiency. We also add some remarks on management complexity and flexibility.

serverless architectures on AWS are now establishing themselves as a solution of choice for companies wishing to combine performance, reliability and energy efficiency. By delegating infrastructure management to a major cloud player, you benefit from tailor-made scalability, a high SLA and shared resources that minimize the overall carbon footprint.

For a training SaaS experiencing significant fluctuations in traffic, serverless is a considerable asset: it automatically responds to peaks (mass video streaming, storage of large files) and goes to sleep during off-peak periods, while guaranteeing a smooth user experience.

Unlike On-Premises, where you must provide hardware continuously, and Self-Hosting, where you still manage part of the infrastructure, serverless frees you from hardware constraints and lets you concentrate on your core business: the design of an attractive, enriching and sustainable training platform.

Whether you are already thinking about the evolution of your IT or are considering a total overhaul, don't forget that the progressive adoption of serverless can be done in stages (migration of certain microservices, proof of concept tests, etc.). Gains in agility, reliability and environmental responsibility make serverless an increasingly essential path to building the IT of tomorrow.

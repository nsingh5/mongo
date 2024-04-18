**Custom Distributed RateLimiter**

Before understanding and building distributed-rate limiters, we should first know what rate-limiting is.
Rate-limiting is a strategy for limiting network traffic. It puts a cap on how often someone is currently able to repeat an action within a certain timeframe — for instance, trying to log in to an account.
Here are some examples:


- A user can send a message no more than twice per second.
- One can create a maximum of 10 accounts per day from the same IP address.
- One can claim rewards no more than 5 times per week from the same device.
  ![image](https://github.com/nsingh5/mongo/assets/10362252/1052e90b-d1a6-4d4c-ae1a-0ab5b352ba3f)

So now, why is rate-limiting important?


- Prevent DoS attacks
- Manage resource utilisation: ensure that a server is not overloaded by requests, which can negatively impact performance and availability.
- Ensuring fairness: prevents any user from monopolising resources.
- Preventing operational cost: puts a cap on the resources to help control operational costs, which are working on a pay-per-use model.


Rate limiters can be implemented on either the client-side or the server-side.

- client-side:

A client is an unstable place to impose rate restrictions. Also, we may not have complete control over client implementation, and it can be manipulated by clients.


- server-side:
Unlike client-side, on the service side, it is secure over tempering.

  ![image](https://github.com/nsingh5/mongo/assets/10362252/5196fe6f-b177-4e4e-b0d0-224bf83e1137)

Common algorithm for rate-limiter

1.	Leaky bucket
2.	Token bucket
3.	Fixed window counter
4.	Sliding Log
5.	Sliding Window
We will be focusing on Token bucket algorithm in this blog.
For deep understanding this link can be checked out.


**Basic rate limiter using Token bucket**
It works by restricting the number of requests a user can make in a given amount of time by assigning each user a “token bucket” of requests they are allowed to send.
When a user sends a request, it is deducted from the token bucket and gets restored when the period ends.
  ![image](https://github.com/nsingh5/mongo/assets/10362252/3281ee7a-e1da-46d4-a6ed-6e32f8a09752)

How to achieve rate-limiting in a service?


There are many libraries that provide rate-limiting, such as bucket4j based on the token-bucket algorithm and resilience4j.
With bucket4j :
  ![image](https://github.com/nsingh5/mongo/assets/10362252/83a6bdfa-508e-404f-ac1b-e2354a5ec07e)

Adding/using these libraries works fine with a single service instance, but for large systems where we have multiple instances, will this work?
Here, we will have rate constraint on per service, i.e.
rate of system = rate on instance * number of instance
  ![image](https://github.com/nsingh5/mongo/assets/10362252/88f7ae62-9d3d-4511-b090-f17453c05409)

Here we will be facing multiple issues:
1) Scalability- For increase or decrease instance it requires to update the rate-configuration which is overhead, thus auto-scalability can’t be achieved
2) Load balance- It is possible that major api requests are directed to a single instance, so if one of instance exceeds limit and other instances have bandwidth. Here our system/rate-limiter will fail to serve its purpose, as randomly some request pass and others fail with exceeding limits.
So, this kind of system can’t handle multiple instances.
To overcome these limitations on distributed system, it will need a common place to share the data it can db or cache.


**Creating Custom Distributed RateLimiter**

To serve this, Hungary-Natco came up with a custom implementation and built a jar using the token bucket algorithm.
Here we have used java8, redis for this. Using redis as a common space for every instance, these can read and write directly to redis instead of local cache.
  ![image](https://github.com/nsingh5/mongo/assets/10362252/945223e5-0ca8-4261-9ea6-0b27efa21593)

approach1: ratelimiter jar used in servers

  ![image](https://github.com/nsingh5/mongo/assets/10362252/9aebe0e6-1e29-4ce0-91c0-aa27cc4f586a)

approach2: ratelimiter jar used in api gateway
Our idea is to have a filter for each url+method that will be responsible for its throttling based on lua-sricpt to redis.
These filters will be able to limit the api/url based on defined bandwidth constraints in configuration(it can be multiple).

Now, let’s move to implementations
1) Create pojo for holding rate-limiter configurations by reading them from property files.
2) Using this configuration, we create a unique-key for each url based on url, method, expression value.
Services that are integrating this solution will provide implementation, definition of expression value, such as
expression: “@rateLimitService.getKey()” can be ip-address, username, user-id or any string which gives support to define additional segregation
- return url + "#" + method.name() + "#" + value;
3) Register RedisRateLimiterFilter per unique-key which is responsible for determining whether to throttle or not, returning response
4) These filters perform throttle by using lua script. We are using lua script as it guarantees the script’s atomic execution on redis
5) Now, lets see contents of lua script(“redis.lua”)

 **Conclusion:**

 
For distributed systems, it is important to limit the api requests, but using basic bucket4j and resilience4j is limited to one instance only.
So, we can use distributed solutions that use common storage. For this, we can choose a reusable, easy-to-use jar for redis.
Also, there are multiple libraries available on the market for this with various kinds of storage. We can choose from them as suitable for the system.
If these solutions do not meet the requirement, we can always go for creating our own library.



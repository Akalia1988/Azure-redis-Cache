# Azure-redis-Cache
Azure Cache for Redis with .NET Core

Azure provides us with it’s own implementation of Redis called Azure Cache for Redis. This is an in-memory data store that helps us improve the performance and scalability of our applications. We’re able to process a large amount of application requests by keeping the most frequently accessed data in our server memory so that it can be written to and read from quickly.

Azure Cache for Redis has the following pricing tiers:
.Basic
This is the tier with the fewest features and less throughput and higher latency.
You should use this tier only for development or testing purposes.


.Standard
This tier offers a two-node primary-secondary replicated Redis cache that is managed by Microsoft
This has a SLA of 99.9%

.Premium
This is a enterprise grade Redis cluster managed by Microsoft.
This tier offers the complete group of features with the highest throughput and lower latencies.
Deployed on more powerful hardware.
This has a SLA of 99.9%

Setup Redis Cache in Azure Portal. It should look like snippet below

![image](https://user-images.githubusercontent.com/58148717/104233512-9ffbea80-5417-11eb-9b13-ef6c3dd67c98.png)

To connect to our new Redis instance, we’ll need to grab the primary connection string. This is straightforward.

Go into your Azure Cache for Redis portal and click Access Keys in the navigation menu.

Click on the copy to clipboard button for the Primary Connection string to copy the value we need to connect to our client. This is made up of both the host name of the cache, along with the primary key for the cache. Save this value for later as we will need it for our configuration file.

![image](https://user-images.githubusercontent.com/58148717/104233701-ec472a80-5417-11eb-8c14-adc74ed1a672.png)

Now that we have our Azure Cache for Redis set up and have our connection string ready, we can start coding our .NET Core console application.
For this tutorial, I’m just going to be creating a basic .NET Console application. To create one, open up Visual Studio 2019 and create a new Console App (.NET Core) project.

Once your Console App is ready, you’ll need to install the following NuGet packages:

Microsoft.Extensions.Configuration
Microsoft.Extensions.Configuration.FileExtensions
Microsoft.Extensions.Configuration.Json
Microsoft.Extensions.DependencyInjection
Newtonsoft.Json
StackExchange.Redis

We will use a local appsettings.json file to store our connection string to our Redis Cache. To add our App Settings file, right click your solution file and select Add New Item. Enter json in the search bar and pick the JavaScript JSON Configuration file option and name the file appsettings.json

![image](https://user-images.githubusercontent.com/58148717/104233865-35977a00-5418-11eb-85e7-4a7e42b8bb97.png)

Replace the contents of your file with the following content (replace the value with the connection string that you retrieved from the portal earlier):
{
  "CacheConnection":  "<valuefromportal>"
}
  
We now have everything setup to write our application (Check the program.cs file)

Let’s go through the code:
In our CreateConfig() method, all we are telling our app to do is to load our appsettings.json file and build our Configuration using the values in that app settings file.
Once we have loaded our connection string into our application, we’ll need to connect to our Redis instance. This is done by using a ConnectionMutiplexer class.
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = _config["CacheConnection"];
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }

Once we’ve connected to our Redis cache in our code, we can start running commands against our instance. We can start by running a simple PING command against our instance like so:

string cacheCommand = "PING";
Console.WriteLine("Cache command: " + cacheCommand);
Console.WriteLine("Response: " + cache.Execute(cacheCommand).ToString());
The PING command in Redis is used to test if a connection to our instance is still alive. We can also use it to measure the latency between our client and our Redis instance. Since we’ve just provided the PING command with no other arguments, we get a PONG response from our Redis instance.

https://redis.io/commands/ping Read more about Ping.

Let’s try an add a new message to the cache. We can do so by using the StringSet() method, like so:

cacheCommand = "SET Message \"Hello! I can get the cache working from this console app :)\"";
Console.WriteLine("Cache command: " + cacheCommand + " or StringGet()");
Console.WriteLine("Response: " + cache.StringSet("Message", "Hello! I can get the cache working from this console app :)").ToString());

We can now retrieve the message from the cache using the StringGet() method, like so:

cacheCommand = "GET Message";
Console.WriteLine("Cache command: " + cacheCommand + " or StringGet()");
Console.WriteLine("Response: " + cache.StringGet("Message").ToString());

We can also list the amount of clients connected to our Redis instance by executing the CLIENT LIST command. This returns information about the clients that are connected to our Redis instance, including information like the ip address of the client, total duration of the connection in seconds etc.
https://redis.io/commands/client-list


































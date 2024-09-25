# API-Gateway-Ocelot

Step-by-Step Code for Ocelot API Gateway Integration
1. Create a New .NET 8 API Project
First, create a new ASP.NET Core Web API project that will act as the API Gateway.


````
dotnet new webapi -n ApiGateway
cd ApiGateway
````

2. Add Ocelot NuGet Package
To add Ocelot to your project, install the required package via NuGet:


````
dotnet add package Ocelot
````


3. Configure Ocelot Routes in ocelot.json
Create a new file in the root of the project named ocelot.json. This file will define the routes, downstream services (the actual backend APIs), and upstream paths (the API Gateway paths).

````
{
  "Routes": [
    {
      "DownstreamPathTemplate": "/finance/{everything}",
      "DownstreamScheme": "https",
      "DownstreamHostAndPorts": [
        {
          "Host": "localhost",
          "Port": 5001
        }
      ],
      "UpstreamPathTemplate": "/finance/{everything}",
      "UpstreamHttpMethod": [ "GET", "POST" ]
    },
    {
      "DownstreamPathTemplate": "/inventory/{everything}",
      "DownstreamScheme": "https",
      "DownstreamHostAndPorts": [
        {
          "Host": "localhost",
          "Port": 5002
        }
      ],
      "UpstreamPathTemplate": "/inventory/{everything}",
      "UpstreamHttpMethod": [ "GET", "POST" ]
    }
  ],
  "GlobalConfiguration": {
    "BaseUrl": "https://localhost:5000"
  }
}
````

Routes: Defines how the API Gateway forwards requests to downstream services. In this example:

Requests made to /finance/{everything} are forwarded to the downstream Finance service running at http://localhost:5001.
Requests made to /inventory/{everything} are forwarded to the downstream Inventory service running at http://localhost:5002.
UpstreamPathTemplate: The path clients use to access services through the gateway.

DownstreamHostAndPorts: The actual location (host and port) of the service.

4. Update Program.cs to Integrate Ocelot Middleware
In .NET 8, the minimal hosting model is used. Update your Program.cs to include Ocelot middleware.

```
using Ocelot.DependencyInjection;
using Ocelot.Middleware;

var builder = WebApplication.CreateBuilder(args);

// Add Ocelot configuration
builder.Configuration.AddJsonFile("ocelot.json", optional: false, reloadOnChange: true);

// Add Ocelot to the services
builder.Services.AddOcelot();

var app = builder.Build();

// Use Ocelot middleware
await app.UseOcelot();

app.Run();
```

This code does the following:

AddOcelot: Registers Ocelot services.
UseOcelot: Enables the Ocelot middleware to handle incoming requests and route them to the appropriate downstream services.

5. Backend Services (Finance and Inventory) refer
https://github.com/Rajkumar-Raj/API-GateWay-YARP


Summary
Create a .NET 8 API Gateway project.
Install Ocelot for routing and API management.
Define the routing in the ocelot.json file for multiple services.
Run backend services and configure the API Gateway to route traffic.
Optionally, add JWT authentication for securing the routes.
This setup allows you to create a fully functional API Gateway using Ocelot with modular, extensible configurations for routing and authentication.
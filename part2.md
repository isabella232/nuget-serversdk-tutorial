#Using the Sinch .Net server-side SDK to receiving an SMS to your Web API applicationYour application will need a phone number for the SMS to be sent to.  You can rent numbers through the Sinch dashboard.  Rent a number, ensuring the Type is SMS:

![get a number](img/numbers.png)Assign this number to the application that should receive the SMS.  Click the Pen symbol, then SMS, select the number you’ve just rented. Click Save.![set a number](img/set-number.png)In Visual Studio create a new ASP.NET Web Application.

![new project](img/new-project2.png)Select the Empty ASP.NET 4.5 Template, and select the checkbox for a core reference to Web API:

![.NET template](img/template.png)Right click on References in Solution Explorer and select **“Manage NuGet Packages…”** If you’re using an older version of Visual Studio and don’t see this you will need to add it to your Visual Studio from here: [http://docs.nuget.org/consume/installing-nuget](http://docs.nuget.org/consume/installing-nuget)
Search for `Sinch.ServerSdk.Callback.WebApi` and click **“Install”** for `Sinch.ServerSdk.Callback.WebApi`.  This will install the Sinch Callback.WebApi package, as well as the Sinch Server SDK NuGet package.

![SMS Nugget](img/nugget2.png)Add a new Controller using the “Web API 2 Controller – Empty” scaffold.  Name it **“IncomingMessagesController”**.![Scaffold](img/scaffold.png)Import the following namespace.`using Sinch.ServerSdk.Messaging.Models;`Add a post method.  Within this method is where you action the SMS sent to the number you bought through the dashboard.
The Web API template will make the default route for this method `[POST] /api/incomingmessages`.Mark the controller as needing validation by adding the “SinchCallback” attribute:````csharp[SinchCallback]public void Post([FromBody]IncomingMessageEvent value){}
````
If this application were hosted now, and the callback URL set on the Sinch dashboard, it would work as expected.  But it would be insecure because anyone could post to that endpoint and mimic an SMS to the number.
To add validation that the messages received are only from Sinch and haven’t been tampered with in any way, add the following to the Global.asax.cs file:````csharpusing Sinch.ServerSdk;using Sinch.ServerSdk.Callback.WebApi;````Then append this to the end of the `Application_Start()` method, replacing the application key and secret with your own.

````csharpvar factory = new SinchFactory("00000000-0000-0000-0000-000000000000", "AAAAAAAAAAAAAAAAAAAAAA==");GlobalConfiguration.Configuration.MessageHandlers.Add(new CallbackMessageHandler(factory));
````The final step is to register this method as the SMS callback URL on the Sinch dashboard.  Host the web application somewhere (AWS, Azure, self-host etc.) then add the route to the API endpoint as the **“Callback URL”**.

![callbacks](img/callbacks.png)And that’s it.  Now any request to this Web API application will be validated by the Sinch CallbackMessageHandler to ensure that they are legitimate.
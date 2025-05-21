Teams Agents in 30 Seconds
1 Hr 55 Min Remaining 
Exercise 1: Build Your Agent in 30 Seconds
First, we will use the Teams CLI to build a simple Echo Bot.

In this first part, you'll learn the basics of creating an application, understanding its structure, and running it locally. By the end, you'll have a solid foundation to build upon as you explore more advanced features and capabilities of the SDK.

Login

Login with the following
Username: Admin

Password: Pa55w.rd

Open VS Code

Left click once on Visual Studio Code. 1zsd9nh9.jpg
Notice that this opens 2 VS Code windows

one for coffee-agent
another for LAB320.
Start with VS Code for LAB320

Navigate to the window for LAB320.
Install and use the Teams CLI

The Teams CLI is a command-line tool that helps you create, manage, and deploy Teams applications. It provides a set of commands to simplify the development process.

Open the terminal and install the Teams CLI globally using npm
npm install -g @microsoft/teams.cli@preview
87szttmy.jpg

Create an Agent. Sweet!

Next, run this command to create your agent. This is a basic agent that repeats back any message it receives.
teams new ts quote-agent --template echo
Notice that this command:

Creates a new directory called quote-agent.
Bootstraps the echo agent template files into it under quote-agent/src.
Creates your agent's manifest files, including a manifest.json file and placeholder icons in the quote-agent/appPackage directory. The Teams app manifest is required for sideloading the app into Teams.
Run Your Echo Bot 🎉

Navigate to your agent's directory
cd quote-agent
Install the dependencies
npm install
zjrxraov.jpg

Start the development server
npm run dev
You may encounter Windows Defender Firewall pop-ups for Visual Studio Code and Node.JS. Click "Allow access".

Check the console, you should see output similar to the following: 05ow6my7.jpg
Notice that when the application starts, you'll see:

A http server starting up. (port 3000) (This is the main server which handles incoming requests and serves the agent application.)
A devtools server starting up. (port 3001) This is a developer server that provides a web interface for debugging and testing your agent quickly, without having to deploy it to Teams.
Send the Agent a message:

Navigate to the devtools server by clicking the link from the console. You can do this by holding Ctrl on your keyboard and clicking with your mousepad. You should see a simple interface where you can interact with your agent.

Send it a message and watch it echo back!

Congrats, you just made your agent in 30 seconds! 🎉 li390omf.jpg

Please proceed to the next section. (Navigation aids are at bottom right)

Teams Agents in 30 Seconds
1 Hr 44 Min Remaining 
Exercise 2: Explore DevTools
One of the main motivations for Teams AI (v2) Library is to provide excellent tools that simplify and speed up building and testing agents. Because of this, we created the CLI for speedy agent initiation and project management, and DevTools as an accessible way to test your agent's behavior without jumping through deployment hoops. DevTools also provides crucial insight on activity payloads on the Activities page.

The developer tools can be used to locally interact with an app to streamline the testing/development process, preventing you from needing to deploy/register the app or expose a public endpoint.

Chat Capabilities
Let's start by examining the chat.

The chat window emulates Teams features as closely as possible. Not all Teams features are available in DevTools, but we are working to add more features over time. h9zandzy.jpg

Send a message in the compose box. The bot should echo back.
Hover over the bot's message to react to the message.
Hover over your own message. Edit your message by selecting the Edit (pencil) icon from the message actions menu. Press Enter or the checkmark button to send the edited message, or the Dismiss (X) button to cancel.
Hover over your own message. Soft delete your message by pressing the More (ellipsis) button, then the Delete (trash) button. Click "Undo" to restore the message.
Check your app connectivity - the DevTools banner shows a green badge or 'Connected' text when connected, and red or 'Disconnected' when not.
Next, hover over the search icon, and let's examine the activity. Click the button.
Inspect Activities
zay4uo1s.jpg

Select an activity in the left grid, this opens a detailed view in Preview mode, showing the full payload as a tree with expandable and collapsible sections.
Toggle from "Preview" to "JSON" under the "Activity details" header to see the raw JSON payload.
Press the Copy button in the top left corner of the Activity details view to copy the payload to your clipboard.
To reset the filter on this specific activity ID, use the filter button in the "Type" column header and de-select the activity ID to show all activities again.
Awesome, now that you're warmed up with our tools, let's dive into our AI bot.

Close the DevTools browser, and make sure to kill the terminal by clicking on the trash button, before proceeding to the next exercise.
jvyl25j0.jpg

Please proceed to the next section. (Navigation aids are at bottom right)
Teams Agents in 30 Seconds
1 Hr 32 Min Remaining 
Exercise 3: Coffee Agent
Step 1: Load the Project and Keys
For today, we're going to be working with our own little barista- the Coffee Agent.

The Coffee Agent helps manage daily coffee orders for your team. Once installed, it automatically starts a new order each day. It randomly selects a café and, when requested, assigns a designated person to place the order. It also maintains a list of available coffee shops.

The Coffee Bot is built with the v2 Teams SDK and shows how easy it is to use activity handlers, dialogs, adaptive cards, and AI to create a fun, interactive bot with just a few building blocks.

1zujrlzq.jpg

First, let's get our keys and endpoints. Open the browser, copy and paste the link to navigate to https://ai.azure.com/resource/deployments. Make sure this opens inside of your VM.

You will be requested to sign in. Open this lab's Resources tab, and sign in with the provided Username and Password.

If you are stuck in a sign in loop, try reopening the link.

If you click on the value, it will auto-populate for you.

Upon successful login, you should see this page. Click into TeamsAgentsAOAI
7stqpxgc.jpg

Your screen should now look like this. Click into gpt-4o
icapf7ud.jpg

Don't see any deployments? Click here.
Due to the high number of model deployements required at the same time for these labs, occasionally the model deployments can fail. No worries, we can deploy these manually:
Click the Deploy model > Deploy base model button qp1nmop9.jpg
Select gpt-4o, and click Confirm. b8ax9n5n.jpg
Click deploy. ohaia7lz.jpg
This page has your endpoint and API key. Save these for later. yosu2xv3.jpg
This page has your endpoint and API key. Save these for later.
tdu4wpv2.jpg

2eeak4id.jpg

Navigate to the coffee-agent window in Visual Studio Code.
Navigate to the sample.env file and right click it. Select rename to rename the file as .env and save.

Open the .env file and set the values from Step 5 in the following way:
The file should look something like this:


Make sure the endpoint does not have an ending slash (/) and that it concludes with ".microsoft.com".

No need to set the other variables. Once you have made the changes, save the file.

Step 2: Launch the Coffee Agent in Teams
At this point, the project and environment variables should be properly configured.

Now, let's start the agent and load it into Teams using the M365 Agent Toolkit extension.

M365 Agent Toolkit is a powerful tool that simplifies deploying and debugging Teams applications. It automates tasks like managing the Teams app manifest, configuring authentication, provisioning, and deployment.

sfqx8qx4.jpg

Click the 'Run and Debug' extension icon (fourth icon in the leftmost column). In the dropdown, select "Debug (Edge)", or press F5 on the keyboard.
This will kickstart the M365 Agent Toolkit process of provisioning the required Azure resources, starting the local bot web server, configuring the local tunnel, and loading the agent into the Teams environment.

You may encounter Windows Defender Firewall pop-ups for Visual Studio Code and Node.JS. Click "Allow access".

First, you will be required by ATK to sign in with a Microsoft account.
Click "Sign In" and a web browser should open with the Microsoft login page. Use the previous credentials on the "Resources" tab. As you signed in earlier, it should already be saved as shown below.
e5due4dr.jpg
Once complete, you can close the tab.

In this step, another web browser will open, and you will be asked to provide Microsoft credentials again. Simply provide the same credentials.

Voila! Click to "Add" or "Open" the bot. sdspltp3.jpg

If this page does not open up for you then follow the below manual sideload steps.

Manual Sideload Instructions
Go back to your VSC console, and open the link to your DevTools from your terminal. This allows us to debug with both DevTools and Teams at the same time.

On Teams, the bot should have sent you an adaptive card with your team's coffee order for today. 7yrhcbv9.jpg

Step 3: Interact with the Coffee Bot
Submit your coffee order by clicking the "Submit an Order" button. Input your name and your order. The bot should return an updated card with your submission.
eops4opn.jpg

Ask the bot, What can you do?. The bot will tell you all the things it can do.

Ask the bot, Who should order the coffee today?. The bot will nominate one of your team members to pick up the coffee. og290qwp.jpg

Message the bot, I want to add a coffee shop. Can we add the Living Room Cafe. It has two drinks, a Matcha Latte (small) and an Oat Lavender Latte (medium). naa51dkq.jpg

Then request from the bot, "Send me an updated list of coffee shops!" 36tx8l9w.jpg

Now, navigate back to your DevTools browser, and head to the "Activities" tab. You should see a full capture of all the events. s99277bv.jpg

Now end the application by clicking the red square (Stop) and close all the terminals. 5souu7wj.jpg

Step 4: Add an MCP Client
On the terminal, install the MCP Client Package via
npm i @microsoft/teams.mcpclient
At the top of the index.ts file, import the library import { McpClientPlugin } from '@microsoft/teams.mcpclient';

Navigate to the index.ts file. Scroll to Line 80. In the ChatPrompt declaration, add [new McpClientPlugin()] as the second parameter. fbtq4owk.jpg

At the end of the ChatPrompt, add the below snippet.

.usePlugin("mcpClient", {
    url: "https://aiacceleratormcp.azurewebsites.net/runtime/webhooks/mcp/sse",
    params: {
      headers: {
        "x-functions-key": process.env.AZURE_FUNCTION_KEY!,
      },
    },
  });
Your code should look something like this aa1hty7h.jpg

In your .env file, add this key.
AZURE_FUNCTION_KEY=BS6aGx4r9E1-vmOFQr88fMjWbYSgoddqoPB-Zte4gEKaAzFuLYnDwg==
Make sure to save all the files. Then, click the 'Run and Debug' extension icon (fourth icon in the leftmost column). In the dropdown, select "Debug (Edge)", or press F5 on the keyboard.

Ask the bot What can you do? The bot will share various capablities, including looking up the hours of various coffee shops.

wa4m3gfn.jpg

Let's ask the bot, What are the timings for Living Room Cafe Coffee?
k8a2yux3.jpg

Kudos, that was the last coding portion! And just like that, you have setup an MCP client!

Please proceed to the next section. (Navigation aids are at bottom right)

Teams Agents in 30 Seconds
58 Minutes Remaining 
Exercise 3: Coffee Agent
Step 1: Load the Project and Keys
For today, we're going to be working with our own little barista- the Coffee Agent.

The Coffee Agent helps manage daily coffee orders for your team. Once installed, it automatically starts a new order each day. It randomly selects a café and, when requested, assigns a designated person to place the order. It also maintains a list of available coffee shops.

The Coffee Bot is built with the v2 Teams SDK and shows how easy it is to use activity handlers, dialogs, adaptive cards, and AI to create a fun, interactive bot with just a few building blocks.

1zujrlzq.jpg

First, let's get our keys and endpoints. Open the browser, copy and paste the link to navigate to https://ai.azure.com/resource/deployments. Make sure this opens inside of your VM.

You will be requested to sign in. Open this lab's Resources tab, and sign in with the provided Username and Password.

If you are stuck in a sign in loop, try reopening the link.

If you click on the value, it will auto-populate for you.

Upon successful login, you should see this page. Click into TeamsAgentsAOAI
7stqpxgc.jpg

Your screen should now look like this. Click into gpt-4o
icapf7ud.jpg

Don't see any deployments? Click here.
Due to the high number of model deployements required at the same time for these labs, occasionally the model deployments can fail. No worries, we can deploy these manually:
Click the Deploy model > Deploy base model button qp1nmop9.jpg
Select gpt-4o, and click Confirm. b8ax9n5n.jpg
Click deploy. ohaia7lz.jpg
This page has your endpoint and API key. Save these for later. yosu2xv3.jpg
This page has your endpoint and API key. Save these for later.
tdu4wpv2.jpg

2eeak4id.jpg

Navigate to the coffee-agent window in Visual Studio Code.
Navigate to the sample.env file and right click it. Select rename to rename the file as .env and save.

Open the .env file and set the values from Step 5 in the following way:
The file should look something like this:


Make sure the endpoint does not have an ending slash (/) and that it concludes with ".microsoft.com".

No need to set the other variables. Once you have made the changes, save the file.

Step 2: Launch the Coffee Agent in Teams
At this point, the project and environment variables should be properly configured.

Now, let's start the agent and load it into Teams using the M365 Agent Toolkit extension.

M365 Agent Toolkit is a powerful tool that simplifies deploying and debugging Teams applications. It automates tasks like managing the Teams app manifest, configuring authentication, provisioning, and deployment.

sfqx8qx4.jpg

Click the 'Run and Debug' extension icon (fourth icon in the leftmost column). In the dropdown, select "Debug (Edge)", or press F5 on the keyboard.
This will kickstart the M365 Agent Toolkit process of provisioning the required Azure resources, starting the local bot web server, configuring the local tunnel, and loading the agent into the Teams environment.

You may encounter Windows Defender Firewall pop-ups for Visual Studio Code and Node.JS. Click "Allow access".

First, you will be required by ATK to sign in with a Microsoft account.
Click "Sign In" and a web browser should open with the Microsoft login page. Use the previous credentials on the "Resources" tab. As you signed in earlier, it should already be saved as shown below.
e5due4dr.jpg
Once complete, you can close the tab.

In this step, another web browser will open, and you will be asked to provide Microsoft credentials again. Simply provide the same credentials.

Voila! Click to "Add" or "Open" the bot. sdspltp3.jpg

If this page does not open up for you then follow the below manual sideload steps.

Manual Sideload Instructions
Go back to your VSC console, and open the link to your DevTools from your terminal. This allows us to debug with both DevTools and Teams at the same time.

On Teams, the bot should have sent you an adaptive card with your team's coffee order for today. 7yrhcbv9.jpg

Step 3: Interact with the Coffee Bot
Submit your coffee order by clicking the "Submit an Order" button. Input your name and your order. The bot should return an updated card with your submission.
eops4opn.jpg

Ask the bot, What can you do?. The bot will tell you all the things it can do.

Ask the bot, Who should order the coffee today?. The bot will nominate one of your team members to pick up the coffee. og290qwp.jpg

Message the bot, I want to add a coffee shop. Can we add the Living Room Cafe. It has two drinks, a Matcha Latte (small) and an Oat Lavender Latte (medium). naa51dkq.jpg

Then request from the bot, "Send me an updated list of coffee shops!" 36tx8l9w.jpg

Now, navigate back to your DevTools browser, and head to the "Activities" tab. You should see a full capture of all the events. s99277bv.jpg

Now end the application by clicking the red square (Stop) and close all the terminals. 5souu7wj.jpg

Step 4: Add an MCP Client
On the terminal, install the MCP Client Package via
npm i @microsoft/teams.mcpclient
At the top of the index.ts file, import the library import { McpClientPlugin } from '@microsoft/teams.mcpclient';

Navigate to the index.ts file. Scroll to Line 80. In the ChatPrompt declaration, add [new McpClientPlugin()] as the second parameter. fbtq4owk.jpg

At the end of the ChatPrompt, add the below snippet.

.usePlugin("mcpClient", {
    url: "https://aiacceleratormcp.azurewebsites.net/runtime/webhooks/mcp/sse",
    params: {
      headers: {
        "x-functions-key": process.env.AZURE_FUNCTION_KEY!,
      },
    },
  });
Your code should look something like this aa1hty7h.jpg

In your .env file, add this key.
AZURE_FUNCTION_KEY=BS6aGx4r9E1-vmOFQr88fMjWbYSgoddqoPB-Zte4gEKaAzFuLYnDwg==
Make sure to save all the files. Then, click the 'Run and Debug' extension icon (fourth icon in the leftmost column). In the dropdown, select "Debug (Edge)", or press F5 on the keyboard.

Ask the bot What can you do? The bot will share various capablities, including looking up the hours of various coffee shops.

wa4m3gfn.jpg

Let's ask the bot, What are the timings for Living Room Cafe Coffee?
k8a2yux3.jpg

Kudos, that was the last coding portion! And just like that, you have setup an MCP client!

Please proceed to the next section. (Navigation aids are at bottom right)

Teams Agents in 30 Seconds
26 Minutes Remaining 
How It Works
Time Check - Whenever there is less than 10 minutes remaining in the lab, you may skip to the last section of the lab.

dr6lw5fz.jpg

The App Class
The heart of your application is the App class. This class handles all incoming activities and manages your application's lifecycle. It also acts as a way to host your application service.

The app configuration includes a variety of options that allow you to customize its behavior, including controlling the underlying server, authentication, and other settings. For simplicity's sake, let's focus on plugins.

Plugins
Plugins are a core part of the Teams AI Library (v2). They allow you to hook into various lifecycles of the application. The lifecycles include server events (start, stop, initialize etc.), and also Teams Activity events (onActivity, onActivitySent, etc.). In fact, DevTools is a plugin too.

typescript
import { App } from '@microsoft/teams.apps';
import { ConsoleLogger } from '@microsoft/teams.common/logging';
import { DevtoolsPlugin } from '@microsoft/teams.dev';

const app = new App({
  logger: new ConsoleLogger('@samples/coffee', { level: 'debug' }),
  plugins: [new DevtoolsPlugin()],
});
Storage
For the Coffee Bot, we managed storage locally.

typescript
const storage = new LocalStorage<StorageState>();
storage.set("local", { coffeeShops: initialCafes, currOrder: initialOrder } as StorageState);
Application Lifecycle
Your application starts when you run:

typescript
(async () => {
  await app.start();
})();
This part initializes your application server and, when configured for Teams, also authenticates it to be ready for sending and receiving messages.

Message Handling
Teams applications respond to various types of activities. The most basic is handling messages:

typescript
app.on("message", async ({ send, activity }) => {
  await send({ type: "typing" });
  const res = await prompt.send(activity.text);
  await send(res.content!)
});
This code:

Listens for all incoming messages using app.on('message').
Sends a typing indicator, which renders as an animated ellipsis (…) in the chat.
Responds by directly sending back the LLM's response.
Type safety is a core tenet of this version of the SDK. You can change the activity name to a different supported value, and the type system will automatically adjust the type of activity to match the new value.

Although not displayed here, we also support streaming on Teams.

Teams Features
Teams Dialogs - Handler and Builder Pattern
This is an example of handling the task/fetch event through the bot card button.

typescript
app.on("dialog.open", async () => {
  const order = storage.get("local")!.currOrder;
  const dialogCard = generateOrderDialogCard(order!);

  return { 
      task: {
        type: 'continue',
        value: {
          card: {
            contentType: "application/vnd.microsoft.card.adaptive",
            content: dialogCard,
          },
          height: 'medium',
          title: 'Submit Your Order',
        },
      },
    } as TaskModuleResponse;
});
Adaptive Cards - Builder Pattern
This lab's code / typings are slightly out-of-date with v2's public preview release of 2.0.0-preview.x. Please note this code would need to be updated when using the latest preview packages.

typescript
/**
 * Generates the order dialog card for the coffee order.
 * @param order - The coffee order object containing the order details.
 * @returns A card object representing the order dialog.
 */
function generateOrderDialogCard(order: CoffeeOrder): Card {
    const card = new Card();
    card.version = '1.5';
    card.body = [new TextBlock("Your Name:", { weight: 'bolder' })];

    const nameInput = new TextInput(); 
    nameInput.id = "userNameInput";
    nameInput.placeholder = "Enter your name";
    card.body.push(nameInput); 

    card.body.push(new TextBlock("Select Your Drink:", { weight: 'bolder', spacing: 'medium' }));
    const drinkChoices = order.coffeeShop.drinks.map(drink => ({
        title: `${drink.name} (${drink.size})`, 
        value: `${drink.name} (${drink.size})`  
    }));

    const drinkInput = new ChoiceSetInput(); 
    drinkInput.id = "selectedDrinkInput";
    drinkInput.style = 'compact';

    drinkInput.choices = drinkChoices.map(choice => ({ title: choice.title, value: choice.value })); 
    card.body.push(drinkInput); 

    card.actions = [
        new SubmitAction().withTitle("Submit Your Order").withId("submitOrderButton") 
    ];

    return card;
}
AI Features
Chat Prompt
A ChatPrompt is a root-level concept which is a wrapper around the Chat Models. It abstracts away the details of the model and provides a simple, unified interface to interact with it. It takes in a variety of options that configure the way the prompt behaves.

To execute the prompt, you can use the send method. This method takes the incoming message (or list of messages), and passes it along to the model. It takes care of keeping a history, orchestrating tool calls, and handling streaming.

Function Calls
In this example, we also demonstrate function calling. Function calls allows models to directly connect to external tools. This unlocks the power of AI to establish deeper integrations between applications and achieve greater capabilities. This consists of performing computations, data retrieval, richer workflows, dynamic user interactions, and more.

typescript
const prompt = new ChatPrompt({
  instructions: [
    "you are an assistant that helps manage daily coffee orders for the team.",
    "every day, you will randomly select a team member to place an order for coffee.",
  ].join("\n"),
  model: new OpenAIChatModel({
    model: process.env.AOAI_MODEL,
    apiKey: process.env.AOAI_API_KEY,
    endpoint: process.env.AOAI_ENDPOINT,
    apiVersion: "2024-05-01-preview",
  } as AzureOpenAIChatModelOptions),
}).function("choose_orderer", "chooses the person assigned to get the coffee", () => {
  const state = storage.get("local");
  const order = state!.currOrder;
  const members = order!.drinks.keys();
  const membersArray = Array.from(members!);
  const selectedOrderer = membersArray[Math.floor(Math.random() * membersArray.length)];
  state!.currOrder!.orderer = selectedOrderer;
  storage.set("local", state!);
  return selectedOrderer;
}).function("add_coffee_shop", "adds a new coffee shop to the list of coffee shops", addCoffeeShopSchema, (cafe: CoffeeShop) => {
  const state = storage.get("local");
  state!.coffeeShops.push(cafe);
  storage.set("local", state!);
  return cafe;
}).function("get_coffee_shops", "returns the list of available coffee shops", () => {
  const state = storage.get("local");
  return state?.coffeeShops;
});
The schema when adding a coffee shop is specified as such:

typescript
const addCoffeeShopSchema = {
    "type": "object",
    "properties": {
      "name": {
        "type": "string",
        "description": "The name of the coffee shop."
      },
      "drinks": {
        "type": "array",
        "description": "The list of drinks available at the coffee shop.",
        "items": {
          "type": "object",
          "properties": {
            "name": {
              "type": "string",
              "description": "The name of the drink."
            },
            "size": {
              "type": "string",
              "description": "The size of the drink."
            }
          },
          "required": ["name", "size"]
        }
      }
    },
    "required": ["name", "drinks"]
} as ObjectSchema;
Please proceed to the next section. (Navigation aids are at bottom right)

Teams Agents in 30 Seconds
25 Minutes Remaining 
Takeaways & Next Steps
Congratulations on building your first agent on Teams AI Library (v2)!

In this lab we covered the following topics:

Used the Teams CLI to build our own intelligent, conversational agent in 30 seconds
Tested the agent by launching it directly on DevTools and Microsoft Teams
Learned and explored the power of integrating Teams DevTools, Teams AI (v2) Library, M365 Agent Toolkit, and Azure OpenAI
Interacted with our Coffee Agent sample
High-level code dive of the Teams AI Library (v2)
If you participated in this lab on-demand, please let us know your thoughts in this quick survey.

Want to learn more and keep building? Here are further resources to look at:

Teams AI Library (v2) Docs
https://aka.ms/teamsai-v2-docs
Coffee Agent https://aka.ms/coffee-agent

If you are using this sample outside of the lab:
update all @microsoft/teams dependencies in the package.json to 2.0.0-preview.2 or preview
Teams AI Library (v2) Code https://aka.ms/teamsai-v2
Teams AI Accelerator Templates https://adaptivecards.microsoft.com/
Adaptive Cards
Please proceed to the End. (Navigation aids are at bottom right)

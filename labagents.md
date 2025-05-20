Build autonomous capabilities for agents
1 Hr 42 Min Remaining 
Build autonomous capabilities for agents
LAB 327 - Building an Autonomous Agent
Welcome to the Autonomous Agents Lab! In this session, you will build an autonomous agents from start to finish. You will learn best practices to help transform your organization with the power of autonomous capabilities. Below are the detailed instructions to guide you through the agent creation process:

🚀 Getting Started with Microsoft Copilot Studio
In this workshop, you'll build your own autonomous agent using Microsoft Copilot Studio. Let's begin by setting up your environment and logging into the required services.

Log into the Virtual Machine
To start, log into the virtual machine using the credentials provided below:

Password: Pa$$w0rd
Once logged in, open Edge.

Environment Setup
You'll create and configure a new Power Platform environment named Autonomous Agent.

Open your browser and go to https://admin.powerplatform.microsoft.com/.
Sign in with your credentials (if asked)
Username: User1-51501394@LODSPRODMCA.onmicrosoft.com
Password: ADg07!e$kH
In the left menu, click Environments, then click + New.
Note: You might see the new UI. First you have to click on Manager before you can see "Environments" nqo3niso.jpg
Enter Autonomous Agent as the name
Enable Get new features early, and choose Developer for the type.
Click Next, enable Deploy sample apps and data?, then click Save.
Watch for Autonomous Agent to appear in the list. Note, provisioning takes about 60 seconds.If you don't see the status changed to ready after 60 seconds, you can refresh the page.
Access Microsoft Copilot Studio
In the browser, navigate to:
copilotstudio.preview.microsoft.com

When prompted to sign in, use the following credentials:

Username: User1-51501394@LODSPRODMCA.onmicrosoft.com
Password: ADg07!e$kH
Landing Page
After signing in, you'll land on a welcome page titled: Welcome to Microsoft Copilot Studio

Follow these steps:

Select your language and region.
Click Get Started to continue.
Navigate to the Autonomous Agent environment on the top right of the screen.
Now you are ready to build your first agent 🤖

Build autonomous capabilities for agents
1 Hr 41 Min Remaining 
Customer Brief Agent 🧑‍💼 📋 🤖
Participants will create a Customer Brief Agent that streamlines prep for high-profile customer meetings by pulling data from a data repository, as well as Outlook Calendar. This common, time-consuming task is ideal for an autonomous agent, improving customer meeting preparedness and saving significant prep time.

Create the AI Agent
From the Home screen, select Create in the left navigation pane.
On the next page, click the + New agent button at the top.
On the Start building your agent screen, select Create on the top-right corner (we won't use the chat description "create" method for this exercise).
Update the agent details
Now, you should be in the Overview page of your agent. Ensure the Orchestration toggle is enabled. This allows you to add triggers and make your agent autonomous.

Fill out agent details as follows:

Click the Edit button to begin making updates to the default text:

Name: Customer Brief Agent
Description: An agent to streamline the preparation process for customer meetings.
Instructions (copy and paste these manually):
Identify the company name from the email.
Get information about the most recent meeting with this company from my Outlook Calendar using Get Events (V4) action.
Get customer information including order history, opportunity and case information from the Knowledge file.
Summarize the information from step 2 and 3 to provide insights and next steps prior for the upcoming meeting.
Send a brief summary in email using rich text.
Add Knowledge
In the Knowledge section, Disable "Allow the AI to use its own general knowledge."
Click Add Knowledge.
Click on the "Select to browse"
rgvns5x9.jpg

Navigate to Desktop -> Lab Assets
Select "customer informatio.docx" and "April 25_Meeting summary.docx".
Click Add, and wait for upload to complete.
In this Knowledge section you can add description to for this knowledge for AI to use. In our case we select the default.

You need to wait until your knowledge source is processed
j38w0sbx.jpg

The status changes from In Progress to Ready. Depending on the workload this might take few minutes. You can continue building your agent while it is processing.

nl00q5e6.jpg

Add and Configure Tools (FKA Actions)
Get Events (V4)

Select the + Add tool option from the Overview screen to add a tool.
In the Add tool wizard, search for and select Get Events (V4) to proceed to the next step.
By default, Get Events (V4) links to the Microsoft Office 365 Outlook account that is associated with the Copilot Studio account. Select "Add & Configure" to continue.
Sometimes loading the Tools details page is slow. You might want to refresh if it doesn't load.

Configure the following fields:
Click on "Additional details"
Authentication: select Copilot author authentication
Inputs -> Calendar id
Fill using: select Custom value
Value: select Calendar
Click save
If your connection is not created. You need to Click on "Not Connected", click on create a new connection, and follow instrcution to create connection. You need to allow Office 365 Outlook to access your account. rjcx6fl7.jpg

Click on the back button from the button to go to the Tools landing page.

Click on 'Add Tool'

Send an email (V2)

In the Add tool wizard, search for and select Office 365 Outlook Send an email (V2) to proceed to the next step.
By default, Send an email (V2) links to the Microsoft Office 365 Outlook account that's associated with the Copilot Studio account. Select Add and Configure to continue.
Configure the following fields:
Click on "Additional details"
Authentication: select Copilot author authentication
Description: This tool sends an email message, which must be formatted in HTML
Inputs -> To
Description: your connected Outlook email address which is your Username User1-51501394@LODSPRODMCA.onmicrosoft.com
Subject -> Your next meeting Overview and Prep
Click save
Add a Trigger
We want this agent to activate when a new engagement request email is received.

In the Triggers section of the Overview tab, click Add trigger.
In the Add trigger wizard, choose When a new email arrives (V3).
Set the trigger name to: When an engagement request email arrives
In some cases you might need to fix your connections. (i.e., Microsoft Copilot Studio, or Office 365 Outlook)
Click "Next" to configure the parameters.
From the Folder picker, select the Inbox folder.
Set the To field to your test email: User1-51501394@LODSPRODMCA.onmicrosoft.com
Set Subject Filter to: Upcoming meeting
Click Create trigger.
Ready to Publish
Click Publish to publish this agent for use (skip over any noted risk items)

Trigger the Agent to Run
First, let's add a 'sample' previous meeting for our agent to use to simulate a previous meeting with the customer, Contoso Pharmaceuticals. To create this, go to the "Lab Assets" folder on your desktop, and double click on "meeting_with_contoso_pharmaceuticals". This will open Outlook. If this is the first time you are using Outlook in this lab, it will ask you for user name and password. You can use the following to setup your mail box.

Username: User1-51501394@LODSPRODMCA.onmicrosoft.com
Password: ADg07!e$kH
When asked about Email Service Provider. Select "Microsoft 365".

2- Add calendar wizrd will be show. Select the "Calendar" from the list.

kiiz1bus.jpg

3- Now, let's trigger the agent's first run by sending a test email while you are already in your inbox. Create a new email:

To: User1-51501394@LODSPRODMCA.onmicrosoft.com
Subject: Upcoming Meeting with Contoso Pharmaceuticals
Body: Looping you in this thread as we prepare for our upcoming meeting with Contoso Pharmaceuticals on Wednesday at 2 PM PT. Given the strategic nature of our discussions, we'd love to have your presence to reinforce our partnership and provide executive-level insight. Alex and their team are particularly interested in discussing our long-term roadmap and how we can align on future initiatives. Your perspective would add great value. Let us know if you're available, and we can set up a quick prep call to align beforehand.
Click Send so that you will see the email request for a meeting in your inbox. This will trigger the agent's first run.
Test Agent & View Agent Activity in Activity Map
Let's jump back into Copilot Studio. (i.e., copilotstudio.preview.microsoft.com). Now, you will test and refine your autonomous agent. Testing provides ability to review an Agent's behavior, ensuring it will perform as desired before deploying it in a live environment.

To test the agent, find the agent you just created, navigate to the activity tab. You should see the first automated run of your agent. You can click on it to see the steps you agent took.

Next, move to the Trigger section and click on the "beaker" icon next to the trigger you added in a previous step. In the modal, you'll see a first run record of the agent, triggered by the email you sent just prior to this. Click Start Testing to kick off a test run.

You will see the Activity Map begin to populate with the dynamic steps the agent is taking, in real-time.

Ensure your Customer Brief Agent completes the following:

Checks Dataverse for the customer's account
Retrieves info on cases, orders, opportunities, and contacts
Pulls recent meeting data from Outlook
Sends a summary email with insights
When the process is complete, go to Outlook. Review the Customer Brief email sent by the agent to confirm it includes:

Insights at the top
Data from Dataverse and Outlook Calendar
🧪 Improve Your Agent Prompt for Better Results
Now that your Customer Brief Agent is working, let's improve its prompt to make outputs more accurate, helpful, and well-structured.

We'll use a method inspired by the OpenAI Cookbook:\ Persona + Task + Context + Format + Exemplar

🧭 Fine-Tune Your Prompt to Improve Your Agent
Using the P+T+C+F+E method, review the current prompt and decide how you would improve it. The goal is to create a version of the prompt that helps the agent:

Respond in a more structured and consistent format
Understand its role and task more clearly
Reduce ambiguity and errors in output
Consider how each of the following elements could be applied to your current version:

🔹 Persona
Who is the agent acting as? How should that influence tone and detail?

🔹 Task
What is the agent being asked to do, specifically? Is it clear and measurable?

🔹 Context
What background information or assumptions would help guide the response?

🔹 Format
What should the response look like? List? Paragraph? sections?

🔹 Exemplar
Would an example output help clarify the expected result?

🏁 Final Step
Use this method to evaluate and improve your prompt. You can keep it high-level or go deep into rewriting - the goal is to become intentional about how your agent interprets instructions.

Test your updated prompt and compare the output!

Congratulations! 🎉 In just a short time, you have created an autonomous "Customer Brief" Agent that replaces a traditionally manual, high-effort process - used by countless professionals across companies, worldwide.

By connecting data from multiple systems, summarizing key insights, and sending a ready-to-use customer brief, you've:

Saved hours of prep work per customer engagement
Improved meeting readiness and decision-making
Developed a consistently repeatable process for your entire org
Welcome to the future of autonomous productivity! 💡



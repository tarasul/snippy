<!--
---
name: Snippy - Intelligent Code Snippet Service with MCP Tools
description: A serverless code snippet management service using Azure Functions, Durable Functions, Azure OpenAI, Microsoft Fabric and Azure AI Agents.
page_type: sample
languages:
- python
- bicep
- azdeveloper
products:
- azure-functions
- azure-durable-functions
- azure-openai
- azure-cosmos-db
- azure-ai-projects
- azure-ai-agents
- fabric
urlFragment: snippy
---
-->

<p align="center">
  <img src="https://raw.githubusercontent.com/Azure-Samples/snippy/main/.github/assets/snippy-logo-large.png" alt="Snippy logo" width="150"><br>
  <b>Snippy · Intelligent Code-Snippet Service with MCP Tools</b>
</p>

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://github.com/codespaces/new?hide_repo_select=true&ref=main&repo=Azure-Samples/snippy&machine=basicLinux32gb&devcontainer_path=.devcontainer%2Fdevcontainer.json)
[![Open in Dev Containers](https://img.shields.io/static/v1?style=for-the-badge&label=Dev%20Containers&message=Open&color=blue&logo=visualstudiocode)](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/Azure-Samples/snippy)

Snippy is an **Azure Functions**–based reference application that turns any function into an **MCP (Model Context Protocol) tool** consumable by GitHub Copilot Chat and other MCP‑aware clients. The sample implements a production‑style *code‑snippet service* with AI‑powered analysis:

* **Save snippets** – persists code, metadata and OpenAI embeddings in **Cosmos DB DiskANN**
* **Semantic retrieve** – vector search over embeddings
* **AI Agents** – generate a **deep wiki** or language‑specific **code style guide** from stored snippets
* **Durable fan‑out/fan‑in with Blueprints** – [in experimental branch](https://github.com/Azure-Samples/snippy/tree/gk/durable-functions) for large‑scale processing
* **Microsoft Fabric integration** – [in gk/fabric branch](https://github.com/Azure-Samples/snippy/tree/gk/fabric) demonstrating how to build Agents with Fabric Data Agents


The project ships with reproducible **azd** infrastructure, so `azd up` will stand up the entire stack – Functions, Cosmos DB, Azure OpenAI and Azure AI Agents – in a single command.

> **Important Security Notice**
> This repository is intended for learning and demonstration purposes. **Do not** deploy it to production without a thorough security review. At a minimum you should:
>
> * Swap connection strings for **Managed Identity** + **Azure Key Vault**
> * Restrict network access to Azure services via Private Endpoints or service‑tags
> * Enable GitHub secret‑scanning and CI security tools

[Features](#features) • [Architecture](#architecture-diagram) • [Getting Started](#getting-started) • [Guidance](#guidance)

---

## Features

* **Remote MCP trigger** – expose Functions as real‑time SSE tools
* **AI‑assisted documentation** – "deep‑wiki" and "code‑style" agents create rich Markdown (Mermaid, diagrams, etc.)
* **Vector search on Cosmos DB DiskANN** – low‑latency semantic retrieval
* **One‑click deploy** – `azd up` provisions and deploys code & infra
* **Codespaces & Dev Containers** – fully configured dev environment in your browser or local VS Code

### Tool Matrix

| Tool Name      | Purpose                                                             |
| -------------- | ------------------------------------------------------------------- |
| `save_snippet` | Save code snippets with vector embeddings for semantic search       |
| `get_snippet`  | Retrieve previously saved code snippets by their unique name        |
| `code_style`   | Generate language-specific code style guides from saved snippets    |
| `deep_wiki`    | Create comprehensive wiki documentation by analyzing code snippets  |

---

### Architecture Diagram

![Snippy Architecture](https://raw.githubusercontent.com/Azure-Samples/snippy/main/.github/assets/snippy-architecture.png)

```mermaid
flowchart LR
    %% ─── MCP Hosts & Clients (local) ──────────────────────────────
    subgraph mcphosts["MCP Hosts & Clients (Your Computer)"]
        Host["Host<br/>(VS Code / IDE)"]
        Client["Client<br/>(GitHub Copilot)"]
    end

    %% ─── Application on Azure (remote) ────────────────────────────
    subgraph app["Application (Azure)"]
        Snippy["MCP Server<br/>Snippy Triggers<br/>(Function App)"]:::dashed
        Foundry["Foundry Agent<br/>Deep Wiki · Code Style"]
        Cosmos["Cosmos DB<br/>Operational + Vector DB"]:::datasource
        AOAI["Azure OpenAI<br/>text‑embedding‑3‑small"]
        FabricDA["Fabric – Data Agent"]
        VectorTool["Tools – Vector Search"]
    end

    %% ─── Local interactions ───────────────────────────────────────
    Host <--> Client

    %% ─── MCP protocol to Azure ────────────────────────────────────
    Client <-- "MCP Protocol (SSE)" --> Snippy

    %% ─── Bindings & data flow inside Azure ────────────────────────
    Snippy -- Bindings --> AOAI
    Snippy --> Cosmos
    Snippy --> Foundry
    Foundry --> FabricDA
    Foundry --> VectorTool

    %% ─── Styling ──────────────────────────────────────────────────
    classDef datasource stroke-width:2,stroke-dasharray:5 5
    classDef dashed stroke-width:2,stroke-dasharray:5 5,fill:transparent
    class Cosmos datasource
    class Snippy dashed
    style mcphosts fill:transparent
    style app fill:transparent
```

---

## Getting Started

You can run Snippy in **GitHub Codespaces**, **VS Code Dev Containers**, or your **local environment**. The fastest path is Codespaces.

> Snippy requires an Azure region that supports *text‑embedding‑3‑small* (or a compatible embeddings model) **and** Azure AI Agents. The `azd` workflow prompts you for a region; we recommend **eastus** for best availability.

### GitHub Codespaces

1. Click **Open in Codespaces** above (first badge) – the container build may take a few minutes.
2. When the terminal appears, sign in:

   ```bash
   azd auth login --use-device-code
   ```
3. Launch the stack:

   ```bash
   azd up
   ```
4. Once deployment completes, copy the printed MCP URL and open GitHub Copilot Chat → *Agent* mode to try commands like “Save this snippet as **hello‑world**”.

### VS Code Dev Containers

Prerequisites: [Docker Desktop](https://www.docker.com/products/docker-desktop) + the [Dev Containers](https://aka.ms/vscode/dev-containers) extension.

1. Click the **Dev Containers** badge (second badge) or run *Remote‑Containers: Open Repository in Container* from VS Code.
2. Sign in and launch as shown for Codespaces:

   ```bash
   azd auth login
   azd up
   ```

### Local Environment

#### Prerequisites

* [azd](https://aka.ms/install-azd) CLI
* Python 3.11 + [`uv`](https://github.com/astral-sh/uv)
* Node 18+ (for Functions Core Tools)
* Azure Functions Core Tools v4 (`npm i -g azure-functions-core-tools@4 --unsafe-perm`)

#### Quickstart

```bash
# 1. Clone & init
azd init --template Azure-Samples/snippy

# 2. Sign in
azd auth login

# 3. Provision & deploy
azd up
```

The CLI prints the Function App URL, MCP endpoint and system key when finished. To remove all resources later:

```bash
azd down --purge
```

---

## Guidance

### Region Availability

Azure OpenAI model support varies by region. Verify availability [here](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#standard-deployment-model-availability) and choose the same region for all Azure resources. **eastus** and **swedencentral** are good default choices.

### Costs

Estimate monthly cost using the [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/). Major components:

* Azure Functions – Consumption / Flex tiers
* Cosmos DB – Serverless or provisioned throughput
* Azure OpenAI – pay‑as‑you‑go per 1K tokens
* Azure AI Agents – per‑execution billing (preview)

### Security

Snippy uses User-Assigned Managed Identity for secure service-to-service authentication. The infrastructure is configured with:

* **User-Assigned Managed Identity** on the Function App with appropriate RBAC roles:
  * Cosmos DB Data Contributor
  * Storage Blob Data Owner and Queue Data Contributor
  * Application Insights Monitoring Metrics Publisher
  * Azure AI Project Developer

For production deployments, we recommend:

* Restrict inbound traffic with Private Endpoints + VNet integration
* Enable network security features like service endpoints and firewall rules

---

## Resources

* Blog – *Build AI agent tools using Remote MCP with Azure Functions* ([https://aka.ms/snippy-blog](https://aka.ms/snippy-blog))
* Model Context Protocol spec – [https://aka.ms/mcp](https://aka.ms/mcp)
* Azure Functions Remote MCP docs – [https://aka.ms/azure-functions-mcp](https://aka.ms/azure-functions-mcp)
* Develop Python apps for Azure AI – [https://learn.microsoft.com/azure/developer/python/azure-ai-for-python-developers](https://learn.microsoft.com/azure/developer/python/azure-ai-for-python-developers)

---

## Contributing

Standard **fork → branch → PR** workflow. Use *Conventional Commits* (`feat:`, `fix:`) in commit messages.

---

## License

MIT © Microsoft Corporation


Build and deploy AI agents with MCP and Azure Functions
48 Minutes Remaining 
Build and deploy AI agents with MCP and Azure Functions
Log in to the Windows 11 lab VM with the credentials below.

Username: LabUser
Password: Pa$$w0rd
If you see a [T] icon next to a value, click in the VM where the text should appear and then select [T] to type‑paste it automatically.

Lab Scenario
In this lab, you'll build Snippy, an intelligent code snippet manager using Azure Functions. You'll implement features to save and retrieve snippets, storing them in Azure Cosmos DB. Semantic meaning will be captured using Azure OpenAI embeddings, which are easily generated within your application thanks to Azure Functions input bindings. You'll also integrate AI-powered features to automatically generate documentation ("Deep Wiki") and coding standards ("Code Style Guide") from your snippets using capabilities within Azure AI Foundry and AI Agents Service.

A key focus of this lab is making Snippy's capabilities accessible in two ways: through standard HTTP APIs and as Tools consumable by AI agents like GitHub Copilot. To enable this agent integration, you'll leverage the Model Context Protocol (MCP). MCP is an open standard that defines how external capabilities, like Snippy's functions, can be discovered and invoked by AI models. You'll use the new MCP Tool Trigger in Azure Functions, which provides a standardized interface based on MCP, allowing agents like GitHub Copilot to seamlessly interact with your Snippy tools without needing custom integration code for each agent.

snippy-architecture-diagram.png

1 Lab set‑up snapshot
Your lab environment comes pre-configured with the necessary software:

Visual Studio Code - Your primary development environment
Edge - Snippy & Portal - Browser with tabs for GitHub, Azure Portal, and Azure AI Foundry
Python 3.12
uv package manager (faster alternative to pip)
Git for source control
Azure CLI for managing Azure resources
Azure Functions Core Tools v4 for local function development
Azure Developer CLI (AZD) for simplified deployment workflows
Ignore any blue "Sign in required" notification stating…
"Your device is having problems with your work or school account" or an "Activate Windows" watermark at the bottom right of the screen. Simply click "Not now" on any prompts and continue with the lab. These notifications will not affect your lab experience.
0-sign-in.png

From your lab environment's Desktop (or Taskbar), open Edge - Snippy & Portal shortcut, which opens three tabs you'll need:

Snippy Lab GitHub Repo: Source code (https://aka.ms/functions-mcp-lab).
Azure Portal: (https://portal.azure.com).
Azure AI Foundry: (https://ai.azure.com).
2 Fork the repository
From Edge, in the Snippy tab, click Fork → Create fork (this will prompt you to sign in to your GitHub account).
Enter your GitHub username below to automatically populate the commands that follow: 
tarasul

Uncheck Copy the main branch only, as we will be working with the build-2025 branch. You may also need the durable-functions branch, which is part of the bonus section.

1-gh-uncheck-copy-main.png

After the fork completes, validate the HTTPS URL of your fork - for example: https://github.com/tarasul/snippy.git

3 Clone the repo and set up using the VS Code Terminal
Launch VS Code from the Taskbar or desktop shortcut.

Open a new Terminal inside VS Code (Terminal → New Terminal).

In the terminal, run the following commands to clone your fork into your home directory and check out the relevant branches (from Terminal, click on T icon next to the script below and press enter. Validate that tarasul is accurate below):

powershell
   # Clone your fork
   git clone https://github.com/tarasul/snippy.git
   cd snippy

   # Check out the main lab branch
   git checkout build-2025

   # (Optional) Also fetch the bonus branch
   git fetch origin durable-functions

   # Open the project in VSCode
   code . --reuse-window
If successful, VS Code will open snippy folder with the build-2025 branch checked out, all within the same window.
4. Sign in to GitHub Copilot
In VS Code, sign in to GitHub Copilot by either:

Clicking the Copilot status icon in the bottom-right corner of the VS Code window.
or, pressing Ctrl+Shift+P to open the Command Palette, then typing and selecting "GitHub Copilot: Sign in".
A browser window will open, prompting you to authorize your GitHub account. If you're already signed in, select the appropriate account. If not, sign in with the GitHub account associated with your Copilot subscription.

After authorization, click Continue to grant access. The browser will then prompt you to open Visual Studio. Confirm to complete the sign-in process.

(Optional) Open the Azure side bar in VS Code and sign in using the credentials below - this allows you to examine the deployed resources later:

Username: User1-51448899@LODSPRODMCA.onmicrosoft.com
Password: Gp1Hh7$#Bk
5 Start resource provisioning with azd (runs in background)
Now, you'll use the Azure Developer CLI (azd) to start provisioning all the necessary Azure resources for Snippy. This process takes several minutes (5-10) and will run in the background while you work on the coding exercises.

Login to azd: Open your terminal inside VS Code (Terminal → New Terminal) and log in to Azure (ensure you are in the snippy root directory):

azd auth login

Username: User1-51448899@LODSPRODMCA.onmicrosoft.com
Password: Gp1Hh7$#Bk
Follow the prompts to authenticate in your browser. Close the browser tab after successful login and return to VS Code.

If successful, in VSCode Terminal, you should see Logged in to Azure as User1-51448899@LODSPRODMCA.onmicrosoft.com

Set up the azd Environment:

An azd environment stores configuration details like subscription ID and location. Create a new environment for this lab (this will be unique to each lab environment):

azd env new snippy-mcp-lab-51448899 --subscription 98a4940c-ee8e-4340-9818-61cfa0c5ebec --location eastus2

Provision and Deploy Resources:

Run the following command. This reads the infrastructure definition (infra folder, main.bicep) and begins creating the resources in Azure. Do not wait for it to complete. It will run in the background. Proceed immediately to the next step (Coding Exercises) while it runs. Run

azd provision

to provision all the Azure resources needed to run snippy.

This command provisions Azure resources such as:

Azure Function App (using the scalable Flex Consumption plan)
Azure Storage Account (for Functions operations and state)
Azure Cosmos DB for NoSQL (pre-configured for vector search)
Azure AI Services (with gpt-4o and text-embedding-3-small models deployed)
Azure AI Foundry resources (Project, Hub for AI Agents Service)
Azure Key Vault (for secrets)
Azure Log Analytics & Application Insights (for monitoring)
These resources are created within a new resource group named rg-snippy-mcp-lab-51448899. You will deploy your application code to the Function App later using azd deploy.

Proceed to coding exercises while this provisioning takes place

Ensure you can easily access the terminal running azd provision to monitor its progress and verify when it finishes.

5b (Optional fallback) Manual resource provisioning (use only if azd provision fails)
6 Coding Exercises: Implement MCP Tools and AI Agents with Azure Functions
As azd provision sets up your cloud resources in the background, you'll dive into the heart of this lab: leveraging Azure Functions to build AI-powered tools and orchestrate AI agents.

What's the Goal?
This lab focuses on harnessing Azure Functions, a powerful serverless compute platform, to build versatile APIs and microservices that integrate deeply with AI. You'll explore two key patterns:

Azure Functions as MCP Tools for AI Agents: Learn how to transform your Azure Functions into discoverable tools for AI agents like GitHub Copilot. By using the MCP trigger, your function becomes directly usable by any MCP-compatible client, eliminating the need for custom integration code.

Azure Functions Creating AI Agents and Serving as Their Tools (Bi-directional Integration): Discover a powerful, bi-directional relationship. Azure Functions can not only be tools for AI agents but also create and invoke these agents. For instance, Assistant API Triggers for Azure Functions enables interaction with the Azure AI Agents Service. Simultaneously, these AI Agents can utilize your Azure Functions as tools, creating a dynamic and reciprocal integration.

In this lab, you'll put both approaches into practice:

For Approach 1 (Functions as MCP Tools): You'll enhance the save_snippet function by adding an MCP Tool Trigger, making it directly callable by GitHub Copilot and other MCP-aware AI agents.
For Approach 2 (Functions Orchestrating Agents): You'll examine the deep_wiki function to understand how it creates an AI agent that, in turn, utilizes tools (which can include other Azure Functions) to analyze code snippets and generate documentation.
Key Azure Technologies You'll Use:

Azure Functions: The serverless compute backbone for your AI tools and agent interactions.
Azure Functions Extensions:
MCP Tool Trigger (@app.generic_trigger): Exposes your function as a discoverable MCP tool.
Embeddings Input Binding (@app.embeddings_input): Seamlessly generates vector embeddings from input data.
Azure Cosmos DB with Vector Search: Stores code snippets and their embeddings, enabling efficient semantic retrieval.
Azure AI Agents Service: Facilitates the creation and management of specialized AI agents that your functions can invoke.
Target File: Navigate to and open src/function_app.py in the VS Code editor.
6.1 Exercise 1: Define Tool Properties for save_snippet
Concept: For an Azure Function to be usable as an MCP tool, you need to define the schema of inputs it expects. This is similar to defining parameters for a REST API endpoint, but specifically formatted for AI assistant consumption.

Locate the TODO: Lab Exercise 1 comment (in src/function_app.py). Replace the empty list assignment for tool_properties_save_snippets with the following code to define the inputs your function expects:

python
# TODO: Lab Exercise 1 - Define the tool properties for save_snippet
# Create a list of ToolProperty objects that define the expected inputs (schema)
# Each property has a name, type, and description for the AI agent
tool_properties_save_snippets = [
    ToolProperty(_SNIPPET_NAME_PROPERTY_NAME, "string", "The unique name for the code snippet."),
    ToolProperty(_PROJECT_ID_PROPERTY_NAME, "string", "The ID of the project the snippet belongs to. Optional, defaults to 'default-project' if not provided."),
    ToolProperty(_SNIPPET_PROPERTY_NAME, "string", "The actual code content of the snippet."),
]
This property schema provides natural language descriptions that are passed to the underlying LLMs powering AI agents like GitHub Copilot. These descriptions help the LLM understand what each parameter is for and how to map user requests in natural language to the specific variables required by your function.

6.2 Exercise 2: Making Your Azure Function Available as an MCP Tool
Concept: The MCP Tool Trigger is a special Azure Functions binding that transforms your function into a tool that any MCP-compatible AI assistant can discover and use. This binding registers your function with the MCP server endpoint provided by the Functions host.

Re-open src/function_app.py and Locate the TODO: Lab Exercise 2 comment. Above the mcp_save_snippet function (In VSCode, use Outline pane to explore or Ctrl + Shift + o), add the following decorator:

python
# TODO: Lab Exercise 2 - Add the MCP tool trigger binding
# Add the @app.generic_trigger decorator to register this function as an MCP tool
@app.generic_trigger(
    arg_name="context", # Variable name for the incoming MCP data
    type="mcpToolTrigger", # Specify the trigger type
    toolName="save_snippet", # How the agent refers to this tool
    description="Save a code snippet with name and project ID.", # Description for the agent
    toolProperties=tool_properties_save_snippets_json, # The input schema (from Ex 1)
)
With this decorator, your Azure Function now listens for calls via the MCP SSE endpoint. When deployed, this creates a fully managed, secure MCP server without any additional infrastructure or code.

6.3 Exercise 3: Enhancing Your Function with Azure Functions Bindings
Concept: Azure Functions provide powerful input and output bindings that can pre-process data before your function runs or post-process results. The Embeddings Input Binding automatically connects to Azure OpenAI to generate vector embeddings from text inputs.

Locate the TODO: Lab Exercise 3 comment. Directly below the MCP trigger decorator (and above the function signature), add the following decorator:

python
# TODO: Lab Exercise 3 - Add the embeddings input binding
# Add the @app.embeddings_input decorator to enable automatic embedding generation
@app.embeddings_input(
    arg_name="embeddings", 
    input="{arguments." + _SNIPPET_PROPERTY_NAME + "}", 
    input_type="rawText", 
    embeddings_model="%EMBEDDING_MODEL_DEPLOYMENT_NAME%"
)
This binding automatically calls Azure OpenAI before your function code runs, providing the generated embedding as a parameter. This dramatically simplifies your function code by handling the embedding generation outside your main logic.

6.4 Exercise 4: Understanding Azure Functions That Call AI Agents (Review)
Concept: Azure Functions can create and invoke AI agents, using them to handle complex tasks. Let's examine how the deep_wiki.py module does this:

Open src/agents/deep_wiki.py and review how this Azure Function module creates and manages agents:

Key parts to understand:

Connecting to the AI Project from your Azure Function:
python
async with AIProjectClient.from_connection_string(
    credential=credential,
    conn_str=os.environ["PROJECT_CONNECTION_STRING"]
) as project_client:
Registering a tool for the agent to use:
python
functions = AsyncFunctionTool(functions=[vector_search.vector_search])
Creating an agent from your Azure Function:
python
agent = await project_client.agents.create_agent(
    name="DeepWikiAgent",
    description="An agent that generates comprehensive wiki documentation",
    instructions=_DEEP_WIKI_SYSTEM_PROMPT,
    tools=functions.definitions,
    model=os.environ["AGENTS_MODEL_DEPLOYMENT_NAME"]
)
Managing agent execution from your Azure Function:
python
run = await project_client.agents.create_run(...)

while True:
    # Check run status
    if run.status == "requires_action":
        # Handle tool calls from the agent
        tool_calls = run.required_action.submit_tool_outputs.tool_calls
        for tool_call in tool_calls:
            output = await functions.execute(tool_call)
            # Collect output for submission back to agent
This pattern lets your Azure Function create an agent on demand, provide it with tools, manage its execution, and return the final result - all within a single serverless function invocation.

Azure Functions Unlock Powerful AI Integration Patterns Azure Functions are the ideal platform for building AI-powered tools because they provide:

Serverless Compute: Scale automatically to handle varying loads
First-class Support for MCP: Built-in triggers make functions available as tools
Rich Bindings Ecosystem: Input/output bindings simplify integration with Azure services
Language Flexibility: Support for Python, .NET, JavaScript and more
Remote Accessibility: Functions can be deployed once and shared across multiple agents
For example, here's how an Azure Function can become a tool for an Agent built using AI Agents Service:

python
# Inside your Azure Function
async def create_analyzer_agent(vector_search_function_url):
    async with AIProjectClient.from_connection_string(...) as client:
        # Create an agent with your Azure Function as a tool
        agent = await client.agents.create_agent(
            name="DeepWikiAgent",
            tools=[{
                "type": "azure_function",
                # 🔑 KEY POINT: This references a remote Azure Function that can be:
                # - Deployed once and used by many agents
                # - Managed independently from agent definitions
                # - Scaled according to demand across all agent users
                # - Secured with proper authentication
                "azure_function": {
                    "function": {
                        "name": "VectorSearchFunction",
                        "description": "Performs semantic vector search on code snippets",
                        "parameters": {
                            "type": "object",
                            "properties": {
                                "searchQuery": {"type": "string", "description": "The search term"},
                                "projectId": {"type": "string"}
                            }
                        }
                    },
                    "url": vector_search_function_url,
                    "api_key": os.environ["FUNCTION_KEY"]
                }
            }]
        )
    return agent
6.5 Exercise 5: Understanding Azure Functions' AI Integration Approaches
Concept: Let's compare the two approaches for integrating Azure Functions with AI:

Comparison point	Azure Functions as MCP Tools	Functions that Create and Invoke Agents
Key Azure Technology	Azure Functions with MCP Tool Trigger	Azure Functions that call Azure AI Agents service
Implementation technique	@app.generic_trigger(type="mcpToolTrigger")	Azure Functions with Azure AI Projects Client
How the function is called	Directly by AI assistants via MCP protocol	Via standard HTTP or other triggers
Who invokes your function	End users via GitHub Copilot or other agents or MCP clients	Any event your application handles or raises
Best for	Authoring remote tools for AI assistants	Building specialized AI Agents with custom logic for specific tasks
By implementing both approaches with Azure Functions, you create a powerful serverless AI architecture where:

Your Azure Functions can be directly invoked by AI assistants
Your Azure Functions can create and manage specialized agents on demand
Both approaches leverage Azure Functions as the serverless compute foundation, with different extensions and integration patterns depending on your specific AI scenario.

Durable Functions is an extension of Azure Functions that simplifies writing stateful orchestrations. Although it isn't included in the lab, you can also use Durable Functions to manage agent behavior, to coordinate multi-agent scenarios, or to create tools that follow defined business processes.

Understanding the Rest of the Implementation
The rest of the implementation demonstrates how Azure Functions power two advanced AI patterns: Retrieval-augmented Generation (RAG) with vector search and AI Agent orchestration. Take time to explore these key components:

Explore RAG Implementation with Cosmos DB Vector Search

The save_snippet and get_snippet functions implement a complete RAG pattern using Azure Cosmos DB DiskANN vector search:

Vector Embedding Generation: When a snippet is saved, the embeddings input binding automatically transforms the code into a vector embedding (a numerical representation capturing semantic meaning)

Cosmos DB Vector Storage: Examine data/cosmos_ops.py to see how these vectors are stored in Cosmos DB with DiskANN indexing:

python
# Container configuration with vector search capability
_container = await database.create_container_if_not_exists(
 id=COSMOS_CONTAINER_NAME,
 partition_key=PartitionKey(path="/name"),
 indexing_policy={
 # ...
 "vectorEmbeddingPolicy": {
  "vectorEmbeddings": [
   {
    "path": "/embedding",
    "dataType": "int8",
    "dimensions": 1536,
    "distanceFunction": "cosine"
   }
  ]
 }
 }
)
Vector Similarity Search: Look at the query_similar_snippets function in cosmos_ops.py to see how vector search is performed: python sql = ( "SELECT TOP @k c.id, c.code, " "VectorDistance(c.embedding, @vec) AS score " "FROM c WHERE c.projectId = @pid " "ORDER BY VectorDistance(c.embedding, @vec)" )

This demonstrates a complete RAG pattern directly within Azure Functions: content is transformed into vectors, stored in a vector database, and retrieved by semantic similarity.

Explore Azure AI Agents Integration

The deep_wiki and code_style functions showcase how Azure Functions can create and orchestrate AI Agents:

AI Agent Lifecycle Management: Review the agent creation, execution, and tool call handling code in agents/deep_wiki.py:

python
 # Agent creation with tools
 agent = await project_client.agents.create_agent(
  name="DeepWikiAgent",
  instructions=_DEEP_WIKI_SYSTEM_PROMPT,
  tools=functions.definitions,
  model=os.environ["AGENTS_MODEL_DEPLOYMENT_NAME"]
 )

 # Agent execution and tool call handling
 run = await project_client.agents.create_run(...)
 while True:
  # Monitor execution
  if run.status == "requires_action":
   # Handle tool calls
   tool_calls = run.required_action.submit_tool_outputs.tool_calls
   for tool_call in tool_calls:
    output = await functions.execute(tool_call)
    # ...
Coming Soon: Azure Functions Assistant Trigger for Agents: Microsoft is evolving the current Azure OpenAI assistant trigger to support Azure AI Agents Service. Similar to the existing assistant trigger shown below:

python
 @skills.function_name("AddTodo")
 @skills.assistant_skill_trigger(arg_name="taskDescription", function_description="Create a new todo task")
 def add_todo(taskDescription: str) ->      None:
  # Function implementation
  # ...
This upcoming trigger will streamline the integration between Azure Functions and Azure AI Agents Service, enabling direct agent invocation through function bindings.

Bi-directional Integration Flow: The complete integration pattern enables a fully managed orchestration where:

Your HTTP-triggered Azure Function creates and runs an agent
The agent calls another Azure Function tool to access specific capabilities
Results flow back through the agent to your original function
A comprehensive response is returned to the user
This creates a powerful architecture where Azure Functions can both create agents and be called by agents, all while maintaining serverless scalability and simplified management.

By combining these patterns, you can build sophisticated serverless AI Agents that leverage both semantic search capabilities and agent-based reasoning, all within the familiar Azure Functions framework.

Next Steps
In the remaining sections, you'll:

Run the functions locally to test your implementation
Deploy to Azure and test with GitHub Copilot
Connect to your deployed cloud endpoint
7 Install Python Dependencies
Let's first install the Python packages required by the Function App:

Ensure your VS Code terminal is in the snippy/src directory:
powershell
   cd $HOME\snippy\src # Or C:\Users\LabUser\snippy\src
Create a Python virtual environment using uv (this is faster than venv):

uv venv .venv

Activate the virtual environment:

.venv\Scripts\activate

(Your terminal prompt should now be prefixed with (.venv))

Install the required Python packages:

uv pip install -r requirements.txt

We'll start the Functions host after verifying that all required settings are available from the provisioning process.

8 Verify Local Settings After Provisioning
Check the terminal window where you ran azd provision in Step 5. Wait for it to complete successfully.

Once provisioning completes, the postProvision hook defined in azure.yaml automatically runs either ./scripts/generate-settings.ps1 (on Windows) or ./scripts/generate-settings.sh (on Linux/macOS). These scripts generate the necessary settings in your src/local.settings.json file.

Open your src/local.settings.json file in VS Code and verify it now contains populated values.

Your src/local.settings.json file should now look similar to this (with your specific resource details, and additional keys):

json
   {
     "IsEncrypted": false,
     "Values": {
       "AzureWebJobsSecretStorageType": "files",
       "FUNCTIONS_WORKER_RUNTIME": "python",
       "EMBEDDING_MODEL_DEPLOYMENT_NAME": "text-embedding-3-small",
       "CHAT_MODEL_DEPLOYMENT_NAME": "gpt-4o",
       "COSMOS_ENDPOINT": "https://cosmos-...",
       "AZURE_OPENAI_ENDPOINT": "https://...",
       "AZURE_OPENAI_KEY": "...",
       "PROJECT_CONNECTION_STRING": "..."
     }
   }
(Optional) If for any reason the local.settings.json file wasn't properly generated or is missing values, you can manually run the generation script:
powershell
   # From the snippy root directory
   .\scripts\generate-settings.ps1
Save the file if you made any changes.
9 Run Functions Locally and Test with Cloud Resources
Now that local.settings.json points to your actual Azure resources provisioned by azd, we can start the Function App and perform end-to-end testing.

Start the Azure Functions runtime locally from the same venv activated Terminal in Step 7:

func start

Look for output indicating the Functions host has started successfully. You should see your functions listed, including the HTTP endpoints and the MCP tool trigger functions, as shown below:

text
   Functions:
     http_code_style: [POST] http://localhost:7071/api/snippets/code-style
     http_deep_wiki: [POST] http://localhost:7071/api/snippets/wiki
     http_get_snippet: [GET] http://localhost:7071/api/snippets/{name}
     http_save_snippet: [POST] http://localhost:7071/api/snippets
     mcp_code_style: mcpToolTrigger
     mcp_deep_wiki: mcpToolTrigger
     mcp_get_snippet: mcpToolTrigger
     mcp_save_snippet: mcpToolTrigger
The Functions runtime is using file-based secret storage via the "AzureWebJobsSecretStorageType": "files" setting, eliminating the need for Azurite or other storage emulators.

(Optional) 9.1 Test with REST Client (End-to-End Smoke test)

Let's test your implementation using the built-in REST Client:

In VS Code Explorer, open src/tests/test.http. This file contains pre-defined HTTP requests for testing different Snippy endpoints.

Find the request block labeled ### Save a new snippet with projectId. Click the small "Send Request" link directly above the POST line.

10-a-send-req.png

Check the response panel that appears on the right. You should see a Status 200 OK response with JSON data showing the saved snippet details, confirming that:

Your function successfully processed the request
Azure OpenAI generated embeddings for the code
The snippet was stored in Cosmos DB with its vector embedding
Now test retrieval: Find the ### Get a snippet by name request block. Modify the snippet name in the URL (/api/snippets/{name}) to match the one you just saved (the default is "test-snippet"). Send this request and verify it returns the correct code snippet data.

Test a few more operations to ensure everything works:

Save a more complex snippet using the ### Save a complex snippet request
Retrieve it using the corresponding GET request
Try the AI agent functions by running the ### Generate wiki documentation or ### Generate code style guide requests
For the agent-based functions (wiki and code style guide), note that these may take longer to execute (10-30 seconds) as they involve creating an AI agent that analyzes your saved snippets.

These successful tests confirm that your entire pipeline is working: HTTP endpoints, embedding generation, Cosmos DB vector storage, and AI agent integration.



9.2 Explore MCP Tools in GitHub Copilot (Local Endpoint)
Now, connect GitHub Copilot Chat to your locally running Function App's MCP endpoint to test the tools you implemented.

Verify MCP Server Configuration:

In VS Code, open the .vscode/mcp.json file

You should see the local-snippy server already configured

If you see a "Running" status with tools count (e.g., "✓ Running | Stop | Restart | 4 tools"), the server is already connected

If not connected, click the Start button in the file (appears at the top of the JSON configuration)

11a-mcp-local-start.png

View MCP Server Logs (optional but helpful):

Press Ctrl+Shift+P to open the Command Palette

Type MCP: List Servers and select it

Select the local-snippy server from the dropdown

Click Show Output to see the server logs

Tip: Click the settings icon next to "MCP: local-snippy" in the output panel to change log level to "trace" for more detailed logs

11b-mcp-local-trace.png

Open GitHub Copilot Chat:

Press Ctrl+Alt+I or click the Copilot Chat icon in the Activity Bar
Configure Copilot Chat for Tools:

Ensure Agent mode is active (select from the dropdown next to the model selector, or Ctrl+.)

12a-mcp-ghcp-agent.png

At the bottom of the chat panel, click Select Tools… (or press Ctrl+Shift+/)

12b-mcp-ghcp-tools.png

Make sure MCP Server: local-snippy and all its tools are checked

Hit Escape, or Click OK to confirm your selection

Test the save_snippet Tool:

Open any code file (e.g., src/agents/code_style.py)

Select some interesting code sections (or it'll take the entire file as a snippet, which is okay)

In Copilot Chat, type: save this snippet as ai-agents-service-usage and click enter or hit Send

Copilot will suggest using the save_snippet tool - click Continue

13-mcp-ghcp-save.png

Test the get_snippet Tool:

In Copilot Chat, type: get the snippet named ai-agents-service-usage and click enter or hit Send

Copilot will suggest using the get_snippet tool - click Continue

14-mcp-ghcp-get.png

Experiment with Advanced AI Agent Tools:

Try these prompts (no need to select code first):

generate a deep wiki for all snippets and place in a new file deep-wiki.md in project root (uses the deep_wiki tool)
create a code style guide based on the saved snippets and place in a new file code-style.md in project root (uses the code_style tool)
These agent-based tools may take 15-30 seconds to run as they orchestrate (via managed AI Agents service) with configured tools and have self-reflection

Once they are done, open the files and Preview the generated Markdown (Ctrl+K V)

15-deep-wiki.png

Check Function Logs:

In the terminal where func start is running, you'll see logs for each tool invocation
This confirms your MCP tools are working end-to-end
The ability to interact with your tools through natural language in Copilot demonstrates the power of the MCP protocol - AI assistants can seamlessly discover and use your Azure Functions without any custom integration code.

10 Deploy the Application Code to Azure
You've implemented the code and verified it works locally against your provisioned cloud resources. Now, deploy the application code to the Azure Function App created by azd provision.

Go back to the Terminal and stop the local Functions host if it's still running (Ctrl+C in the func start terminal - wait a few seconds).

Ensure your terminal is in the root snippy directory (the one containing azure.yaml).

powershell
cd $HOME\snippy # Or C:\Users\LabUser\snippy
Run the AZD deploy command:

azd deploy

This command performs the following:

Reads the azure.yaml file to determine which service code to deploy (configured to deploy the src directory to the Function App).
Packages your application code.
Deploys the code to the Azure Function App provisioned earlier.
Configures the application settings in the deployed Function App based on your .azure/snippy-mcp-lab-51448899/.env file, ensuring it can connect to OpenAI, Cosmos DB, etc., in the cloud.
Wait for the deployment to complete successfully. AZD will output the endpoints for your deployed application, including the Function App's base URL (e.g., https://func-api-…azurewebsites.net). Make a note of this URL.

11 Connect VS Code / Copilot to the Cloud MCP Endpoint
Finally, configure VS Code and Copilot Chat to use the tools running on your deployed Azure Function App.

Authenticate with Azure using Device Code:

Before you can retrieve the system key, you must authenticate using the Azure CLI (az):

powershell
az login --use-device-code
This command will output a URL and a device code. Copy the code, open the URL in your browser, and paste the code when prompted to complete authentication.

Get the MCP System Key:

The cloud MCP SSE endpoint (/runtime/webhooks/mcp/sse) is protected by a system key. You'll need this key to authenticate calls. Once logged in, run the following commands in your VS Code terminal to retrieve the system key:

powershell
$appName = azd env get-value AZURE_FUNCTION_NAME
az functionapp keys list -n $appName --query "systemKeys.mcp_extension" -g rg-snippy-mcp-lab-51448899 --output tsv
The output will be a long string - copy this value. This is your required for calling the MCP SSE endpoint.

Add Cloud Endpoint to .vscode/mcp.json: After retrieving your Function App name and MCP system key, you can now configure the cloud endpoint in .vscode/mcp.json.

Click Start above to remote-snippy in mcp.json - this will prompt you to enter:

The Function App name
The MCP system key
You don't need to manually replace any values in the JSON - just copy the following values from your PowerShell session:

powershell
  # Print the Function App name
  $appName
Use the $appName value for the functionapp-name input, and paste the system key you retrieved earlier using:

powershell
 az functionapp keys list -n $appName --query "systemKeys.mcp_extension" -g rg-snippy-mcp-lab-51448899 --output tsv
This setup allows the cloud-hosted MCP to receive and stream events securily.

16-remote-snippy.png

Switch Copilot to Cloud Server:

Open Copilot Chat (Ctrl + Alt + I).
Ensure Agent mode is selected.
Verify the tools are loaded correctly.
Test Cloud hosted Tools: Repeat the tests from step 9.2, but this time with the remote-snippy server:

Select some code.
Ask Copilot: save the selected snippet as my-cloud-mcp-test
Confirm the tool usage.
Ask Copilot: get the snippet named my-cloud-mcp-test
Try other tools like deep_wiki as we did in section 9.2.
Verify that the tools work correctly by interacting with your deployed Azure Function App.

12 Clean‑up
Important clean-up information

This lab environment will be automatically deleted after completion, and all profiles will be signed out. The clean-up steps below help ensure no resources continue to run unnecessarily and teaches you how to do this when you use Azure in your company or personal projects. Even if you're unable to complete all clean-up steps, the lab environment itself will be terminated.

When you are finished with the lab, delete the Azure resources created by azd provision.

Open a terminal in VS Code.

Ensure you are in the snippy project root directory.

powershell
  cd $HOME\snippy # Or C:\Users\LabUser\snippy
Run the following AZD command:

azd down --purge --force

--purge attempts to permanently delete resources like Key Vaults that support soft-delete.
--force skips the confirmation prompt.
AZD will list the resources being deleted and remove them from Azure. This will also delete the local AZD environment configuration (.azure folder).

This will take a while, so proceed with the next steps

Sign out of all accounts:

Sign out of GitHub in VS Code:

Click on your profile picture in the bottom-left corner of VS Code
Select "Sign Out" from the menu
Sign out of GitHub Copilot:

Open Command Palette (Ctrl+Shift+P)
Type and select "GitHub Copilot: Sign Out"
Sign out of all accounts in Microsoft Edge:

Go to https://github.com and Sign out of your GitHub account
Conclusion
Congratulations on completing the Functions MCP lab! You've successfully built a powerful, AI-enhanced code snippet manager (Snippy!) that demonstrates cutting-edge integration patterns between serverless compute and AI services. Let's recap what you've learned and accomplished:

Key Technologies and Concepts Mastered
1. Azure Functions as an AI Integration Platform
You've built a complete serverless application using Azure Functions that serves both as a traditional API and as tools for AI Agents. This demonstrates how Azure Functions provides an ideal foundation for AI-powered applications, offering serverless scalability with minimal infrastructure overhead.

2. Model Context Protocol (MCP) Integration
You've implemented and deployed MCP tools that enable AI Agents like GitHub Copilot to discover and use your Azure Functions via a standardized protocol. This powerful capability allows any MCP-compatible AI assistant to leverage your tools without custom integration code.

3. Vector Embeddings and Semantic Search
You've explored a complete Retrieval-Augmented Generation (RAG) pattern by:

Using Azure OpenAI's embedding models to convert code into vector representations
Leveraging Azure Functions' embeddings input binding to simplify this process
Storing these vectors in Cosmos DB's DiskANN vector index
Implementing semantic search functionality to find similar code snippets
4. AI Agent Orchestration
You've learned how Azure Functions can create and manage specialized AI agents by:

Creating agents on demand with specific tools and instructions
Managing agent execution, including handling tool calls
Processing the final output from agents (documentation, style guides)
5. Dual-Interface Architecture
You've explored a system that exposes identical functionality through two parallel interfaces:

Traditional HTTP endpoints for standard API access
MCP tools for AI assistant integration
6. DevOps with Azure Developer CLI
You've used Azure Developer CLI (azd) to automate the provisioning and deployment of all required resources:

Azure Function App with Flex Consumption Plan
Azure Cosmos DB with vector search capability
Azure OpenAI with embeddings and chat models deployed
Azure AI Foundry resources for agent execution
Technical Implementation Highlights
MCP Tool Registration: You implemented the @app.generic_trigger decorator with appropriate tool properties to register Azure Functions as MCP tools.

Automated Vector Embedding: You leveraged the @app.embeddings_input binding to automatically generate embeddings before your function executes.

Vector Database Configuration: You explored how Cosmos DB is configured with DiskANN vector search to enable semantic retrieval.

AI Agent Creation: You examined how agents are created, provided with tools, and managed throughout their execution.

End-to-End Testing: You tested your tools through multiple interfaces:

HTTP requests using REST Client
Local MCP endpoint via GitHub Copilot
Deployed cloud endpoint via GitHub Copilot
Practical Applications and Next Steps
The patterns and technologies you've learned in this lab have broad applications:

Enhanced Developer Experiences: Create custom tools that extend AI coding assistants with domain-specific knowledge and capabilities
Knowledge Management: Build systems that capture, index, and retrieve organizational knowledge with semantic understanding
AI-Powered Automation: Create AI agents that can execute complex workflows using serverless functions as tools
Hybrid AI Systems: Combine the reasoning capabilities of LLMs with the specialized functions of traditional applications
Next Steps
Here are some specific ideas for further exploration:

Explore Durable Functions: Switch to the durable‑functions branch (git checkout durable-functions) in your cloned repository to explore an example using Azure Durable Functions for orchestrating more complex, stateful agent tasks.

Create Custom MCP Tools: Build your own custom MCP tools by adding new Python functions decorated with @app.generic_trigger(type="mcpToolTrigger", …). Each new function automatically becomes a tool available to connected agents after deployment.

Experiment with Different Models: Try different Azure OpenAI models or tweak the vector search parameters in the Cosmos DB configuration (infra/main.bicep, then azd provision again).

Expand MCP Integration: Explore integrating your Snippy tools with other MCP Hosts beyond VS Code Copilot Chat, such as MCP Inspector and others.

Production Enhancements: Add authentication, rate limiting, and monitoring to your MCP endpoints for production-grade deployments.

You now have the skills to build sophisticated, serverless AI-powered applications that leverage the best of both traditional cloud computing and modern AI capabilities.

Happy coding at Microsoft Build 2025!




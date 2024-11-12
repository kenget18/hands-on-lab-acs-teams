---
published: true
type: workshop
title: Use Azure Communication Services to build a chat application with Teams
short_title:
description: Discover how to integrate Azure Communication Services with Microsoft Teams to build a chat application.
level: beginner # Required. Can be 'beginner', 'intermediate' or 'advanced'
navigation_numbering: false
authors: # Required. You can add as many authors as needed
  - Lucas Peirone
  - Damien Aicheh
contacts: # Required. Must match the number of authors
  - "@lucas.peirone"
  - "@damienaicheh"
duration_minutes: 60
tags: azure, azure communication services, microsoft teams, javascript, codespace, devcontainer, csu
navigation_levels: 3
---

# Product Hands-on Lab - Azure Communication Services with Microsoft Teams

Welcome to this Azure Communication Services Workshop. You will learn how to integrate Azure Communication Services with Microsoft Teams to build a chat application. Don't worry, even if the challenges will increase in difficulty, this is a step by step lab, you will be guided through the whole process.

During this workshop you will have the instructions to complete each steps. It is recommended to search for the answers in provided resources and links before looking at the solutions placed under the 'Toggle solution' panel.

<div class="task" data-title="Task">

> You will find the instructions and expected configurations for each Lab step in these yellow **TASK** boxes.
>
> Inputs and parameters to select will be defined, all the rest can remain as default as it has no impact on the scenario.
>
> Log into your Azure subscription locally using Azure CLI and on the [Azure Portal][az-portal] using your own credentials.
>
> Instructions and solutions will be given for the Azure CLI, but you can also use the Azure Portal if you prefer.

</div>

## Naming conventions

Before starting to deploy any resource in Azure, it's important to follow a naming convention to ensure resource name uniqueness and ease their identification. Based on the official [documentation][az-naming-convention] you need to define a few things:

- The application name
- The environment
- The region
- The instance number

You will also add an owner property, so for the purpose of this lab the values will be:

- The service prefix: `acs` (for Azure Communication Services)
- The environment: `dev`
- The region: `we` (for West Europe)
- The application name: `hol` (for Hands On Lab)
- The owner: `ms`
- The instance: `01`

You will use this convention for the rest of the scenario:

```xml
<!--If the resource prefix has a dash: -->
<service-prefix>-<environment>-<region>-<application-name>-<owner>-<instance>
<!--If the resource does not autorize any special caracters: -->
<service-prefix><environment><region><application-name><owner><instance>
```

<div class="info" data-title="Note">

> Be sure to use **your own values** to have unique names or use your own convention.
> [Official resource abbreviations][az-abrevation]
>
> Some services like Azure Storage Account or Azure KeyVault have a maximum size of 24 characters, so please consider using relevant abbreviations as small as possible.

</div>

# Prerequisites

## Dev Environment Setup

To retrieve the lab content :

- A Github account (Free, Team or Enterprise)
- Create a [fork][Repo-fork] of the repository from the **main** branch to help you keep track of your changes

3 development options are available:

- _Preferred method_ : Pre-configured GitHub Codespace
- Local Devcontainer
- Local Dev Environment with all the prerequisites detailed below

<div class="tip" data-title="Tips">

> To focus on the main purpose of the lab, we encourage the usage of devcontainers/codespace as they abstract the dev environment configuration, and avoid potential local dependencies conflict.
>
> You could decide to run everything without relying on a devcontainer : To do so, make sure you install all the prerequisites detailed below.

</div>

### Pre-configured GitHub Codespace

To use a Github Codespace, you will need :

- [A GitHub Account][github-account]

Github Codespace offers the ability to run a complete dev environment (Visual Studio Code, Extensions, Tools, Secure port forwarding etc.) on a dedicated virtual machine.
The configuration for the environment is defined in the `.devcontainer` folder, making sure everyone gets to develop and practice on identical environments : No more conflict on dependencies or missing tools !

Every Github account (even the free ones) grants access to 120 vcpu hours per month, _**for free**_. A 2 vcpu dedicated environment is enough for the purpose of the lab, meaning you could run such environment for 60 hours a month at no cost!

To get your codespace ready for the labs, here are a few steps to execute :

- After you forked the repo, click on `<> Code`, `Codespaces` tab and then click on the `+` button:

![codespace-new](./assets/codespace-new.png)

- You can also provision a beefier configuration by defining creation options and select the **Machine Type** you like :

![codespace-configure](./assets/codespace-configure.png)

### Using a local Devcontainer

This repo comes with a Devcontainer configuration that will let you open a fully configured dev environment from your local Visual Studio Code, while still being completely isolated from the rest of your local machine configuration : No more dependancy conflict.
Here are the required tools to do so :

- [Git client][git-client]
- [Docker Desktop][docker-desktop] running
- [Visual Studio Code][vs-code] installed

Open the local repository you just cloned or forked in Visual Studio Code. Make sure Docker Desktop is up and running and open the cloned repository in Visual Studio Code.

You will be prompted to open the project in a Dev Container. Click on `Reopen in Container`.

If you are not prompted by Visual Studio Code, you can open the command palette (`Ctrl + Shift + P`) and search for `Reopen in Container` and select it:

![devcontainer-reopen](./assets/devcontainer-reopen.png)

### Using your own local environment

The following tools and access will be necessary to run the lab in good conditions on a local environment :

- [Git client][git-client]
- [Visual Studio Code][vs-code] installed (you will use Dev Containers)
- [Azure CLI][az-cli-install] installed on your machine
- [Node 22][download-node] with Npm

Once you have set up your local environment, you can clone the Hands-on-lab repository you just forked on your machine, and open the local folder in Visual Studio Code and head to the next step.

## Sign in to Azure

Before starting this lab, be sure to set your Azure environment :

- An Azure Subscription with the **Contributor** role to create and manage the labs' resources
- A dedicated resource group for this lab to ease the cleanup at the end.
- Register the Azure providers on your Azure Subscription if not done yet: `Microsoft.Communication`.

<div class="task" data-title="Task">

> - Log into your Azure subscription in your environment using Azure CLI and on the [Azure Portal][az-portal] using your credentials.
> - Instructions and solutions will be given for the Azure CLI, but you can also use the Azure Portal if you prefer.
> - Register the Azure providers on your Azure Subscription if not done yet: `Microsoft.Communication`

</div>

<details>

<summary>Toggle solution</summary>

```bash
# Login to Azure :
# --tenant : Optional | In case your Azure account has access to multiple tenants

# Option 1 : Local Environment
az login --tenant <yourtenantid or domain.com>
# Option 2 : Github Codespace : you might need to specify --use-device-code parameter to ease the az cli authentication process
az login --use-device-code --tenant <yourtenantid or domain.com>

# Display your account details
az account show
# Select your Azure subscription
az account set --subscription <subscription-id>

# Register the following Azure providers if they are not already

# Azure Communication Services
az provider register --namespace 'Microsoft.Communication'
```

</details>

[az-cli-install]: https://learn.microsoft.com/en-us/cli/azure/install-azure-cli
[az-naming-convention]: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming
[az-abrevation]: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations
[az-portal]: https://portal.azure.com
[vs-code]: https://code.visualstudio.com/
[docker-desktop]: https://www.docker.com/products/docker-desktop/
[Repo-fork]: https://github.com/microsoft/hands-on-lab-acs-teams/fork
[git-client]: https://git-scm.com/downloads
[github-account]: https://github.com/join
[download-node]: https://nodejs.org/en

---

# Lab 0 - Create an Azure Communication Services resource

In this lab, you will create an Azure Communication Services resource to enable communication capabilities in your application.

<div class="task" data-title="Task">

> Create an Azure Communication Services resource in your Azure subscription.
> The naming convention for an Azure Communication Service is: `acs-<environment>-<region>-<application-name>-<owner>-<instance>`

</div>

<details>
<summary>Toggle solution</summary>

Go to the Azure Portal and create a new resource by searching for `Azure Communication Services`.

Make sure to select the right subscription and resource group or create one, and choose a unique name for your Azure Communication Service resource.

![create-acs](./assets/create-acs-resource.png)

</details>

---

# Lab 1 - Create a call between two ACS users

In this lab, you will create a call between two users using Azure Communication Services.

To start this lab you will use the code inside `src/add-1-on-1-acs`

---

# Lab 2 - Interact with Azure Communication Services and Microsoft Teams

In this lab, you will create a real world scenario where you have a backend that interacts with Azure Communication Services and a basic frontend.

To start this lab you will use the code inside `src/acs-to-external`.

## Prepare the backend server

The role of the server is to interract with Azure Communication Services to create a chat, a call or a video call. The server will also be responsible to create authentification for users and provide Azure Communication Services endpoint.

### Provide user token

Inside the `src/acs-to-external/back/svc` folder, you will find a `createUserAndToken.js` file. This file contains a method to create a user access token for chat and VOIP.

The goal of this method is to create an equivalent of the processed you did in the first lab to create a user access token inside the **Identities & User Access Tokens** tab of the Azure Communication Services resource. By calling this method, you will be able to create a user access token for chat and VOIP.

<div class="task" data-title="Task">

> Implement the `createUserAndToken` method to create a user access token for chat and VOIP.
> Test it with Postman or any other API client.

</div>

<details>
<summary>Toggle solution</summary>

To achieve this, you will need to use the `@azure/communication-identity` package.

```javascript
export async function createUserAndToken(scopes) {
  const client = new CommunicationIdentityClient(env["ACS_CONNECTION_STRING"]);
  return client.createUserAndToken(scopes);
}
```

### Add the Azure Communication Services connection string

Inside the `src/acs-to-external/back` folder, you will find a `.env.example` file. Rename this file to `.env` and fill in the `ACS_CONNECTION_STRING` with the connection string of your Azure Communication Services resource.

You can find it in **Settings** > **Keys** tab of your Azure Communication Services resource:

![create-acs](./assets/acs-connection-string.png)

### Test the method

You can now start the server by running the following commands:

```bash
cd src/acs-to-external/back

npm install

npm start
```

To check if the server is running, you can go to `http://localhost:8080` in your browser and try to call the `/getEndpointUrl` endpoint which is defined in the `src/acs-to-external/back/app.js` file.

</details>

## Prepare the frontend

Now that you have a backend server running, you can create a chat between two users. In this lab you will create a chat between an Azure Communication Services user and a Microsoft Teams user.

You can find the frontend code of a basic chat inside the `src/acs-to-external/front` folder. The frontend is a simple HTML page with a chat box and a form to send messages.

The next step is to connect the frontend to the backend.

<div class="task" data-title="Task">

> Inside the `src/acs-to-external/front/client.js` file, implement the `main` method to create a `callClient`, `callAgent` and `chatClient` objects.
> Use the methods inside the `src/acs-to-external/front/utils` folder in the `utils.js` file to initiate those objects.

</div>

<details>
<summary>Toggle solution</summary>

The `main` method should look like this:

```javascript
async function main() {
  const callClient = new CallClient();
  const creds = new AzureCommunicationTokenCredential(await getToken());

  const callAgent = await callClient.createCallAgent(creds, {
    displayName: "ACS user",
  });
  const chatClient = new ChatClient(await getEndpointUrl(), creds);

  const deviceManager = await callClient.getDeviceManager();
  await deviceManager.askDevicePermission({ video: true, audio: true });
  await registerEvents(callAgent, chatClient, deviceManager);
}
```

The `callClient` is responsible of creating the connection between an Azure Communication Services and your frontend.
The `callAgent` is responsible of creating a call between two users. The `chatClient` is responsible of creating a chat between two users.

You will use those different objects in the next steps to create a call, a video call and a chat.

</details>

## Create a call

Next step is to create a call between two users: An Azure Communication Services user and a Microsoft Teams user.

The file where you will find the code to interract with the backend is `src/acs-to-external/front/client.js` file to create a call between two users.

<div class="task" data-title="Task">

> Implement the `startCall` method to create a call between two users.

</div>

<details>
<summary>Toggle solution</summary>

### Test the call

Inside the `src/acs-to-external/front` folder, you will find a `.env.example` file. Rename this file to `.env` and fill in the `BACKEND_URL` with the URL of your backend server which is `http://localhost:8080` by default.

You can now start the frontend by running the following commands:

```bash
cd src/acs-to-external/front

npm install

npm start
```

To check if the frontend is running, you can go to `http://localhost:8081` in your browser.

Now you can create a Teams meeting and pass the invitation link that will start with `https://teams.microsoft.com/l/meetup-join/...`. You will be able to join the call by clicking on the `Start Call` button.

</details>

## Create a chat

Next step is to create a chat between two users: An Azure Communication Services user and a Microsoft Teams user.

You will continue to use the code inside the `src/acs-to-external/front/client.js` file to create a chat between two users.

<div class="task" data-title="Task">

> Implement the `sendMessage` method to create a chat between two users.
> Set the sender name as you want

</div>

<details>
<summary>Toggle solution</summary>

### Add the sendMessage method

The `sendMessage` method should look like this:

```javascript
async function sendMessage(chatThreadClient, content) {
  const opt = { senderDisplayName: "Jack" };
  await chatThreadClient.sendMessage({ content }, opt);
}
```

As you can see, the `sendMessage` method takes a `chatThreadClient` object and a `content` parameter. The `senderDisplayName` is the name of the user who sends the message. To keep it simple, we set it to `Jack` but in a real world scenario, you would get the name of the user from a database that will do the mapping between the Azure Communication Services user and the frontend user.

### Test the chat

You can now start the frontend again by running the following commands:

```bash
cd src/acs-to-external/front

npm start
```

Go back to `http://localhost:8081` in your browser and once again start a Teams meeting, it can be the same one you used for the call. You will be able to send a message by clicking on the `Send` button.

</details>

## Create a video call

---

# Lab 3 - Call a phone number

Let's use the Azure Communication Services to call a phone number. First step is to go to the Azure Communication Services resource you created in the first lab and inside the `Phone Numbers` tab, you will need to buy a phone number.

<div class="task" data-title="Task">

> Buy a `Toll-free` phone number inside the Azure Communication Services resource.

</div>

<details>
<summary>Toggle solution</summary>

Click on the `+ Get` button to buy a new phone number. Select a country and the `Toll-free` option, then click on `Search`.

Select one of the available phone numbers and click on add `Add to cart`. Click on `Review + buy` and then `Buy`.

</details>

---

# Closing the workshop

Once you're done with this lab you can delete the resource group you created at the beginning.

To do so, click on `delete resource group` in the Azure Portal to delete all the resources and audio content at once. The following Az-Cli command can also be used to delete the resource group :

```bash
# Delete the resource group with all the resources
az group delete --name <resource-group>
```
# Develop an Azure AI chat agent with the Microsoft Agent Framework SDK

In this exercise, you'll use Azure AI Agent Service and Microsoft Agent Framework to create an AI agent that processes expense claims.

This exercise should take approximately **30** minutes to complete.

> **Note**: Some of the technologies used in this exercise are in preview or in active development. You may experience some unexpected behavior, warnings, or errors.


## Create a Foundry project with the Foundry Toolkit VS Code extension

Follow the [VS Code Toolkit project creation steps in Prerequisites](00.Prerequisites.md#toolkit-create-project).

For this exercise, you can use the suggested naming convention: `foundry-ex-07-agent-framework`.

## Deploy a model

Follow the [Model deployment steps in Prerequisites](00.Prerequisites.md#deploy-model).

For this exercise, deploy the **gpt-5** model. After deployment completes, copy your project endpoint as you'll need it in the next steps.

## Clone the starter code repository

For this exercise, you'll use starter code that will help you connect to your Foundry project and create an agent that can process expenses data. You'll clone this code from a GitHub repository.


1. In VS Code, select **File > New Window > Open Folder** and navigate to `Labfiles/07-agent-framework`, then choose **Select Folder**.

1. In the Explorer pane, expand the **Python** folder to view the code files for this exercise.

1. Right-click on the **requirements.txt** file and select **Open in Integrated Terminal**.

1. In the terminal, enter the following command to install the required Python packages in a virtual environment:

    ```
   python -m venv labenv
   .\labenv\Scripts\Activate.ps1
   pip install -r requirements.txt
    ```

1. Open the **.env** file, replace the **your_project_endpoint** placeholder with the endpoint for your project (copied from the project deployment resource in the Foundry Toolkit extension) and ensure that the MODEL_DEPLOYMENT_NAME variable is set to your model deployment name. Use **Ctrl+S** to save the file after making these changes.

Now you're ready to create an AI agent that uses a custom tool to process expenses data.

## Create an agent with a custom tool

> **Tip**: As you add code, be sure to maintain the correct indentation. Use the existing comments as a guide, entering the new code at the same level of indentation.

1. Open the **agent-framework.py** file in the code editor.

1. Review the code in the file. It contains:
    - Some **import** statements to add references to commonly used namespaces
    - A *main* function that loads a file containing expenses data, asks the user for instructions, and and then calls...
    - A **process_expenses_data** function in which the code to create and use your agent must be added

1. At the top of the file, after the existing **import** statement, find the comment **Add references**, and add the following code to reference the namespaces in the libraries you'll need to implement your agent:

    ```python
   # Add references
   from agent_framework import tool, Agent
   from agent_framework.foundry import FoundryChatClient
   from azure.identity import AzureCliCredential
   from pydantic import Field
   from typing import Annotated
    ```

1. Near the bottom of the file, find the comment **Create a tool function for the email functionality**, and add the following code to define a function that your agent will use to send email (tools are a way to add custom functionality to agents)

    ```python
   # Create a tool function for the email functionality
   @tool(approval_mode="never_require")
   def submit_claim(
       to: Annotated[str, Field(description="Who to send the email to")],
       subject: Annotated[str, Field(description="The subject of the email.")],
       body: Annotated[str, Field(description="The text body of the email.")]):
           print("\nTo:", to)
           print("Subject:", subject)
           print(body, "\n")
    ```

    > **Note**: The function *simulates* sending an email by printing it to the console. In a real application, you'd use an SMTP service or similar to actually send the email!

1. Back up above the **send_email** code, in the **process_expenses_data** function, find the comment **Create a foundry chat client**, and add the following code:

    (Be sure to maintain the indentation level)

    ```python
   # Create a foundry chat client 
   client = FoundryChatClient(
       project_endpoint=os.getenv("PROJECT_ENDPOINT"),
       model=os.getenv("MODEL_DEPLOYMENT_NAME"),
       credential=AzureCliCredential()
   )
    ```

    Note that the **AzureCliCredential** object will allow your code to authenticate to your Azure account. This client will be used to interact with the Foundry agent services.

2. Find the comment **Initialize an agent with the tool and instructions**, and add the following code:

    (Be sure to maintain the indentation level)

    ```python
   # Initialize an agent with the tool and instructions
   async with (
       Agent(
           client=client,
           name="ExpenseClaimAgent",
           instructions="""You are an AI assistant for expense claim submission.
                       At the user's request, create an expense claim and use the plug-in function to send an email to expenses@contoso.com with the subject 'Expense Claim`and a body that contains itemized expenses with a total.
                       Then confirm to the user that you've done so. Don't ask for any more information from the user, just use the data provided to create the email.""",
           tools=[submit_claim],
       ) as agent,
   ):
    ```
    In this code, the **Agent** object is initialized with the client, instructions for the agent, and the tool function you defined to send emails.

1. Find the comment **Use the agent to process the expenses data**, and add the following code to create a thread for your agent to run on, and then invoke it with a chat message.

    (Be sure to maintain the indentation level):

    ```python
   # Use the agent to process the expenses data
   try:
       # Add the input prompt to a list of messages to be submitted
       prompt_messages = [f"{prompt}: {expenses_data}"]
       # Invoke the agent for the specified thread with the messages
       response = await agent.run(prompt_messages)
       # Display the response
       print(f"\n# Agent:\n{response}")
   except Exception as e:
       # Something went wrong
       print (e)
    ```

1. Review that the completed code for your agent, using the comments to help you understand what each block of code does, and then save your code changes (**CTRL+S**).

## Test the application

1. In the integrated terminal, enter the following commands to run the application:

    ```
   az login
    ```

    ```
   python agent-framework.py
    ```

    `az login` allows the AzureCliCredential to authenticate to your Azure account.

1. When asked what to do with the expenses data, enter the following prompt:

    ```
   Submit an expense claim
    ```

1. When the application has finished, review the output. The agent should have composed an email for an expenses claim based on the data that was provided.

    > **Tip**: If the app fails because the rate limit is exceeded. Wait a few seconds and try again. If there is insufficient quota available in your subscription, the model may not be able to respond.

1. When you're finished, enter `deactivate` in the terminal to exit the Python virtual environment.

---

[Next workshop: 08 - Agent Framework Multi Agents](08-agent-framework-multi-agents.md)

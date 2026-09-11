# Develop a multi-agent solution with Microsoft Agent Framework

In this exercise, you'll practice using the sequential orchestration pattern in the Microsoft Agent Framework SDK. You'll create a simple pipeline of three agents that work together to process customer feedback and suggest next steps. You'll create the following agents:

- The Summarizer agent will condense raw feedback into a short, neutral sentence.
- The Classifier agent will categorize the feedback as Positive, Negative, or a Feature request.
- Finally, the Recommended Action agent will recommend an appropriate follow-up step.

You'll learn how to use the Microsoft Agent Framework SDK to break down a problem, route it through the right agents, and produce actionable results. Let's get started!

This exercise should take approximately **30** minutes to complete.

> **Note**: Some of the technologies used in this exercise are in preview or in active development. You may experience some unexpected behavior, warnings, or errors.


## Create a Foundry project with the Foundry Toolkit for VS Code extension

Follow the [VS Code Toolkit project creation steps in Prerequisites](00.Prerequisites.md#toolkit-create-project).

For this exercise, you can use the suggested naming convention: `foundry-ex-08-multi-agents`.

## Deploy a model

Follow the [Model deployment steps in Prerequisites](00.Prerequisites.md#deploy-model).

For this exercise, deploy the **gpt-5** model. After deployment completes, copy your project endpoint as you'll need it in the next steps.

## Clone the starter code repository

For this exercise, you'll use starter code that will help you connect to your Foundry project and create a multi-agent solution that can process customer feedback. You'll clone this code from a GitHub repository.

1. In VS Code, select **File > New Window > Open Folder** and navigate to `Labfiles/08-agent-orchestration`, then choose **Select Folder**.

1. In the Explorer pane, expand the **Python** folder to view the code files for this exercise.

1. Right-click on the **requirements.txt** file and select **Open in Integrated Terminal**.

1. In the terminal, enter the following command to install the required Python packages in a virtual environment:

    ```
   python -m venv labenv
   .\labenv\Scripts\Activate.ps1
   pip install -r requirements.txt
    ```

1. Open the **.env** file, replace the **your_project_endpoint** placeholder with the endpoint for your project (copied from the project deployment resource in the Foundry Toolkit extension) and ensure that the MODEL_DEPLOYMENT_NAME variable is set to your model deployment name. Use **Ctrl+S** to save the file after making these changes.

## Create AI agents

Now you're ready to create the agents for your multi-agent solution! Let's get started!

1. Open the **agents.py** file in the code editor.

1. At the top of the file under the comment **Add references**, and add the following code to reference the namespaces in the libraries you'll need to implement your agent:

    ```python
   # Add references
   from agent_framework import Message
   from agent_framework.foundry import FoundryChatClient
   from agent_framework.orchestrations import SequentialBuilder
   from azure.identity import AzureCliCredential
    ```

1. In the **main** function, take a moment to review the agent instructions. These instructions define the behavior of each agent in the orchestration.

1. Add the following code under the comment **Create the chat client**:

    ```python
   # Create the chat client
   credential = AzureCliCredential()
   chat_client = FoundryChatClient(
       credential=credential,
       project_endpoint=os.getenv("AZURE_AI_PROJECT_ENDPOINT"),
       model=os.getenv("AZURE_AI_MODEL_DEPLOYMENT_NAME"),
   )
    ```

    Note that the **AzureCliCredential** object will allow your code to authenticate to your Azure account. The **FoundryChatClient** object connects to your Foundry project using the endpoint and model deployment name from the .env configuration.

1. Add the following code under the comment **Create agents**:

    (Be sure to maintain the indentation level)

    ```python
   # Create agents
   summarizer_agent = chat_client.as_agent(
       name="summarizer",
       instructions=summarizer_instructions,
   )

   classifier_agent = chat_client.as_agent(
       name="classifier",
       instructions=classifier_instructions,
   )

   action_agent = chat_client.as_agent(
       name="action",
       instructions=action_instructions,
   )
    ```

## Create a sequential orchestration

1. In the **main** function, find the comment **Initialize the current feedback** and add the following code:

    (Be sure to maintain the indentation level)

    ```python
   # Initialize the current feedback
   feedback="""
   I use the dashboard every day to monitor metrics, and it works well overall. 
   But when I'm working late at night, the bright screen is really harsh on my eyes. 
   If you added a dark mode option, it would make the experience much more comfortable.
   """
    ```

1. Under the comment **Build a sequential orchestration**, add the following code to define a sequential orchestration with the agents you defined:

    ```python
   # Build sequential orchestration
   workflow = SequentialBuilder(
       participants=[summarizer_agent, classifier_agent, action_agent],
       output_from="all",
   ).build()
    ```

    The agents will process the feedback in the order they are added to the orchestration. The `output_from="all"` parameter ensures that outputs from all agents are collected.

1. Add the following code under the comment **Run and collect outputs**:

    ```python
   # Run and collect outputs
   result = await workflow.run(f"Customer feedback: {feedback}")
   outputs = result.get_outputs()
    ```

    This code runs the orchestration and collects the output from each of the participating agents.

1. Add the following code under the comment **Display outputs**:

    ```python
   # Display outputs
   i = 1
   for response in outputs:
       for msg in cast(list[Message], response.messages):
           name = msg.author_name or ("assistant" if msg.role == "assistant" else "user")
           print(f"{'-' * 60}\n{i:02d} [{name}]\n{msg.text}")
           i += 1
    ```

    This code formats and displays the messages from the workflow outputs you collected from the orchestration.

1. Use the **CTRL+S** command to save your changes to the code file.

## Test the application

Now you're ready to run your code and watch your AI agents collaborate.

1. In the integrated terminal, enter the following commands to run the application:

    ```
   az login
    ```

    ```
   python agents.py
    ```

1. You should see some output similar to the following:

    ```output
   User requests a dark mode option for more comfortable nighttime use.
   Feature request
   Log as enhancement request to add dark mode for improved user comfort during nighttime use.
   ------------------------------------------------------------
   01 [summarizer]
   User requests a dark mode option for more comfortable nighttime use.
   ------------------------------------------------------------
   02 [classifier]
   Feature request
   ------------------------------------------------------------
   03 [action]
   Log as enhancement request to add dark mode for improved user comfort during nighttime use.
    ```

1. Optionally, you can try running the code using different feedback inputs, such as:

    ```output
   I reached out to your customer support yesterday because I couldn't access my account. The representative responded almost immediately, was polite and professional, and fixed the issue within minutes. Honestly, it was one of the best support experiences I've ever had.
    ```

1. When you're finished, enter `deactivate` in the terminal to exit the Python virtual environment.

---

[Next workshop: 09 - Multi Remote Agents With A2A](09-multi-remote-agents-with-a2a.md)

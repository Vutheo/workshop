---
title: "UI Automation with Amazon Nova Act"
date: 2026-06-05
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# UI Automation with Amazon Nova Act

Browser automation has become an essential part of modern software development. Tasks such as UI testing, web scraping, and business process automation all require browsers to perform user interactions accurately and consistently.

For many years, frameworks such as Selenium, Playwright, and Puppeteer have been the standard tools for browser automation. However, these frameworks rely heavily on XPath and CSS Selectors to locate web elements. Whenever the user interface changes, these selectors often become invalid, requiring developers to spend time maintaining and updating automation scripts.

Amazon Nova Act addresses this challenge by introducing an Agentic AI approach that enables browser automation through natural language instead of relying solely on HTML structures.

---

# Limitations of Traditional Web Automation

Traditional browser automation frameworks interact with web pages by identifying elements through specific selectors.

Typical actions include:

- Clicking a login button
- Typing text into a search box
- Selecting a menu item

To perform these actions, developers usually depend on:

- XPath
- CSS Selectors
- ID
- Name

Although this approach is reliable, it has several limitations:

- Even small front-end changes can break existing automation scripts.
- Maintenance costs increase as the application's UI evolves.
- Large automation workflows become more difficult to maintain over time.

---

# What is Amazon Nova Act?

Amazon Nova Act is an AI-powered browser automation service on AWS that enables developers to control web browsers using natural language.

Instead of specifying XPath or CSS Selectors, users simply describe the desired action in English.

Example:

```python
nova.act("Type 'iPhone' into the search bar and press Enter")
```

Nova Act automatically:

- Identifies the search box
- Types the requested text
- Presses the Enter key

Rather than depending directly on HTML elements, Nova Act understands the visual interface and performs actions based on contextual information.

---

# How Amazon Nova Act Works

Amazon Nova Act is built on the **Amazon Nova 2 Lite** foundation model and trained using **Reinforcement Learning** in simulated browser environments known as **Web Gyms**.

Instead of locating elements through XPath, the AI analyzes:

- Visual page layout
- Website structure
- Context surrounding UI components
- Information displayed on the screen

As a result, automation workflows remain functional even when minor UI changes occur, significantly reducing maintenance effort.

---

# Amazon Nova Act Workflow

## Step 1: Experiment with the Playground

Amazon provides an online Playground that allows developers to test Nova Act directly from a web browser.

Users simply need to:

- Enter a website URL
- Describe the desired task using natural language

Nova Act then automatically:

- Opens the webpage
- Scrolls through the page
- Moves the mouse
- Clicks elements
- Enters text

This provides a quick way to validate automation workflows before writing any code.

---

## Step 2: Build Automation with the Python SDK

Once the workflow has been validated, developers can use the Python SDK to build complete automation scripts.

Installation:

```bash
pip install nova-act
```

Example:

```python
for customer in customer_list:
    nova.act(
        f"Fill name {customer['name']} and email {customer['email']} into register form"
    )

    nova.act(
        "Click Submit button and wait page reload"
    )
```

In this example:

- Python handles business logic and data processing.
- Nova Act performs browser interactions.

This approach combines AI-powered browser automation with traditional programming logic.

---

## Step 3: Deploy to AWS

After development is complete, Nova Act supports deployment directly to AWS infrastructure.

The deployment workflow includes:

- Packaging the application as a container
- Uploading the container image to Amazon ECR
- Running automation workflows on Bedrock AgentCore
- Creating an isolated browser sandbox for each execution

This architecture allows automation workflows to scale without requiring developers to manage browser environments manually.

---

# Key Features

## Human-in-the-Loop (HITL)

During execution, automation workflows may encounter situations such as:

- CAPTCHA challenges
- Multi-Factor Authentication (MFA)
- Locked user accounts

Instead of terminating the workflow, Nova Act sends a notification through Amazon SNS.

An administrator can complete the required verification manually, after which the workflow resumes automatically.

---

## Observability

Nova Act provides comprehensive monitoring capabilities for automation workflows.

Users can review:

- Execution videos
- Step-by-step screenshots
- Workflow execution history

These features simplify debugging and make troubleshooting more intuitive than traditional browser automation frameworks.

---

## Enterprise Security

Every automation workflow runs inside an isolated AWS sandbox environment.

Nova Act also supports enterprise-grade security features, including:

- Fine-grained IAM permissions
- Session isolation
- Cookie and credential protection
- Reduced risk of sensitive data leakage

These capabilities make Nova Act suitable for enterprise environments with strict security requirements.

---

# When Should You Use Amazon Nova Act?

Amazon Nova Act is well suited for a variety of browser automation scenarios, including:

- UI Automation Testing
- Web Automation
- Web Scraping
- Automated form submission
- Repetitive business workflows
- AI-powered browser agents

It is particularly valuable for applications whose user interfaces change frequently.

---

# Conclusion

Amazon Nova Act introduces a new approach to browser automation by combining AI agents with modern web interaction.

Instead of relying entirely on XPath or CSS Selectors, developers can describe actions using natural language, significantly reducing the maintenance required when web interfaces change.

Although Selenium and Playwright remain powerful tools for many automation scenarios, Amazon Nova Act offers a promising alternative for workflows that require greater adaptability and seamless integration with the AWS ecosystem. It represents a compelling solution for QA engineers, DevOps teams, and organizations building AI-powered automation systems.
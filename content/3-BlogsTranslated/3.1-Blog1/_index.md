---
title: "Blog 1"
date: 2026-06-05
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Exploring Amazon Nova Act: AI-Powered UI Automation Without Fixing XPath

![Amazon Nova Act](/images/3-Blog/blog1.jpg)

Today I'd like to share my experience with an automation service that I recently discovered and had the opportunity to explore. I had been struggling with writing UI automation scripts for my team's project. The biggest challenge was that the frontend team kept updating the user interface, causing my Selenium scripts to break because selectors or XPath expressions could no longer locate the target elements.

Just when I was about to give up, I came across **Amazon Nova Act**. At first, I assumed it was simply another AI wrapper around Selenium. However, after trying it on my own automation tasks, I realized it was fundamentally different. In this article, I'd like to share my experience and review why I believe Amazon Nova Act is a promising solution for modern UI automation.

## The Pain of Traditional Web Automation

Before diving into Nova Act, let's briefly review what Web Automation is.

Web Automation is the process of writing programs that automatically interact with web browsers just like a human user—clicking buttons, entering text, scrolling pages, and navigating websites.

This technique is widely used in:

- **Automation Testing** – Automatically testing web applications before deployment.
- **Web Scraping** – Collecting information from websites.
- **Business Process Automation** – Automating repetitive office tasks such as filling forms or processing internal workflows.

Traditionally, I use tools such as:

- Selenium
- Playwright
- Puppeteer

These tools all rely on locating HTML elements using:

- XPath
- CSS Selectors

The problem is that even a small frontend update—such as changing a CSS class, restructuring HTML, or moving a button—can immediately break the automation scripts.

Maintaining these scripts often becomes the most time-consuming part of UI automation.

## How Does Amazon Nova Act Solve This Problem?

Amazon Nova Act is a service designed to build **AI Agents** capable of controlling web browsers similarly to how humans interact with them.

The biggest difference is:

**No XPath required.**

**No CSS Selectors required.**

Instead, interactions are described using natural language.

For example:

```python
nova.act("Type 'iPhone' into the search bar and then press enter")
```

Nova Act automatically:

- locates the search box
- enters the text
- presses Enter

without relying on the website's HTML structure.

According to AWS, Nova Act is powered by **Amazon Nova 2 Lite**, a foundation model trained using **Reinforcement Learning** in simulated browser environments known as **Web Gyms**.

Rather than memorizing XPath expressions, the model learns to interpret the visual interface and interact with web pages the way humans do.

AWS reports that Nova Act achieves **over 90% accuracy** on real-world benchmark tasks.

# From Playground to Production

## Step 1. Trying the Playground

My first experience was through the official Playground:

https://nova.amazon.com/act

The Playground is completely **no-code**.

Simply:

- enter a website URL
- describe your task in English

The AI will automatically perform the requested actions.

You can watch it:

- scroll pages
- click buttons
- fill forms
- navigate between pages

all in real time.

## Step 2. Developing with the Python SDK

After experimenting with the Playground, I moved on to writing Python code.

Installation is straightforward:

```bash
pip install nova-act
```

AWS also provides a Visual Studio Code extension that makes development much easier.

Nova Act supports a **Hybrid Automation** model.

This means you can combine:

- AI-powered browser interaction
- traditional Python programming logic

For example:

```python
for customer in customer_list:
    nova.act(
        f"Fill name {customer['name']} and email {customer['email']} into form register"
    )

    nova.act(
        "Click Submit button and wait page reload"
    )
```

Python handles the application logic.

Nova Act handles the browser interactions.

The VS Code extension also provides **Live Debugging**, allowing you to watch each browser action directly inside the editor.

## Step 3. Deploying to AWS

Once development is complete, deployment is surprisingly simple.

Nova Act automatically:

- packages the application
- creates a container
- pushes it to Amazon ECR
- deploys it through Bedrock AgentCore

Each workflow runs inside its own isolated browser sandbox.

This allows hundreds of concurrent automation sessions without managing Chrome Headless, EC2 instances, or custom infrastructure.

# Three Standout Features of Nova Act

## Human-in-the-Loop (HITL)

This is probably my favorite feature.

When the AI encounters situations such as:

- CAPTCHA challenges
- account verification
- login restrictions

the workflow doesn't simply fail.

Instead, Nova Act sends a notification through Amazon SNS.

An administrator can:

- open the secure session
- solve the CAPTCHA manually
- allow the workflow to continue automatically

This significantly improves reliability for long-running automation tasks.

## Observability

Nova Act provides excellent visibility into every automation run.

You can review:

- video replays
- step-by-step screenshots

to verify exactly what the AI did during execution.

This makes debugging much easier compared to traditional automation tools.

## Enterprise-Grade Security

All workflows execute inside isolated AWS sandbox environments.

Organizations can leverage:

- IAM permissions
- secure session management
- protected browser cookies
- isolated execution environments

making Nova Act suitable for enterprise-scale deployments.

# My Thoughts

After spending time with Amazon Nova Act, I don't see it as just another AI demo.

What impressed me the most is its ability to automate browser interactions without relying on fragile XPath expressions.

Some of the features I particularly like include:

- No dependency on XPath or CSS Selectors.
- Natural language instructions for browser automation.
- Seamless integration between AI and traditional Python code.
- Simple deployment directly on AWS infrastructure.

If you're working in areas such as:

- QA Automation
- DevOps
- Robotic Process Automation (RPA)
- Web Automation
- AI Agents

I believe Amazon Nova Act is definitely worth exploring.

# References

- Playground: https://nova.amazon.com/act
- AWS News Blog: https://aws.amazon.com/blogs/aws/build-reliable-ai-agents-for-ui-workflow-automation-with-amazon-nova-act/
- AWS Documentation: https://docs.aws.amazon.com/nova-act/latest/userguide/what-is-nova-act.html
# LinkedIn AI Post Automation (n8n)

An end to end n8n workflow that generates AI written LinkedIn posts, creates matching images, routes them through an email based approval step, and automatically publishes and logs them.

![Workflow Overview](./assest.png)

## Overview

This workflow runs on a schedule, drafts a post using an AI agent, refines it, generates a matching image, and waits for human approval via email before publishing to LinkedIn and logging the result to Google Sheets.

## How It Works

1. **Schedule Trigger**. Kicks off the workflow on a set interval.
2. **Main Agent**. An AI agent (OpenRouter Chat Model and Structured Output Parser) generates the initial post draft, backed by memory and tools.
3. **Refiner**. A second AI agent with its own model and structured parser polishes the draft into a final version.
4. **Basic LLM Chain**. Generates a visual concept and prompt for the post based on the refined content.
5. **Image Generator**. Creates a matching image using an AI image generation model.
6. **Edit Image (Resize)**. Resizes and processes the image and strips metadata such as C2PA tags that could trigger platform AI content flags.
7. **Merge**. Combines the text and image branches back into a single item.
8. **Send a Message (Gmail)**. Emails the draft post and image for human approval.
9. **Wait**. Pauses the workflow until the approver responds.
10. **If**. Branches based on whether the post was approved or rejected.
11. **Create a Post (LinkedIn)**. Publishes the approved post to LinkedIn.
12. **Append Row in Sheet (Google Sheets)**. Logs the published post for record keeping.

## Tech Stack

1. n8n, self hosted for workflow orchestration
2. OpenRouter and OpenAI Chat Models for post generation and refinement
3. AI Image Generation for visual creation
4. Gmail for the approval step
5. Google Sheets for logging
6. LinkedIn API for publishing

## Setup

1. Import the workflow JSON into your n8n instance.
2. Configure credentials for OpenRouter, OpenAI, Gmail, Google Sheets, and LinkedIn.
3. Set your desired schedule in the Schedule Trigger node.
4. Update the Google Sheet ID in the Append Row in Sheet node.
5. Activate the workflow.

## Notes

The Wait node requires the approval email to include a response link back to n8n. If resuming from Wait, downstream nodes must reference upstream data directly, for example using `$('Main Agent').item.json`, rather than relying on the default previous node reference. Image metadata is stripped in the Edit Image step to avoid being flagged as AI generated content on LinkedIn.

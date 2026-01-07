# First Workflow: Webhook Test

**Date:** January 7, 2026  
**Goal:** Learn basic n8n mechanics

## Description

Simple workflow that receives an HTTP request and responds with processed data.

## Nodes Used

- Webhook: Receives HTTP GET requests
- Set: Processes and adds data
- Respond to Webhook: Returns response

## How It Works

1. Client makes a GET request to `/webhook-test/test`
2. n8n receives the request
3. Processes and adds a timestamp
4. Responds with JSON

## Result

- **Status:** Working
- **URL:** `http://localhost:5678/webhook-test/test`
- **Use case:** API testing


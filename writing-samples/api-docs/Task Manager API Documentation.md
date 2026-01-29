# Task Manager API Documentation

![API Endpoints Diagram](./images/Task%20Manager%20API%20Flow.png)

## Overview

This guide provides you with all the necessary knowledge to build a Task Manager API, a simple REST API built with Node.js and Express that allows you to manage tasks. 

It's designed as an educational project for junior developers learning about REST APIs and backend development. No authentication, database setup, or configuration needed!



**Key features:**

- Full CRUD operations (Create, Read, Update, Delete)
- RESTful design with JSON responses
- No authentication required (educational project)
- In-memory storage for quick setup
- Clear error handling with descriptive messages

**Tech stack:**
- Node.js
- Express
- JSON (in-memory storage)

## Data Storage

This API uses **in-memory storage** for simplicity. This means:

- Tasks are stored in RAM while the server is running
- When you restart the server, all data is reset to the initial demo tasks
- Perfect for learning and testing, but not suitable for production

**Initial demo tasks:**
- Learn Node.js
- Build a REST API
- Write documentation

### Example behavior

```bash
# Start server
npm start

# Create a task
curl -X POST http://localhost:3000/api/tasks -H "Content-Type: application/json" -d '{"title":"My task"}'
# ✅ Task created with id "4"

# View all tasks
curl http://localhost:3000/api/tasks
# ✅ You see 4 tasks (3 initial + your new one)

# Restart server (Ctrl + C, then npm start)

# View all tasks again
curl http://localhost:3000/api/tasks
# ⚠️ Only 3 tasks (back to initial state, your task is gone)
```

---

## Getting Started

### Prerequisites

* **Node JS:** Use the latest active or maintenance LTS (Long Term Support) version of Node.js. Verify the installation using `node -v`
* **npm:** Comes with Node.js
* **Code editor:** Visual Studio Code or similar 
* **Git** - [Download here](https://git-scm.com/)
* **Basic JavaScript knowledge** (understanding of async/await and HTTP concepts)


### Installation

This repository is already set up and ready to use. Clone it and install dependencies.

```bash
git clone https://github.com/mcocapelaz/task-manager-api.git
cd task-manager-api
npm install
npm start
```

### Base URL

```
http://localhost:3000/api
```

### Quick Test

Let's make an initial request to verify that everything is working correctly!  Run the following command:  

```bash
curl http://localhost:3000/api/tasks
```

This is the **expected response** you should see: 

```json
{
  "status": "success",
  "data": [
    {"id": "1", "title": "Learn Node.js", "completed": false},
    {"id": "2", "title": "Build a REST API", "completed": true},
    {"id": "3", "title": "Write documentation", "completed": true}
  ]
}
```

**Note:** If the server doesn't respond or returns an error, don't worry! Go to the [Troubleshooting Installation](#troubleshooting-installation) section and check possible issues. 

**Important:** Tasks you create are stored in memory and will be reset when you restart the server. See the [Data Storage](#data-storage) section for more details.

---

## API Reference

This API provides five endpoints for managing tasks. All responses are in JSON format.

**Base URL:** `http://localhost:3000/api`

| Method | Endpoint          | Description          |
|--------|-------------------|----------------------|
| GET    | /tasks            | Get all tasks        |
| GET    | /tasks/:id        | Get a specific task  |
| POST   | /tasks            | Create a new task    |
| PUT    | /tasks/:id        | Update a task        |
| DELETE | /tasks/:id        | Delete a task        |

**Note:** You can test these endpoints using curl (terminal), Postman, or any HTTP client.
**Note:** Task IDs are represented as strings for simplicity.

### Response Format

All endpoints return a consistent JSON structure:

**Success:**
```json
{
  "status": "success",
  "data": { }
}
```

**Error:**
```
{
  "status": "error",
  "message": "Error description"
}
```

This section explains how to interact with each endpoint. For each one, you'll find:

- **What it does**: A brief description
- **How to call it**: The HTTP method and URL
- **What to send**: Request parameters and body (if needed)
- **What you get back**: Response structure with examples
- **Common errors**: Possible error codes and solutions

**Note:** The examples below use `curl`, a command-line tool for making HTTP requests. If you prefer a graphical interface, you can use [Postman](https://www.postman.com/) or any HTTP client.

### Get All Tasks

**Endpoint:** `GET /api/tasks`

**Description:** This endpoint returns all the tasks in the list. 

**Use this endpoint to:**
- Display all tasks in a to-do list interface
- Check how many tasks are pending or completed
- Retrieve the initial task list when your app loads
- Verify that tasks were created successfully

**Example:**

```bash
curl http://localhost:3000/api/tasks
```

**Response (200 OK):**

```json
{
  "status": "success",
  "data": [
    {
      "id": "1",
      "title": "Learn Node.js",
      "completed": false
    },
    {
      "id": "2",
      "title": "Build a REST API",
      "completed": true
    },
    {
      "id": "3",
      "title": "Write documentation",
      "completed": true
    }
  ]
}
```

---

### Get Task by ID

**Endpoint:** `GET /api/tasks/:id`

**Description:** This endpoint returns a specific task or an error when this task does not exist.

**Use this endpoint to:**
- Display details of a single task
- Verify a task exists before updating or deleting it
- Retrieve a task after creating it to confirm the operation
- Build task detail pages in your application

**Path Parameters:**

| Parameter | Type   | Required | Description           |
|-----------|--------|----------|-----------------------|
| `id`      | string | Yes      | Unique identifier of the task (e.g., "1", "2")  |

**Example:**

```bash
curl http://localhost:3000/api/tasks/1
```
**Response (200 OK):**

```json
{
  "status": "success",
  "data": {
    "id": "1",
    "title": "Learn Node.js",
    "completed": false
  }
}
```

**Error Responses:**

**404 Not Found:**

```json
{
  "status": "error",
  "message": "Task not found"
}
```
---

### Create New Task

**Endpoint:** `POST /api/tasks`

**Description:** This endpoint creates a new task and adds it to the task list. The API automatically generates a unique ID for the task.

**Use this endpoint to:**
- Add a task when a user submits a form
- Create multiple tasks programmatically from a script
- Populate your app with initial or demo data
- Integrate task creation from external services or automation tools

**Request Body:**

| Field       | Type    | Required | Description           |
|-------------|---------|----------|-----------------------|
| `title`     | string  | Yes      | The task description or name |
| `completed` | boolean | No       | Task completion status (default: `false`) |

**Example:**


```bash
curl -X POST http://localhost:3000/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Write API docs"}'

```

**Example Request Body:**

```json
{
  "title": "Write API docs",
  "completed": false
}
```

**Response (201 Created):**

```json
{
  "status": "success",
  "data": {
    "id": "4",
    "title": "Write API docs",
    "completed": false
  }
}
```

**Error Responses:**

**400 Bad Request:**

```json
{
  "status": "error",
  "message": "Title is required"
}
```
**Important:** Remember! When you restart the server, all data is reset to the initial demo tasks, so your new task will disappear. 

---

### Update Task

**Endpoint:** `PUT /api/tasks/:id`

**Description:** This endpoint updates one or more properties of a task. In this simple project, we use PUT to accept partial updates for simplicity. In more strict REST APIs, PATCH is often used for partial updates.

**Use this endpoint to:**
- Mark a task as completed when a user checks it
- Edit the task title when a user modifies it
- Toggle task status between completed and pending
- Update task properties without deleting and recreating

**Path Parameters:**

| Parameter | Type   | Required | Description           |
|-----------|--------|----------|-----------------------|
| `id`      | string | Yes      | Unique identifier of the task (e.g., "1", "2")   |

**Request Body:**

| Field       | Type    | Required | Description           |
|-------------|---------|----------|-----------------------|
| `title`     | string  | No       | Task title       |
| `completed` | boolean | No       | Task completed       |

**Example:**

```bash
curl -X PUT http://localhost:3000/api/tasks/4 \
  -H "Content-Type: application/json" \
  -d '{"title":"Buy groceries","completed":false}'
```


**Example Request Body:**

```json
{
  "title": "Buy groceries",
  "completed": false
}
```

**Response (200 OK):**

```json
{
  "status": "success",
  "data": {
    "id": "4",
    "title": "Buy groceries",
    "completed": false
  }
}
```

**Error Responses:**

**404 Not Found:**

```json
{
  "status": "error",
  "message": "Task not found"
}
```

---

### Delete Task

**Endpoint:** `DELETE /api/tasks/:id`

**Description:** This endpoint permanently deletes a specific task.

**Use this endpoint to:**
- Remove completed tasks from the list
- Delete tasks the user no longer needs
- Clean up test or demo data

**Path Parameters:**

| Parameter | Type   | Required | Description           |
|-----------|--------|----------|-----------------------|
| `id`      | string | Yes      | Unique identifier of the task (e.g., "1", "2")  |

**Example:**

```bash
curl -X DELETE http://localhost:3000/api/tasks/2
```

**Response (200 OK):**

```json
{
  "status": "success",
  "message": "Task deleted successfully"
}
```
**Note:** This endpoint returns 200 OK with a success message for clarity in educational contexts.

**Error Responses:**

**404 Not Found:**

```json
{
  "status": "error",
  "message": "Task not found"
}
```

---

## Error Handling

Errors returned by the API have the following characteristics:

### HTTP Status Codes

| Status Code | Message               |
|-------------|-----------------------|
| 200         | OK                    |
| 201         | Created               |
| 400         | Bad Request           |
| 404         | Not Found             |
| 500         | Internal Server Error |

### Error Response Format

If the API request fails it returns an error response in JSON format with the status of "error" and its description.  

```json
{
  "status": "error",
  "message": "Descriptive error message here"
}
```

---

## Code Examples

### JavaScript (Fetch API)

```javascript
// Get all tasks
fetch('http://localhost:3000/api/tasks')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));

// Create a new task
fetch('http://localhost:3000/api/tasks', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    title: 'New task',
    completed: false
  })
})
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

### Node.js (Axios)

**Prerequisites:** Install axios first:

```bash
npm install axios
```

Run the following command: 

```javascript
const axios = require('axios');

// Get all tasks
async function getAllTasks() {
  try {
    const response = await axios.get('http://localhost:3000/api/tasks');
    console.log(response.data);
  } catch (error) {
    console.error('Error:', error.message);
  }
}

// Create a new task
async function createTask() {
  try {
    const response = await axios.post('http://localhost:3000/api/tasks', {
      title: 'New task',
      completed: false
    });
    console.log(response.data);
  } catch (error) {
    console.error('Error:', error.message);
  }
}

getAllTasks();
createTask();
```

---

## FAQ

### Can I use this API in production?

No, this API is designed for educational purposes only. It uses in-memory storage 
and lacks authentication, making it unsuitable for production environments.

### How can I make the data persistent?

To persist data, you would need to implement file-based storage (using `fs` module) 
or a database (MongoDB, PostgreSQL, etc.).

### Why doesn't the API require authentication?

To keep the project simple and focused on learning REST API fundamentals. 
In a real-world scenario, you should always implement authentication.

---
## Troubleshooting Installation

**"Command not found: git"**
- Install Git from https://git-scm.com/

**"Command not found: npm"**
- Install Node.js (includes npm) from https://nodejs.org/
- Verify installation: `node --version` and `npm --version`

**"Port 3000 already in use"**
- Another application is using port 3000
- Stop the other app or change the port in `index.js`. For example: port 3001

**"Cannot find module 'express'"**
- Run `npm install` to install dependencies
- Make sure you're in the correct directory

**Server starts but requests fail**
- Check that you're using the correct URL: `http://localhost:3000/api/tasks`
- Make sure the server is running (you should see "Server running..." in the terminal)

**"My tasks disappeared after restarting the server"**
- Tasks only persist while the server is running. See [Data Storage section](#data-storage) for more information. 


---

## License

MIT License - feel free to use this project for learning, personal, or commercial purposes. See the License file for details.

---

## Contact

- **GitHub:** [@mcocapelaz](https://github.com/mcocapelaz/)
- **LinkedIn:** [Mónica Coca](https://www.linkedin.com/in/mcocapelaz/)

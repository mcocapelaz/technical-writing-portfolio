# Node.js and Express Installation Guide for Beginners

<p align="center">
<img src="./images/Node.js_logo.svg.png" alt="Node.js logo" width="30%">
</p>


## About This Guide

Node.js is a free and open-source tool that lets you run JavaScript outside the web browser to build fast and scalable applications like web servers, APIs, and more. This guide uses the official installer for simplicity. Version managers like nvm are recommended for advanced workflows.

Express is a lightweight framework for Node.js that makes building web servers and APIs easier without writing everything from scratch. It's fast, flexible, and the most popular backend framework for Node.js.

Node.js and Express can feel confusing at the beginning for junior developers. This guide aims to make the learning process smoother by focusing on practical examples and explaining core concepts step by step. 

---

## What You'll Learn

By the end of this guide, you will be able to:

* Download and install Node.js and npm on your operating system
* Verify that your installation works correctly
* Create your first Node.js project
* Install the Express web framework
* Write and run a basic web server
* Test your server in a web browser
* Troubleshoot common problems

---

## Prerequisites

Before you begin, make sure you have:

- **Administrator access** on your computer (you need permission to install software)
- **Internet connection** (to download Node.js and npm)
- **A text editor** (such as VS Code, Sublime Text, or even Notepad++)
- **A web browser** (Chrome, Firefox, Safari, Edge—any modern browser)
- **A supported operating system** (Windows, macOS, or Linux—see System Requirements below)

If you are new to **the command-line**, don't worry! This guide explains everything you need to know.

---

## System Requirements

| Operating System | Minimum Version | RAM | Free Disk Space |
|---|---|---|---|
| Windows | Windows 10 or later | 512 MB or more | At least 500 MB |
| macOS | macOS 10.15 (Catalina) or later | 512 MB or more | At least 500 MB |
| Linux | Ubuntu 18.04 LTS or newer | 512 MB or more | At least 500 MB |

**Note:** For a comfortable development environment, ensuring **at least 8 GB of free space** is advisable because multiple projects and their dependencies (especially node_modules folders) can grow quickly.

---

## Part 1: Install Node.js and npm

### macOS Installation

#### Step 1: Download Node.js

1. Open your **web browser**
2. Go to **https://nodejs.org**
3. Go to the **Download** section
4. Click the drop-down menu and choose **LTS** (Long Term Support) to select the stable version
5. Click the macOS Installer button: **macOS Installer(.pkg)**
6. A file will download to your **Downloads** folder

#### Step 2: Install Node.js

1. Open your **Downloads folder**
2. Find the **file** named something like `node-vXX.X.X.pkg`
3. Double-click the **installer**
4. Follow the **steps**:
   - Click "Continue"
   - Read the license and click "Agree"
   - Click "Install"
   - Enter your macOS password when asked
   - Wait for installation to complete
5. Click **"Close"**

#### Step 3: Verify Installation

1. Open **Terminal** (search for "Terminal" in Spotlight)
2. Type this **command** and press Enter:

```bash
node -v
```

If you see a version number like `vXX.XX.X`, the step was successful.
   
3. Type this command and press Enter:

```bash
npm -v
```
   
If you see a version number like `XX.X.X`, the step was successful.

Do both commands show version numbers? Congratulations! Node.js is installed correctly. Proceed to Part 2.

If you see **"command not found"**:
- Don't panic! Just close Terminal completely
- Open a NEW Terminal window and try again
- Try `node -v` and `npm -v`
- If it doesn't work run `node-vXX.X.X.pkg` again 

---

### Windows Installation

#### Step 1: Download Node.js

1. Open your **web browser**
2. Go to **https://nodejs.org**
3. Go to the **Download** section
4. Click the drop-down menu and choose **LTS** (Long Term Support) to select the stable version
5. Choose a **prebuilt Node version for Windows** 
6. Press the Windows Installer button: **Windows Installer(.msi)**
7. A file named `node-vXX.XX.X-x64.msi` will download to your **Downloads** folder

#### Step 2: Install Node.js

1. Open your **Downloads** folder
2. Double-click the **`.msi` file**
3. A **setup wizard** will open:
   - Click "Next"
   - Click "I Agree" (accept license)
   - Set the Destination Folder 
   - Click "Next"
   - Keep default settings and click "Next"
   - Click "Install"
   - Enter your administrator password if prompted
4. **Wait** for installation (about 2-3 minutes). Do not close or cancel the process until the install is complete
5. Click **"Finish"**

#### Step 3: Verify Installation

1. Open **Command Prompt**:
   - Press **Windows key**
   - Type `cmd`
   - Click **"Command Prompt"**
2. Type this **command** and press Enter:

```bash
node -v
```
  
If you see a version number `vXX.XX.X` (or similar), the step was successful.
  
3. Type this command and press Enter:

```bash
npm -v
```
   
If you see a version number like `XX.X.X` (or similar), the step was successful.
   
If both show version numbers, you're done! Proceed to Part 2.

If you see **"command not found"**, don't worry!:
- Close Command Prompt completely
- Open a NEW Command Prompt window
- Try again

---

### Linux Installation

**Note:** This guide focuses on installing Node.js on **Ubuntu and Debian-based systems**, which are the most common environments for junior developers. For other Linux distributions, refer to the **[official Node.js installation documentation](https://nodejs.org/en/download/package-manager)**.

#### Step 1: Update Your Package Manager

It is considered best practice to run this command before installing or upgrading software to avoid installing outdated packages. That's why we recommend it! 

Open **Terminal** and run:

```bash
sudo apt update
```

#### Step 2: Install Node.js

```bash
sudo apt install -y curl
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
```

**Why NodeSource?** The default Ubuntu/Debian repositories contain outdated Node.js versions. NodeSource provides the latest LTS releases for better compatibility.

**Note:** npm is automatically included with Node.js. You don't need to install it separately.


#### Step 3: Verify Installation

In Terminal, run:

```bash
node -v
```

If you see a version number like `vXX.XX.X`, the step was successful.  

Then run:

```bash
npm -v
```

If you see a version number like `XX.X.X` (or similar), the step was successful.

Do you see both version numbers? Then proceed to Part 2!

---

## Part 2: Create Your Express Project

#### Step 1: Create a Folder

To create a new folder called `my-express-app` on your Desktop, open Terminal (macOS/Linux) or Command Prompt (Windows) and run this code: 

```bash
cd Desktop
mkdir my-express-app
cd my-express-app
```

**Note:** If cd Desktop fails, create the project in any folder you prefer.

#### Step 2: Initialize npm

This step creates a basic configuration file for your project. It allows npm to manage dependencies like Express. 

While in the `my-express-app` folder, run:

```bash
npm init -y
```

You should see output showing a `package.json` file was created. This file stores information about your project and its dependencies.

#### Step 3: Install Express

Run:

```bash
npm install express
```

Wait for it to finish (usually 30 seconds). You'll see many packages being installed. This is normal and expected.

---

## Part 3: Create Your First Express Server

#### Step 1: Create app.js

Using your text editor, create a new file called `app.js` in the `my-express-app` folder and copy this code exactly:

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello World! Your Express server is running.');
});

app.listen(port, () => {
  console.log(`Example app listening on http://localhost:${port}`);
});
```

**What this code does:**
- Line 1: Import Express
- Line 2: Create an Express app
- Line 3: Set port to 3000
- Lines 5-7: Create a route that responds to visits to the root URL
- Lines 9-11: Start the server

#### Step 2: Run Your Server

In Terminal/Command Prompt (in the `my-express-app` folder), run:

```bash
node app.js
```

You should see:

```
Example app listening on http://localhost:3000
```

#### Step 3: Test It

1. Open your web browser
2. Go to `http://localhost:3000`
3. You should see: **"Hello World! Your Express server is running."**

Success! Your Express server is working. 

#### Step 4: Stop Your Server

If you want to stop your server, run in Terminal/Command Prompt, press `Ctrl + C` (or `Cmd + C` on Mac).

---

## Verification Checklist

After completing this guide, verify everything works:

- [ ] `node -v` displays a version number
- [ ] `npm -v` displays a version number
- [ ] Folder `my-express-app` exists on your Desktop
- [ ] File `package.json` exists in that folder
- [ ] File `app.js` exists in that folder
- [ ] Running `node app.js` shows "listening on http://localhost:3000"
- [ ] Browser shows "Hello World! Your Express server is running."
- [ ] Pressing Ctrl+C stops the server

If all items are checked ✓, your installation is complete!

---

## Troubleshooting:  

### Problem: "node: command not found"

**Symptom:** When you run `node -v`, you see "command not found"

**Cause:** Node.js wasn't added to your PATH, or Terminal doesn't know about it yet

**Solution:**
1. Close Terminal/Command Prompt completely (do not just clear the screen)
2. Open a NEW Terminal/Command Prompt window
3. Try `node -v` again

If it still doesn't work, reinstall Node.js

---

### Problem: "npm install express" fails or times out

**Symptom:** Installation hangs or shows error messages

**Cause:** Network issue or npm registry problem

**Solution:**
1. Wait 30 seconds and see if it continues
2. If it's stuck, press Ctrl+C to stop it
3. Try again: `npm install express`
4. If it keeps failing, check your internet connection

---

### Problem: "Port 3000 is already in use"

**Symptom:** When you run `node app.js`, you see "EADDRINUSE" error

**Cause:** Another program is using port 3000

**Solution:** Change the port in your `app.js` file:

Change this line:
```javascript
const port = 3000;
```

To this:
```javascript
const port = 3001;
```

Then run `node app.js` again.

---

### Problem: "Cannot find module 'express'"

**Symptom:** When you run `node app.js`, you see this error

**Cause:** You didn't run `npm install express`, or you're in the wrong folder

**Solution:**
1. Make sure you're in the `my-express-app` folder
2. Run: `npm install express`
3. Try `node app.js` again

---

## Next Steps

Now that you have a working Express server, you can:

1. **Try modifying the message** - Change "Hello World!" to something else in app.js
2. **Add more routes** - Create routes for /about, /contact, etc.
3. **Learn Express documentation** - Visit https://expressjs.com
4. **Build a real project** - Create a todo app, personal website, etc.
5. **Deploy your server** - Put it online using beginner-friendly platforms such as Render or Railway.

The world of Node.js/Express is huge. You now have the foundation!

---

## Resources

| Resource | Link | Purpose |
|---|---|---|
| Node.js Official | https://nodejs.org | Official Node.js documentation |
| Express Official | https://expressjs.com | Official Express documentation |
| npm Registry | https://www.npmjs.com | Find and learn about packages |

---

## Document Info

**Version:** 4  
**Date:** January 2026  
**Audience:** Junior developers (1-3 years of programming experience)  
**Time to complete:** 15-20 minutes  
**Difficulty:** Beginner
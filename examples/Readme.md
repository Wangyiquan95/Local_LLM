# Local LLM Usage Guide

This guide outlines the steps to access and utilize the local LLM.

## Prerequisites

*   SSH client.
*   Chrome browser.

## Steps

**Step 1: SSH Connection**

Connect to the remote server using the following command:

```bash
ssh -N -L 8080:localhost:8080 user_id@nicwulab-linux2.life.illinois.edu
```
![SSH Connection](./ssh_connect.png)
**Step 2: Access the Web Interface**

Open Chrome and navigate to: http://0.0.0.0:8080/

![Web Interface](./sign_up.png)

**Step 3: Account Registration**

Sign up for a new account via the provided registration form.

Account Registration

**Step 4: Start Chatting**

Log in and begin interacting with the LLM. 

![Chat Interface](./chat_interface.png)

**Examples**

Refer to the examples provided within the interface to explore available functionalities.

[PDF Question Answering](./Paper PDF Q&A.pdf)

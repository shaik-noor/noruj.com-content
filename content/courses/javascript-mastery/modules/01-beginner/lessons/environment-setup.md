# Setting Up Your Development Environment

To write and run JavaScript code for web development, you need two main tools: a code editor and a web browser.

## 1. Code Editor

A code editor helps you write code efficiently. We recommend **Visual Studio Code (VS Code)** because it's free, popular, and has great features for JavaScript.

**Installation:**
1.  Go to [code.visualstudio.com](https://code.visualstudio.com/).
2.  Download and install the version for your operating system (Windows, macOS, or Linux).

## 2. Web Browser

You already have one! Modern browsers like Chrome, Firefox, Edge, or Safari can run JavaScript and have built-in "Developer Tools" for testing and debugging.

**Developer Tools (DevTools):**
-   **How to open:** Right-click on any webpage and select "Inspect" or "Inspect Element", or press the F12 key.
-   **Console Tab:** This is where you'll see output from your JavaScript code (`console.log`) and can type simple JavaScript commands to test things.

## 3. Node.js (Optional but Recommended)

While not strictly needed for basic browser JavaScript, Node.js is essential for modern JavaScript development tools and running JS outside the browser. It also includes **npm** (Node Package Manager) for installing helpful code packages.

**Installation:**
1.  Go to [nodejs.org](https://nodejs.org/).
2.  Download and install the **LTS (Long Term Support)** version.
3.  Verify installation by opening your terminal (Command Prompt, PowerShell, or Terminal) and typing:
    ```bash
    node -v
    npm -v
    ```
    You should see version numbers printed.

## Creating Your First Project Folder

1.  Create a new folder on your computer where you'll keep your course files (e.g., `javascript-course`).
2.  Open this folder in VS Code:
    *   Launch VS Code.
    *   Go to `File > Open Folder...` and select the folder you created.

## Writing Your First Code

1.  **Create HTML File:** In VS Code's Explorer panel (left side), click the "New File" icon and name it `index.html`.
    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>My First JS Page</title>
    </head>
    <body>
        <h1>Check the Console!</h1>
        
        <!-- Link to your JavaScript file -->
        <script src="script.js"></script> 
    </body>
    </html>
    ```
2.  **Create JavaScript File:** Create another new file named `script.js`.
    ```javascript
    // This is your first JavaScript code!
    console.log("Hello from script.js!");
    
    let message = "JavaScript is fun!";
    console.log(message);
    ```

## Running Your Code

1.  Save both `index.html` and `script.js` in VS Code.
2.  Find the `index.html` file on your computer using your file explorer.
3.  Double-click `index.html` to open it in your web browser.
4.  Open the browser's Developer Tools (F12 or right-click > Inspect).
5.  Go to the **Console** tab. You should see:
    ```
    Hello from script.js!
    JavaScript is fun!
    ```

## Conclusion

You now have a basic setup to write and run JavaScript code in the browser. You can create HTML files, link JS files using the `<script>` tag, and see output in the browser console.

Next, we'll learn about variables and data types in JavaScript.

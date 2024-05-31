# Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in your browser.

The page will reload when you make changes.\
You may also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can't go back!**

If you aren't satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you're on your own.

You don't have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn't feel obligated to use this feature. However we understand that this tool wouldn't be useful if you couldn't customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

### Code Splitting

This section has moved here: [https://facebook.github.io/create-react-app/docs/code-splitting](https://facebook.github.io/create-react-app/docs/code-splitting)

### Analyzing the Bundle Size

This section has moved here: [https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size](https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size)

### Making a Progressive Web App

This section has moved here: [https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app](https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app)

### Advanced Configuration

This section has moved here: [https://facebook.github.io/create-react-app/docs/advanced-configuration](https://facebook.github.io/create-react-app/docs/advanced-configuration)

### Deployment

This section has moved here: [https://facebook.github.io/create-react-app/docs/deployment](https://facebook.github.io/create-react-app/docs/deployment)

### `npm run build` fails to minify

This section has moved here: [https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify](https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify)



# from https://medium.com/@deeksharungta/how-to-build-your-own-ai-chatbot-with-open-ai-api-cb779b03bae2

App.js

import React, { useState } from "react";
import "./App.css";

const App = () => {
  const API_KEY = process.env.REACT_APP_OPEN_API_KEY; // Make sure your environment variable is prefixed with REACT_APP

  // Setting the primary prompt as the initial state
  const [messages, setMessages] = useState([
    {
      role: "system",
      content: "Welcome! How can I help you today?",
    },
  ]);

  const [isTyping, setIsTyping] = useState(false);

  const chatData = async (userMessage) => {
    try {
      const response = await fetch(
        "https://api.openai.com/v1/chat/completions",
        {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
            Authorization: `Bearer ${API_KEY}`,
          },
          body: JSON.stringify({
            model: "gpt-3.5-turbo",
            messages: [...messages, { role: "user", content: userMessage }],
            temperature: 0.7,
          }),
        }
      );

      if (!response.ok) {
        throw new Error("Oops! Something went wrong while processing your request.");
      }

      const responseData = await response.json();
      setMessages((prevMessages) => [
        ...prevMessages,
        { role: "assistant", content: responseData.choices[0].message.content },
      ]);
    } catch (error) {
      console.error("Error while fetching chat data:", error);
    } finally {
      setIsTyping(false);
    }
  };

  const handleSendMessage = (messageContent) => {
    setMessages((prevMessages) => [
      ...prevMessages,
      { role: "user", content: messageContent },
    ]);
    setIsTyping(true);
    chatData(messageContent);
  };

  return (
    <>
      <div>
        {messages.map((message, index) => (
          <div key={index}>
            <h3>{message.role}</h3>
            <p>{message.content}</p>
          </div>
        ))}
        {isTyping && <p>Bot is typing...</p>}
      </div>
      <form
        onSubmit={(e) => {
          e.preventDefault();
          const input = e.target.input.value;
          if (input.trim() !== "") {
            handleSendMessage(input);
            e.target.reset();
          }
        }}
      >
        <input
          type="text"
          name="input"
          placeholder="Type your message..."
          disabled={isTyping}
        />
        <button type="submit" disabled={isTyping}>
          Send
        </button>
      </form>
    </>
  );
}

export default App;

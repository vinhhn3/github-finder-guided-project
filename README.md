# Guided Project: Github Finder

## What does it do?

It searches for all the Github users using the text inputted by the user in the form. It returns all the users matching the string in a grid format. Clicking on the `more` tab takes the user to a custom made page with the vital stats.

## Archiecture Overall

![alt text](image.png)

In this project, we will create a React App and use GitHub API to search information then display it

![alt text](demo.gif)

We will use following endpoints from GitHub API to make requests

```
GET https://api.github.com/users/                 # Get list of Users
GET https://api.github.com/users/:username        # Get user information by name
GET https://api.github.com/users/:username/repos  # Get repos information by name
GET https://api.github.com/search/users?q=search  # Search users
```

## Setup CSS

To speed up the development time, we use the provided `App.css` on the file `/src/App.css`.

This will contains all needed styling

## Setup Folder Structure

Now, folder structure is very crucial. You will setup the folder structure like this.

![alt text](image-1.png)

## Create Navbar Component

Now, it's time to create our first component. We will create the `Navbar` component then place it on `src/components/layout`

```js
// /src/components/layout/Navbar.js
import React from "react";

const Navbar = () => {
  return (
    <nav className="navbar bg-success">
      <h1>
        <i className="fab fa-github" /> GitHub Finder
      </h1>
      <ul>
        <li>
          <a href="#>">Home</a>
          <a href="#>">About</a>
        </li>
      </ul>
    </nav>
  );
};

export default Navbar;
```

Then, update the `App.js` to display the Navbar

```js
import "./App.css";
import Navbar from "./components/layout/Navbar";

function App() {
  return (
    <div className="App">
      <Navbar />
      <div className="container">
        <h1>Hello from React</h1>
      </div>
    </div>
  );
}

export default App;
```

This is the result
![alt text](image-2.png)

## Use `useEffect` to fetch data with `axios`

## Create `Users` and `UserItem` component to display all information

## Create Search Component

##

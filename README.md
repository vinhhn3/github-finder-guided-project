# Guided Project: Github Finder

## What does it do?

It searches for all the Github users using the text inputted by the user in the form. It returns all the users matching the string in a grid format. Clicking on the `more` tab takes the user to a custom made page with the vital stats.

## Archiecture Overall

![alt text](image.png)

In this project, we will create a React App and use GitHub API to search information then display it

![alt text](demo.gif)

We will use following endpoints from GitHub API to make requests

```bash
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

To fetch data, we will use `axios` to make request.

Use the following command to install it

```bash
npm install axios
```

Great. We will update the `App.js` file to fetch the data then display on the console first.

```js
import axios from "axios";
import { useEffect } from "react";
import "./App.css";
import Navbar from "./components/layout/Navbar";

function App() {
  // Use the 'useEffect' hook to perform side effects in function components
  useEffect(() => {
    // Define an asynchronous function 'fetchData' to fetch data from the GitHub API
    const fetchData = async () => {
      try {
        // Use the 'axios' library to make a GET request to the GitHub API endpoint
        const response = await axios.get("https://api.github.com/users");

        // Log the fetched data to the console
        console.log("GitHub Users:", response.data);
      } catch (error) {
        // Log an error message if there's an issue fetching data
        console.error("Error fetching data:", error);
      }
    };

    // Call the 'fetchData' function when the component mounts
    fetchData();
  }, []); // The empty dependency array ensures that 'useEffect' runs only once when the component mounts
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

Now, we you reload the app, you can see the data being fetch

![alt text](fetch_api.gif)

You may wonder why there is 2 response. It's because of StrictMode. Just remove it on `index.js` to correct the behavior.

```js
// index.js
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>    # Remove this line
    <App />
  </React.StrictMode>   # Remove this line
);
```

## Create `Users` and `UserItem` component to display all information

Now, we will create `Users` and `UserItem` to display all data.

First, we will create the `UserItem` first.

```js
// /src/components/users/UserItem.js
import React from "react";

const UserItem = (props) => {
  const { login, avatar_url, html_url } = props.user;

  return (
    <div className="card text-center">
      <img
        src={avatar_url}
        alt=""
        className="round-img"
        style={{ width: "60px" }}
      />
      <h3>{login}</h3>
      <div>
        <a href="#" className="btn btn-dark btn-sm my-1">
          More
        </a>
      </div>
    </div>
  );
};

export default UserItem;
```

Then, create `Users` components.

```js
// Users.js
import React from "react";
import UserItem from "./UserItem";

const Users = (props) => {
  const { users } = props;
  return (
    <div style={userStyle}>
      {users.map((user) => (
        <UserItem key={user.id} user={user} />
      ))}
    </div>
  );
};

const userStyle = {
  display: "grid",
  gridTemplateColumns: "repeat(3,1fr)",
  gridGap: "1rem",
};

export default Users;
```

Now, we will update the `App.js` file to user `Users` component

```js
// App.js
const App = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await axios.get("https://api.github.com/users");
        setUsers(response.data);
      } catch (error) {
        console.error("Error fetching data:", error);
      }
    };

    fetchData();
  }, []);

  return (
    <div className="App">
      <Navbar />
      <div className="container">
        <h1>GitHub Users Data</h1>
      </div>
      <Users users={users} />
    </div>
  );
};

export default App;
```

Reload the app to see the result
![alt text](image-3.png)

By doing the previous guided, you know how to

- Make API request with `axios` then handle the response
- Use `useEffect` and see the react life cycle

## Create Search Component

Now, we will create the Search component to handle the input then fetch the api.

Steps will be:

1. User will input to `Search` component then submit
2. Fetch the data
3. Display the response with `Users` component

### Create the `Search` component

You will create it on `/src/components/users`

```js
// Search.js
import axios from "axios";
import React, { useState } from "react";
import Users from "./Users";

const Search = () => {
  const [text, setText] = useState("");
  const [users, setUsers] = useState([]);

  const searchUsers = async (text) => {
    try {
      const response = await axios.get(
        `https://api.github.com/search/users?q=${text} `
      );
      setUsers(response.data.items);
    } catch (error) {
      console.error("Error fetching data:", error);
    }
  };

  const onSubmit = (e) => {
    e.preventDefault();
    if (text === "") {
      alert("Please enter something");
    } else {
      searchUsers(text);
      setText("");
    }
  };

  const onChange = (e) => setText(e.target.value);

  return (
    <div>
      <form onSubmit={onSubmit} className="form">
        <input
          type="text"
          name="text"
          placeholder="Search User"
          value={text}
          onChange={onChange}
        />
        <input
          type="submit"
          value="Search"
          className="btn btn-success btn-block"
        />
      </form>
      <Users users={users} />
    </div>
  );
};

export default Search;
```

After that, update the `App.js` to use the `Search`

```js
// App.js
import React from "react";
import "./App.css";
import Navbar from "./components/layout/Navbar";
import Search from "./components/users/Search";

const App = () => {
  return (
    <div className="App">
      <Navbar />
      <div className="container">
        <h1>GitHub Users Data</h1>
        <Search />
      </div>
    </div>
  );
};

export default App;
```

You can see the application now has a search input then it will display the search result for us.

![alt text](demo_search.gif)

Now, we will add a Clear button to clear all search results

```js
// Search.js
import axios from "axios";
import React, { useState } from "react";
import Users from "./Users";

const Search = () => {
  const [text, setText] = useState("");
  const [users, setUsers] = useState([]);

  const searchUsers = async (text) => {
    // .. Rest of the code
  };

  const clearUsers = () => {
    setUsers([]);
  };

  const onSubmit = (e) => {
    // ... rest of the code
  };

  const onChange = (e) => setText(e.target.value);

  return (
    <div>
      <form onSubmit={onSubmit} className="form">
        {/*<!..> ... The rest of the form */}
      </form>
      {/*Adding Clear button */}
      {users.length > 0 && (
        <button className="btn btn-danger btn-block" onClick={clearUsers}>
          Clear
        </button>
      )}
      <Users users={users} />
    </div>
  );
};

export default Search;
```

![alt text](demo_search_1.gif)

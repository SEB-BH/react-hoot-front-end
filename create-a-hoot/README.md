<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Create a Hoot</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to build a component for creating new hoots.

## Overview

In this lesson, we'll implement the following user story:

> 👤 As a user, I should be able to create a hoot post.

This will require a `<form>` component that allows users to create new hoots. Upon submitting a new hoot, the user should be redirected back to the 'List' page.

We'll make a `POST` request to our back-end server to create a hoot. When a request is made, we'll use the response to update the `hoots` state held in the `App` component. This data will then flow down to the `HootList` component, where we will be able to see our newly added hoot.

## Scaffold the component

1. First, let's add a new link to the `NavBar` component. It should direct users to `/hoots/new`.

   ```jsx
   // src/components/Nav.jsx

    return (
        <nav>
            <Link className="nav-brand" to="/">Hoot</Link>
            { props.user ? (
                <ul>
                    <li>Welcome, {props.user.username}!</li>
                    <li><Link to='/hoots'>HOOTS</Link></li>
                    <li><Link to='/hoots/new'>NEW HOOT</Link></li>
                    <li><Link to="/" onClick={handleSignOut}>Sign Out</Link></li>
                </ul>
            ) : (
            <ul>
                <li><Link to='/'>Home</Link></li>
                <li><Link to='/sign-up'>Sign Up</Link></li>
                <li><Link to='/sign-in'>Sign In</Link></li>
            </ul>
            ) }
        </nav>
    )
   ```

2. Next, add a corresponding route to `App.jsx`. We don't have a component to render yet, but that's ok.

   If you ever wish to test out your client-side routes *before* creating the component, you can define the `<Route>` and render a single element, like in the example below:

   ```jsx
   <Route path='/hoots/new' element={<h1>New Hoot</h1>} />
   ```

   > 💡 Notice how we use RESTful/resourceful routing conventions in our client-side routes. This isn't a requirement, but sticking to familiar conventions can be helpful when collaborating with other developers.

3. Next, create the component.

   Run the following commands in your terminal:

   ```bash
   touch src/pages/HootForm.jsx
   ```

   Add the following to `src/pages/HootForm.jsx`:

```jsx
// src/pages/HootForm.jsx

import { useState } from 'react'

const HootForm = (props) => {
  const initialState = {
        title: '',
        text: '',
        category: 'News',
    }
  const [formData, setFormData] = useState(initialState)

  const handleChange = (evt) => {
    setFormData({ ...formData, [evt.target.name]: evt.target.value })
  }

  const handleSubmit = (evt) => {
    evt.preventDefault()
    console.log('formData', formData)
    // We'll update this function shortly...
  }

  return (
    <main className='card'>
      <form onSubmit={handleSubmit}>
        <label htmlFor='title-input'>Title</label>
        <input
          required
          type='text'
          name='title'
          id='title-input'
          value={formData.title}
          onChange={handleChange}
        />
        <label htmlFor='text-input'>Text</label>
        <textarea
          required
          type='text'
          name='text'
          id='text-input'
          value={formData.text}
          onChange={handleChange}
        />
        <label htmlFor='category-input'>Category</label>
        <select
          required
          name='category'
          id='category-input'
          value={formData.category}
          onChange={handleChange}
        >
          <option value='News'>News</option>
          <option value='Sports'>Sports</option>
          <option value='Games'>Games</option>
          <option value='Movies'>Movies</option>
          <option value='Music'>Music</option>
          <option value='Television'>Television</option>
          <option value='Other'>Other</option>
        </select>
        <button type='submit'>SUBMIT</button>
      </form>
    </main>
  )
}

export default HootForm
   ```

   This component is similar to other forms you've seen in React, but let's take a closer look at the `<select>` tag:

   - The `<select>` element creates dropdown menus in React. Its `value` is controlled by the `formData.category` state, meaning it updates automatically when the state changes.
   - Each `<option>` tag has a predefined `value` attribute (for example, `'News'`, `'Games'`). The initial value of the dropdown is set by the default `category` value in the `formData` state.
   - If your back-end uses an `enum` constraint in the database schema for this field, ensure the `value` attributes on the `<option>` tags match the values defined in your schema. This consistency prevents errors when submitting the form.

   > ❓ Notice our `handleSubmit` function. Why do we need [e.preventDefault()](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault) when we submit a `<form>` in React? What default behavior are we preventing [here](https://react.dev/learn/responding-to-events#preventing-default-behavior)?

   If you're using the Auth Template css, add `textarea` and `select` to the `form input` rules in `App.css`:

   ```css
   form input, textarea, select {
    padding: 12px;
    border-radius: 8px;
    border: 1px solid var(--color-primary);
  }

    form input:focus, textarea:focus, select:focus {
    border-color: var(--color-primary);
    outline: 2px solid var(--color-primary-soft);
  }
  ```

4. Let's import the new `HootForm` component into the `App` component:

   ```jsx
   // src/App.jsx

   import HootForm from './pages/HootForm';
   ```

5. Update your route in `App.jsx` to render the new `HootForm` component.

   ```jsx
   <Route path='/hoots/new' element={<HootForm />} />
   ```

Take a moment to verify that you can successfully change the `formData` state. When you submit the form, you should only see a `console.log` of state, as we have not yet built the logic to create a new hoot.

## Build the `handleAddHoot()` function

To make our form fully functional, we'll need to build out a `handleAddHoot()` function.

First, let's import the `useNavigate()` hook from `react-router`. This will allow us to redirect a user back to the hoot list page after submitting a new hoot.

Import `useNavigate` at the top of `src/App.jsx`:

```jsx
// src/App.jsx

import { Routes, Route, useNavigate } from 'react-router';
```

Next, create a new instance of the `useNavigate()` hook within the component function:

```jsx
// src/App.jsx

const navigate = useNavigate();
```

Add the following function:

```jsx
// src/App.jsx

  const handleAddHoot = async (formData) => {
    console.log('formData: ', formData)
    navigate('/hoots')
  }
```

At this point, we'll just confirm that the `hootFormData` is passed to the function and that `useNavigate()` is functioning correctly.

With the function in place, update your route by passing the new handler function to the `HootForm` component:

```jsx
// src/App.jsx

            <Route 
              path='/hoots/new' 
              element={<HootForm handleAddHoot={handleAddHoot} />}
            />
```

Now that we are passing down `handleAddHoot()` as props, we can finish building out the `handleSubmit()` function in `HootForm.jsx`:

```jsx
// src/pages/HootForm.jsx

  const handleSubmit = (evt) => {
    evt.preventDefault()
    props.handleAddHoot(formData)
  }
```

> 🚨 Be sure to pass in `formData` state when calling `handleAddHoot()`.

### Test the form

Verify that the `hootFormData` is being passed up the component tree to the `App` component correctly. When you submit the form, you should see a `console.log()` from the `handleAddHoot()` function and be redirected to the hoot list page.

## Build the service function

Now, let's create the `create()` service function, which uses a `POST` request. Unlike `GET` requests, `POST` requests with the Fetch API require additional properties:

- **`method`**: Specifies the HTTP method. For `POST` requests, this must be explicitly set.
- **`body`**: Contains the form data, converted to JSON using `JSON.stringify()`. Learn more about the [JSON object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON).
- **`'Content-Type'`**: Specifies the data type in the `headers` as `'application/json'`, making sure the server correctly interprets the request body.

Let's add the service:

```javascript
// src/services/hoots.js

const create = async (hootFormData) => {
  try {
    const res = await fetch(BASE_URL, {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${localStorage.getItem('token')}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(hootFormData),
    })
    return res.json()
  } catch (error) {
    console.log(error)
  }
}

export { 
  index,
  show,
  create,
}
```

## Call the service

Back in `src/App.jsx`, update `handleAddHoot()` with the service function:

```jsx
// src/App.jsx

  const handleAddHoot = async (formData) => {
    const newHoot = await hootService.create(formData)
    setHoots([newHoot, ...hoots])
    navigate('/hoots')
  }
```

> Notice how when we `setHoots()`, the `newHoot` is added to the **front of the array**, ensuring it appears at the top of the page. This matches the behavior of our `index()` function, which retrieves `hoots` in descending order (newest first). Adding `newHoot` to the end would disrupt this order when the page refreshes, as the `index()` service re-fetches the data.

Test the form in your browser. You should now be able to add new hoots!

<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Build the Hoot Details Component</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to build a component that renders details on a specific hoot.

## Overview

In this lesson, we'll implement the following user story:

> 👤 As a user, clicking on a hoot in the 'List' page should navigate me to a hoot's 'Details' page to view information about a single hoot post and its associated comments.

Our 'Details' page will be represented by a new `HootDetails` component. This component will be responsible for rendering the details of a single hoot, including its associated comments. This component will be displayed whenever a user clicks on a hoot from the 'List' page.

Rendering details on a specific hoot will require a new service function to `fetch` a single `hoot` from our back-end app. For the service function to work, we'll need to provide it with a `hoot._id` so it can make the request to the correct route.

Our `HootDetails` component will hold `hoot` state locally. This differs from the `HootList` component, which receives the data it displays (`hoots`) as a prop.

## Scaffold the `HootDetails` component

Let's build out the scaffolding for our new component. Run the following commands in your terminal:

```bash
touch src/pages/HootDetails.jsx
```

Add the following to the new `HootDetails` component:

```jsx
// src/pages/HootDetails.jsx

const HootDetails = () => {
  return <main>Hoot Details</main>
}

export default HootDetails
```

Next, import the `HootDetails` component in the `App` component:

```jsx
// src/App.jsx

import HootDetails from './pages/HootDetails';
```

Then create the new indicated **protected** route below:

```jsx
// src/App.jsx

      {user ? (
          <>
            <Route path='/hoots' element={<HootList hoots={hoots} />} />
            {/* new protected hoot details route */}
            <Route path='/hoots/:hootId' element={<HootDetails />} />
          </>
        ) : (
          <>
            <Route path='/sign-up' element={<SignUpForm setUser={setUser} />} />
            <Route path='/sign-in' element={<SignInForm setUser={setUser} />} />
          </>
        )}
```

With the addition of this client-side route, users should now be able to navigate to the `HootDetails` page by clicking on a hoot from the list page.

## Add `useParams`

When a user navigates to the `HootDetails` page, we'll need to `fetch()` details on that hoot. An individual hoot can be identified by its `ObjectId`, with this value being accessible through the `hootId` parameter as defined in the `<Route>` above.

If `hootId` is our **parameter**, you might wonder where we passed in our **argument**. Recall the `<Link>` we wrapped around our hoot cards. We gave it a `to` property set to `/hoots/${hoot._id}`.

This is where we passed in the actual `_id` of the hoot:

```jsx
// src/components/HootList/HootList.jsx

<Link key={hoot._id} to={`/hoots/${hoot._id}`}>
```

To extract this value for use in our component, we'll use the `useParams()` hook.

Add the following import to the `HootDetails` component:

```jsx
// src/pages/HootDetails.jsx

import { useParams } from 'react-router';
```

Next, let's call `useParams()` to get access to the `hootId`:

```jsx
// src/pages/HootDetails.jsx

import { useParams } from "react-router"

const HootDetails = () => {
    const { hootId } = useParams()
    console.log('hootId: ', hootId)

    return <main>Hoot Details</main>
}

export default HootDetails
```

> 💡 Be sure to [destructure](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) the `hootId` when calling `useParams()`!

Confirm that you can access the `hootId` in the `HootDetails` component.

Now that we have the `hootId`, we should be able to retrieve details for that hoot from the back-end app using a new service function.

## Build the service function

Once again, our service function will require an Authorization header.

Add the following to `src/services/hootService.js`:

```js
// src/services/hoots.js

const show = async (hootId) => {
  try {
    const res = await fetch(`${BASE_URL}/${hootId}`, {
      headers: { Authorization: `Bearer ${localStorage.getItem('token')}` },
    })
    return res.json()
  } catch (error) {
    console.log(error)
  }
}

export {
  index,
  // Don't forget to export:
  show,
}
```

> ❓ Let's take a moment to connect the dots of our application. Notice the `hootId` in the above service function. Where will this information be used in the back-end app?

## Call the service

Next up, we'll call the service, and store the response from the server in state.

We'll need to add a few imports to the `HootDetails` component to proceed:

```jsx
// src/pages/HootDetails.jsx

import { useParams } from "react-router"
import * as hootService from '../services/hoots'
```

Create a new `useState()` variable called `hoot` with an initial value of `null`:

```jsx
// src/pages/HootDetails.jsx
import { useState } from "react"

const [hoot, setHoot] = useState(null)
```

> 💡 Giving the `hoot` state an initial value of `null` will simplify some conditional rendering that we will implement shortly.

And add the following `useEffect()`:

```jsx
// src/pages/HootDetails.jsx
import { useState, useEffect } from "react"

useEffect(() => {
    const fetchHoot = async () => {
        const hootData = await hootService.show(hootId)
        setHoot(hootData)
    }
    fetchHoot()
}, [hootId])

  // Verify the hoot state is set correctly:
  console.log('hoot state:', hoot)
```

> 💡 Remember to include `hootId` in the [dependency array](https://react.dev/reference/react/useEffect#specifying-reactive-dependencies) of your `useEffect()`. This tells the `useEffect()` to fire off whenever the value of the `hootId` changes.

Take a moment to confirm you've set the `hoot` state correctly. You should notice that the `author` property of a `hoot` is being populated.

## Render hoot details

If you included the `console.log()` in the step above, you might notice that the `hoot` state is `null` when the component first mounts. This can cause issues if we try to render data that is not there. Let's add a condition to account for that.

Add the following directly above your existing `return`:

```jsx
// src/pages/HootDetails.jsx

  if (!hoot) return <main>Loading...</main>
```

Consider making a spinner with [this generator](https://www.w3schools.com/tools/tool_css_loader.php). Replace `Loading...` with this html element: `<div class="loader"></div>` and add the gnerated `css` to `App.css`.

With our condition in place, let's build out the remaining JSX:

```jsx
// src/pages/HootDetails.jsx

    return (
        <article className="card hoot-card">
            <header className="hoot-header">
                <span className="hoot-category">{hoot.category}</span>
                <h2>{hoot.title}</h2>
                <p className="hoot-author">Posted by {hoot.author?.username || 'Unknown user'} on <span>{new Date(hoot.createdAt).toLocaleDateString()}</span></p>
            </header>
            <p className="hoot-text">{hoot.text}</p>
            <footer className="hoot-footer">
              {/* comments go here */}
            </footer>
        </article>
    )
```

Notice the `<section>` tag at the bottom. This will act as our 'Comments' section. The `commentSchema` is embedded within `hootSchema`, so the relevant `comment` data should already exist within this component's `hoot` state.

## Display comments

To display the comments associated with a hoot, we'll want to `map()` over `hoot.comments` and produce a list of `<article>` tags.

Each comment's `<article>` tag should include a few things:

- The `username` of the comment's `author`.
- The `createdAt` date property of the `comment`.
- The `text` content of the `comment`.

Regarding the `author` property of a `comment`, you might recall that our `show` controller on the back-end is already populating the `author` information for each `comment`:

```javascript
// controllers/hoots.js

const hoot = await Hoot.findById(req.params.hootId).populate([
  'author',
  'comments.author',
]);
```

In our comments section, we'll also want to include a condition that displays a message if there are not yet any `comments` embedded within the `hoot`.

Update `src/pages/HootDetails.jsx` with the following:

```jsx
// src/pages/HootDetails.jsx

      {/* All updates are in the comments section! */}
      <section>
        <h2>Comments</h2>

        {!hoot.comments.length && <p>There are no comments.</p>}

        {hoot.comments.map((comment) => (
          <article key={comment._id}>
            <header>
              <p>{`${comment.author.username} posted on ${new Date(comment.createdAt).toLocaleDateString()}`}</p>
            </header>
            <p>{comment.text}</p>
          </article>
        ))}
      </section>
```

Check your browser. If you have any existing comments associated with a hoot, you should be able to see them now.

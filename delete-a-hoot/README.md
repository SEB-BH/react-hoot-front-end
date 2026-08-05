<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Delete a Hoot</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to implement the functionality for deleting a hoot.

## Overview

In this lesson, we'll implement the following user story:

> 👤 As the author of a hoot, I should see a button to 'Delete' a hoot on the 'Details' page. Clicking the button should delete the hoot and redirect me back to the 'List' page.

When implementing delete functionality, it's important to ensure that **only the author of a given resource can delete it**. Our application should take measures to prevent users from deleting hoots that do not belong to them.

We should address this need in both the back-end and front-end. We've already included a check for this in our **back-end** app:

```javascript
// controllers/hoots.js

if (!hoot.author._id.equals(req.user._id)) {
  return res.status(403).send("You're not allowed to do that!");
}
```

In this lesson, we will focus on restricting access in the **front-end** app.

Based on our user story, we'll need to **conditionally render the delete button based on the author of the hoot**. We can accomplish this using the `UserContext`. This makes the signed-in `user` object easily accessible throughout our component tree. We'll make use of this `user` object when we render the delete button in the `HootDetails` component.

# Build the UI

Because the `user` state is defined in `App.jsx`, we need to pass it to the `HootDetails` component as a prop.

1. In `App.jsx`, find the route that renders `HootDetails` and pass the `user` state to it:

   ```jsx
   // src/App.jsx

   <Route
     path="/hoots/:hootId"
     element={<HootDetails user={user} />}
   />
   ```

   The `HootDetails` component can now access the signed-in user through its props.

2. Update the `HootDetails` component to accept `props`:

   ```jsx
   // src/pages/HootDetails.jsx

  import { useParams } from "react-router"
  import * as hootService from '../services/hoots'
  import { useState, useEffect } from "react"
  import CommentForm from "../components/CommentForm"
  import * as commentsService from '../services/comments'

   const HootDetails = (props) => {
     const { hootId } = useParams()
     const [hoot, setHoot] = useState(null)

     // useEffect, handleAddComment, and return statements here
   }

   export default HootDetails
   ```

## Conditionally render the author controls

Next, add conditional rendering for the edit and delete controls.

For our conditional rendering, we'll use the [Logical AND (`&&`)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND) operator.

```jsx
{props.user && hoot.author._id === props.user._id && (
  <div>
    {/* Edit and delete controls */}
  </div>
)}
```

This condition checks two things:

1. A user is currently signed in.
2. The signed-in user's `_id` matches the hoot author's `_id`.

When both conditions are true, the edit and delete controls will be displayed. Otherwise, this part of the UI will not be rendered.

This means only the author of a particular hoot will see the controls used to update or delete it.

3. Modify the contents of the `<header>` in the main return for the `HootDetails` component to conditionally render the delete button:

   ```jsx
   // src/pages/HootDetails.jsx

            <header className="hoot-header">
                <span className="hoot-category">{hoot.category.toUpperCase()}</span>
                <h2>{hoot.title}</h2>
                <p className="hoot-author">Posted by {hoot.author?.username || 'Unknown user'} on <span>{new Date(hoot.createdAt).toLocaleDateString()}</span></p>
                {hoot.author._id === props.user._id && (
                 <>
                   <button>Delete</button>
                 </>
               )}
            </header>
   ```

   > 💡 Notice the use of a React fragment (`<> </>`) here. While we don't need a fragment now, we'll add another element alongside the delete button soon.

## Build the `handleDeleteHoot()` function

1. Stub up the `handleDeleteHoot()` function in the `App` component:

   ```jsx
   // src/App.jsx

  const handleDeleteHoot = async (hootId) => {
    console.log('hootId: ', hootId)
  }
   ```

2. Next, pass the function down to `HootDetails`:

```jsx
// src/App.jsx

<Route path='/hoots/:hootId' element={<HootDetails user={user} handleDeleteHoot={handleDeleteHoot} />} />
```

3. In the `HootDetails` component, let's update the delete button we added earlier. We'll attach an `onClick` event handler that triggers the `props.handleDeleteHoot(hootId)` function when the button is clicked.

   Update your button with the following:

   ```jsx
   // src/pages/HootDetails.jsx

           {hoot.author._id === user._id && (
               {/* Modify the button */}
                 <>
                   <button onClick={() => props.handleDeleteHoot(hootId)}>Delete</button>
                 </>
           )}
   ```

   > 🚨 Be sure to pass in `hootId` as an argument when you call the function. We won't know which hoot to delete without it.

4. In your browser, try deleting a hoot. You should see a `console.log()` originating from `App.jsx` confirming that the `hootId` is being passed up the component tree.

5. With the `hootId` accessible in `handleDeleteHoot()`, let's confirm that we can `filter()` state using this value:

   ```jsx
   // src/App.jsx

  const handleDeleteHoot = async (hootId) => {
    setHoots(hoots.filter((hoot) => hoot._id !== hootId))
    navigate('/hoots')
  }
   ```

   > Remember, the [array's filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) method returns a shallow copy of the array, excluding all elements that do not pass the test implemented by the provided callback function.
   >
   > In the code block above, our `filter()` method returns only the `hoot` objects whose `_id` values **do not match** the `hootId`. This effectively excludes the hoot we want to delete from the array used to update state.

Try deleting a hoot. After clicking the delete button, you should be redirected to the list page where the hoot is no longer visible. However, if you refresh the browser, you'll see the hoot reappear. This happens because **we are currently only managing local state**.

No changes have been made to the database, so when the browser refreshes, `hootService.index()` runs again, loading hoots from the database.

Managing local state is useful for providing immediate visual updates. However, for changes to persist beyond the current session, we need to update both the local state **and** the database. We'll address this in the next step!

## Build the service function

Let's finish up our delete functionality by adding the `deleteHoot()` service function to the hoot service:

```javascript
// src/services/hoots.js

const deleteHoot = async (hootId) => {
  try {
    const res = await fetch(`${BASE_URL}/${hootId}`, {
      method: 'DELETE',
      headers: {
        Authorization: `Bearer ${localStorage.getItem('token')}`,
      },
    });
    return res.json();
  } catch (error) {
    console.log(error);
  }
};

export {
  index,
  show,
  create,
  // Add export:
  deleteHoot,
};
```

## Call the service

Now that we have our service function, we'll add it to `handleDeleteHoot()`, along with one other minor change.

In our back-end, you might recall that the delete hoot controller function responds with a `deletedHoot`:

```javascript
res.status(200).json(deletedHoot);
```

If we call `hootService.deleteHoot()`, what we get back is this `deletedHoot` object:

```jsx
const deletedHoot = await hootService.deleteHoot(hootId);
```

The `deletedHoot` object contains the `_id` (ObjectId) of the hoot removed from the database. With this in mind, when we use the `filter()` method inside `handleDeleteHoot()`, we can use `deletedHoot._id` instead of the current `hootId`.

This approach assures us that the deletion was successfully processed on the back-end and database *before* we update the front-end.

Back in `src/App.jsx`, update `handleDeleteHoot()` with the following:

```jsx
// src/App.jsx

  const handleDeleteHoot = async (hootId) => {
    const deletedHoot = await hootService.deleteHoot(hootId)
    setHoots(hoots.filter((hoot) => hoot._id !== hootId))
    navigate('/hoots')
  }
```

Try it out! You should now be able to delete hoots.

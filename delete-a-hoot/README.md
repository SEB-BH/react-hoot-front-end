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

## Build the UI

1. At the top of the `HootDetails` component, add imports for import `UserContext` and `useContext`:

   ```jsx
   // src/components/HootDetails/HootDetails.jsx

   // Add useContext to the existing import statement for react
   import { useState, useEffect, useContext } from 'react';
   import { useParams } from 'react-router';

   import CommentForm from '../CommentForm/CommentForm';

   import * as hootService from '../../services/hootService';

   // Import the UserContext
   import { UserContext } from '../../contexts/UserContext';
   ```

2. Within the component function, create the following `user` constant:

   ```jsx
   // src/components/HootDetails/HootDetails.jsx

   const HootDetails = () => {
     const { hootId } = useParams();
     // Access the user object from the UserContext
     const { user } = useContext(UserContext);
     const [hoot, setHoot] = useState(null);

     // useEffect, handleAddComment, and return statements here
   };
   ```

   Time to add some conditional rendering for our button.

   For our conditional rendering, we'll use the [Logical AND ( && )](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND) operator.

   If the `hoot.author._id` matches `user._id`, this piece of UI should be visible. If not, the UI should not be rendered. This means only the author of this particular `hoot` can access the UI to update or delete a `hoot`.

3. Modify the contents of the `<header>` in the main return for the `HootDetails` component to conditionally render the delete button:

   ```jsx
   // src/components/HootDetails/HootDetails.jsx

             <header>
               <p>{hoot.category.toUpperCase()}</p>
               <h1>{hoot.title}</h1>
               <p>
                 {`${hoot.author.username} posted on
                 ${new Date(hoot.createdAt).toLocaleDateString()}`}
               </p>
               {/* Add the following */}
               {hoot.author._id === user._id && (
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
     console.log('hootId', hootId);
   };
   ```

2. Next, pass the function down to `HootDetails`:

   ```jsx
   // src/App.jsx

               <Route 
                 path='/hoots/:hootId'
                 element={<HootDetails handleDeleteHoot={handleDeleteHoot}/>}
               />
   ```

3. The `HootDetails` component will need to receive the `handleDeleteHoot()` function as a prop. It currently doesn't have any props, so let's add the `props` parameter to the component function:

   ```jsx
   // src/components/HootDetails/HootDetails.jsx

   // The HootDetails component function needs to receive props
   const HootDetails = (props) => {
     // HootDetails function code here
   };
   ```

4. In the `HootDetails` component, let's update the delete button we added earlier. We'll attach an `onClick` event handler that triggers the `props.handleDeleteHoot(hootId)` function when the button is clicked.

   Update your button with the following:

   ```jsx
   // src/components/HootDetails/HootDetails.jsx

           {hoot.author._id === user._id && (
             <>
               {/* Modify the button */}
               <button onClick={() => props.handleDeleteHoot(hootId)}>
                 Delete
               </button>
             </>
           )}
   ```

   > 🚨 Be sure to pass in `hootId` as an argument when you call the function. We won't know which hoot to delete without it.

5. In your browser, try deleting a hoot. You should see a `console.log()` originating from `App.jsx` confirming that the `hootId` is being passed up the component tree.

6. With the `hootId` accessible in `handleDeleteHoot()`, let's confirm that we can `filter()` state using this value:

   ```jsx
   // src/App.jsx

   const handleDeleteHoot = async (hootId) => {
     console.log('hootId', hootId);
     setHoots(hoots.filter((hoot) => hoot._id !== hootId));
     navigate('/hoots');
   };
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
// src/services/hootService.js

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
  createComment,
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
    const deletedHoot = await hootService.deleteHoot(hootId);
    // Filter state using deletedHoot._id:
    setHoots(hoots.filter((hoot) => hoot._id !== deletedHoot._id));
    navigate('/hoots');
  };
```

Try it out! You should now be able to delete hoots.

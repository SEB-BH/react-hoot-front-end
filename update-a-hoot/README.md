<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Update a Hoot</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to implement the functionality for updating a hoot.

## Overview

In this lesson, we'll implement the following user story:

> As the author of a hoot, I should see a link to 'Edit' a hoot on the 'Details' page. Clicking on the link should direct me to an 'Edit' page where I can modify the hoot. Upon submitting the update, I should be redirected back to the 'Details' page.

This functionality will **not require a new component**, as we will refactor the existing `HootForm` component to handle both **create** and **update**.

To accomplish this, we'll use the `useParams()` hook. The `useParams()` hook allows us to read data held in the URL, and based on that data, we'll augment the content and functionality of our component.

## Build the UI

Before we modify our form, we'll add the 'Edit' button that directs a user to that page.

1. Add `useNavigate` to the existing `react-router` import in the `HootDetails` component:

```jsx
   // src/pages/HootDetails.jsx

import { useParams, useNavigate } from 'react-router'

const HootDetails = (props) => {
 // use the useNavigate hook
const navigate = useNavigate()

// rest of component below...
}
```

2. Next, add the edit`<button>` directly above the **Delete** `<button>`:

```jsx
   // src/pages/HootDetails.jsx

 <header>
    <p>{hoot.category.toUpperCase()}</p>
    <h1>{hoot.title}</h1>
    <p>
      {`${hoot.author.username} posted on
      ${new Date(hoot.createdAt).toLocaleDateString()}`}
    </p>
    {hoot.author._id === user._id && (
        <div className="actions">
        {/* add Edit button */}
           <button onClick={() => navigate(`/hoots/${hootId}/edit`)}>Edit</button>
           <button onClick={() => props.handleDeleteHoot(hootId)}>Delete</button>
        </div>
    )}
  </header>
```

   Take note of the value given to `navigate`; it will be important in the following steps:

   ```javascript
   `/hoots/${hootId}/edit`;
   ```

3. Add a new **protected** route in the `App` component:

```jsx
   // src/App.jsx

 <>
   <Route path='/hoots' element={<HootList hoots={hoots} />} />
   <Route path='/hoots/:hootId' element={<HootDetails user={user} handleDeleteHoot={handleDeleteHoot} />} />
   <Route path='/hoots/new' element={<HootForm handleAddHoot={handleAddHoot} />} />
   <Route path='/hoots/:hootId/edit' element={<HootForm />} />
 </>
```

In the next section, we'll access the value of this `hootId` parameter with the `useParams()` hook.

## Modify the `HootForm`

1. Head to the `HootForm` component and import `useParams` from `react-router`:

   ```jsx
   // src/pages/HootForm.jsx

   import { useParams } from 'react-router'
   ```

2. Within the component, call `useParams()` to access the `hootId`:

   ```jsx
   // src/pages/HootForm.jsx

   const HootForm = (props) => {
     // Destructure hootId from the useParams hook, and console log it
     const { hootId } = useParams()
     console.log(hootId)
     const [formData, setFormData] = useState({
       title: '',
       text: '',
       category: 'News',
     })

     // handleChange, handleSubmit, and return statement code here
   }
   ```

   > 💡 Test this now! Your console log should show that `hootId` is undefined when you navigate to the form using the `NEW HOOT` link. However, `hootId` will have a value when you navigate to the form using the **Edit** link on the `HootDetails` page.

3. We can also confirm this visually by adding an `<h1>` and a ternary to our JSX to change the heading based on the presence of a `hootId`:

   ```jsx
   // src/pages/HootForm.jsx

     return (
       <main className='card'>
         {/* Add a heading */}
         <h1>{hootId ? 'Edit Hoot' : 'New Hoot'}</h1>
         <form onSubmit={handleSubmit}>
           {/* Form labels and inputs */}
         </form>
       </main>
     );
   ```

Now, if you navigate between the 'Edit' and 'New' pages, you should notice the page's title change, even though we're using the same component for both actions.

This example demonstrates how we can modify other elements and behaviors of the component.

- If a `hootId` is present, we can assume the user has accessed the 'Edit' page, which requires update functionality.
- Otherwise, React should render the basic 'New' form and use the existing code to create a hoot.

## Set `formData` state

The first modification we'll make to the component's functionality relates to its initial state. If the user is updating a hoot, we should prefill the inputs of our form with any existing hoot details. This will require calling the `hootService.show()` service within the `HootForm` component.

1. At the top of the `HootForm` component, add imports for `hootService` and `useEffect`:

   ```jsx
   // src/pages/HootForm.jsx

   // Add useEffect to the existing import statement for react
   import { useState, useEffect } from 'react'
   import { useParams } from 'react-router'

   // Import the hootService's exports
   import * as hootService from '../services/hoots'
   ```

2. Add the following `useEffect()`

   ```jsx
   // src/pages/HootForm.jsx

     useEffect(() => {
       const fetchHoot = async () => {
         const hootData = await hootService.show(hootId)
         setFormData(hootData)
       }
       if (hootId) fetchHoot()
     }, [hootId])
   ```

   > 💡 Notice the `if` condition and the inclusion of `hootId` in the dependency array. If a `hootId` is present, we make a request to our server and use the `hootData` response to set the `formData` state. If there is no `hootId`, we leave the initial state of `formData` unchanged.

3. Take a moment to confirm that the initial state of `formData` is correctly set when editing a hoot.

4. You may have noticed a bug if you happened to click on the **NEW HOOT** link in the nav bar while editing a hoot. The heading changes as it should, but the form fields still have the details of the hoot you were editing. To fix this issue, we need to add a *cleanup function* to our `useEffect()`:

   ```jsx
   // src/pages/HootForm.jsx

     useEffect(() => {
       const fetchHoot = async () => {
         const hootData = await hootService.show(hootId);
         setFormData(hootData)
       }
       if (hootId) fetchHoot()

       // Add a cleanup function
       return () => setFormData(initialState)
     }, [hootId])
   ```

   > 💡 A cleanup function is a function that is returned from the `useEffect()` hook. The job of a cleanup function is to undo whatever the effect did.
   >
   > React will call the cleanup function when a component is removed from the DOM or immediately before the `useEffect()` is re-run (like when something in the dependency array changes).
   >
   > In this case, the cleanup function helps us by resetting the `formData` state when the `hootId` changes from having a value (when we're editing an existing hoot) to not having a value (when we create a new hoot).

## Build the `handleUpdateHoot()` function

1. Next, we'll add the `handleUpdateHoot()` function in the `App` component:

```jsx
   // src/App.jsx

  const handleUpdateHoot = async (hootId, formData) => {
    console.log('hootId: ', hootId)
    console.log('formData: ', formData)
    navigate(`/hoots/${hootId}`)
  }
```

   For now, we'll confirm that the function is receiving two pieces of data:

   - `hootId`
   - `hootFormData`

2. Next, pass the function down to the `<HootForm>`:

```jsx
   // src/App.jsx

  <>
   {/* Protected Routes (available only to signed-in users) */}
    <Route path='/hoots' element={<HootList hoots={hoots} />} />
    <Route path='/hoots/:hootId' element={<HootDetails user={user} handleDeleteHoot={handleDeleteHoot} />} />
    <Route path='/hoots/new' element={<HootForm handleAddHoot={handleAddHoot} />} />
      {/* Pass the new handleUpdateHoot function */}
    <Route path='/hoots/:hootId/edit' element={<HootForm handleUpdateHoot={handleUpdateHoot} />} />
  </>
```

   > 🚨 There are currently **two** routes rendering the `<HootForm>` in `src/App.jsx`. Be sure to pass `handleUpdateHoot` to the component being rendered for the `/hoots/:hootId/edit` route!

3. Back in the `HootForm` component, update the existing`handleSubmit()` function to use this new function when we're updating an existing hoot:

```jsx
   // src/pages/HootForm.jsx

  const handleSubmit = (evt) => {
    evt.preventDefault()
    if (hootId) {
        props.handleUpdateHoot(hootId, formData)
    } else {
        props.handleAddHoot(formData)
    }
  }
```

   > 💡 Once again, we are relying on the `hootId` to determine the behavior of our component. If a `hootId` is present, we call `props.handleUpdateHoot(hootId, formData)`. Otherwise, we call `props.handleAddHoot(formData)`

4. Submit the `edit` form and confirm that the necessary data is being passed up the component tree. On submit, you should see a `console.log()` originating from `App.jsx`.

## Build the service function

The following code is similar to what you've seen in previous parts of the lesson. However, our `update()` service function will differ slightly from the `create()` service function. It uses a `PUT` request and takes two parameters:

- The first parameter is `hootId`, which is used to identify the hoot to update.
- The second parameter contains the updated `hootFormData` for the hoot.

With all of this in mind, let's write the `update()` service function:

```javascript
// src/services/hoots.js

async function update(hootId, hootFormData) {
  try {
    const res = await fetch(`${BASE_URL}/${hootId}`, {
      method: 'PUT',
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
  deleteHoot,
  // As always, remember to export:
  update,
};
```

## Call the service

Next, we'll update the `handleUpdateHoot()` function in the `App` component with our service and set state accordingly.

```jsx
// src/App.jsx

  const handleUpdateHoot = async (hootId, formData) => {
    const updatedHoot = await hootService.update(hootId, formData)
    const updatedHootsList = hoots.map((hoot) => {
      return hootId === hoot._id ? updatedHoot : hoot
    })
    setHoots(updatedHootsList)
    navigate(`/hoots/${hootId}`)
  }
```

> 💡 This implementation of the [map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) method is a bit different from the mapping of JSX elements you've seen in React previously.

We use `map()` here to update a specific `hoot` in the `hoots` state array. Here's a breakdown of what is happening:

- `hootService.update()` returns the `updatedHoot` after sending the update request to the back-end.
- We use `map()` to iterate over the `hoots` array and check each `hoot` object.
  - If the `_id` of the current `hoot` matches the `hootId`, we replace it with the `updatedHoot`.
  - If the `_id` of the current `hoot` doesn't match, we return the original `hoot` object.

Through this process, we can update a single object held in `hoots` state while maintaining an accurate record of the remaining elements in the array.

> 🧠 If you are curious as to why something like the `splice()` method is not applicable here, check out React documentation on [updating arrays without mutation](https://react.dev/learn/updating-arrays-in-state#updating-arrays-without-mutation).

Try it out! After updating a hoot, you should be directed to the list page with the modified hoot information.

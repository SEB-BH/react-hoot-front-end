<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Create a Comment</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to build a component for creating comments embedded within a hoot.

## Overview

In this lesson, we'll implement the following user story:

> 👤 As a User, I should be able to add a comment on a hoot 'Details' page.

To do this, we'll create a new component: `CommentForm`. This component will work similarly to the one used for creating hoots, but with one key difference:

- `CommentForm` **will not be a standalone page**. Instead, it will be embedded as a child component inside the `HootDetails` component.

When adding a new comment, we'll update the `hoot` state directly since the `comments` array is stored within the `hoot` object.

This lesson will serve as a good example of how to handle creating an embedded resource in a React app.

## Scaffold the `CommentForm` component

Let's build out the scaffolding for our component.

1. Run the following commands in your terminal to create the new component file:

   ```bash
   mkdir src/components/CommentForm
   touch src/components/CommentForm/CommentForm.jsx
   ```

2. Add the following to the new `CommentForm` component you just created:

   ```jsx
   // src/components/CommentForm/CommentForm.jsx

   import { useState } from 'react';

   const CommentForm = (props) => {
     const [formData, setFormData] = useState({ text: '' });

     const handleChange = (evt) => {
       setFormData({ ...formData, [evt.target.name]: evt.target.value });
     };

     const handleSubmit = (evt) => {
       evt.preventDefault();
       // add handleAddComment
       setFormData({ text: '' });
     };

     return (
       <form onSubmit={handleSubmit}>
         <label htmlFor='text-input'>Your comment:</label>
         <textarea
           required
           type='text'
           name='text'
           id='text-input'
           value={formData.text}
           onChange={handleChange}
         />
         <button type='submit'>SUBMIT COMMENT</button>
       </form>
     );
   };

   export default CommentForm;

   ```

   > 💡 Notice how we reset `formData` in our `handleSubmit()` function. This is an important step, as we don't navigate the user away from this page when a new comment is submitted.

3. Next, import the component into the `HootDetails` component:

   ```jsx
   // src/components/HootDetails/HootDetails.jsx

   import CommentForm from '../CommentForm/CommentForm';
   ```

4. Add the component to the comments section as shown below:

   ```jsx
   // src/components/HootDetails/HootDetails.jsx

         <section>
           <h2>Comments</h2>
           {/* Make use of the CommentForm component */}
           <CommentForm />
           
           {!hoot.comments.length && <p>There are no comments.</p>}

           {hoot.comments.map((comment) => (
             <article key={comment._id}>
               <header>
                 <p>
                   {`${comment.author.username} posted on
                   ${new Date(comment.createdAt).toLocaleDateString()}`}
                 </p>
               </header>
               <p>{comment.text}</p>
             </article>
           ))}
         </section>
   ```

5. In your browser, verify that typing in the `CommentForm` updates the `formData` state correctly.

## Build the `handleAddComment()` function

Next, let's create a `handleAddComment()` function.

1. Add the new `handleAddComment()` function to the `HootDetails`:

   ```jsx
   // src/components/HootDetails/HootDetails.jsx

     const handleAddComment = async (commentFormData) => {
       console.log('commentFormData', commentFormData);
     };
   ```

2. With the new function in place, pass it down to the `<CommentForm />`:

   ```jsx
   // src/components/HootDetails/HootDetails.jsx

         <section>
           <h2>Comments</h2>
           {/* Pass the handleAddComment function to the CommentForm Component */}
           <CommentForm handleAddComment={handleAddComment}/>

           {!hoot.comments.length && <p>There are no comments.</p>}

           {hoot.comments.map((comment) => (
             <article key={comment._id}>
               <header>
                 <p>
                   {`${comment.author.username} posted on
                   ${new Date(comment.createdAt).toLocaleDateString()}`}
                 </p>
               </header>
               <p>{comment.text}</p>
             </article>
           ))}
         </section>
   ```

3. Return to the `CommentForm` component and update the `handleSubmit()` function by calling `props.handleAddComment(formData)`:

   ```jsx
   // src/components/CommentForm/CommentForm.jsx

     const handleSubmit = (evt) => {
       evt.preventDefault();
       props.handleAddComment(formData);
       setFormData({ text: '' });
     };
   ```

4. Confirm you are passing `formData` up to `src/components/HootDetails/HootDetails.jsx`. When you submit the comment form, you should see a `console.log()` originating from the `HootDetails` component.

## Build the service function

Time to build out the service function. Despite being another resource, our comment service functions will live inside `src/services/hootService.js`. This is because all of the endpoints for comments will share the same `BASE_URL` as hoots (`/hoots`). We'll append more specific endpoints to each comment service function as necessary.

Add the following to `src/services/hootService.js`:

```javascript
// src/services/hootService.js

const createComment = async (hootId, commentFormData) => {
  try {
    const res = await fetch(`${BASE_URL}/${hootId}/comments`, {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${localStorage.getItem('token')}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(commentFormData),
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
  // Don't forget to export:
  createComment,
};
```

## Call the service

With the service in place, we can update the `handleAddComment` function in `src/components/HootDetails/HootDetails.jsx` to call the service and set state:

```jsx
// src/components/HootDetails/HootDetails.jsx

  const handleAddComment = async (commentFormData) => {
    const newComment = await hootService.createComment(hootId, commentFormData);
    setHoot({ ...hoot, comments: [...hoot.comments, newComment] });
  };
```

There is a lot going on in the last line of this function with `setHoot()`.

Let's break it down:

- The `hoot` state stores a single `hoot` object. We only want to modify the `comments` property when we update it, leaving the rest of the object unchanged.

- Using the [spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax), we copy all existing properties of the `hoot` object: `{ ...hoot }`.

- Inside the `hoot` object, the `comments` property is an array of `comment` objects.

- To update it, we create a new array: `[...hoot.comments, newComment]`. This combines the existing `hoot.comments` array with the new comment at the end.

Let's look at it another way, with a step-by-step breakdown in code:

```jsx
// reset state: sets hoot to an empty object
setHoot({});

// update state: keeps all current properties of hoot state unchanged
setHoot({ ...hoot });

// update state: keeps all current properties and sets comments to an empty array
setHoot({ ...hoot, comments: [] });

// update state: keeps all current properties and copies the existing comments array
setHoot({ ...hoot, comments: [...hoot.comments] });

// update state: copies the existing comments and adds newComment at the end
setHoot({ ...hoot, comments: [...hoot.comments, newComment] });
```

Try it out in your browser. You should now be able to add comments using the form!

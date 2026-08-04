<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Style the Form Components</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to style form components with CSS modules.

## Style the `HootForm` component

Run the following command in your terminal:

```bash
touch src/components/HootForm/HootForm.module.css
```

Add the following to that new file:

```css
/* src/components/HootForm/HootForm.module.css */

.container {
  height: 100%;
  display: flex;
  overflow-x: hidden;
  overflow-y: scroll;
  padding: 28px 21px;
  align-items: center;
  flex-direction: column;
}

.container input, textarea, select, button { 
  width: 100%;
  padding: 14px;
  font-size: 14px;
  resize: vertical;
  border-radius: 5px;
  margin: 7px 0px 14px 0px;
  border: 1px solid var(--border);
}

.container textarea {
  min-height: 50px;
}

.container label {
  font-weight: 600;
  margin: 7px 0px 14px 0px;
}

.container button {
  color: white;
  font-weight: 600;
  margin: 21px 0px 0px 0px;
  background: var(--primary);
}

input:focus, select:focus, textarea:focus {
  outline: none;
  border: 1px solid var(--primary);
}
```

Here we have some fairly standard form styling, mostly focused on arranging things in a vertical layout and cleaning up the default appearance of several form elements. Let's touch on a few interesting details.

Notice the `resize` property being used here. This is useful when your forms use `textarea` tags. By setting `resize` to `vertical`, we can prevent the user from dragging a `textarea` out horizontally and maintain the general appearance of our form.

You'll also notice the [:focus pseudo-class](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus) used here. This allows us to apply styling to inputs when a user has clicked on the element. In this example, we just apply a `border` to convey that the field is active.

Import the `styles` object at the top of the `HootForm` component:

```jsx
// src/components/HootForm/HootForm.jsx

import styles from './HootForm.module.css'
```

And apply `styles.container` to the `className` of the outermost element (`<main>`):

```jsx
// src/components/HootForm/HootForm.jsx

<main className={styles.container}>
```

Check out the `HootForm` component in your browser!

## Style the `CommentForm`

We have a unique situation to handle when it comes to the `CommentForm` component. When creating a comment, the form is rendered as a child component inside the `HootDetails` component. When editing a comment, the form is rendered as its own page.

Throughout our application, we have used a `<main>` tag to signify a 'page', with our stylesheets built to follow this pattern.

When the `CommentForm` is used to create a comment, its styling is inherited from `src/components/HootForm/HootForm.module.css`. But when rendered as a distinct page, it looks out of place, departing too much from the appearance of other pages in our application. Let's address that issue now!

We'll modify the `CommentForm` component slightly, allowing it to `return` different JSX elements based on its usage. We'll also create a new stylesheet that can be applied to the form when it is rendered as a 'page'.

Run the following command in your terminal:

```bash
touch src/components/CommentForm/CommentForm.module.css
```

Add the following to the new file:

```css
/* src/components/CommentForm/CommentForm.module.css */

.container {
  height: 100%;
  display: flex;
  overflow-x: hidden;
  overflow-y: scroll;
  padding: 28px 21px;
  align-items: center;
  flex-direction: column;
}

.container label {
  font-weight: 600;
  margin: 7px 0px 14px 0px;
}

.container button {
  color: white;
  font-weight: 600;
  margin: 21px 0px 0px 0px;
  background: var(--primary);
}

input:focus, select:focus, textarea:focus {
  outline: none;
  border: 1px solid var(--primary);
}
```

Add the following import to the top of the `CommentForm` component:

```jsx
// src/components/CommentForm/CommentForm.jsx

import styles from './CommentForm.module.css';
```

Next, we'll add a conditional return. If both the `hootId` and `commentId` params are present, we know this component is being used to edit a comment and should therefore be rendered as a 'page', complete with a `<main>` tag around the `<form>`.

Add the following above the existing return statement in the `CommentForm` component:

```jsx
// src/components/CommentForm/CommentForm.jsx

// New code:
  if (hootId && commentId) return (
    <main className={styles.container}>
      <form onSubmit={handleSubmit}>
        <h1>Edit Comment</h1>
        <label htmlFor='text-input'>Your comment:</label>
        <textarea
          required
          type='text'
          name='text'
          id='text-input'
          value={formData.text}
          onChange={handleChange}
        />
        <button type='submit'>SUBMIT</button>
      </form>
    </main>
  );

// The existing return should remain below
```

> 🚨 Be sure to leave the existing return unchanged! This will still be used when the component is placed inside the `HootForm` component.

Check your browser and try out our newly styled forms!

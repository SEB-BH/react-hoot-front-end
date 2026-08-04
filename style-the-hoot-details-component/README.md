<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Style the Hoot Details Component</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to style a details component with CSS modules.

## Add the module

Run the following command in your terminal:

```bash
touch src/components/HootDetails/HootDetails.module.css
```

Add the following to this new file :

```css
/* src/components/HootDetails/HootDetails.module.css */

/* Details and comments */

.container {
  height: 100%;
  display: flex;
  overflow: scroll;
  align-items: center;
  flex-direction: column;
  background-color: var(--card-background);
}

.container > section {
  width: 100%;
  padding: 21px;
  display: flex;
  max-width: 700px;
  flex-direction: column;
}

.container section article {
  padding: 0;
  width: 100%;
  min-height: 140px;
  margin: 4px 0px 14px;
}

.container header div {
  width: 100%;
  display: flex;
  margin-top: 10px;
  align-items: center;
}

.container header {
  display: flex;
  margin-bottom: 21px;
  flex-direction: column;
  align-items: flex-start;
}

.container h1 {
  margin: 0 0 7px 0;
}

.container header > p {
  margin: 0;
  opacity: 1;
  font-weight: 600;
  color: var(--primary);
  margin: 7px 0px 14px 0px;
}

.container header button,
.container header a {
  width: 30px;
  height: 30px;

  font-size: 14px;
  font-weight: 500;

  background: none;
  border-radius: 50%;
  border: 1px solid var(--border);

  display: flex;
  align-items: center;
  justify-content: center;

  padding: 5px;
  margin: 0 0 0 7px;
}

/* Button icon styling */
/* This styling will be used when we add in our reusable icon component */

.container header button img,
.container header a img {
  width: 24px;
  height: 24px;
  opacity: 0.25;
}

.container header button:hover,
.container header a:hover {
  background-color: var(--border);
}

.container header button:hover img,
.container header a:hover img {
  filter: invert(1);
  opacity: 1;
}

.container header button:active {
  background-color: rgb(190, 190, 190);
}

/* Comment form styling */

.container form {
  padding: 0;
  display: flex;
  border: none;
  max-width: 100%;
  box-shadow: none;
  flex-direction: row;
  align-items: center;
  -moz-box-shadow: none;
  -webkit-box-shadow: none;
}

.container form input,
.container form textarea {
  width: 100%;
  padding: 14px;
  font-size: 14px;
  resize: vertical;
  border-radius: 5px;
  margin: 14px 0px 14px 0px;
  border: 1px solid var(--border);
}

.container form textarea {
  min-height: 50px;
}

.container form button {
  color: white;
  font-weight: 600;
  background: #00a0df;
  width: 40px;
  height: 40px;
  margin: 0 0 0 14px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.container form button img {
  width: 25px;
  height: 25px;
}
```

The `HootDetails` component is rather complex, as it features several subsections and subcomponents. As a result, we have quite a bit of styling in here.

First, note how we utilize **descendant** and **child** selectors in our CSS. This approach helps us to reduce the number of `className` and `id` attributes in our `JSX` by selecting elements based on their relationship to the parent `container`. This makes our code cleaner and more maintainable. If you need a refresher on how these selectors work, take a look at the MDN documentation on [CSS Selectors](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_selectors).

Some other interesting details include:

- Several flex-boxes are used to achieve the desired layout. Each `<section>` takes the shape of a column, with each column stacked on top of one another. Additionally, each `<section>` has its `width` capped at `700px`. This helps us maintain our layout without stretching content too wide on larger screens.

- Interactive elements like buttons and links are styled to be **circular**. This will look slightly awkward until we replace the current text content with iconography.

- When buttons and links are hovered over, we communicate this interaction visually with the use of color changes and filters.

Let's add the `styles` object to our component.

Add the following import to the top of the `HootDetails` component:

```jsx
// src/components/HootDetails/HootDetails.jsx

import styles from './HootDetails.module.css';
```

And add apply `styles.container` to the `className` of the outermost element(`<main>`):

```jsx
// src/components/HootDetails/HootDetails.jsx

<main className={styles.container}>
```

Next, we'll need to make a small change to **authorship** and **interactive elements** of our hoot details. For our layout, we'll need to wrap these elements in a `<div>`.

Apply the following changes to the `HootDetails` component:

```jsx
// src/components/HootDetails/HootDetails.jsx

      <section>
        <header>
          <p>{hoot.category.toUpperCase()}</p>
          <h1>{hoot.title}</h1>
          {/* Add this div */}
          <div>
            <p>
              {`${hoot.author.username} posted on
              ${new Date(hoot.createdAt).toLocaleDateString()}`}
            </p>
            {hoot.author._id === user._id && (
              <>
                <Link to={`/hoots/${hootId}/edit`}>Edit</Link>
                <button onClick={() => props.handleDeleteHoot(hootId)}>
                  Delete
                </button>
              </>
            )}
          {/* Don't forget to close it */}
          </div>

        </header>
        <p>{hoot.text}</p>
      </section>
```

And now do the same to the `<article>` tag in your comments section like so:

```jsx
// src/components/HootDetails/HootDetails.jsx

          <article key={comment._id}>
            <header>
              {/* Add this div */}
              <div>
                <p>
                  {`${comment.author.username} posted on
                  ${new Date(comment.createdAt).toLocaleDateString()}`}
                </p>
                {comment.author._id === user._id && (
                  <>
                    <Link to={`/hoots/${hootId}/comments/${comment._id}/edit`}>Edit</Link>
                    <button onClick={() => handleDeleteComment(comment._id)}>
                      Delete
                    </button>
                  </>
                )}
              {/* Don't forget to close it */}
              </div>
            </header>
            <p>{comment.text}</p>
          </article>
```

Take a look at the newly styled `HootDetails` component in your browser.

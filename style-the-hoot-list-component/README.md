<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Style the Hoot List Component</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to style a list component with CSS modules.

## Add the module

Run the following command in your terminal:

```bash
touch src/components/HootList/HootList.module.css
```

Add the following to the file you just created:

```css
/* src/components/HootList/HootList.module.css */

/* List styling */

.container {
  gap: 14px;
  width: 100%;
  display: grid;
  padding: 21px;
  grid-template-columns: repeat(1, 1fr);
}

/* Media queries */

@media only screen and (min-width: 900px) {
  .container {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media only screen and (min-width: 1200px) {
  .container {
    grid-template-columns: repeat(3, 1fr);
  }
}

@media only screen and (min-width: 1500px) {
  .container {
    grid-template-columns: repeat(4, 1fr);
  }
}

/* Hoot card styling */

.container article {
  width: 100%;
  height: 220px;
  padding: 14px 21px;
  border-radius: 5px;
  border: 1px solid var(--border);
  background-color: var(--card-background);
  box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.25);
  -moz-box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.25);
  -webkit-box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.25);
}

.container article header {
  margin-bottom: 14px;
}

.container article h2 {
  margin: 0;
  font-size: 20px;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 1;
  text-overflow: ellipsis;
  -webkit-box-orient: vertical;
}

.container article p {
  width: 100%;
  font-size: 14px;
  font-weight: 400;
  overflow: hidden;
  line-height: 1.5rem;
  display: -webkit-box;
  text-align: justify;
  -webkit-line-clamp: 3;
  word-break: keep-all;
  text-align-last: left;
  -webkit-box-orient: vertical;
}

.container header div:first-child {
  display: flex;
  align-items: center;
  margin: 7px 0px 12px 0px;
  justify-content: space-between;
}

.container header div:first-child img {
  margin: 0;
  width: 32px;
  height: 32px;
  opacity: 0.8;
  padding: 3px;
  border-radius: 50%;
  border: 1px solid var(--border);
}
```

This styling is mainly concerned with arranging our list view of hoot cards into a grid layout.

On mobile, the cards are displayed in a single column. Through a series of [media queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries), we are able to add additional columns to the grid layout based on the width of the browser (`grid-template-columns`).

We also do some work to transform each `<article>` tag into a distinct card element, complete with `box-shadow` and some `padding` for legibility.

The interesting bit is how we handle `overflow` text. When the characters in a hoot's title or text property can't fit within the confines of an `<article>`, we hide the overflow and replace it with an ellipsis. After we apply the `styles` object to the component, try adding a new hoot with a large amount of text to test this out!

Time to apply the styling!

Add the following import to the `HootList` component:

```jsx
// src/components/HootList/HootList.jsx

import styles from './HootList.module.css';
```

And apply `styles.container` to the `className` of the outermost element (`<main>`):

```jsx
// src/components/HootList/HootList.jsx

<main className={styles.container}>
```

And with that, you should now have a styled `HootList` component!

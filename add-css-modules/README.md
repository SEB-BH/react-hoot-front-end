<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Add CSS Modules</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to style an application with CSS modules.

## Overview

In this lesson, we'll learn how to apply CSS modules to components in a React application and begin the process of styling the app. We'll also add some visual assets, update our base styling (`src/index.css`), and make the `NavBar` component a bit more organized.

## CSS Modules

[**Vite's CSS Modules**](https://vite.dev/guide/features#css-modules) make it so that the **class names** in a CSS module will be unique and dedicated to the component that imports the CSS Module. No more worrying about class name collisions!

Using a CSS Module differs from using a CSS stylesheet in three ways:

1. The filename ends with `module.css`. For example, `App.module.css` instead of `App.css`.
2. The CSS Module is imported as an object.
3. **Class selectors** are unique to the component. However, other selectors become global CSS rules, just like regular CSS stylesheets.

Below is an example of how a CSS Module can be imported inside a React component:

```jsx
import styles from './MyComponent.module.css';
```

Once imported, a specific class can be applied to an element like so:

```jsx
<div className={styles.myClassName}>
```

> 💡 Note that the class names become keys on the styles object. Within the component function, a `console.log()` of `styles` will reveal that the tooling has generated a unique name for each class.

## Visual assets

Next, we'll add some visual assets to the app. These assets include a logo, decorative background images, and iconography for UI elements.

The assets can be found in [React Hoot Front-end Assets](https://git.generalassemb.ly/modular-curriculum-all-courses/react-hoot-front-end-assets).

To add these to your project, first **make sure you are in your project's root directory**.

> 🚨 Check your terminal and ensure you are in your project's root directory before running the following command!

Next, run the following command in your terminal:

```bash
git clone https://git.generalassemb.ly/modular-curriculum-all-courses/react-hoot-front-end-assets.git ./src/assets/images
rm -rf ./src/assets/images/.git
```

Now if you look at `src/assets`, you should see a new `images` directory. From here on out, we'll be able to utilize these images throughout our app.

> 💡 You might notice that many of these are `.svg` files. This format works quite well for web applications, as these images are fully scalable, meaning resizing them will not impact resolution.

## Base styling

Let's make some changes to our base styling.

First, remove all of the CSS held in `src/App.css`. We won't need it and don't want it to interfere with any new styles we'll add here. Instead, we'll consolidate all of the base styling inside `src/index.css`.

Once you have removed `src/App.css`, replace the existing contents of `src/index.css` with the following:

```css
/* src/index.css */

* {
  box-sizing: border-box;
}

html,
body {
  margin: 0;
  height: 100%;
  overflow: hidden;
  background-color: #f2f4f8;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  font-family: sans-serif;
  background-image: url("data:image/svg+xml,%3Csvg width='60' height='60' viewBox='0 0 60 60' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%2385cfee' fill-opacity='0.25'%3E%3Cpath d='M36 34v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4h-4v2h4v4h2V6h4V4h-4zM6 34v-4H4v4H0v2h4v4h2v-4h4v-2H6zM6 4V0H4v4H0v2h4v4h2V6h4V4H6z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E");

  --primary: #00a0df;
  --background: #f2f4f8;
  --card-background: white;
  --border: rgb(220, 220, 220);
}

#root {
  height: 100%;
}

body {
  overflow: auto;
  padding-top: 80px;
}

article img {
  width: 80px;
}

h1,
h3 {
  margin: 7px 0px 14px 0px;
}

h1 {
  font-size: 28px;
  font-weight: 600;
}

h3 {
  font-size: 20px;
  font-weight: 500;
}

h4 {
  font-size: 18px;
  font-weight: 600;
  margin-bottom: 2px;
}

p {
  margin: 0;
  opacity: 0.4;
  width: 100%;
  font-size: 16px;
  text-align: justify;
  line-height: 28px;
}

a {
  color: black;
  font-weight: 600;
  text-decoration: none;
}

form {
  width: 100%;
  display: flex;
  max-width: 500px;
  border-radius: 5px;
  flex-direction: column;
  background-color: white;
  padding: 21px 21px 28px 21px;
  border: 1px solid rgb(220, 220, 220);
  box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.25);
  -moz-box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.25);
  -webkit-box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.25);
}

button,
a {
  cursor: pointer;
}
```

Let's take a moment to review some of the more interesting aspects of this stylesheet:

1. **Global box-sizing:** The `box-sizing: border-box;` applied universally (`*`), changes the CSS box model so that widths and heights include padding and borders. This simplifies layout design and is a common practice in responsive design.

2. **Full-height layout with no overflow:** The `html` and `body` are set to a height of 100% with overflow hidden, ensuring the entire viewport is used while preventing scrolling at the root level. Scrolling is enabled only within the body content itself (`overflow: auto;`), specifically after a top padding of 80px. This accommodates a fixed header (the `NavBar` component).

3. **Background image:** The background is set to an an SVG provided by a URL. The background we are using was provided by [Hero Patterns](https://heropatterns.com/). This is a great resource if you ever need patterned backgrounds for your applications.

4. **Typography:** With `font-family` set to `sans-serif`, we ensure a more modern look to the typography in our app. We are also setting specific sizes and weights for headings and paragraph elements. Margins and font sizes have been set to maintain a clear visual hierarchy and readability.

5. **Form styling:** We also have some basic form styling. Of note is the use of `box-shadow`. This helps our `<form>` element stand out from the rest of the page. If you'd like to add `box-shadow` to your application, check out this [CSS Box Shadow Generator](https://www.cssmatic.com/box-shadow).

6. **CSS variables:** Our stylesheet also defines a few CSS variables for the `primary` application color, the `background` color, the `card-background` color, and a standard `border` color. These CSS variables make it easier to maintain consistency with our design throughout the app.

## Style the `NavBar` component

With our base styling up to date, we can start working with CSS Modules. We'll apply our first CSS Modules to the `NavBar` component.

Run the following command in your terminal:

```bash
touch src/components/NavBar/NavBar.module.css
```

Add the following to that file:

```css
/* src/components/NavBar/NavBar.module.css */

.container {
  top: 0;
  z-index: 1;
  width: 100%;
  height: 80px;
  display: flex;
  position: fixed;
  padding: 14px 21px;
  align-items: center;
  background: white;
  border-bottom: 1px solid rgb(200, 200, 200);
}

.container img {
  width: 50px;
}

.container ul {
  margin: 0;
  width: 100%;
  display: flex;
  list-style: none;
  font-weight: bold;
  align-items: center;
  justify-content: flex-end;
}

.container li {
  margin-left: 35px;
  text-align: right;
}

@media only screen and (max-width: 414px) {
  .container ul {
    padding-left: 0px;
  }
  .container li {
    font-size: 15px;
  }
}
```

Let's highlight a few key aspects of the styling above.

The first is the use of `position: fixed` and `top: 0`. This ensures that the **navigation bar stays locked and visible at the top of the page**, even as the user scrolls.

Next, we have some **responsive design adjustments** in the form of media queries. This ensures that our content stays legible and utilizes space effectively at smaller screen sizes.

Add the following import to the `NavBar` component:

```jsx
// src/components/NavBar/NavBar.jsx

import styles from './NavBar.module.css';
```

And apply `styles.container` as a `className` to the outermost element (`<nav>`):

```jsx
<nav className={styles.container}>
```

Much better! Let's make one more change by adding a **logo** to our app. This SVG file is included in the visual assets we downloaded earlier.

Add another import to the `NavBar` component:

```jsx
// src/components/NavBar/NavBar.jsx

import Logo from '../../assets/images/logo.svg';
```

And finally, add the following `<Link>` and `<img>` tags right below the opening of the `<nav>`. This will replace the two existing links that go to the `/` route in this component:

```jsx
// src/components/NavBar/NavBar.jsx

<nav className={styles.container}>
  <Link to='/'><img src={Logo} alt='A cute owl' /></Link>
```

Congrats! You've successfully applied CSS Modules to the `NavBar` component and added a logo to the app.

<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Build a Loading Screen</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to create a reusable loading screen component.

## Overview

In this lesson, we'll build a reusable loading screen component. Our loading screen will use a decorative banner that is included in your visual assets.

A component like this is useful when fetching data from your server, as the information is not immediately available to render. With a loading screen, we can give our users a visual indication that the content is forthcoming. Making the loading screen its own component, complete with styling, makes it easier to include wherever needed in our application. Additionally, componentizing the loading screen can be helpful if you ever wish to incorporate animations.

## Build and style the component

Run the following commands in your terminal:

```bash
mkdir src/components/Loading
touch src/components/Loading/Loading.jsx
touch src/components/Loading/Loading.module.css
```

Add the following to `src/components/Loading/Loading.module.css`:

```css
/* src/components/Loading/Loading.module.css */

.container {
  width: 100%;
  height: 100%;
  display: flex;
  padding: 21px;
  overflow: hidden;
  align-items: center;
  justify-content: center;
}

.container img {
  width: 100%;
  margin-bottom: 60px;
}

@media only screen and (max-height: 600px) {
  .container img {
    margin-bottom: 20px;
  }
}

@media only screen and (min-width: 500px) {
  .container img {
    max-width: 460px;
  }
}
```

Add the following to the `Loading` component:

```jsx
// src/components/Loading/Loading.jsx

import styles from './Loading.module.css'
import LoadingIcon from '../../assets/images/loading.svg';

const Loading = () => {
  return (
    <main className={styles.container}>
      <img src={LoadingIcon} alt='A cute owl' />
    </main>
  )
}

export default Loading
```

The last step is to add our new `Loading` component to the `HootDetails` component. Add the following import:

```jsx
// src/components/HootDetails/HootDetails.jsx

import Loading from '../Loading/Loading';
```

And finally, locate the `if` condition inside the `HootDetails` component.

Replace the existing `<main>` tag with the `<Loading />` component:

```jsx
// src/components/HootDetails/HootDetails.jsx

// Replace 
if (!hoot) return <Loading />

return (
  ...
);
```

Navigate to the details page and refresh your browser. For a split second, you should see a loading image!

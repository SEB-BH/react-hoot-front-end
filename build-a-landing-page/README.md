<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Build a Landing Page</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to create a landing page.

## Overview

In this lesson, we'll build a landing page for our application. A landing page is a great way to introduce users to your application. Landing pages typically feature a headline and hero image, a list of features or selling points, and a prominent call to action ('Sign Up', 'Learn More', and so on).

Our landing page will be fairly simple, featuring a large hero image with 'About Us' and 'Testimonials' sections. This should be a helpful jumping-off point for developing landing pages in your own applications.

## Build and style the component

The auth template includes a `Landing` component, so we won't need to create any files. Instead, we'll update this file with new JSX and copy. But first, let's add a corresponding CSS Module.

Run the following command in your terminal:

```bash
touch src/components/Landing/Landing.module.css
```

Add the following to this new file:

```css
/* src/components/Landing/Landing.module.css */

.container {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.container section {
  padding: 0px 22px;
}

.container > section {
  width: 100%;
  display: flex;
  align-items: center;
  flex-direction: column;
}

.container section > img {
  width: 25%;
  min-width: 460px;
  margin-bottom: 80px;
}

.splash {
  height: 91vh;
  justify-content: center;
}

.about {
  height: 430px;
  background-color: white;
  box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.15);
  -moz-box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.15);
  -webkit-box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.15);
}

.testimonial {
  height: 80vh;
}

.about header {
  margin: 0;
  width: 100%;
  display: flex;
  padding: 50px 0px;
  align-items: center;
  flex-direction: column;
}

.about article {
  width: 100%;
  max-width: 1000px;
}

.about article p {
  margin: 0;
  width: 100%;
  font-size: 16px;
  line-height: 28px;
}

.container section > header * {
  margin: 0px;
  font-weight: 600;
}

.container section > header h1 {
  margin: 5px;
  font-size: 36px;
  letter-spacing: 2px;
}

.container section > header h3 {
  font-size: 16px;
  color: #00a0df;
}

.testimonial > header {
  width: 100%;
  padding: 50px 0px;
  display: flex;
  align-items: center;
  margin-bottom: 50px;
  flex-direction: column;
}

.testimonial article {
  margin: 5px;
  display: flex;
  max-width: 525px;
  border-radius: 5px;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  background-color: #f2f5f7;
  padding: 12px 28px 14px 28px;
  border: 1px solid rgb(220, 220, 220);
  box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.25);
  -moz-box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.25);
  -webkit-box-shadow: -1px 5px 19px -5px rgba(0, 0, 0, 0.25);
}

.container article header {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  flex-direction: column;
  justify-content: center;
}

.container footer {
  margin: 0;
  width: 100%;
  padding: 0px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.testimonial article * {
  margin: 5px;
}

.testimonial header p {
  margin: 0;
  opacity: 0.4;
  font-size: 14px;
  font-weight: 400;
}

.testimonial article > p {
  font-size: 14px;
  line-height: 24px;
}

.testimonial footer img {
  height: 20px;
}

.footer {
  padding: 50px 0px;
  display: flex;
  background: white;
  background: #131415;
  justify-content: center;
  color: rgb(220, 220, 220);
  border-top: 1px solid rgb(220, 220, 220);
}

@media only screen and (max-width: 1200px) {
  .testimonial {
    height: 680px;
  }
}

@media only screen and (max-width: 1024px) {
  .about {
    height: 420px;
  }
  .testimonial {
    height: 580px;
  }
}

@media only screen and (max-width: 912px) {
  .about {
    height: 460px;
  }
  .testimonial {
    height: 600px;
  }
}

@media only screen and (max-width: 540px) {
  .about {
    height: 560px;
  }
  .testimonial {
    height: 700px;
  }
}

@media only screen and (max-width: 414px) {
  .about {
    height: 660px;
  }
  .container section > img {
    min-width: 340px;
  }
}

@media only screen and (max-width: 300px) {
  .about {
    height: 800px;
  }
  .container section > img {
    min-width: 340px;
  }
}
```

Most of this styling concerns sizing sizing different elements and sections correctly. We also have a handful of media queries that adjust the layout for given screen sizes.

This styling differs from previous examples in this lesson by using specific class names for subsections (`.splash`, `.about`, `.testimonial`). This approach makes it easier to target JSX elements that are reused across different parts of the component and apply unique styles based on their parent container. This can help keep the CSS organized and straightforward when working with components that appear in multiple contexts.

Now add the following to the `Landing` component:

```jsx
// src/components/Landing/Landing.jsx

import styles from './Landing.module.css';
import Stars from '../../assets/images/stars.svg';
import Logotype from '../../assets/images/logotype.svg';

const Landing = () => {
  return (
    <>
      <main className={styles.container}>
        <section className={styles.splash}>
          <img src={Logotype} alt='A cute owl' />
        </section>

        <section className={styles.about}>
          <header>
            <h3>HOO WE ARE</h3>
            <h1>ABOUT US</h1>
          </header>
          <article>
            <p>
              Not everyone is a morning person. That's why we're building Hoot,
              an open and inclusive place for night owls to share their ideas,
              thoughts, and knowledge with one another. We provide a platform
              for people to share ideas in the early hours of the morning when
              owl brains work best. Hoot is a community of self-identifying owls
              like you, that wants to make sure you always have something
              interesting to say no matter what time of day it is. You no longer
              have to worry about your troubles keeping you up during the day.
              You can now blog about your favorite topics and connect with other
              owls at night. With Hoot, it's never too late to post.
            </p>
          </article>
        </section>

        <section className={styles.testimonial}>
          <header>
            <h3>WHO GIVES A HOOT?</h3>
            <h1>TESTIMONIALS</h1>
          </header>
          <article>
            <header>
              <h4>Ben Manley</h4>
              <p>Software Engineer</p>
            </header>
            <p>
              I found Hoot through a friend of mine, and I'm so glad I did. As a
              night owl, I have a hard time finding blogging apps that fit my
              lifestyle. The interface is so easy to use and makes it really
              convenient for me to write my blog posts at night.
            </p>
            <footer>
              <img src={Stars} alt='Four blue stars' />
            </footer>
          </article>
        </section>
      </main>

      <footer className={styles.footer}>
        © 2025 HOOT INC. OWL RIGHTS RESERVED
      </footer>
    </>
  );
};

export default Landing;
```

Take a look at the structure of our new landing page. Here, we have three distinct `<section>` tags inside `<main>`. Below the `<main>`, we have a simple `<footer>`. Notice how two of the `<section>` blocks follow a very similar structure. This level of consistency in JSX can make styling more predictable and reusable.

Finally, let's ensure users can see this new landing page whether they are logged in or not.

In `src/App.jsx`, locate the `/` route, and remove the conditional logic and `<Dashboard />` component so that only the the `<Landing />` component remains:

```jsx
// src/App.jsx

<Route path='/' element={<Landing />} />
```

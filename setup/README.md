<h1>
  <span class="headline">Hoot Front-End</span>
  <span class="subhead">Setup</span>
</h1>

## Set up the Express API

The app we're building in this lecture will require a back-end to handle requests from your React app. If you already have the **Express API - Hoot Back-End** set up, skip to the **Use an existing Express API - Hoot Back-End** section below. Otherwise, start from here and follow the steps to set up your development environment.

### Get the Express API - Hoot Back-End starter

Open your Terminal application and navigate to your `~/code/ga/lectures` directory:

```bash
cd ~/code/ga/lectures
```

Clone the Express API - Hoot Back-End from the [solution code repo](https://git.generalassemb.ly/modular-curriculum-all-courses/express-api-hoot-back-end-solution):

```bash
git clone https://git.generalassemb.ly/modular-curriculum-all-courses/express-api-hoot-back-end-solution.git express-api-hoot-back-end
```

> 💡 By including the new folder name in the git clone command, you can immediately rename the directory during the cloning process, saving you from having to rename it manually later.

Move into the directory you just cloned the solution code into:

```bash
cd express-api-hoot-back-end
```

Install the dependencies:

```bash
npm i
```

Open the project in VS Code:

```bash
code .
```

Open the built-in terminal in VS Code. You'll need to set up a `.env` file that includes values for the `MONGODB_URI` and `JWT_SECRET` before the app can function. Start by creating the `.env` file:

```bash
touch .env
```

Add your MongoDB URI to the `.env` file. It will look similar to the following but with your username, password, and database name:

```plaintext
MONGODB_URI=mongodb+srv://<username>:<password>@sei-w0kys.azure.mongodb.net/hoot?retryWrites=true
```

> ⚠️ If you're unsure where to obtain your MongoDB URI, please refer to the MongoDB Atlas Setup Lab.

On the next line in the `.env` file, add a `JWT_SECRET`:

```plaintext
JWT_SECRET=your_secure_random_string_here
```

> ⚠️ Replace `your_secure_random_string_here` with a secure random string of your choice.

Remove the existing Git information from this solution code:

```bash
rm -rf .git
```

> ⚠️ Removing the `.git` directory is important as this is just starter code provided by GA. You do not need the existing Git history for this project.

You're done! If your setup is complete, you should be able to run the server with this command:

```bash
npm run dev
```

You should see a message that the Express app is ready and that you've connected to a MongoDB database. Troubleshoot any errors before continuing.

Skip to the **Clone the auth template** section below.

### Use an existing Express API - Hoot Back-End

Move into the `~/code/ga/lectures/express-api-hoot-back-end` directory:

```bash
cd ~/code/ga/lectures/express-api-hoot-back-end
```

Open the project in VS Code:

```bash
code .
```

Open the integrated terminal in VS Code and run the server:

```bash
npm run dev
```

You should see a message that the Express app is ready and that you've connected to a MongoDB database. Troubleshoot any errors before continuing.

## Clone the auth template

This lecture uses the [React JWT Auth Template](https://git.generalassemb.ly/modular-curriculum-all-courses/react-jwt-auth-template.git) as starter code. The template includes code to authenticate users in React using JWT tokens generated from an existing Express back-end API.

Return to your terminal window, and navigate to your `~/code/ga/lectures` directory:

```bash
cd ~/code/ga/lectures
```

Clone the repository to your machine, and rename it `react-hoot-front-end`:

```bash
git clone https://git.generalassemb.ly/modular-curriculum-all-courses/react-jwt-auth-template.git react-hoot-front-end
```

> 💡 By including the new folder name in the git clone command, you can immediately rename the directory during the cloning process, saving you from having to rename it manually later.

Next, `cd` into your new directory:

```bash
cd react-hoot-front-end
```

Remove the existing Git information from this template:

```bash
rm -rf .git
```

> ⚠️ Removing the `.git` directory is important as this is just a starter template provided by GA. You do not need the existing Git history for this project.

### Install dependencies

Next, you will want to install all of the packages listed in `package.json`

```bash
npm i
```

Open the project's folder in your code editor:

```bash
code .
```

### Create a `.env`

Open the integrated terminal in VS Code and run the following command to create a `.env` file:

```bash
touch .env
```

Add a new `VITE_BACK_END_SERVER_URL` environment variable by adding the following secret key to your `.env`:

```plaintext
VITE_BACK_END_SERVER_URL=http://localhost:3000
```

### Update the `.gitignore`

Add `package-lock.json` and `.env` to the `.gitignore` file.

```plaintext
node_modules
package-lock.json
.env
```

### GitHub setup

To add this project to GitHub, initialize a new Git repository:

```bash
git init
git add .
git commit -m "init commit"
```

Make a new repository on [GitHub](https://github.com/) named `react-hoot-front-end`.

Link your local project to your remote GitHub repo:

```bash
git remote add origin https://github.com/<github-username>/react-hoot-front-end.git
git push origin main
```

> 🚨 Do not copy the above command. It will not work. Your GitHub username will replace `<github-username>` (including the `<` and `>`) in the URL above.

### Start your application

Start the application with the following command:

```bash
npm run dev
```

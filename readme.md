# Quick and simple create and publish React component on npm
Jan 14, 2024

Forked for backup/reference, originally from https://github.com/irekrog/my-awesome-button-alert-component and article the is at https://medium.com/@irekrog/quick-and-simple-create-and-publish-react-component-on-npm-df528cd26b0

## Our component will be a button where when you click on it an alert will appear with today’s date.

### Basic project setup
1. We create a new folder with the name of our component on our PC
 - For example, I will create a folder my-awesome-button-alert-component
2. Navigate to this folder and launch the console and initialize the project with npm init
 - Fill a basic data about our component
3. Add the necessary packages to package.json to develop the component run
```sh
npm i -D react react-dom typescript @types/react @types/react-dom
```
this is what our package.json should look like after installation
```json
{
 "name": "my-awesome-button-alert-component",
 "version": "1.0.0",
 "description": "",
 "main": "index.js",
 "scripts": {
   "test": "echo \"Error: no test specified\" && exit 1"
 },
 "author": "@irekrog",
 "license": "MIT",
 "devDependencies": {
   "@types/react": "^18.0.12",
   "@types/react-dom": "^18.0.5",
   "react": "^18.1.0",
   "react-dom": "^18.1.0",
   "typescript": "^4.7.3"
 }
}
```
4. Generate a tsconfig file to use TypeScript
```sh
run tsc --init
```
the tsconfig.json file has been generated, but to work properly with React we need to add to it:
```js
"jsx": "react"
```
### The code of our component
5. Let’s create a folder src/button in the project, in it a file index.tsx and in it put the following code:
```ts
import React from 'react';
import './style.css';
export type ButtonProps = {
   title: string;
   className?: string;
}
export const Button = ({title, className = ''}: ButtonProps) => {
   const onShowAlert = () => { alert(new Date().toLocaleDateString()) }
   return (
       <button className={`my-awesome-component ${className}`} onClick={onShowAlert}>
           {title}
       </button>
   )
}
```
6. Now let’s create a src/button/style.css file with the following styles:
```css
.my-awesome-component {
   background: #6750a4;
   color: #ffffff;
   border: 0;
   padding: 1rem 2rem;
   font-size: 1rem;
   border-radius: 0.25rem;
   cursor: pointer;
   transition: .3s background;
}
.my-awesome-component:hover,
.my-awesome-component:focus-visible {
   transition: .3s background;
   background: #432b7e;
}
.my-awesome-component:focus-visible {
   outline: 4px dashed #49454e;
}
```
Great, we have created the code with our component. But how to display it now? For this, we will use the Ladle tool (it’s similar to Storybook, but less advanced, lighter and faster).

### Ladle tool
https://ladle.dev/

7. We add to our Ladle project running npm i -D @ladle/react

8. Then we create a button.stories.ts file in src/button
```ts
import React from 'react';
import { Button } from './index';
export default {
   title: 'Button',
};
export const ButtonPrimary = () => <Button title={'Hello World! Click me to show current date :)'} />
```
9. To run Ladle we add a script to package.json, in the scripts field:
```sh
"ladle": "ladle serve"
```
10. Run this script npm run ladle and in a moment you will see the component


Great, we have already created our component in code and we can display it easily with the Ladle tool.

### Deploy a component to npm
To deploy our component to npm we need to do some important things. So far we only have the source code, we don’t want to upload this code because these are just our sources which are not optimized or minified. Therefore to build the result files we will use the tsup tool. This is a great and simple tool that uses esbuild under the hood, which is a very fast bundler (definitely faster than rollup or webpack for example)

#### tsup bundler
https://tsup.egoist.sh/

11. Install tsupby running `npm i -D tsup`

12. Create a new file tsup.config.json and in it:
```json
{
 "splitting": true,
 "sourcemap": true,
 "clean": true,
 "minify": true,
 "entry": [
   "src/index.ts"
 ]
}
```
As we can see, the entry point that the bundler refers to is src/index.ts. We don’t have this file yet, so let’s create it: `src/index.ts`, and in it we export our component, so:
```ts
export * from './button';
```
13. In package.json we add another script:
```json
"build": "tsup"
```
And we give peerDependencies to inform which versions of React our component will work with:
```json
"peerDependencies": {
 "react": "^16.8 || ^17 || ^18",
 "react-dom": "^16.8 || ^17 || ^18"
}
```
15. Run the npm run build command to generate the output files, once run, a dist folder will be created and in it will appear the optimized and minified files and types for use with TypeScript.

### Customizing package.json for deployment
16. To deploy the appropriate files to npm, we need to set the path to them in package.json. Under the files field we specify the path to the dist folder.
```json
"files": [
 "dist"
]
```
and set the path of our main file and types:
```json
"main": "dist/index.js",
"types": "dist/index.d.ts",
```
It is worth mentioning that in package.json we also have name and version fields. name is the name of our component, version is the version.

### Deploy
I assume you already have an account created on npm, so now all you need to do in the console is log in to your account by running the `npm login` command. Once logged in correctly, we can make sure by typing the `npm publish` command. Voila! Our component has now appeared in npm.

https://www.npmjs.com/package/my-awesome-button-alert-component

### How to use our component?
As with any other package so install `npm install my-awesome-button-alert-component` and import file to project:
```ts
import { Button } from 'my-awesome-button-alert-component
import 'my-awesome-button-alert-component/dist/index.css
```

### What else is worth knowing?
Our component is basic and briefly useless and we focused on deploying it quickly. What are some good practices if we wanted to deploy a more complex component?

 - create a repository on GitHub
 - add a README.md file
 - add a CHANGELOG.md file
 - add a license (for open source component MIT license is popular)
 - docs eg. using Docusaurushttps://docusaurus.io/
 - release-it https://github.com/release-it/release-it tool for easier versioning management
 - additional fields in package.jsondescribing our component
 - ESLint / prettier to code formatting
 - unit testing / E2E testing
 - automation via GitHub actions

The repository that has most of the above mentioned best practices is the https://github.com/MobileReality/react-native-select-pro (at npm https://www.npmjs.com/package/@mobile-reality/react-native-select-pro)

The source code of this component is here: [https://github.com/irekrog/my-awesome-button-alert-component](https://github.com/anandchakru/my-awesome-button-alert-component)

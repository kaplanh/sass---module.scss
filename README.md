# Sass & Module.scss with React

![sass   module scss with react](https://github.com/kaplanh/sass---module.scss/assets/101884444/6a01c757-7099-4123-903c-d60ab7ec217e)


[:point_right: Click here to see on browser](https://kaplanh.github.io/sass---module.scss/)

 **What's used in this app ?** |  **Author** |
|----------|------------|
|React - component |[Take a look at my portfolio](https://kaplanh.github.io/Portfolio_with_CssFlex/)|
|React - props|[Visit me on Linkedin](https://www.linkedin.com/in/kaplan-h/)|
|React - sass||   
|React - module.scss||   
|Deploy | with GitHub Pages|   
  

<br/>

## How To Run This Project 🚀

<br/>

### 💻 Install React 👇

```bash
yarn create react-app .  or npx create-react-app .
```
### 💻 Install Sass 👇

```bash
yarn add sass  or npm i sass
```

## 🔴 Delete these files and delete the imports👇

    - App.test.js
    - reportWebVitals.js
    - setupTests.js
    - favicon.ico
    - logo192.png
    - logo512.png
    - manifest.json
    - robots.txt

### 💻 Start the project 👇

```bash
yarn start or npm start
```
OR
- <strong>Clone the Repo</strong>

  ```sh
  git clone
  ```
  

- <strong>Install NPM packages</strong>

  ```sh
  npm install or yarn 
  ```

- <strong>Run the project</strong>

  ```sh
  npm start or yarn start
  ```

- <strong>Open the project on your browser</strong>

  ```sh
  http://localhost:3000/
  ```

- ### <strong>Enjoy! 🎉</strong>


## Project Skeleton 

```
Module Css in React(folder)
|
|----public (folder)
│     └── index.html
|----src (folder)
|    |--- components (folder)
│    │       ├── Card.js
│    │       ├── Footer.js
│    │       ├── Header.js
│    │         
│    │          
│    │          
│    │      
│    │            
│    │             
│    |--- scss (folder)
|    |      ├── _reset.scss
|    |      ├── _variables.scss
|    |      ├── app.scss
|    |      ├── card.module.scss
|    |      ├── footer.module.scss
|    |      ├── header.module.scss
│    |
│    ├--- App.js
│    │--- data.js
│    └--- index.js
│
│
|--- .gitignore
|── package-lock.json
├── package.json
|── README.md
|── yarn.lock

```

### At the end of the project, the following topics are to be covered;

- Component
- JSX & JS
- sass with react
  ```
   // src/scss/_reset.scss
      * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
      }

   // src/scss/_variables.scss
       //? Colors
      $navyBlue: #042940;
      $darkGreen: #005c53;
      $olive: #9fc131;
      $neon: #dbf227;
      $beige: #d6d58e;
  
   // src/scss/app.scss
  
    @import "reset", "variables";
    body {
        background-color:$beige;
        text-align: center;
        }
   // src/App.jsx
     import "./scss/app.scss";

  ```
   


- Module.css with react
  ```
   // src/scss/card.module.scss
  .container {
    .card {
        box-shadow: 8px 8px 25px rgba(0, 0, 0, 0.4);

        max-width: 300px;
        margin: 2rem auto;
        border-radius: 1rem;
        padding: 1rem;
        h1 {
            color: red;
        }
   }
   // src/components/Card.jsx
  
      import CardStyle from "../scss/card.module.scss";
      
      const Card = ({ data }) => {
          console.log(data);
          //? js
          return (
              <div className={CardStyle.container}>
                  {data.map((item) => {
                      //? JS
                      const { id, name, job, img, comment } = item;
                      return (
                          <div key={id} className={CardStyle.card}>
                              <h1>{name}</h1>
                              <h3>{job}</h3>
                              <p>{comment}</p>
                              <img src={img} alt="img" />
                              <div className={CardStyle.buttons}>
                                  <button className={CardStyle.small}>Small</button>
                                  <button className={CardStyle.large}>Large</button>
                              </div>
                          </div>
                      );
                  })}
              </div>
          );
      };
      
      export default Card;

  ```
- Component icinde json datayi map() leme
   ```
   // src/components/Card.jsx
   import CardStyle from "../scss/card.module.scss";

          const Card = ({ data }) => {
              console.log(data);
              //? js
              return (
                  <div className={CardStyle.container}>
                      {data.map((item) => {
                          //? JS
                          const { id, name, job, img, comment } = item;
                          return (
                              <div key={id} className={CardStyle.card}>
                                  <h1>{name}</h1>
                                  <h3>{job}</h3>
                                  <p>{comment}</p>
                                  <img src={img} alt="img" />
                                  <div className={CardStyle.buttons}>
                                      <button className={CardStyle.small}>Small</button>
                                      <button className={CardStyle.large}>Large</button>
                                  </div>
                              </div>
                          );
                      })}
                  </div>
              );
          };
          
          export default Card;
  ```
- Deploy with GitHub Pages
  
-    ### 💻 write in Terminal 👇

```bash
// src
 npm i gh-pages or yarn add gh-pages
```

-    ### 💻  add this in scripts: "predeploy": "yarn run build",  "deploy": "gh-pages -d build" // if you use npm: "predeploy": "npm run build","deploy": "gh-pages -d build" 👇

  ```sh
// src/package.json

  "scripts": {
        "start": "react-scripts start",
        "build": "react-scripts build",
        "test": "react-scripts test",
        "eject": "react-scripts eject",
        "predeploy": "yarn run build", 
        "deploy": "gh-pages -d build"
    },
```
   
   ### 💻  add github.io link as homepage:  "homepage": "https://kaplanh.github.io/sass---module.scss"  👇

```bash
// src/package.json
 {
    "homepage": "https://kaplanh.github.io/sass---module.scss",
    "name": "sass---module.scss",
    "version": "0.1.0",
    "private": true,
    "dependencies": {
        "gh-pages": "^6.1.1",
        "react": "^18.2.0",
        "react-dom": "^18.2.0",
        "react-scripts": "5.0.1",
        "sass": "^1.70.0"
    },
```



-    ### 💻  write in Terminal 👇

  ```sh
    yarn run deploy  or npm run deploy
  ```

-    ### 💻  visit your page link 👇

  ```sh
   https://kaplanh.github.io/sass---module.scss
 ```


  

 

## Feedback and Collaboration
I value your feedback and suggestions. If you have any comments, questions, or ideas for improvement regarding this project or any of my other projects, please don't hesitate to reach out.
I'm always open to collaboration and welcome the opportunity to work on exciting projects together.
Thank you for visiting my project. I hope you have a wonderful experience exploring it, and I look forward to connecting with you soon!



<p align="center"> ⌛<strong> Happy Coding </strong> ✍ </p>


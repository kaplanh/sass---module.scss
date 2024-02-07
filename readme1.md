# <center>💨 BLOG-APP FRONTEND 💨</center>

## 💻 Install React 👇

```bash
yarn create react-app .
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

## 💻 Start the project 👇

```bash
yarn start
```

## 💻 Install Material-UI 👇

```bash
yarn add @mui/material @emotion/react @emotion/styled
```

## 💻 Install Material-UI Icons 👇

```bash
yarn add @mui/icons-material
```

## 💻 Save the Material-UI Icons 👇

```bash
yarn add react-icons --save
```

## 💻 Install React-Router-Dom 👇

```bash
yarn add react-router-dom
```

## 💻 Install axios 👇

```bash
yarn add axios
```

## 💻 Install "Toastify" 👇

```bash
yarn add react-toastify
```

## 🚩 Create "ToatsNotify" file under "helper" folder 👇

```javascript
import { toast } from "react-toastify";
import 'react-toastify/dist/ReactToastify.css';

export const toastWarnNotify = (msg) => {
  toast.warn(msg, {
    position: "bottom-right",
    autoClose: 3000,
    hideProgressBar: false,
    closeOnClick: true,
    pauseOnHover: true,
    draggable: true,
    progress: undefined,
  });
};

export const toastSuccessNotify = (msg) => {
  toast.success(msg, {
    position: "bottom-right",
    autoClose: 3000,
    hideProgressBar: false,
    closeOnClick: true,
    pauseOnHover: true,
    draggable: true,
    progress: undefined,
  });
};

export const toastErrorNotify = (msg) => {
  toast.error(msg, {
    position: "bottom-right",
    autoClose: 4000,
    hideProgressBar: false,
    closeOnClick: true,
    pauseOnHover: true,
    draggable: true,
    progress: undefined,
  });
};
```

## 🚩 Go to "app.js" and add Tostify 👇

```javascript
import { ToastContainer } from 'react-toastify';
import './App.css';
import AppRouter from './router/AppRouter';

function App() {
  return (
    <div >
      <AppRouter />
      <ToastContainer/>
    </div>
  );
}
export default App;
```

# <center>🚩 Let's start with Router 👇 </center>

## 🚩 Create "router" folder under "src", create "AppRouter.jsx" file under "router" folder 👇

```javascript
import {
    BrowserRouter as Router,
    Route,
    Routes,
    Navigate,
    Outlet,
  } from 'react-router-dom'
  import NavBar from '../components/NavBar'
  import Home from '../pages/Home'
  import Login from '../pages/Login'
  import NewBlog from '../pages/NewBlog'
  import PostDetails from '../pages/PostDetails'
  import Profile from '../pages/Profile'
  import Register from '../pages/Register'
  import UpdateBlog from '../pages/UpdateBlog'

  const AppRouter = () => {
   const currentUser = false

    function PrivateRouter() {
      return currentUser ? <Outlet /> : <Navigate to="/login" replace />;
    }

    return (
      <Router>
        <NavBar />
        <Routes>
          <Route path="/" element={<Home />}></Route>
          <Route path="/login" element={<Login />}></Route>

          <Route path="/newblog" element={<PrivateRouter />}>
            <Route path="" element={<NewBlog />} />
          </Route>

          <Route path="/profile" element={<PrivateRouter />}>
            <Route path="" element={<Profile />} />
          </Route>

          <Route path="/register" element={<Register />}></Route>

          <Route path="/update/:str" element={<PrivateRouter />}>
            <Route path="" element={<UpdateBlog />} />
          </Route>

          <Route path="/details/:str" element={<PrivateRouter />}>
            <Route path="" element={<PostDetails />} />
          </Route>
        </Routes>
      </Router>
    )
  }

  export default AppRouter
```

## 🚩 Go to "app.js" and import the "AppRouter" file 👇

```javascript
import './App.css';
import AppRouter from './router/AppRouter';

function App() {
  return (
    <div >
      <AppRouter />
    </div>
  );
}

export default App;
```

## 🚩 Create page files under "pages" folder 👇

    - Home.jsx
    - Login.jsx
    - NewBlog.jsx
    - PostDetails.jsx
    - Profile.jsx
    - Register.jsx
    - UpdateBlog.jsx

## 👉 Sample page structure 👇

```javascript
import React from 'react'

const Home = () => {
  return (
    <div>Home</div>
  )
}

export default Home
```

## 🚩 Create "NavBar" component under "components" folder 👇

```javascript
import React from 'react'

const NavBar = () => {
  return (
    <div>NavBar</div>
  )
}

export default NavBar
```

## 🚩 Create "AuthContext.jsx" file under "context" folder 👇

```javascript
import axios from 'axios';
import {createContext, useState} from 'react'
import { toastErrorNotify, toastSuccessNotify } from '../helper/ToastNotify';

export const AuthContext = createContext();

const url = "http://127.0.0.1:8000/"

const AuthContextProvider = (props)=>{

  const [currentUser,setCurrentUser] = useState(JSON.parse(sessionStorage.getItem("currentuser")) || false)
  let keys = sessionStorage.getItem('token')
  const [myKey,setMyKey] = useState(keys && window.atob(keys))

  const createUser = async (email,password,firstName,lastName,username,profile_pic,biography,password1,navigate)=>{

    const userInfo = {
              "username": username,
              "email": email,
              "first_name": firstName,
              "last_name": lastName,
              "profile_pic": profile_pic,
              "biography":biography,
              "password": password,
              "password1": password1
          }

    try {
      const res = await axios.post(`${url}auth/register/`,userInfo)

      if(res.data.token){

        setMyKey(res.data.token)
        setCurrentUser({...res.data,'token':''})

        const userData = { ...res.data, token: '' }
        sessionStorage.setItem("currentuser", JSON.stringify(userData))
       const myToken = window.btoa(res.data.token)
        console.log(res.data);
        console.log(currentUser);
        sessionStorage.setItem('token',myToken)
        toastSuccessNotify('User registered successfully.')
        navigate("/")
      }

    } catch (error) {
      toastErrorNotify(error.message)
    }
  }

  const signIn = async (email,password,userName,navigate)=>{

    console.log(email,password,userName);
    try {
      console.log(email)
      const res = await axios.post(`${url}auth/login/`,{
        "email": email,
        "username":userName,
        "password":password
      })
      console.log(res)
      if(res.data.key){
        console.log(res)
        setMyKey(res.data.key)
        setCurrentUser(res.data.user)
        sessionStorage.setItem('currentuser',JSON.stringify(res.data.user))
        const myToken = window.btoa(res.data.key)
        sessionStorage.setItem('token',myToken)

        toastSuccessNotify('User login successfully.')
        navigate("/")
      }

    } catch (error) {
      toastErrorNotify(error.message)
    }
  }

  const logOut = async (navigate)=>{
    try {
      var config = {
        method: 'post',
        url: `${url}auth/logout/`,
        headers: { 
          'Authorization': `Token ${myKey}`,
        }
      };
      const res = await axios(config)
      console.log(res)
      if (res.status === 200) {
        setCurrentUser(false)
        setMyKey(false)
        sessionStorage.clear()
        toastSuccessNotify('User log out successfully.')
        navigate("/")
      }

    } catch (error) {
      toastErrorNotify(error.message)
    }
  }

 let value = {
    createUser,
    currentUser,
    myKey,
    signIn,
    logOut
 }

  return (
    <AuthContext.Provider value={value}>
      {props.children}
    </AuthContext.Provider>
  )
}

export default AuthContextProvider;
```

## 🚩 Update "app.js" 👇

```javascript
import { ToastContainer } from 'react-toastify';
import './App.css';
import AuthContextProvider from './context/AuthContext';
import AppRouter from './router/AppRouter';

function App() {
  return (
    <div >
      <AuthContextProvider>
        <AppRouter />
        <ToastContainer />
      </AuthContextProvider>
    </div>
  );
}

export default App;
```

## 🚩 Start NavBar with [MUI](https://mui.com/material-ui/react-app-bar/#main-content) 👇

```javascript
import React, { useContext } from 'react'
import { AppBar } from '@mui/material'
import Box from '@mui/material/Box'
import Toolbar from '@mui/material/Toolbar'
import IconButton from '@mui/material/IconButton'
import Typography from '@mui/material/Typography'
import Menu from '@mui/material/Menu'
import MenuIcon from '@mui/icons-material/Menu'
import Container from '@mui/material/Container'
import Avatar from '@mui/material/Avatar'
import Button from '@mui/material/Button'
import Tooltip from '@mui/material/Tooltip'
import MenuItem from '@mui/material/MenuItem'
import AdbIcon from '@mui/icons-material/Adb'
import { useNavigate } from 'react-router-dom'
import { AuthContext } from '../context/AuthContext'

const pages = ['Products', 'Pricing', 'Blog']

const NavBar = () => {
  const navigate = useNavigate()
  const { currentUser, logOut } = useContext(AuthContext)
  const settings = currentUser
    ? ['About', 'Profile', 'NewBlog', 'Logout']
    : ['About', 'Login', 'Register']
  const [anchorElNav, setAnchorElNav] = React.useState(null)
  const [anchorElUser, setAnchorElUser] = React.useState(null)

  const handleOpenNavMenu = event => {
    setAnchorElNav(event.currentTarget)
  }
  const handleOpenUserMenu = event => {
    setAnchorElUser(event.currentTarget)
  }

  const handleCloseNavMenu = e => {
    setAnchorElNav(null)
    console.log(e.target.innerText)
    if (e.target.innerText.toLocaleLowerCase() === 'new blog') {
      navigate('/newblog')
      console.log('new blog')
    } else if (e.target.innerText.toLocaleLowerCase() === 'about') {
      navigate('/')
    }
  }

  const handleCloseUserMenu = () => {
    setAnchorElUser(null)
  }

  return (
    <AppBar position="static">
      <Container maxWidth="xl">
        <Toolbar disableGutters>
          <AdbIcon sx={{ display: { xs: 'none', md: 'flex' }, mr: 1 }} />
          <Typography
            variant="h6"
            noWrap
            onClick={() => navigate('/')}
            sx={{
              mr: 2,
              display: { xs: 'none', md: 'flex' },
              fontFamily: 'monospace',
              fontWeight: 700,
              letterSpacing: '.3rem',
              color: 'inherit',
              textDecoration: 'none',
              cursor: 'pointer',
            }}
          >
            LOGO
          </Typography>

          <Box sx={{ flexGrow: 1, display: { xs: 'flex', md: 'none' } }}>
            <IconButton
              size="large"
              aria-label="account of current user"
              aria-controls="menu-appbar"
              aria-haspopup="true"
              onClick={handleOpenNavMenu}
              color="inherit"
            >
              <MenuIcon />
            </IconButton>
            <Menu
              id="menu-appbar"
              anchorEl={anchorElNav}
              anchorOrigin={{
                vertical: 'bottom',
                horizontal: 'left',
              }}
              keepMounted
              transformOrigin={{
                vertical: 'top',
                horizontal: 'left',
              }}
              open={Boolean(anchorElNav)}
              onClose={handleCloseNavMenu}
              sx={{
                display: { xs: 'block', md: 'none' },
              }}
            >
              <MenuItem onClick={handleCloseNavMenu}>
                <Typography textAlign="center">New Blog</Typography>
              </MenuItem>
              <MenuItem onClick={handleCloseNavMenu}>
                <Typography textAlign="center">About</Typography>
              </MenuItem>
            </Menu>
          </Box>
          <AdbIcon
            sx={{ display: { xs: 'flex', md: 'none' }, mr: 1 }}
            onClick={() => navigate('/')}
          />
          <Typography
            variant="h5"
            noWrap
            onClick={() => navigate('/')}
            sx={{
              mr: 2,
              display: { xs: 'flex', md: 'none' },
              flexGrow: 1,
              fontFamily: 'monospace',
              fontWeight: 700,
              letterSpacing: '.3rem',
              color: 'inherit',
              textDecoration: 'none',
            }}
          >
            LOGO
          </Typography>
          <Box sx={{ flexGrow: 1, display: { xs: 'none', md: 'flex' } }}>
            <Button
              onClick={e => handleCloseNavMenu(e)}
              sx={{ my: 2, color: 'white', display: 'block' }}
            >
              New Blog
            </Button>{' '}
            <Button
              onClick={e => handleCloseNavMenu(e)}
              sx={{ my: 2, color: 'white', display: 'block' }}
            >
              About
            </Button>
          </Box>

          <Box sx={{ flexGrow: 0 }}>
            <Tooltip title=  {currentUser ?currentUser.username :'Authorization'}>
              <IconButton onClick={handleOpenUserMenu} sx={{ p: 0 }}>
                <Avatar alt="Emre Sharp" src={currentUser.profile_pic} />
              </IconButton>
            </Tooltip>
            <Menu
              sx={{ mt: '45px' }}
              id="menu-appbar"
              anchorEl={anchorElUser}
              anchorOrigin={{
                vertical: 'top',
                horizontal: 'right',
              }}
              keepMounted
              transformOrigin={{
                vertical: 'top',
                horizontal: 'right',
              }}
              open={Boolean(anchorElUser)}
              onClose={handleCloseUserMenu}
            >
              {settings.map(setting => (
                <MenuItem key={setting} onClick={handleCloseUserMenu}>
                  {setting === 'Logout' ? (
                    <Typography
                      onClick={() => logOut(navigate)}
                      textAlign="center"
                    >
                      {setting}
                    </Typography>
                  ) : (
                    <Typography
                      onClick={() =>
                        navigate(`/${setting.toLocaleLowerCase()}`)
                      }
                      textAlign="center"
                    >
                      {setting}
                    </Typography>
                  )}
                </MenuItem>
              ))}
            </Menu>
          </Box>
        </Toolbar>
      </Container>
    </AppBar>
  )
}

export default NavBar
```

## 💻 For "Register page" install formik and yup 👇

```bash
yarn add formik
yarn add yup
```

## 🚩 Go to "Register.jsx" and add 👇

```javascript
import * as React from 'react';
import Avatar from '@mui/material/Avatar';
import Button from '@mui/material/Button';
import CssBaseline from '@mui/material/CssBaseline';
import TextField from '@mui/material/TextField';
import Link from '@mui/material/Link';
import Paper from '@mui/material/Paper';
import Box from '@mui/material/Box';
import Grid from '@mui/material/Grid';
import LockOutlinedIcon from '@mui/icons-material/LockOutlined';
import Typography from '@mui/material/Typography';
import { createTheme, ThemeProvider } from '@mui/material/styles';
import { useNavigate } from "react-router-dom";
import {Formik,Form} from 'formik';
import * as Yup from 'yup';
import { AuthContext } from '../context/AuthContext';
import InputAdornment from '@mui/material/InputAdornment';
import SettingsAccessibilityIcon from '@mui/icons-material/SettingsAccessibility';

const theme = createTheme();

export default function Register() {

  const navigate = useNavigate()
  const {createUser} = React.useContext(AuthContext)

  return (
    <ThemeProvider theme={theme}>
      <Grid container component="main" sx={{ maxHeight: '91.5vh' }}>
        <CssBaseline />
        <Grid
          item
          xs={false}
          sm={4}
          md={7}
         style={{ backgroundImage:'https://images.unsplash.com/photo-1666059369018-bb3e93e413c0?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=687&q=80'}}
        ></Grid> 
        <Grid item xs={12} sm={8} md={5} component={Paper} elevation={6} square>

            <Box
            sx={{
              my: 8,
              mx: 4,
              display: 'flex',
              flexDirection: 'column',
              alignItems: 'center',
            }}
          >
            <Avatar sx={{ m: 1, bgcolor: 'darkslategray' }}>
              <LockOutlinedIcon />
            </Avatar>

            <Typography component="h1" variant="h5" sx={{color:"tomato"}}>
              Register
            </Typography> 
            <Formik initialValues={{userName:"",firstName:"",lastName:"",email:"",password:"",profile_pic:"",biography:"",password1:''}}
        validationSchema={Yup.object().shape({
          userName: Yup.string().required("Name is required"),
          firstName: Yup.string().required("Name is required"),
          lastName: Yup.string().required("Name is required"),
          email: Yup.string().email("Email is invalid").required("Email is required"),
          profile_pic: Yup.string().url('Please enter a valid url address').required('profile picture information must be filled'), 
          biography: Yup.string(),
          password: Yup.string().min(8,"Şifre en az 8 karakter olmalıdır").max(12).matches(/\d+/,"Şifre en az bir sayı içermelidir").matches(/[a-z]+/,"Şifre en az bir küçük harf içermelidir.").matches(/[A-Z]+/,"Şifre en az bir büyük harf içermelidir.").matches(/[!,?{}><%&$#£+-.]+/, 'Sifreniz ozel karakterlerden (!,?{}><%&$#£+-.) en az birini icermelidir.').required("Şifre gereklidir"),
          password1: Yup.string().min(8,"Şifre en az 8 karakter olmalıdır").max(12).matches(/\d+/,"Şifre en az bir sayı içermelidir").matches(/[a-z]+/,"Şifre en az bir küçük harf içermelidir.").matches(/[A-Z]+/,"Şifre en az bir büyük harf içermelidir.").matches(/[!,?{}><%&$#£+-.]+/, 'Sifreniz ozel karakterlerden (!,?{}><%&$#£+-.) en az birini icermelidir.').required("Şifre gereklidir"),
        })}
        onSubmit={(values,actions)=>{
          createUser(values.email,values.password,values.firstName,values.lastName,values.userName,values.profile_pic,values.biography,values.password1,navigate)

          // email,password,firstName,lastName,username,profile_pic,biography,password1,navigate
          actions.resetForm()
          actions.setSubmitting(false)
        }}
        >
          {({values,handleChange,errors,touched,handleBlur})=>(
        <Form>
        <Box sx={{ display: 'flex', flexDirection: 'column', gap: 3.8 }}>
          <TextField
            label="User Name"
            name="userName"
            id="userName"
            type="text"
            variant="outlined"
            value={values.userName}
            onChange={handleChange}
            onBlur={handleBlur}
            error={touched.userName && Boolean(errors.userName)}
            helperText={touched.userName && errors.userName}
          />
          <TextField
            label="First Name"
            name="firstName"
            id="firstName"
            type="text"
            variant="outlined"
            value={values.firstName}
            onChange={handleChange}
            onBlur={handleBlur}
            error={touched.firstName && Boolean(errors.firstName)}
            helperText={touched.firstName && errors.firstName}
          />
          <TextField
            label="Last Name"
            name="lastName"
            id="lastName"
            type="text"
            variant="outlined"
            value={values.lastName}
            onChange={handleChange}
            onBlur={handleBlur}
            error={touched.lastName && Boolean(errors.lastName)}
            helperText={touched.lastName && errors.lastName}
          />
          <TextField
            label="Email"
            name="email"
            id="email"
            type="email"
            variant="outlined"
            value={values.email}
            onChange={handleChange}
            onBlur={handleBlur}
            helperText={touched.email && errors.email}
            error={touched.email && Boolean(errors.email)}
          />
           <TextField
            label="Profile Picture"
            name="profile_pic"
            id="profile_pic"
            type="url"
            variant="outlined"
            value={values.profile_pic}
            onChange={handleChange}
            onBlur={handleBlur}
            error={touched.profile_pic && Boolean(errors.profile_pic)}
            helperText={touched.profile_pic && errors.profile_pic}
          />

          <TextField
            label="Biography"
            name="biography"
            id="biography"
            type="url"
            variant="outlined"
            multiline
            rows={6}
            maxRows={18}
            placeholder='Biography'
            value={values.biography}
            onChange={handleChange}
            onBlur={handleBlur}
            error={touched.biography && Boolean(errors.biography)}
            helperText={touched.biography && errors.biography}
            InputProps={{
              startAdornment: (
                  <InputAdornment position='start' >
                      <SettingsAccessibilityIcon style={{position:'absolute',top:'-1px',left:'-25px'}} />
                  </InputAdornment>
              )
          }}
          />
          <TextField
            label="password"
            name="password"
            id="password"
            type="password"
            variant="outlined"
            value={values.password}
            onChange={handleChange}
            onBlur={handleBlur}
            helperText={touched.password && errors.password}
            error={touched.password && Boolean(errors.password)}
          />
          <TextField
            label="password1"
            name="password1"
            id="password1"
            type="password"
            variant="outlined"
            value={values.password1}
            onChange={handleChange}
            onBlur={handleBlur}
            helperText={touched.password1 && errors.password1}
            error={touched.password1 && Boolean(errors.password1)}
          />
          <Button type="submit" variant="contained" size="large">
            Register
          </Button>
        </Box>
        </Form>
        )}
        </Formik>
          <Grid container>
                <Grid item>
                  <Link href="/login" variant="body2" style={{color:"darkslategray"}}>
                    {"Do you have an account? Login"}
                  </Link>
                </Grid>
              </Grid>
          </Box>
        </Grid>
      </Grid>
    </ThemeProvider>
  );
}
```

## 🚩 Go to "Login.jsx" and add 👇

```javascript
import AlternateEmailSharpIcon from '@mui/icons-material/AlternateEmailSharp';
import KeySharpIcon from '@mui/icons-material/KeySharp';
import BadgeTwoToneIcon from '@mui/icons-material/BadgeTwoTone';
import { Grid, Stack, TextField, InputAdornment, Button, Box } from '@mui/material'
import { Formik, Form } from 'formik';
import * as Yup from 'yup';
import { useNavigate } from 'react-router-dom'
import { AuthContext } from '../context/AuthContext';
import { useContext } from 'react';
// import { toastSuccessNotify } from '../helper/helper';

const Login = () => {
    const navigate = useNavigate()
    const { signIn } = useContext(AuthContext)
    return (
        <div style={{ margin: "2rem 5rem" }}>
            <Grid style={{ width: '25rem', backgroundColor: 'whitesmoke', padding: '2rem', borderRadius: '0.75rem', boxShadow: '18px 18px 25px black' }} >
                <Box style={{ textAlign: 'center', mb: 2 }}>
                    <img className='blogimg' src="https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.treehugger.com%2Ffacts-about-beautiful-bald-eagle-4857896&psig=AOvVaw1CGZQ2xeQD1BgCKrv5v22m&ust=1666295476292000&source=images&cd=vfe&ved=0CA0QjRxqFwoTCMDrmsOI7foCFQAAAAAdAAAAABAc" alt="Eagle" />
                    <h3>- Login -</h3>
                </Box>
                <Formik
                    initialValues={{ username: '', email: '', password: '' }}
                    validationSchema={Yup.object().shape({
                        username: Yup.string().max(25, 'You must enter a maximum of 25 characters').required('User Name information must be filled'),
                        email: Yup.string().email('Please enter a valid e-mail address').required('e-mail information must be filled').matches(/([\w._%+-]+@[\w.-]+\.[a-zA-Z]{0,4})/, 'Such as : asdf@dfgv.com'),
                        password: Yup.string().min(8,"Şifre en az 8 karakter olmalıdır").max(12).matches(/\d+/,"Şifre en az bir sayı içermelidir").matches(/[a-z]+/,"Şifre en az bir küçük harf içermelidir.").matches(/[A-Z]+/,"Şifre en az bir büyük harf içermelidir.").matches(/[!,?{}><%&$#£+-.]+/, 'Sifreniz ozel karakterlerden (!,?{}><%&$#£+-.) en az birini icermelidir.').required("Şifre gereklidir"),
                    })
                    }
                    onSubmit={(values, action) => {

                        // createUser(values.username, values.email, values.firstName, values.lastName, values.profile_pic, values.biography, values.password, values.password1);
                        signIn(values.email, values.password,values.username, navigate);
                        action.resetForm();
                        action.setSubmitting(false);
                        // toastSuccessNotify('Logined successfully')
                    }}
                >
                    {({ values, handleChange, errors, touched, handleBlur }) => (
                        <Form>
                            <Stack spacing={4} direction='column' >
                                <TextField
                                    label='User Name'
                                    variant='outlined'
                                    id='username'
                                    type='text'
                                    name='username'
                                    value={values.username}
                                    onChange={handleChange}
                                    placeholder='User Name'
                                    onBlur={handleBlur}
                                    helperText={touched.username && errors.username}
                                    error={touched.username && Boolean(errors.username)}
                                    InputProps={{
                                        startAdornment: (
                                            <InputAdornment position='start' >
                                                <BadgeTwoToneIcon />
                                            </InputAdornment>
                                        ),
                                    }}
                                />
                                <TextField
                                    label='E-mail'
                                    variant='outlined'
                                    id='email'
                                    type='email'
                                    name='email'
                                    placeholder='e-mail'
                                    value={values.email}
                                    onChange={handleChange}
                                    onBlur={handleBlur}
                                    helperText={touched.email && errors.email}
                                    error={touched.email && Boolean(errors.email)}
                                    InputProps={{
                                        startAdornment: (
                                            <InputAdornment position='start' >
                                                <AlternateEmailSharpIcon />
                                            </InputAdornment>
                                        )
                                    }}
                                />
                                <TextField
                                    label='Password'
                                    type='password'
                                    variant='outlined'
                                    value={values.password}
                                    name='password'
                                    onChange={handleChange}
                                    placeholder='Password'
                                    onBlur={handleBlur}
                                    helperText={touched.password && errors.password}
                                    error={touched.password && Boolean(errors.password)}
                                    InputProps={{
                                        startAdornment: (
                                            <InputAdornment position='start' >
                                                <KeySharpIcon />
                                            </InputAdornment>
                                        )
                                    }}
                                />
                                <Button variant='contained' type='submit' value='Submit' >Login</Button>
                            </Stack>
                        </Form>
                    )}
                </Formik>
            </Grid>
        </div>
    )
}

export default Login
```

## 🚩 Create "BlogContext.jsx" file under "context" folder 👇

```javascript
import axios from "axios";
import { createContext, useState } from "react";
import { toastErrorNotify, toastSuccessNotify } from "../helper/ToastNotify";

export const BlogContext = createContext();

const BlogContextProvider = (props)=>{

    const [blogs, setBlogs] = useState([]);

    const base_url = "http://127.0.0.1:8000/"

    const getBlogs = async () => {

        const blogUrl = base_url + `api/posts/?limit=${page}&offset=0`
        try {
            const res = await axios.get(blogUrl)
            setBlogs(res.data.results)
            // toastSuccessNotify('Posts fetched successfully.')
            return res;
        } catch (error) {
            toastErrorNotify(error.message)
        }
    }

    function getOneBlog(slug) {
        const result = blogs?.filter((item) => item.slug === slug);
        return result;
      }

    let value = {
        blogs,
        setBlogs,
        getBlogs,
        getOneBlog
     }

    return (
        <BlogContext.Provider value={value}>
          {props.children}
        </BlogContext.Provider>
      )
}

export default BlogContextProvider;
```

## 🚩 Update "app.js" 👇

```javascript
import { ToastContainer } from 'react-toastify';
import './App.css';
import AuthContextProvider from './context/AuthContext';
import BlogContextProvider from './context/BlogContext';
import AppRouter from './router/AppRouter';

function App() {
  return (
    <div >
      <AuthContextProvider>
        <BlogContextProvider>
          <AppRouter />
          <ToastContainer />
        </BlogContextProvider>
      </AuthContextProvider>
    </div>
  );
}

export default App;
```

## 🚩 "Home.jsx" 👇

```javascript
import React, { useContext, useEffect } from 'react'
import { AuthContext } from '../context/AuthContext';
import { BlogContext } from '../context/BlogContext';
import Card from '@mui/material/Card';
import CardHeader from '@mui/material/CardHeader';
import CardMedia from '@mui/material/CardMedia';
import CardContent from '@mui/material/CardContent';
import CardActions from '@mui/material/CardActions';
import Avatar from '@mui/material/Avatar';
import IconButton from '@mui/material/IconButton';
import Typography from '@mui/material/Typography';
import { red } from '@mui/material/colors';
import FavoriteIcon from '@mui/icons-material/Favorite';
import { Grid } from '@mui/material';
import ChatOutlinedIcon from '@mui/icons-material/ChatOutlined';
import RemoveRedEyeOutlinedIcon from '@mui/icons-material/RemoveRedEyeOutlined';
import { useNavigate } from "react-router-dom";
import { toastErrorNotify } from '../helper/ToastNotify';

const Home = () => {
  const { currentUser } = useContext(AuthContext)
  console.log(currentUser);

  const { getBlogs, blogs } = useContext(BlogContext)

  useEffect(() => {
    getBlogs();

  }, [])

  const navigate = useNavigate()
  const openDetails = (blog) => {
    if (!currentUser) {
      toastErrorNotify("Login for details of blog!");
    } else {
      navigate(`/details/${blog}`)
    }
  }
  return (
    <div>
      <Grid container spacing={2} justifyContent="center" sx={{ mt: 2 }}>
        {blogs.map((blog) => (
          <Grid item xs={12} md={6} lg={4} xl={3}>
            <Card sx={{ maxWidth: 345, height: 457, position: "relative" }}>
              <CardHeader
                avatar={
                  <Avatar alt="Emre Sharp" aria-label="blog" sx={{ bgcolor: red[500] }} />

                }
                title={blog.author}
                subheader={blog.last_updated_date.slice(0, 10)}
              />
              <div style={{ cursor: "pointer" }} onClick={() => openDetails(blog.slug)}>

                <CardMedia
                  component="img"
                  height="194"
                  image={blog.image}
                  alt={blog.title}
                />
                <CardContent>
                  <Typography gutterBottom variant="h5" component="div">
                    {blog.title}
                  </Typography>
                  <Typography sx={{
                    overflow: 'hidden',
                    textOverflow: 'ellipsis',
                    display: '-webkit-box',
                    WebkitLineClamp: '3',
                    WebkitBoxOrient: 'vertical',
                  }} variant="body2" color="text.secondary">
                    {blog.content}
                  </Typography>
                </CardContent>
              </div>
              <CardActions disableSpacing sx={{ position: "absolute", bottom: "5px", left: "5px" }}>
                <IconButton aria-label="add to favorites">
                  <FavoriteIcon />
                  <Typography sx={{ marginLeft: 1 }}>
                    {blog.like_count}
                  </Typography>
                </IconButton>
                <IconButton aria-label="comment">
                  <ChatOutlinedIcon />
                  <Typography sx={{ marginLeft: 1 }}>
                    {blog.comment_count}
                  </Typography>
                </IconButton>
                <IconButton aria-label="view">
                  <RemoveRedEyeOutlinedIcon />
                  <Typography sx={{ marginLeft: 1 }}>
                    {blog.post_view_count}
                  </Typography>
                </IconButton>
              </CardActions>
            </Card>
          </Grid>))}
      </Grid>
    </div>
  )
}

export default Home
```

## 🚩 Create "Cards.jsx" component under "components" folder 👇

```javascript
import * as React from 'react';
import Card from '@mui/material/Card';
import CardHeader from '@mui/material/CardHeader';
import CardMedia from '@mui/material/CardMedia';
import CardContent from '@mui/material/CardContent';
import CardActions from '@mui/material/CardActions';
import Avatar from '@mui/material/Avatar';
import IconButton from '@mui/material/IconButton';
import Typography from '@mui/material/Typography';
import { red } from '@mui/material/colors';
import FavoriteIcon from '@mui/icons-material/Favorite';
import ShareIcon from '@mui/icons-material/Share';
import MoreVertIcon from '@mui/icons-material/MoreVert';

const Cards = () => {
  return (
    <Card sx={{ maxWidth: 345 }}>
      <CardHeader
        avatar={
          <Avatar sx={{ bgcolor: red[500] }} alt='Selman' aria-label="recipe">

          </Avatar>
        }
        action={
          <IconButton aria-label="settings">
            <MoreVertIcon />
          </IconButton>
        }
        title="Shrimp and Chorizo Paella"
        subheader="September 14, 2016"
      />
      <CardMedia
        component="img"
        height="194"
        image="/static/images/cards/paella.jpg"
        alt="Paella dish"
      />
      <CardContent>
        <Typography variant="body2" color="text.secondary">
          This impressive paella is a perfect party dish and a fun meal to cook
          together with your guests. Add 1 cup of frozen peas along with the mussels,
          if you like.
        </Typography>
      </CardContent>
      <CardActions disableSpacing>
        <IconButton aria-label="add to favorites">
          <FavoriteIcon />
        </IconButton>
        <IconButton aria-label="like">
          <ShareIcon />
        </IconButton>
        <IconButton aria-label="view">
          <ShareIcon />
        </IconButton>

      </CardActions>
    </Card>
  )
}

export default Cards;
```

## 🚩 Update "NewBlog.jsx" 👇

```javascript
import React, { useEffect } from 'react';
import Avatar from '@mui/material/Avatar';
import Button from '@mui/material/Button';
import CssBaseline from '@mui/material/CssBaseline';
import TextField from '@mui/material/TextField';
import Paper from '@mui/material/Paper';
import Box from '@mui/material/Box';
import Grid from '@mui/material/Grid';
import NewspaperIcon from '@mui/icons-material/Newspaper';
import Typography from '@mui/material/Typography';
import { createTheme, ThemeProvider } from '@mui/material/styles';
import { Form, Link, useNavigate } from "react-router-dom";
import { FormControl } from '@mui/material';
import { AuthContext } from '../context/AuthContext';
import { InputLabel, MenuItem, Select, TextareaAutosize } from '@mui/material';
import { BlogContext } from '../context/BlogContext';

const theme = createTheme();

const NewBlog = () => {
  const navigate = useNavigate()

  const { getCategory, categories, createPost } = React.useContext(BlogContext)

  const { createUser } = React.useContext(AuthContext)

  const [newBlog, setNewBlog] = React.useState({
    "title": "",
    "category_id": 0,
    "content": "",
    "image": "",
    "status": ""
  });

  useEffect(() => {
    getCategory();
  }, [])

  console.log(newBlog);
  const handleSubmit = (e) => {
    e.preventDefault();
    createPost(newBlog);
  }

  return (
    <ThemeProvider theme={theme}>
      <Grid container component="main" sx={{ maxHeight: '91.5vh' }}>
        <CssBaseline />

        <Grid item xs={12} component={Paper} elevation={6} square>

          <Box
            sx={{
              my: 8,
              mx: 4,
              display: 'flex',
              flexDirection: 'column',
              alignItems: 'center',
            }}
          >
            <Avatar sx={{ m: 1, bgcolor: 'darkslategray' }}>
              <NewspaperIcon />
            </Avatar>

            <Typography component="h1" variant="h5" sx={{ color: "tomato" }}>
              New Blog
            </Typography>
            <form onSubmit={handleSubmit}>
              <FormControl>
                <Box sx={{ display: 'flex', flexDirection: 'column', gap: 3.8, width: 350 }}>
                  <TextField
                    label="Title"
                    name="title"
                    id="title"
                    type="text"
                    variant="outlined"
                    required
                    value={newBlog.title}
                    onChange={(e) => setNewBlog({ ...newBlog, "title": e.target.value })}
                  />
                  <FormControl>
                    <InputLabel id="demo-simple-select-helper-label">Categories</InputLabel>
                    <Select
                      labelId="demo-simple-select-helper-label"
                      id="demo-simple-select-helper"
                      name='category'
                      defaultValue=""
                      label="Categories"
                      required
                      onChange={(e) => setNewBlog({ ...newBlog, "category_id": e.target.value })}
                    >
                      <MenuItem value="">
                        <em>Categories</em>
                      </MenuItem>
                      {categories?.map((item, index) => (
                        <MenuItem key={index} value={item.id}>{item.name}</MenuItem>
                      ))}
                    </Select>
                  </FormControl>

                  <TextareaAutosize
                    minRows={5}
                    aria-label="Content"
                    placeholder="Content"
                    value={newBlog.content}
                    defaultValue=""
                    required
                    onChange={(e) => setNewBlog({ ...newBlog, "content": e.target.value })}
                  />

                  <TextField
                    label="Image URL"
                    name="image"
                    id="image"
                    type="url"
                    variant="outlined"
                    value={newBlog.image}
                    onChange={(e) => setNewBlog({ ...newBlog, "image": e.target.value })}
                  />
                  <FormControl>
                    <InputLabel id="demo-simple-select-helper-label">Status</InputLabel>
                    <Select
                      labelId="status"
                      id="status"
                      name='status'
                      defaultValue=""
                      label="Status"
                      required
                      onChange={(e) => setNewBlog({ ...newBlog, "status": e.target.value })}
                    >
                      <MenuItem value="">
                        <em>Status</em>
                      </MenuItem>
                      <MenuItem value="d">Draft</MenuItem>
                      <MenuItem value="p">Published</MenuItem>
                    </Select>
                  </FormControl>

                  <Button type="submit" variant="contained" size="large">
                    Send
                  </Button>
                </Box>
              </FormControl>
            </form>
          </Box >

        </Grid >
      </Grid >
    </ThemeProvider >
  )
}

export default NewBlog
```

## 🚩 Code "PostDetails.jsx" page 👇

```javascript
import React, { useContext, useState } from 'react'
import { useEffect } from 'react'
import { useLocation, useNavigate } from 'react-router-dom'
import { BlogContext } from '../context/BlogContext'
import Card from '@mui/material/Card';
import CardHeader from '@mui/material/CardHeader';
import CardMedia from '@mui/material/CardMedia';
import CardContent from '@mui/material/CardContent';
import CardActions from '@mui/material/CardActions';
import Avatar from '@mui/material/Avatar';
import IconButton from '@mui/material/IconButton';
import Typography from '@mui/material/Typography';
import { red } from '@mui/material/colors';
import FavoriteIcon from '@mui/icons-material/Favorite';
import ChatOutlinedIcon from '@mui/icons-material/ChatOutlined';
import RemoveRedEyeOutlinedIcon from '@mui/icons-material/RemoveRedEyeOutlined';
import CircularProgress from '@mui/material/CircularProgress';
import Box from '@mui/material/Box';
import axios from 'axios';
import { AuthContext } from '../context/AuthContext';
import { Button, Divider, InputAdornment, List, ListItem, ListItemText, TextField } from '@mui/material';

const PostDetails = () => {

  const [likeColor, setLikeColor] = useState(false);
  const [comment, setComment] = useState();
  const { getOneBlog, blogDetail, detailLoading, setComments, deletePost } = useContext(BlogContext)
  const { currentUser } = useContext(AuthContext)
  const { state } = useLocation()
  console.log(blogDetail)
  console.log(currentUser)

  const navigate = useNavigate()

  const handleChange = (e) => {
    e.preventDefault();
    const value = e.target.value;
    setComment(value);
  }

  const handleSubmit = (e) => {
    e.preventDefault();
    setComments(state.slug, comment);
    setComment("");
  }

  useEffect(() => {
    getOneBlog(state.slug)
  }, [])

  const base_url = "http://127.0.0.1:8000/"

  const token = window.atob(sessionStorage.getItem('token'));

  const like = async () => {
    var data = {
      "user_id": currentUser.id,
      "post": blogDetail.id
    };
    console.log("Like isteği yapıldı.");
    var config = {
      method: 'post',
      url: `${base_url}api/like/`,
      headers: {
        'Authorization': `Token ${token}`,
      },
      data: data
    }
    try {
      const x = await axios(config)
      getOneBlog(state.slug);
      setLikeColor(!likeColor)
      console.log(x)
    } catch (error) {
      console.log(error)
    }
  }
  const handleFavorite = () => {
    like()
  }

  return (
    <div>
      {detailLoading ? (
        <Box sx={{ display: 'flex' }}>
          <CircularProgress />
        </Box>
      ) : (
        <Box style={{ display: "flex", alignItem: "center", justifyContent: "center", position: "relative" }}>
          <Box sx={{ maxWidth: { xs: "100%", sm: "80%", md: "70%" }, minWidth:{ xs: "100%", sm: "80%", md: "40%" }, marginX: "auto",}}>

            <CardMedia
              height="500px"
              component="img"
              image={blogDetail.image}
              alt={blogDetail.title}
            />

            <CardHeader
              avatar={
                <Avatar sx={{ bgcolor: red[500] }} alt={blogDetail.author} aria-label="blog">
                </Avatar>
              }
              title={blogDetail.author}
              subheader={blogDetail.last_updated_date.slice(0, 10)}
            />
            <CardContent>
              <Typography variant="body2" color="text.secondary">
                {blogDetail.title}
              </Typography>
              <Typography variant="body2" color="text.secondary">
                {blogDetail.content}
              </Typography>
            </CardContent>
            <CardActions disableSpacing sx={{ bottom: "5px", left: "5px" }}>
              <IconButton aria-label="add to favorites">
                <FavoriteIcon onClick={() => handleFavorite()} sx={{ color: (blogDetail.like_post?.filter((like) => like.user_id === currentUser.id)[0]?.user_id) && "red" }} />
                <Typography sx={{ marginLeft: 1 }}>
                  {blogDetail.like_count}
                </Typography>
              </IconButton>
              <IconButton aria-label="comment">
                <ChatOutlinedIcon />
                <Typography sx={{ marginLeft: 1 }}>
                  {blogDetail.comment_count}
                </Typography>
              </IconButton>
              <IconButton aria-label="view">
                <RemoveRedEyeOutlinedIcon />
                <Typography sx={{ marginLeft: 1 }}>
                  {blogDetail.post_view_count}
                </Typography>
              </IconButton>
            </CardActions>

            {blogDetail.author_id === currentUser.id && <Box sx={{ my: 3, display: "flex", gap: 3 }}>
              <Button variant="contained" size="small" color="success" onClick={()=>navigate(`/update/${state.slug}`, {state: {blogDetail}})}>Update Blog</Button>
              <Button variant="contained" size="small" color="error" onClick={()=> deletePost(navigate, state.slug) }>Delete Blog</Button>
            </Box>}

            <Box>
              <List sx={{ width: '100%', maxWidth: 360, bgcolor: 'background.paper' }}>
                {blogDetail.comment_post.map((comment) => (
                  <>
                    <ListItem alignItems="flex-start">
                      <ListItemText
                        primary={comment.user}
                        secondary={
                          <React.Fragment>
                            <Typography
                              sx={{ display: 'inline', mr: 2 }}
                              component="span"
                              variant="body4"
                              color="text.secondary"
                            >
                              {(new Date(comment.time_stamp).toUTCString()).slice(0, 16)}
                            </Typography>
                            <Typography
                              component="p"
                              variant="body1"
                              color="text.primary">
                              {comment.content}
                            </Typography>
                          </React.Fragment>
                        }
                      />
                    </ListItem>
                    <Divider variant="inset" component="li" />
                  </>
                ))}
              </List>
            </Box>

            <form onSubmit={handleSubmit}>
              <Box sx={{ display: 'flex', flexDirection: 'column', gap: 3.8 }}>
                <TextField
                  label="Comment"
                  name="comment"
                  id="comment"
                  type="text"
                  variant="outlined"
                  multiline
                  rows={6}
                  maxRows={18}
                  placeholder='Add a comment'
                  value={comment}
                  onChange={handleChange}
                  InputProps={{
                    startAdornment: (
                      <InputAdornment position='start' ></InputAdornment>
                    )
                  }}
                />
                <Button type="submit" variant="contained" size="large">
                  Add Comment
                </Button>
              </Box>
            </form>
          </Box>
        </Box>
      )

      }

    </div>
  )
}

export default PostDetails;
```

## 🚩 Create getCategory function in "BlogContext.jsx" 👇

```javascript
const [categories, setCategories] = useState([]);

async function getCategory() {

    try {
      var config = {
        method: 'get',
        url: `${base_url}api/category/`,
      };
      const result = await axios(config);
      console.log(result.data);
      setCategories(result.data);
    } catch (error) {
      toastErrorNotify(error.message)
    }
  }
```

## 🚩 Add the states to sent values 👇

```javascript
let value = {
    ...
    getCategory,
    categories,
  }
```

## 🚩 Update "Home.jsx" (View More button added) 👇

```javascript
import React, { useContext, useEffect } from 'react'
import { AuthContext } from '../context/AuthContext';
import { BlogContext } from '../context/BlogContext';
import Card from '@mui/material/Card';
import CardHeader from '@mui/material/CardHeader';
import CardMedia from '@mui/material/CardMedia';
import CardContent from '@mui/material/CardContent';
import CardActions from '@mui/material/CardActions';
import Avatar from '@mui/material/Avatar';
import IconButton from '@mui/material/IconButton';
import Typography from '@mui/material/Typography';
import { red } from '@mui/material/colors';
import FavoriteIcon from '@mui/icons-material/Favorite';
import { Badge, Box, Button, Grid } from '@mui/material';
import ChatOutlinedIcon from '@mui/icons-material/ChatOutlined';
import RemoveRedEyeOutlinedIcon from '@mui/icons-material/RemoveRedEyeOutlined';
import { useNavigate } from "react-router-dom";
import { toastErrorNotify } from '../helper/ToastNotify';

const Home = () => {
  const { currentUser } = useContext(AuthContext)
  console.log(currentUser);

  const { getBlogs, blogs, getCategory, categories, page, setPage } = useContext(BlogContext)

  useEffect(() => {
    getBlogs();
    getCategory();
  }, [page])

  console.log(categories);
  const navigate = useNavigate()
  const openDetails = (slug) => {
    if (!currentUser) {
      toastErrorNotify("Login for details of blog!");
    } else {
      navigate(`/details/${slug}`, { state: { slug } })
    }
  }
  return (
    <Box>
      <Box style={{ margin: "2px auto" }}>
        <Box spacing={2}>
          <Box xs={12} md={6} lg={4} xl={3} sx={{ my:3, display:"flex", justifyContent:"center", gap:3, flexWrap:"wrap", mx: "auto" }}>
            {blogs.map((blog) => (
              <Card sx={{ width: 345, height: 457, position: "relative" }}>
                <CardHeader
                  avatar={
                    <Avatar alt="Emre Sharp" aria-label="blog" sx={{ bgcolor: red[500] }} />

                  }
                  title={blog.author}
                  subheader={blog.last_updated_date.slice(0, 10)}
                />
                <div style={{ cursor: "pointer" }} onClick={() => openDetails(blog.slug)}>

                  <CardMedia
                    component="img"
                    height="194"
                    image={blog.image}
                    alt={blog.title}
                  />
                  <CardContent>
                    <Typography gutterBottom variant="h5" component="div">
                      {blog.title}
                    </Typography>
                    <Typography sx={{
                      overflow: 'hidden',
                      textOverflow: 'ellipsis',
                      display: '-webkit-box',
                      WebkitLineClamp: '3',
                      WebkitBoxOrient: 'vertical',
                    }} variant="body2" color="text.secondary">
                      {blog.content}
                    </Typography>
                  </CardContent>

                </div>
                <CardActions disableSpacing sx={{ width: "90%", display: "flex", justifyContent: "space-between", position: "absolute", bottom: "5px", left: "5px" }}>
                  <div>
                    <IconButton aria-label="add to favorites">
                      <FavoriteIcon sx={{ color: (blog.like_post?.filter((like) => like.user_id === currentUser.id)[0]?.user_id) && "red" }} />
                      <Typography sx={{ marginLeft: 1 }}>
                        {blog.like_count}
                      </Typography>
                    </IconButton>
                    <IconButton aria-label="comment">
                      <ChatOutlinedIcon />
                      <Typography sx={{ marginLeft: 1 }}>
                        {blog.comment_count}
                      </Typography>
                    </IconButton>
                    <IconButton aria-label="view">
                      <RemoveRedEyeOutlinedIcon />
                      <Typography sx={{ marginLeft: 1 }}>
                        {blog.post_view_count}
                      </Typography>
                    </IconButton>
                  </div>
                  <div>
                    <Badge badgeContent={blog.category} color="primary" sx={{ mx: 2 }} />
                  </div>
                </CardActions>
              </Card>
            ))}
          </Box>
        </Box>
        <Box sx={{ display: "flex", justifyContent: "center", my: 3 }}>
          <Button variant="contained" size="large" onClick={() => setPage(page + 6)} >
            View More...
          </Button>
        </Box>
      </Box>
    </Box>
  )
}

export default Home
```

## 🚩 Create "createPost" function in "BlogContext.jsx" 👇

```javascript
  const createPost = async (data, navigate) => {

    const token = window.atob(sessionStorage.getItem('token'));

    var config = {
      method: 'post',
      url: `${base_url}api/posts/`,
      headers: {
        'Authorization': `Token ${token}`,
        'Content-Type': 'application/json'
      },
      data: data
    };

    try {
      console.log(data)
      const res = await axios(config);
      console.log(res)
      if (res.status === 201) {
        toastSuccessNotify("New blog created successfully.")
        navigate("/")
      }
    } catch (error) {
      toastErrorNotify(error.message);
    }
  }

  let value = {
      ...
      createPost,
    }
```

## 🚩 Update "NewBlog.jsx" 👇

```javascript
import React, { useEffect } from 'react';
import Avatar from '@mui/material/Avatar';
import Button from '@mui/material/Button';
import CssBaseline from '@mui/material/CssBaseline';
import TextField from '@mui/material/TextField';

import Paper from '@mui/material/Paper';
import Box from '@mui/material/Box';
import Grid from '@mui/material/Grid';
import NewspaperIcon from '@mui/icons-material/Newspaper';
import Typography from '@mui/material/Typography';
import { createTheme, ThemeProvider } from '@mui/material/styles';
import { Form, Link, useNavigate } from "react-router-dom";
import { FormControl } from '@mui/material';
import { AuthContext } from '../context/AuthContext';
import { InputLabel, MenuItem, Select, TextareaAutosize } from '@mui/material';
import { BlogContext } from '../context/BlogContext';

const theme = createTheme();

const NewBlog = () => {
  const navigate = useNavigate()

  const { getCategory, categories, createPost } = React.useContext(BlogContext)

  const { createUser } = React.useContext(AuthContext)

  const [newBlog, setNewBlog] = React.useState({
    "title": "",
    "category_id": 0,
    "content": "",
    "image": "",
    "status": ""
  });

  useEffect(() => {
    getCategory();
  }, [])

  console.log(newBlog);
  const handleSubmit = (e) => {
    e.preventDefault();
    createPost(newBlog, navigate);
  }

  return (
    <ThemeProvider theme={theme}>
      <Grid container component="main" sx={{ maxHeight: '91.5vh' }}>
        <CssBaseline />

        <Grid item xs={12} component={Paper} elevation={6} square>

          <Box
            sx={{
              my: 8,
              mx: 4,
              display: 'flex',
              flexDirection: 'column',
              alignItems: 'center',
            }}
          >
            <Avatar sx={{ m: 1, bgcolor: 'darkslategray' }}>
              <NewspaperIcon />
            </Avatar>

            <Typography component="h1" variant="h5" sx={{ color: "tomato" }}>
              New Blog
            </Typography>
            <form onSubmit={handleSubmit}>
              <FormControl>
                <Box sx={{ display: 'flex', flexDirection: 'column', gap: 3.8, width: 350 }}>
                  <TextField
                    label="Title"
                    name="title"
                    id="title"
                    type="text"
                    variant="outlined"
                    required
                    value={newBlog.title}
                    onChange={(e) => setNewBlog({ ...newBlog, "title": e.target.value })}
                  />
                  <FormControl>
                    <InputLabel id="demo-simple-select-helper-label">Categories</InputLabel>
                    <Select
                      labelId="demo-simple-select-helper-label"
                      id="demo-simple-select-helper"
                      name='category'
                      defaultValue=""
                      label="Categories"
                      required
                      onChange={(e) => setNewBlog({ ...newBlog, "category_id": e.target.value })}
                    >
                      <MenuItem value="">
                        <em>Categories</em>
                      </MenuItem>
                      {categories?.map((item, index) => (
                        <MenuItem key={index} value={item.id}>{item.name}</MenuItem>
                      ))}
                    </Select>
                  </FormControl>

                  <TextareaAutosize
                    minRows={5}
                    aria-label="Content"
                    placeholder="Content"
                    value={newBlog.content}
                    defaultValue=""
                    required
                    onChange={(e) => setNewBlog({ ...newBlog, "content": e.target.value })}
                  />

                  <TextField
                    label="Image URL"
                    name="image"
                    id="image"
                    type="url"
                    variant="outlined"
                    value={newBlog.image}
                    onChange={(e) => setNewBlog({ ...newBlog, "image": e.target.value })}
                  />
                  <FormControl>
                    <InputLabel id="demo-simple-select-helper-label">Status</InputLabel>
                    <Select
                      labelId="status"
                      id="status"
                      name='status'
                      defaultValue=""
                      label="Status"
                      required
                      onChange={(e) => setNewBlog({ ...newBlog, "status": e.target.value })}
                    >
                      <MenuItem value="">
                        <em>Status</em>
                      </MenuItem>
                      <MenuItem value="d">Draft</MenuItem>
                      <MenuItem value="p">Published</MenuItem>
                    </Select>
                  </FormControl>

                  <Button type="submit" variant="contained" size="large">
                    Send
                  </Button>
                </Box>
              </FormControl>
            </form>
          </Box >

        </Grid >
      </Grid >
    </ThemeProvider >
  )
}

export default NewBlog
```

## 🚩 Create "Update" and "Delete" button in "PostDetails.jsx" before the "Comments" section 👇

```javascript
  {blogDetail.author_id === currentUser.id && <Box sx={{ my: 3, display: "flex", gap: 3 }}>
              <Button variant="contained" size="small" color="success">Update Blog</Button>
              <Button variant="contained" size="small" color="error">Delete Blog</Button>
            </Box>}
```

## 🚩 Create "updatePost" function in "BlogContext.jsx" 👇

```javascript
const updatePost = async (data, navigate, slug) => {

    const token = window.atob(sessionStorage.getItem('token'));

    var config = {
      method: 'put',
      url: `${base_url}api/posts/${slug}/`,
      headers: {
        'Authorization': `Token ${token}`,
        'Content-Type': 'application/json'
      },
      data: data
    };

    try {
      console.log(data)
      const res = await axios(config);
      console.log(res)
      if (res.status === 200) {
        toastSuccessNotify("Post updated successfully.")
        navigate(-1)
      }
    } catch (error) {
      toastErrorNotify(error.message);
    }
  }

  let value = {
    ...
    updatePost
  }
```

## 🚩 Create "deletePost" function in "BlogContext.jsx" 👇

```javascript
const deletePost = async (navigate, slug) => {

    const token = window.atob(sessionStorage.getItem('token'));

    var config = {
      method: 'delete',
      url: `${base_url}api/posts/${slug}/`,
      headers: {
        'Authorization': `Token ${token}`,
        'Content-Type': 'application/json'
      }
    };

    try {
      const res = await axios(config);
      console.log(res)
      if (res.status === 204) {
        toastSuccessNotify("Post deleted successfully.")
        navigate("/")
      }
    } catch (error) {
      toastErrorNotify(error.message);
    }
  }

  let value = {
  ...
    deletePost
  }
```

## 🚩 Create "UpdateBlog" page 👇

```javascript
import React, { useEffect } from 'react';
import Avatar from '@mui/material/Avatar';
import Button from '@mui/material/Button';
import CssBaseline from '@mui/material/CssBaseline';
import TextField from '@mui/material/TextField';

import Paper from '@mui/material/Paper';
import Box from '@mui/material/Box';
import Grid from '@mui/material/Grid';
import NewspaperIcon from '@mui/icons-material/Newspaper';
import Typography from '@mui/material/Typography';
import { createTheme, ThemeProvider } from '@mui/material/styles';
import { Form, Link, useLocation, useNavigate } from "react-router-dom";
import { FormControl } from '@mui/material';
import { AuthContext } from '../context/AuthContext';
import { InputLabel, MenuItem, Select, TextareaAutosize } from '@mui/material';
import { BlogContext } from '../context/BlogContext';

const theme = createTheme();


const NewBlog = () => {
  const navigate = useNavigate()
  const {state} = useLocation()

  const { getCategory, categories, createPost, updatePost } = React.useContext(BlogContext)

  const { createUser } = React.useContext(AuthContext)

  const [newBlog, setNewBlog] = React.useState({
    "title": state.blogDetail.title,
    "category_id":state.blogDetail.category_id,
    "content": state.blogDetail.content,
    "image": state.blogDetail.image,
    "status": state.blogDetail.status
  });

  useEffect(() => {
    getCategory();
  }, [])

  console.log(newBlog);
  const handleSubmit = (e) => {
    e.preventDefault();
    updatePost(newBlog, navigate, state.blogDetail.slug);
  }

  return (
    <ThemeProvider theme={theme}>
      <Grid container component="main" sx={{ maxHeight: '91.5vh' }}>
        <CssBaseline />

        <Grid item xs={12} component={Paper} elevation={6} square>

          <Box
            sx={{
              my: 8,
              mx: 4,
              display: 'flex',
              flexDirection: 'column',
              alignItems: 'center',
            }}
          >
            <Avatar sx={{ m: 1, bgcolor: 'darkslategray' }}>
              <NewspaperIcon />
            </Avatar>

            <Typography component="h1" variant="h5" sx={{ color: "tomato" }}>
              New Blog
            </Typography>
            <form onSubmit={handleSubmit}>
              <FormControl>
                <Box sx={{ display: 'flex', flexDirection: 'column', gap: 3.8, width: 350 }}>
                  <TextField
                    label="Title"
                    name="title"
                    id="title"
                    type="text"
                    variant="outlined"
                    required
                    value={newBlog.title}
                    onChange={(e) => setNewBlog({ ...newBlog, "title": e.target.value })}
                  />
                  <FormControl>
                    <InputLabel id="demo-simple-select-helper-label">Categories</InputLabel>
                    <Select
                      labelId="demo-simple-select-helper-label"
                      id="demo-simple-select-helper"
                      name='category'
                      value={newBlog.category_id}
                      label="Categories"
                      required
                      onChange={(e) => setNewBlog({ ...newBlog, "category_id": e.target.value })}
                    >
                      <MenuItem value="">
                        <em>Categories</em>
                      </MenuItem>
                      {categories?.map((item, index) => (
                        <MenuItem key={index} value={item.id}>{item.name}</MenuItem>
                      ))}
                    </Select>
                  </FormControl>

                  <TextareaAutosize
                    minRows={5}
                    aria-label="Content"
                    placeholder="Content"
                    value={newBlog.content}
                    defaultValue=""
                    required
                    onChange={(e) => setNewBlog({ ...newBlog, "content": e.target.value })}
                  />

                  <TextField
                    label="Image URL"
                    name="image"
                    id="image"
                    type="url"
                    variant="outlined"
                    value={newBlog.image}
                    onChange={(e) => setNewBlog({ ...newBlog, "image": e.target.value })}
                  />
                  <FormControl>
                    <InputLabel id="demo-simple-select-helper-label">Status</InputLabel>
                    <Select
                      labelId="status"
                      id="status"
                      name='status'
                      value={newBlog.status}
                      label="Status"
                      required
                      onChange={(e) => setNewBlog({ ...newBlog, "status": e.target.value })}
                    >
                      <MenuItem value="">
                        <em>Status</em>
                      </MenuItem>
                      <MenuItem value="d">Draft</MenuItem>
                      <MenuItem value="p">Published</MenuItem>
                    </Select>
                  </FormControl>

                  <Button type="submit" variant="contained" size="large">
                    Send
                  </Button>
                </Box>
              </FormControl>
            </form>
          </Box >

        </Grid >
      </Grid >
    </ThemeProvider >
  )
}

export default NewBlog
```

## 🚩 Create "Profile.jsx" page 🚩

```javascript
import PhotoCamera from '@mui/icons-material/PhotoCamera';
import { CardMedia, Box, Paper, Button, Typography, IconButton, TextareaAutosize } from '@mui/material'
import React, { useContext, useState } from 'react'
import { AuthContext } from '../context/AuthContext'
import { blue } from '@mui/material/colors';
import AutoFixHighIcon from '@mui/icons-material/AutoFixHigh';

const Profile = () => {
  const { currentUser, updateProfile } = useContext(AuthContext);

  const [camera, setCamera] = useState(false)
  const [profilePic, setProfilePic] = useState("");
  const [userName, setUserName] = useState(currentUser.username);
  const [bio, setBio] = useState(currentUser.biography);
  const [userToggle, setUserToggle] = useState(false);
  const [bioToggle, setBioToggle] = useState(false);

  const handleUserClick = (data) => {
    updateProfile(data)
    setUserToggle(false);
  }

  const handleBioClick = (data) => {
    updateProfile(data)
    setBioToggle(false);
  }

  return (
    <Box style={{ display: "flex", justifyContent: "center", flexDirection: "column", alignItems: "center", margin: "1rem" }}>
      <Box>
        <CardMedia
          component="img"
          image={currentUser.profile_pic}
          alt={currentUser.username}
          sx={{ width: 300, height: 300, overflow: "hidden" }} style={{ objectFit: "cover" }}
        />
      </Box>

      <IconButton color="primary" aria-label="upload picture" component="label">
        {camera &&
          <>
            <input value={profilePic} onChange={(e) => setProfilePic(e.target.value)} type="url" placeholder='Photo URL..' />
            <Button onClick={() => updateProfile({ profile_pic: profilePic })}>Update</Button>

          </>
        }
        <PhotoCamera onClick={() => setCamera(!camera)} />
      </IconButton>
      <Box>
        <Box sx={{ width: "300px", marginBottom: 3 }}>
          {userToggle ? (<Box>
            <input value={userName} onChange={(e) => setUserName(e.target.value)} />
            <Button onClick={() => handleUserClick({ username: userName })}>Update</Button>
          </Box>) : (<Typography variant="h5" sx={{ my: 2 }}>
            <AutoFixHighIcon color="primary" sx={{ cursor: "pointer", marginRight: 2 }} onClick={() => setUserToggle(true)} />
            Username : <span style={{ color: blue[800], fontWeight: 500 }}>{currentUser.username}</span>
          </Typography>)}
        </Box>

        <Box sx={{ width: "300px", marginBottom: 3 }}>
          {bioToggle ? (<Box>
            <TextareaAutosize minRows={3} maxRows={10} value={bio} onChange={(e) => setBio(e.target.value)} />
            <Button onClick={() => handleBioClick({ biography: bio })}>Update</Button>
          </Box>) : (<Typography variant="h5" sx={{ my: 2 }}>
            <AutoFixHighIcon color="primary" sx={{ cursor: "pointer", marginRight: 2 }} onClick={() => setBioToggle(true)} />
            Biography : <span style={{ color: blue[800], fontWeight: 500 }}>{currentUser.biography}</span>
          </Typography>)}
        </Box>
      </Box>
    </Box>
  )
}

export default Profile
```

## 🚩 Create "updateProfile() in "AuthContext.jsx" 👇

```javascript
 const updateProfile = async (data) => {
    try {
      var config = {
        method: 'patch',
        url: `${url}auth/update-profile/${currentUser.id}`,
        headers: {
          'Authorization': `Token ${myKey}`,
        },
        data: data
      };
      const res = await axios(config)
      if (res.status === 200) {
        setCurrentUser(res.data)
        sessionStorage.setItem('currentuser', JSON.stringify(res.data))
        toastSuccessNotify('User updated successfully.')
      }
    } catch (error) {
      toastErrorNotify(error.message)
    }
  }

  let value = {
    ...
    updateProfile
  }
```

## 🚩 Create "userAllPosts() in "BlogContext.jsx" 👇

```javascript
  const [userPosts, setUserPosts] = useState([]);

  const usersAllPosts = async () => {
    const token = window.atob(sessionStorage.getItem('token'));
    const config = {
      method: 'get',
      url: `${base_url}api/all-posts/`,
      headers: {
        'Authorization': `Token ${token}`,
      }
    };
    try {

      const res = await axios(config)
      setUserPosts(res.data)
    } catch (error) {
      toastErrorNotify(error.message)
    }
  }

  let value = {
    ...
    usersAllPosts,
    userPosts
  }
```

## 🚩 Add the "myposts" path in "AppRouter.jsx" 👇

```javascript
  <Route path="/myposts" element={<PrivateRouter />}>
      <Route path="" element={<MyPosts />} />
  </Route>
```

## 🚩 Customize "Navbar.jsx" for "MyPosts" page under NavBar() 👇

```javascript
const settings = currentUser
    ? ['Profile', 'MyPosts', 'NewBlog', 'Logout']
    : ['About', 'Login', 'Register']
```

## 🚩 Create "About.jsx" under "pages" folder 👇

```javascript
import { Box, Typography } from '@mui/material'
import React from 'react'

const About = () => {
  return (
    <Box sx={{ height: "calc(100vh - 70px)", width: "100%", backgroundImage: 'url("https://images.unsplash.com/photo-1625838144804-300f3907c110?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1170&q=80")' }}>

      <Box sx={{ display: "flex", textAlign:"center", padding:{xs:"1rem", sm: "7% 7% 0 7%" , md: "10% 10% 0 10%", xl:"15% 15% 0 15%"}}}>
        <Typography variant="h5" sx={{ color: "white"}}>
        On this blog page; you can create blogs in the categories of "Home, Holiday, Cars, Sports, Health, People, History, Cities, Technology" and get information from blogs created by other users. Happy blogging...
        </Typography>
      </Box>
    </Box>
  )
}

export default About
```

## 🚩 Add the "/about" path in "AppRouter.jsx" 👇

```javascript
  <Route path="/about" element={<About />}/>
```

## 🚩 To add "About" page; customize "handleCloseNavMenu()" in "NavBar.jsx" 👇

```javascript
const handleCloseNavMenu = e => {
    setAnchorElNav(null)
    if (e.target.innerText.toLocaleLowerCase() === 'new blog') {
      navigate('/newblog')
    } else if (e.target.innerText.toLocaleLowerCase() === 'about') {
      navigate('/about')
    }
  }
```

## ‼ After deploying the backend part of the project to "pythonanywhere", update the "base_url" in "AuthContext" and "BlogContext" 👇

```javascript
  const base_url = "https://stocks.pythonanywhere.com/"
```

<hr>

# <center>🥳 END OF THE PROJECT 🥳</center>
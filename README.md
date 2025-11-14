<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Simple Social Media App (HTML Only)</title>
<style>
  body { font-family: Arial, sans-serif; margin: 0; background: #f0f2f5; }
  .container { max-width: 600px; margin: auto; padding: 20px; }
  .card { background: #fff; padding: 20px; border-radius: 10px; margin-bottom: 20px; }
  input, button { width: 100%; padding: 10px; margin-top: 10px; }
  button { background: #1877f2; color: #fff; border: none; cursor: pointer; border-radius: 6px; }
  .nav { display: flex; gap: 10px; margin-bottom: 20px; }
  .post { background: #fff; padding: 15px; border-radius: 10px; margin-bottom: 15px; }
  .post img { max-width: 100%; border-radius: 10px; margin-top: 10px; }
  .search-box input { width: 100%; padding: 10px; }
</style>
</head>
<body>

<div class="container">

  <!-- LOGIN PAGE -->
  <div id="loginPage" class="card">
    <h2>Login</h2>
    <input id="login_user" placeholder="Username" />
    <input id="login_pass" type="password" placeholder="Password" />
    <button onclick="login()">Login</button>
    <p>Don't have an account? <a href="#" onclick="showRegister()">Register</a></p>
  </div>

  <!-- REGISTER PAGE -->
  <div id="registerPage" class="card" style="display:none;">
    <h2>Register</h2>
    <input id="reg_user" placeholder="Username" />
    <input id="reg_pass" type="password" placeholder="Password" />
    <button onclick="register()">Create Account</button>
    <p>Already have an account? <a href="#" onclick="showLogin()">Login</a></p>
  </div>

  <!-- MAIN APP PAGE -->
  <div id="appPage" style="display:none;">
    <div class="nav">
      <button onclick="showUploader()">Upload Post</button>
      <button onclick="showFeed()">Home</button>
      <button onclick="showSearch()">Search</button>
      <button onclick="logout()">Logout</button>
    </div>

    <!-- UPLOAD POST -->
    <div id="uploadBox" class="card" style="display:none;">
      <h3>Create Post</h3>
      <input id="post_title" placeholder="Post Title" />
      <input id="post_photo" type="file" accept="image/*" />
      <button onclick="uploadPost()">Upload</button>
    </div>

    <!-- FEED -->
    <div id="feed" class="card"></div>

    <!-- SEARCH -->
    <div id="searchBox" class="card" style="display:none;">
      <h3>Search Posts</h3>
      <input id="search_input" placeholder="Search title..." oninput="searchPosts()" />
      <div id="search_results"></div>
    </div>

  </div>

</div>

<script>
// Load users & posts
let users = JSON.parse(localStorage.getItem("users") || "{}");
let posts = JSON.parse(localStorage.getItem("posts") || "[]");
let currentUser = localStorage.getItem("currentUser");

// Switch Pages
function showLogin(){ document.getElementById('loginPage').style.display='block'; document.getElementById('registerPage').style.display='none'; }
function showRegister(){ document.getElementById('loginPage').style.display='none'; document.getElementById('registerPage').style.display='block'; }
function showUploader(){ hideAll(); document.getElementById('uploadBox').style.display='block'; }
function showFeed(){ hideAll(); renderFeed(); }
function showSearch(){ hideAll(); document.getElementById('searchBox').style.display='block'; }
function hideAll(){ document.getElementById('uploadBox').style.display='none'; document.getElementById('feed').style.display='block'; document.getElementById('searchBox').style.display='none'; }

// Register
function register() {
  let u = reg_user.value.trim();
  let p = reg_pass.value;
  if(!u || !p) return alert("Fill all fields");
  if(users[u]) return alert("Username already exists");
  users[u] = p;
  localStorage.setItem("users", JSON.stringify(users));
  alert("Account created!");
  showLogin();
}

// Login
function login() {
  let u = login_user.value.trim();
  let p = login_pass.value;
  if(users[u] && users[u] === p){
    currentUser = u;
    localStorage.setItem("currentUser", u);
    loginPage.style.display='none';
    registerPage.style.display='none';
    appPage.style.display='block';
    renderFeed();
  } else {
    alert("Incorrect login");
  }
}

// Logout
function logout(){ localStorage.removeItem("currentUser"); location.reload(); }

// Upload Post
function uploadPost(){
  let title = post_title.value.trim();
  let file = post_photo.files[0];
  if(!title || !file) return alert("Fill all fields");

  let reader = new FileReader();
  reader.onload = function(){
    posts.unshift({ user: currentUser, title: title, img: reader.result, time: Date.now() });
    localStorage.setItem("posts", JSON.stringify(posts));
    alert("Posted!");
    showFeed();
  }
  reader.readAsDataURL(file);
}

// Render Feed
function renderFeed(){
  feed.innerHTML = '';
  posts.forEach(p => {
    feed.innerHTML += `
      <div class='post'>
        <b>@${p.user}</b><br>
        <span>${p.title}</span><br>
        <img src="${p.img}" />
      </div>
    `;
  });
}

// Search Posts
function searchPosts(){
  let q = search_input.value.toLowerCase();
  let result = posts.filter(p => p.title.toLowerCase().includes(q));

  search_results.innerHTML = result.map(p => `
    <div class='post'>
      <b>@${p.user}</b><br>
      ${p.title}<br>
      <img src="${p.img}" />
    </div>
  `).join('');
}

// If already logged in
if(currentUser){ loginPage.style.display='none'; registerPage.style.display='none'; appPage.style.display='block'; renderFeed(); }
</script>

</body>
</html>

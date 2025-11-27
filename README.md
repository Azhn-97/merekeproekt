(<!DOCTYPE html>
<html lang="kk">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Электронды оқу порталы</title>
<style>
  body {
    font-family: "Segoe UI", sans-serif;
    margin: 0;
    padding: 0;
    background: url('https://images.unsplash.com/photo-1581091215367-59ab6c2a1e82?auto=format&fit=crop&w=1920&q=80') no-repeat center center/cover;
    color: #fff;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .container {
    background: rgba(0, 0, 0, 0.7);
    padding: 30px;
    border-radius: 12px;
    max-width: 500px;
    width: 100%;
    box-shadow: 0 0 25px rgba(0,0,0,0.4);
  }
  h1 {
    text-align: center;
    font-size: 22px;
    margin-bottom: 25px;
    color: #00c3ff;
  }
  .btn-select {
    display: flex;
    justify-content: space-around;
    margin-bottom: 20px;
  }
  button {
    background: #00aaff;
    border: none;
    border-radius: 8px;
    padding: 10px 20px;
    color: white;
    font-size: 16px;
    cursor: pointer;
    transition: 0.3s;
  }
  button:hover {
    background: #0080cc;
  }
  .form {
    display: none;
  }
  input, select {
    width: 100%;
    padding: 10px;
    margin: 6px 0;
    border-radius: 6px;
    border: none;
    font-size: 15px;
  }
  label {
    font-size: 14px;
  }
  #studentFields { display: none; }
  .back-btn {
    background: #ff5252;
    margin-top: 10px;
  }
  .message {
    text-align: center;
    color: #00ff88;
    margin-top: 5px;
    font-size: 14px;
  }
</style>
</head>
<body>
  <div class="container">
    <div style="text-align: center;">
</div>
    <h1>Электронды оқу порталы</h1>
    <div class="btn-select">
      <button onclick="showForm('login')">Кіру</button>
      <button onclick="showForm('register')">Тіркелу</button>
    </div>

    <!-- Кіру формасы -->
    <div id="loginForm" class="form">
      <label>Email:</label>
      <input id="login_email" placeholder="Пайдаланушы email">
      <label>Құпиясөз:</label>
      <input id="login_password" type="password" placeholder="Құпиясөз">
      <button onclick="loginUser()">Кіру</button>
      <div class="message" id="login_status"></div>
      <button class="back-btn" onclick="hideForms()">Артқа</button>
    </div>

    <!-- Тіркелу формасы -->
    <div id="registerForm" class="form">
      <label>Толық аты-жөні:</label>
      <input id="reg_fullname" placeholder="Мысалы: Аманова Еркежан">
      <label>Email:</label>
      <input id="reg_email" placeholder="Email (логин)">
      <label>Құпиясөз:</label>
      <input id="reg_password" type="password" placeholder="Құпиясөз">
      <label>Телефон:</label>
      <input id="reg_phone" placeholder="Мысалы: +7 700 123 45 67">

      <label>Рөлі:</label>
      <select id="reg_role" onchange="toggleStudentFields()">
        <option value="">-- Рөлді таңдаңыз --</option>
        <option value="student">Білім алушы</option>
        <option value="teacher">Оқытушы</option>
      </select>

      <div id="studentFields">
        <label>Сынып:</label>
        <select id="reg_specialty">
          <option value="">-- Сыныпты таңдаңыз: --</option>
          <option>7А</option>
          <option>7Б</option>
          <option>7В</option>
          <option>8А</option>
          <option>8Б</option>
          <option>8В</option>
          <option>9А</option>
          <option>9Б</option>
          <option>10А</option>
          <option>11А</option>
        </select>
      </div>

      <button onclick="registerUser()">Тіркелу</button>
      <div class="message" id="reg_status"></div>
      <button class="back-btn" onclick="hideForms()">Артқа</button>
    </div>
  </div>

<script>
  const USERS_KEY = 'portal_users_v1';
  const SESSION_KEY = 'portal_current_user_v1';

  function showForm(type){
    document.getElementById('loginForm').style.display = (type === 'login') ? 'block' : 'none';
    document.getElementById('registerForm').style.display = (type === 'register') ? 'block' : 'none';
  }
  function hideForms(){
    document.getElementById('loginForm').style.display = 'none';
    document.getElementById('registerForm').style.display = 'none';
  }

  function toggleStudentFields(){
    const role = document.getElementById('reg_role').value;
    document.getElementById('studentFields').style.display = (role === 'student') ? 'block' : 'none';
  }

  function loadUsers(){ try{return JSON.parse(localStorage.getItem(USERS_KEY)||'[]')}catch(e){return[]} }
  function saveUsers(users){ localStorage.setItem(USERS_KEY, JSON.stringify(users)); }

  function registerUser(){
    const fullname = reg_fullname.value.trim(ё);
    const email = reg_email.value.trim().toLowerCase();
    const password = reg_password.value;
    const phone = reg_phone.value.trim();
    const role = reg_role.value;
    const specialty = role === 'student' ? reg_specialty.value : '';
    const group = role === 'student' ? reg_group.value : '';
    const status = reg_status;

    if(!fullname || !email || !password || !role){
      status.textContent = 'Барлық міндетті өрістерді толтырыңыз!';
      return;
    }
    if(role === 'student' && (!specialty || !group)){
      status.textContent = 'Білім алушы үшін мамандық пен топты таңдаңыз!';
      return;
    }

    const users = loadUsers();
    if(users.find(u => u.email === email)){
      status.textContent = 'Бұл email тіркелген.';
      return;
    }

    users.push({id:Date.now(),fullname,email,password,phone,role,specialty,group});
    saveUsers(users);
    status.textContent = 'Тіркелу сәтті! Енді кіруге болады.';
    status.style.color = '#00ff88';
    reg_fullname.value = reg_email.value = reg_password.value = reg_phone.value = '';
    reg_role.value = '';
    toggleStudentFields();
  }

  function loginUser(){
    const email = login_email.value.trim().toLowerCase();
    const password = login_password.value;
    const status = login_status;
    const users = loadUsers();
    const user = users.find(u => u.email === email && u.password === password);
    if(!user){ status.textContent = 'Email немесе құпиясөз дұрыс емес!'; return; }
    localStorage.setItem(SESSION_KEY, JSON.stringify(user));
    status.textContent = 'Кіру сәтті! Журнал немесе кесте бетіне өтіңіз.';
    status.style.color = '#00ff88';
    setTimeout(()=>window.location.href='basy.html',600);
  }
</script>
</body>
</html>
)

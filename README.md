<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>School Management System (CBC)</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(135deg, #2196F3 25%, #F44336 25%, #4CAF50 50%, #FFEB3B 50%, #2196F3 75%);
      background-size: 200% 200%;
      animation: bgShift 20s infinite;
      color: #333;
    }
    @keyframes bgShift {
      0% { background-position: 0% 0%; }
      50% { background-position: 100% 100%; }
      100% { background-position: 0% 0%; }
    }
    .container {
      width: 90%;
      max-width: 1000px;
      margin: 40px auto;
      background: rgba(255,255,255,0.95);
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.2);
    }
    .header {
      text-align: center;
      margin-bottom: 20px;
    }
    .nav {
      text-align: right;
      margin-bottom: 10px;
    }
    .nav button {
      margin-left: 10px;
      padding: 6px 12px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      background-color: #2980b9;
      color: white;
    }
    .sidebar {
      float: left;
      width: 200px;
      background-color: #2c3e50;
      min-height: 600px;
      color: #ecf0f1;
      padding-top: 20px;
      border-radius: 8px;
    }
    .sidebar a {
      display: block;
      color: #bdc3c7;
      padding: 12px 20px;
      text-decoration: none;
      margin-bottom: 5px;
      transition: background 0.3s, color 0.3s;
    }
    .sidebar a:hover, .sidebar a.active {
      background-color: #1abc9c;
      color: white;
    }
    .main {
      margin-left: 220px;
      padding: 20px;
    }
    .section-title {
      font-size: 24px;
      margin-bottom: 15px;
      color: #34495e;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-bottom: 15px;
    }
    th, td {
      padding: 10px;
      border: 1px solid #bdc3c7;
      text-align: left;
    }
    th {
      background-color: #34495e;
      color: #ecf0f1;
    }
    .btn {
      padding: 8px 15px;
      margin: 5px 2px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    .btn-add { background-color: #27ae60; color: white; }
    .btn-edit { background-color: #f39c12; color: white; }
    .btn-delete { background-color: #c0392b; color: white; }
    .btn-report { background-color: #2980b9; color: white; }
    .btn-secondary { background-color: #95a5a6; color: white; }
    .form-group {
      margin-bottom: 10px;
    }
    .form-group label {
      display: block;
      margin-bottom: 4px;
    }
    .form-group input, .form-group select, .form-group textarea {
      width: 100%;
      padding: 8px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }
    #reportArea {
      display: none;
      margin-top: 20px;
    }
    #reportArea table th, #reportArea table td {
      border: 1px solid #7f8c8d;
    }
    /* Simple login form styles */
    #loginForm, #signupForm {
      max-width: 400px;
      margin: 40px auto;
      background: white;
      padding: 20px;
      border-radius: 8px;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    }
    #loginForm h2, #signupForm h2 {
      text-align: center;
      margin-bottom: 20px;
    }
    .clear { clear: both; }
    @media(max-width:768px) {
      .sidebar { position: relative; width: 100%; min-height: auto; }
      .main { margin-left: 0; }
    }
  </style>
</head>
<body>

  <div class="container" id="authContainer">
    <div id="signupForm" style="display:none;">
      <h2>Sign Up</h2>
      <div class="form-group">
        <label>Username</label><input type="text" id="signUsername"/>
      </div>
      <div class="form-group">
        <label>Password</label><input type="password" id="signPassword"/>
      </div>
      <button class="btn btn-add" onclick="signup()">Register</button>
      <p>Have an account? <a href="#" onclick="showLogin()">Login</a></p>
    </div>
    <div id="loginForm">
      <h2>Login</h2>
      <div class="form-group">
        <label>Username</label><input type="text" id="loginUsername"/>
      </div>
      <div class="form-group">
        <label>Password</label><input type="password" id="loginPassword"/>
      </div>
      <button class="btn btn-add" onclick="login()">Login</button>
      <p>No account? <a href="#" onclick="showSignup()">Sign Up</a></p>
    </div>
  </div>

  <div class="container" id="appContainer" style="display:none;">
    <div class="nav">
      Logged in as <span id="currentUser"></span>
      <button class="btn btn-secondary" onclick="logout()">Logout</button>
    </div>
    <div class="sidebar">
      <a href="#" class="active" onclick="navigate('dashboard', this)">Dashboard</a>
      <a href="#" onclick="navigate('students', this)">Students</a>
      <a href="#" onclick="navigate('teachers', this)">Teachers</a>
      <a href="#" onclick="navigate('classes', this)">Classes</a>
      <a href="#" onclick="navigate('attendance', this)">Attendance</a>
      <a href="#" onclick="navigate('lessonplans', this)">Lesson Plans</a>
      <a href="#" onclick="navigate('reports', this)">Reports</a>
    </div>
    <div class="main" id="mainContent">
      <div class="header"><h1>School Management System</h1></div>
      <div id="contentArea">
        <p>Welcome! Navigate using the menu.</p>
      </div>
    </div>
    <div class="clear"></div>
  </div>

  <script>
    // Sample user account storage (in-memory)
    let users = [
      { username: 'admin', password: 'admin123' }  // sample
    ];
    let currentUser = null;

    // Sample data
    let students = [
      { id:1, name:"Alice Johnson", grade:"Grade 1", age:7 },
      { id:2, name:"Brian Smith", grade:"Grade 2", age:8 }
    ];
    let teachers = [
      { id:1, name:"Mrs. Rose", subject:"English" },
      { id:2, name:"Mr. John", subject:"Mathematics" }
    ];
    let classes = [
      { id:1, name:"Grade 1", teacherId:1 },
      { id:2, name:"Grade 2", teacherId:2 }
    ];
    let attendance = [
      // date, studentId, classId, present
      { date:"2025-09-20", studentId:1, classId:1, present:true },
      { date:"2025-09-20", studentId:2, classId:2, present:false }
    ];
    let lessonPlans = [
      // grade, subject, topic, objectives, activities, assessment
      { id:1, grade:"Grade 1", subject:"English", topic:"Alphabet", objectives:"Recognize vowels and consonants", activities:"Letter games, flashcards", assessment:"Oral recitation" },
      { id:2, grade:"Grade 2", subject:"Mathematics", topic:"Addition", objectives:"Add two‑digit numbers", activities:"Worksheets, group work", assessment:"Quiz" }
    ];

    // Authentication functions
    function showSignup(){
      document.getElementById('loginForm').style.display = 'none';
      document.getElementById('signupForm').style.display = 'block';
    }
    function showLogin(){
      document.getElementById('signupForm').style.display = 'none';
      document.getElementById('loginForm').style.display = 'block';
    }
    function signup(){
      const u = document.getElementById('signUsername').value.trim();
      const p = document.getElementById('signPassword').value;
      if(!u || !p){ alert("Fill all fields"); return; }
      if(users.find(x=>x.username===u)){
        alert("Username exists");
        return;
      }
      users.push({ username:u, password:p });
      alert("Signup success. Please log in.");
      showLogin();
    }
    function login(){
      const u = document.getElementById('loginUsername').value.trim();
      const p = document.getElementById('loginPassword').value;
      const user = users.find(x=>x.username===u && x.password===p);
      if(!user){
        alert("Invalid credentials");
        return;
      }
      currentUser = user.username;
      document.getElementById('currentUser').innerText = currentUser;
      document.getElementById('authContainer').style.display = 'none';
      document.getElementById('appContainer').style.display = 'block';
      navigate('dashboard', document.querySelector('.sidebar a'));
    }
    function logout(){
      currentUser = null;
      document.getElementById('authContainer').style.display = 'block';
      document.getElementById('appContainer').style.display = 'none';
      document.getElementById('loginUsername').value = "";
      document.getElementById('loginPassword').value = "";
    }

    // Navigation
    function navigate(section, linkElem){
      document.querySelectorAll('.sidebar a').forEach(a=>a.classList.remove('active'));
      linkElem.classList.add('active');
      const content = document.getElementById('contentArea');
      content.innerHTML = "";
      switch(section){
        case 'dashboard':
          content.innerHTML = `
            <div class="section-title">Dashboard</div>
            <p>Welcome, ${currentUser}!</p>
            <p>Total Students: ${students.length}</p>
            <p>Total Teachers: ${teachers.length}</p>
            <p>Total Classes: ${classes.length}</p>
          `;
          break;
        case 'students':
          renderStudents();
          break;
        case 'teachers':
          renderTeachers();
          break;
        case 'classes':
          renderClasses();
          break;
        case 'attendance':
          renderAttendance();
          break;
        case 'lessonplans':
          renderLessonPlans();
          break;
        case 'reports':
          renderReports();
          break;
        default:
          content.innerHTML = `<p>Section not found</p>`;
      }
    }

    /***** STUDENTS *****/
    let editingStudentId = null;
    function renderStudents(){
      const content = document.getElementById('contentArea');
      content.innerHTML = `
      <div class="section-title">Students</div>
      <button class="btn btn-add" onclick="showStudentForm()">Add Student</button>
      <div id="studentForm"></div>
      <div id="studentTable"></div>
      `;
      updateStudentTable();
    }
    function updateStudentTable(){
      const area = document.getElementById('studentTable');
      let html = `<table><thead><tr><th>ID</th><th>Name</th><th>Grade</th><th>Age</th><th>Actions</th></tr></thead><tbody>`;
      students.forEach(s=>{
        html += `<tr>
          <td>${s.id}</td>
          <td>${s.name}</td>
          <td>${s.grade}</td>
          <td>${s.age}</td>
          <td>
            <button class="btn btn-edit" onclick="editStudent(${s.id})">Edit</button>
            <button class="btn btn-delete" onclick="deleteStudent(${s.id})">Delete</button>
          </td>
        </tr>`;
      });
      html += `</tbody></table>`;
      document.getElementById('studentTable').innerHTML = html;
    }
    function showStudentForm(student = null){
      editingStudentId = student ? student.id : null;
      const formArea = document.getElementById('studentForm');
      const name = student ? student.name : "";
      const grade = student ? student.grade : "";
      const age = student ? student.age : "";
      formArea.innerHTML = `
      <div class="form-group">
        <label>Name</label><input type="text" id="studentName" value="${name}"/>
      </div>
      <div class="form-group">
        <label>Grade</label><input type="text" id="studentGrade" value="${grade}"/>
      </div>
      <div class="form-group">
        <label>Age</label><input type="number" id="studentAge" value="${age}" min="3"/>
      </div>
      <button class="btn btn-add" onclick="saveStudent()">Save</button>
      <button class="btn btn-secondary" onclick="cancelStudent()">Cancel</button>
      `;
    }
    function saveStudent(){
      const name = document.getElementById('studentName').value.trim();
      const grade = document.getElementById('studentGrade').value.trim();
      const age = parseInt(document.getElementById('studentAge').value);
      if(!name || !grade || isNaN(age)) { alert("Fill all fields properly"); return; }
      if(editingStudentId){
        const s = students.find(st=>st.id===editingStudentId);
        s.name = name; s.grade = grade; s.age = age;
      } else {
        const newId = students.length>0 ? Math.max(...students.map(s=>s.id))+1 : 1;
        students.push({ id:newId, name, grade, age });
      }
      cancelStudent();
      updateStudentTable();
    }
    function editStudent(id){
      const s = students.find(st=>st.id===id);
      if(s) showStudentForm(s);
    }
    function deleteStudent(id){
      if(confirm("Delete student?")){
        students = students.filter(st=>st.id!==id);
        updateStudentTable();
      }
    }
    function cancelStudent(){
      document.getElementById('studentForm').innerHTML = "";
    }

    /***** TEACHERS *****/
    let editingTeacherId = null;
    function renderTeachers(){
      const content = document.getElementById('contentArea');
      content.innerHTML = `
        <div class="section-title">Teachers</div>
        <button class="btn btn-add" onclick="showTeacherForm()">Add Teacher</button>
        <div id="teacherForm"></div>
        <div id="teacherTable"></div>
      `;
      updateTeacherTable();
    }
    function updateTeacherTable(){
      let area = document.getElementById('teacherTable');
      let html = `<table><thead><tr><th>ID</th><th>Name</th><th>Subject</th><th>Actions</th></tr></thead><tbody>`;
      teachers.forEach(t=>{
        html += `<tr>
          <td>${t.id}</td><td>${t.name}</td><td>${t.subject}</td>
          <td>
            <button class="btn btn-edit" onclick="editTeacher(${t.id})">Edit</button>
            <button class="btn btn-delete" onclick="deleteTeacher(${t.id})">Delete</button>
          </td>
        </tr>`;
      });
      html += `</tbody></table>`;
      document.getElementById('teacherTable').innerHTML = html;
    }
    function showTeacherForm(teacher = null){
      editingTeacherId = teacher ? teacher.id : null;
      const formArea = document.getElementById('teacherForm');
      const name = teacher ? teacher.name : "";
      const subj = teacher ? teacher.subject : "";
      formArea.innerHTML = `
        <div class="form-group"><label>Name</label><input type="text" id="teacherName" value="${name}"/></div>
        <div class="form-group"><label>Subject</label><input type="text" id="teacherSubject" value="${subj}"/></div>
        <button class="btn btn-add" onclick="saveTeacher()">Save</button>
        <button class="btn btn-secondary" onclick="cancelTeacher()">Cancel</button>
      `;
    }
    function saveTeacher(){
      const name = document.getElementById('teacherName').value.trim();
      const subj = document.getElementById('teacherSubject').value.trim();
      if(!name || !subj) { alert("Fill all fields properly"); return; }
      if(editingTeacherId){
        const t = teachers.find(th=>th.id===editingTeacherId);
        t.name = name; t.subject = subj;
      } else {
        const newId = teachers.length>0 ? Math.max(...teachers.map(t=>t.id))+1 : 1;
        teachers.push({ id:newId, name, subject:subj });
      }
      cancelTeacher();
      updateTeacherTable();
    }
    function editTeacher(id){
      const t = teachers.find(th=>th.id===id);
      if(t) showTeacherForm(t);
    }
    function deleteTeacher(id){
      if(confirm("Delete teacher?")){
        teachers = teachers.filter(th=>th.id!==id);
        updateTeacherTable();
      }
    }
    function cancelTeacher(){
      document.getElementById('teacherForm').innerHTML = "";
    }

    /***** CLASSES *****/
    let editingClassId = null;
    function renderClasses(){
      const content = document.getElementById('contentArea');
      content.innerHTML = `
        <div class="section-title">Classes</div>
        <button class="btn btn-add" onclick="showClassForm()">Add Class</button>
        <div id="classForm"></div>
        <div id="classTable"></div>
      `;
      updateClassTable();
    }
    function updateClassTable(){
      const area = document.getElementById('classTable');
      let html = `<table><thead><tr><th>ID</th><th>Name</th><th>Teacher</th><th>Actions</th></tr></thead><tbody>`;
      classes.forEach(c=>{
        const teacher = teachers.find(t=>t.id===c.teacherId);
        const teacherName = teacher ? teacher.name : "Unassigned";
        html += `<tr><td>${c.id}</td><td>${c.name}</td><td>${teacherName}</td><td>
          <button class="btn btn-edit" onclick="editClass(${c.id})">Edit</button>
          <button class="btn btn-delete" onclick="deleteClass(${c.id})">Delete</button>
        </td></tr>`;
      });
      html += `</tbody></table>`;
      document.getElementById('classTable').innerHTML = html;
    }
    function showClassForm(cls = null){
      editingClassId = cls ? cls.id : null;
      const formArea = document.getElementById('classForm');
      const name = cls ? cls.name : "";
      const teacherId = cls ? cls.teacherId : "";
      let teacherOptions = teachers.map(t=>`<option value="${t.id}" ${t.id===teacherId?"selected":""}>${t.name}</option>`).join('');
      formArea.innerHTML = `
        <div class="form-group"><label>Class Name</label><input type="text" id="className" value="${name}"/></div>
        <div class="form-group"><label>Assign Teacher</label><select id="classTeacher">${teacherOptions}</select></div>
        <button class="btn btn-add" onclick="saveClass()">Save</button>
        <button class="btn btn-secondary" onclick="cancelClass()">Cancel</button>
      `;
    }
    function saveClass(){
      const name = document.getElementById('className').value.trim();
      const teacherId = parseInt(document.getElementById('classTeacher').value);
      if(!name || isNaN(teacherId)){ alert("Fill all fields properly"); return; }
      if(editingClassId){
        const c = classes.find(cs=>cs.id===editingClassId);
        c.name = name; c.teacherId = teacherId;
      } else {
        const newId = classes.length>0 ? Math.max(...classes.map(c=>c.id))+1 : 1;
        classes.push({ id:newId, name, teacherId });
      }
      cancelClass();
      updateClassTable();
    }
    function editClass(id){
      const c = classes.find(cs=>cs.id===id);
      if(c) showClassForm(c);
    }
    function deleteClass(id){
      if(confirm("Delete class?")){
        classes = classes.filter(cs=>cs.id!==id);
        updateClassTable();
      }
    }
    function cancelClass(){
      document.getElementById('classForm').innerHTML = "";
    }

    /***** ATTENDANCE *****/
    function renderAttendance(){
      const content = document.getElementById('contentArea');
      content.innerHTML = `
        <div class="section-title">Attendance</div>
        <div class="form-group">
          <label>Select Date</label><input type="date" id="attendanceDate" value="${new Date().toISOString().split('T')[0]}"/>
        </div>
        <div class="form-group">
          <label>Select Class</label><select id="attendanceClass">${classes.map(c=>`<option value="${c.id}">${c.name}</option>`).join('')}</select>
        </div>
        <button class="btn btn-add" onclick="markAttendance()">Load Students</button>
        <div id="attendanceTableArea"></div>
      `;
    }
    function markAttendance(){
      const date = document.getElementById('attendanceDate').value;
      const classId = parseInt(document.getElementById('attendanceClass').value);
      const content = document.getElementById('attendanceTableArea');
      const studentsInClass = students.filter(s=>s.grade === classes.find(c=>c.id===classId)?.name);
      if(studentsInClass.length===0){
        content.innerHTML = `<p>No students found in that class.</p>`;
        return;
      }
      let html = `<table><thead><tr><th>Student</th><th>Present</th></tr></thead><tbody>`;
      studentsInClass.forEach(s=>{
        // find if attendance already marked
        const att = attendance.find(a=>a.date===date && a.studentId===s.id && a.classId===classId);
        const checked = att ? (att.present ? "checked" : "") : "";
        html += `<tr><td>${s.name}</td><td><input type="checkbox" data-student="${s.id}" ${checked} /></td></tr>`;
      });
      html += `</tbody></table><button class="btn btn-add" onclick="saveAttendance(${classId}, '${date}')">Save Attendance</button>`;
      content.innerHTML = html;
    }
    function saveAttendance(classId, date){
      const rows = document.querySelectorAll('#attendanceTableArea table tbody tr');
      rows.forEach(r=>{
        const studentId = parseInt(r.querySelector('input[type="checkbox"]').dataset.student);
        const present = r.querySelector('input[type="checkbox"]').checked;
        // check existing
        const idx = attendance.findIndex(a=>a.date===date && a.studentId===studentId && a.classId===classId);
        if(idx >=0){
          attendance[idx].present = present;
        } else {
          attendance.push({ date: date, studentId: studentId, classId: classId, present: present });
        }
      });
      alert("Attendance saved");
    }

    /***** LESSON PLANS *****/
    let editingLessonPlanId = null;
    function renderLessonPlans(){
      const content = document.getElementById('contentArea');
      content.innerHTML = `
        <div class="section-title">Lesson Plans</div>
        <button class="btn btn-add" onclick="showLessonPlanForm()">Add Lesson Plan</button>
        <div id="lessonPlanForm"></div>
        <div id="lessonPlanTable"></div>
      `;
      updateLessonPlanTable();
    }
    function updateLessonPlanTable(){
      const area = document.getElementById('lessonPlanTable');
      let html = `<table><thead><tr><th>ID</th><th>Grade</th><th>Subject</th><th>Topic</th><th>Actions</th></tr></thead><tbody>`;
      lessonPlans.forEach(lp=>{
        html += `<tr>
          <td>${lp.id}</td><td>${lp.grade}</td><td>${lp.subject}</td><td>${lp.topic}</td>
          <td>
            <button class="btn btn-edit" onclick="editLessonPlan(${lp.id})">Edit</button>
            <button class="btn btn-delete" onclick="deleteLessonPlan(${lp.id})">Delete</button>
          </td>
        </tr>`;
      });
      html += `</tbody></table>`;
      area.innerHTML = html;
    }
    function showLessonPlanForm(lp = null){
      editingLessonPlanId = lp ? lp.id : null;
      const formArea = document.getElementById('lessonPlanForm');
      const grade = lp ? lp.grade : "";
      const subject = lp ? lp.subject : "";
      const topic = lp ? lp.topic : "";
      const objectives = lp ? lp.objectives : "";
      const activities = lp ? lp.activities : "";
      const assessment = lp ? lp.assessment : "";
      formArea.innerHTML = `
        <div class="form-group"><label>Grade</label><input type="text" id="planGrade" value="${grade}"/></div>
        <div class="form-group"><label>Subject</label><input type="text" id="planSubject" value="${subject}"/></div>
        <div class="form-group"><label>Topic</label><input type="text" id="planTopic" value="${topic}"/></div>
        <div class="form-group"><label>Objectives</label><textarea id="planObjectives">${objectives}</textarea></div>
        <div class="form-group"><label>Activities</label><textarea id="planActivities">${activities}</textarea></div>
        <div class="form-group"><label>Assessment</label><textarea id="planAssessment">${assessment}</textarea></div>
        <button class="btn btn-add" onclick="saveLessonPlan()">Save Plan</button>
        <button class="btn btn-secondary" onclick="cancelLessonPlan()">Cancel</button>
      `;
    }
    function saveLessonPlan(){
      const grade = document.getElementById('planGrade').value.trim();
      const subject = document.getElementById('planSubject').value.trim();
      const topic = document.getElementById('planTopic').value.trim();
      const objectives = document.getElementById('planObjectives').value.trim();
      const activities = document.getElementById('planActivities').value.trim();
      const assessment = document.getElementById('planAssessment').value.trim();
      if(!grade || !subject || !topic || !objectives || !activities || !assessment){
        alert("Fill all fields");
        return;
      }
      if(editingLessonPlanId){
        const lp = lessonPlans.find(l=>l.id===editingLessonPlanId);
        lp.grade = grade; lp.subject = subject; lp.topic = topic;
        lp.objectives = objectives; lp.activities = activities; lp.assessment = assessment;
      } else {
        const newId = lessonPlans.length>0 ? Math.max(...lessonPlans.map(l=>l.id))+1 : 1;
        lessonPlans.push({ id:newId, grade, subject, topic, objectives, activities, assessment });
      }
      cancelLessonPlan();
      updateLessonPlanTable();
    }
    function editLessonPlan(id){
      const lp = lessonPlans.find(l=>l.id===id);
      if(lp) showLessonPlanForm(lp);
    }
    function deleteLessonPlan(id){
      if(confirm("Delete this lesson plan?")){
        lessonPlans = lessonPlans.filter(l=>l.id!==id);
        updateLessonPlanTable();
      }
    }
    function cancelLessonPlan(){
      document.getElementById('lessonPlanForm').innerHTML = "";
    }

    /***** REPORTS *****/
    function renderReports(){
      const content = document.getElementById('contentArea');
      content.innerHTML = `
        <div class="section-title">Reports</div>
        <button class="btn btn-report" onclick="showStudentsReport()">Students Report</button>
        <button class="btn btn-report" onclick="showTeachersReport()">Teachers Report</button>
        <button class="btn btn-report" onclick="showAttendanceReport()">Attendance Report</button>
        <button class="btn btn-report" onclick="showLessonPlansReport()">Lesson Plans Report</button>
        <div id="reportArea"></div>
      `;
    }

    function showStudentsReport(){
      const area = document.getElementById('reportArea');
      let html = `<h3>Students Report</h3><table><thead><tr><th>ID</th><th>Name</th><th>Grade</th><th>Age</th></tr></thead><tbody>`;
      students.forEach(s=>{
        html += `<tr><td>${s.id}</td><td>${s.name}</td><td>${s.grade}</td><td>${s.age}</td></tr>`;
      });
      html += `</tbody></table><button class="btn btn-report" onclick="printReport('Students Report')">Print</button>`;
      area.innerHTML = html;
      area.style.display = 'block';
    }

    function showTeachersReport(){
      const area = document.getElementById('reportArea');
      let html = `<h3>Teachers Report</h3><table><thead><tr><th>ID</th><th>Name</th><th>Subject</th></tr></thead><tbody>`;
      teachers.forEach(t=>{
        html += `<tr><td>${t.id}</td><td>${t.name}</td><td>${t.subject}</td></tr>`;
      });
      html += `</tbody></table><button class="btn btn-report" onclick="printReport('Teachers Report')">Print</button>`;
      area.innerHTML = html;
      area.style.display = 'block';
    }

    function showAttendanceReport(){
      const area = document.getElementById('reportArea');
      let html = `<h3>Attendance Report</h3><table><thead><tr><th>Date</class="th"><th>Class</th><th>Student</th><th>Status</th></tr></thead><tbody>`;
      attendance.forEach(a=>{
        const stud = students.find(s=>s.id===a.studentId);
        const cls = classes.find(c=>c.id===a.classId);
        html += `<tr><td>${a.date}</td><td>${cls ? cls.name : ""}</td><td>${stud ? stud.name : ""}</td><td>${a.present?"Present":"Absent"}</td></tr>`;
      });
      html += `</tbody></table><button class="btn btn-report" onclick="printReport('Attendance Report')">Print</button>`;
      area.innerHTML = html;
      area.style.display = 'block';
    }

    function showLessonPlansReport(){
      const area = document.getElementById('reportArea');
      let html = `<h3>Lesson Plans Report</h3><table><thead><tr><th>ID</th><th>Grade</th><th>Subject</th><th>Topic</th></tr></thead><tbody>`;
      lessonPlans.forEach(lp=>{
        html += `<tr><td>${lp.id}</td><td>${lp.grade}</td><td>${lp.subject}</td><td>${lp.topic}</td></tr>`;
      });
      html += `</tbody></table><button class="btn btn-report" onclick="printReport('Lesson Plans Report')">Print</button>`;
      area.innerHTML = html;
      area.style.display = 'block';
    }

    function printReport(title){
      const reportContent = document.getElementById('reportArea').innerHTML;
      const printWindow = window.open('', '', 'height=600,width=800');
      printWindow.document.write('<html><head><title>' + title + '</title>');
      printWindow.document.write('<style>body{font-family:Arial;} table{width:100%; border-collapse:collapse;} th,td{border:1px solid #777; padding:8px;} th{background-color:#ddd;}</style>');
      printWindow.document.write('</head><body>');
      printWindow.document.write(reportContent);
      printWindow.document.write('</body></html>');
      printWindow.document.close();
      printWindow.print();
    }

  </script>

</body>
</html>

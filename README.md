# BD-story
<html lang="bn">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Advanced Story Website</title>

<style>
*{
margin:0;
padding:0;
box-sizing:border-box;
font-family:Arial,sans-serif;
}

body{
background:#f1f4f9;
padding:20px;
transition:0.3s;
}

.dark{
background:#121212;
color:white;
}

.container{
max-width:1100px;
margin:auto;
}

.header{
background:white;
padding:20px;
border-radius:20px;
box-shadow:0 5px 15px rgba(0,0,0,0.1);
margin-bottom:20px;
}

.dark .header,
.dark .story-box,
.dark .form-box{
background:#1e1e1e;
}

.title{
font-size:35px;
font-weight:bold;
text-align:center;
color:#0d6efd;
margin-bottom:15px;
}

.top-bar{
display:flex;
gap:10px;
flex-wrap:wrap;
justify-content:center;
margin-top:15px;
}

input,
textarea,
select{
width:100%;
padding:12px;
border-radius:12px;
border:1px solid #ccc;
margin-top:10px;
font-size:16px;
}

.dark input,
.dark textarea,
.dark select{
background:#2a2a2a;
color:white;
border:1px solid #444;
}

textarea{
min-height:220px;
resize:vertical;
}

button{
border:none;
padding:12px 18px;
border-radius:12px;
cursor:pointer;
font-size:16px;
transition:0.3s;
}

.save-btn{
background:#0d6efd;
color:white;
}

.edit-btn{
background:#ffc107;
}

.delete-btn{
background:#dc3545;
color:white;
}

.like-btn{
background:#ff4081;
color:white;
}

.comment-btn{
background:#198754;
color:white;
}

.dark-btn{
background:#222;
color:white;
}

.form-box{
background:white;
padding:20px;
border-radius:20px;
box-shadow:0 5px 15px rgba(0,0,0,0.1);
margin-bottom:20px;
}

.story-box{
background:white;
padding:20px;
border-radius:20px;
margin-bottom:20px;
box-shadow:0 5px 15px rgba(0,0,0,0.1);
}

.story-title{
font-size:28px;
font-weight:bold;
margin-bottom:10px;
color:#0d6efd;
}

.story-category{
display:inline-block;
padding:6px 12px;
border-radius:30px;
background:#e7f1ff;
color:#0d6efd;
margin-bottom:15px;
}

.story-image{
width:100%;
max-height:450px;
object-fit:cover;
border-radius:15px;
margin-bottom:15px;
}

.story-content{
line-height:1.8;
white-space:pre-wrap;
font-size:17px;
}

.story-date{
margin-top:15px;
color:gray;
}

.action-buttons{
display:flex;
gap:10px;
flex-wrap:wrap;
margin-top:15px;
}

.comment-box{
margin-top:15px;
}

.single-comment{
background:#f1f1f1;
padding:10px;
border-radius:10px;
margin-top:8px;
}

.dark .single-comment{
background:#2a2a2a;
}

.visitor-box{
text-align:center;
font-size:18px;
font-weight:bold;
margin-top:10px;
}

@media(max-width:700px){
.title{
font-size:25px;
}

.story-title{
font-size:22px;
}
}
</style>
</head>
<body>

<div class="container">

<div class="header">
<div class="title">📚 Advanced Story Website</div>

<div class="visitor-box" id="visitorCounter"></div>

<div class="top-bar">
<button class="dark-btn" onclick="toggleDarkMode()">🌙 Dark Mode</button>
<button class="save-btn" onclick="downloadPDF()">📄 PDF Export</button>
</div>
</div>

<div class="form-box">
<input type="text" id="storyTitle" placeholder="গল্পের নাম লিখুন">

<select id="storyCategory">
<option value="রোমান্টিক">রোমান্টিক</option>
<option value="ভৌতিক">ভৌতিক</option>
<option value="ইসলামিক">ইসলামিক</option>
<option value="অ্যাডভেঞ্চার">অ্যাডভেঞ্চার</option>
<option value="ফানি">ফানি</option>
<option value="অন্যান্য">অন্যান্য</option>
</select>

<input type="file" id="storyImage" accept="image/*">

<textarea id="storyContent" placeholder="এখানে গল্প লিখুন..."></textarea>

<button class="save-btn" onclick="saveStory()">💾 গল্প Save করুন</button>
</div>

<input type="text" id="searchInput" placeholder="গল্প খুঁজুন..." onkeyup="searchStories()">

<select onchange="filterCategory(this.value)">
<option value="all">সব ক্যাটাগরি</option>
<option value="রোমান্টিক">রোমান্টিক</option>
<option value="ভৌতিক">ভৌতিক</option>
<option value="ইসলামিক">ইসলামিক</option>
<option value="অ্যাডভেঞ্চার">অ্যাডভেঞ্চার</option>
<option value="ফানি">ফানি</option>
<option value="অন্যান্য">অন্যান্য</option>
</select>

<div id="storyList"></div>

</div>

<script>
let stories = JSON.parse(localStorage.getItem('stories')) || [];
let editIndex = -1;

// Visitor Counter
let visitors = localStorage.getItem('visitors') || 0;
visitors++;
localStorage.setItem('visitors', visitors);
document.getElementById('visitorCounter').innerHTML = `👁 মোট ভিজিটর: ${visitors}`;

// Dark Mode
function toggleDarkMode(){
document.body.classList.toggle('dark');
localStorage.setItem('darkMode', document.body.classList.contains('dark'));
}

if(localStorage.getItem('darkMode') === 'true'){
document.body.classList.add('dark');
}

function saveStory(){
const title = document.getElementById('storyTitle').value.trim();
const category = document.getElementById('storyCategory').value;
const content = document.getElementById('storyContent').value.trim();

if(!title || !content){
alert('গল্পের নাম ও গল্প লিখুন');
return;
}

const imageInput = document.getElementById('storyImage');

if(imageInput.files[0]){
const reader = new FileReader();
reader.onload = function(e){
saveStoryData(e.target.result);
}
reader.readAsDataURL(imageInput.files[0]);
}else{
saveStoryData('');
}
}

function saveStoryData(imageData){
const title = document.getElementById('storyTitle').value.trim();
const category = document.getElementById('storyCategory').value;
const content = document.getElementById('storyContent').value.trim();

const oldImage = editIndex !== -1 ? stories[editIndex].image : '';
const oldLikes = editIndex !== -1 ? stories[editIndex].likes || 0 : 0;
const oldComments = editIndex !== -1 ? stories[editIndex].comments || [] : [];

const storyData = {
title,
category,
content,
image:imageData || oldImage,
likes:oldLikes,
comments:oldComments,
date:new Date().toLocaleString('bn-BD')
};

if(editIndex === -1){
stories.unshift(storyData);
}else{
stories[editIndex] = storyData;
editIndex = -1;
}

localStorage.setItem('stories', JSON.stringify(stories));

clearForm();
renderStories();
}

function clearForm(){
document.getElementById('storyTitle').value='';
document.getElementById('storyContent').value='';
document.getElementById('storyImage').value='';
}

function renderStories(filteredStories = stories){
const storyList = document.getElementById('storyList');

if(filteredStories.length === 0){
storyList.innerHTML = '<div class="form-box">কোনো গল্প পাওয়া যায়নি</div>';
return;
}

storyList.innerHTML = filteredStories.map((story,index)=>`
<div class="story-box">
<div class="story-title">${story.title}</div>

<div class="story-category">${story.category}</div>

${story.image ? `<img src="${story.image}" class="story-image">` : ''}

<div class="story-content">${story.content}</div>

<div class="story-date">🕒 ${story.date}</div>

<div class="action-buttons">
<button class="like-btn" onclick="likeStory(${stories.indexOf(story)})">❤️ Like (${story.likes || 0})</button>

<button class="comment-btn" onclick="addComment(${stories.indexOf(story)})">💬 Comment</button>

<button class="edit-btn" onclick="editStory(${stories.indexOf(story)})">✏️ Edit</button>

<button class="delete-btn" onclick="deleteStory(${stories.indexOf(story)})">🗑 Delete</button>
</div>

<div class="comment-box">
${(story.comments || []).map(c=>`
<div class="single-comment">💬 ${c}</div>
`).join('')}
</div>

</div>
`).join('');
}

function editStory(index){
const story = stories[index];

document.getElementById('storyTitle').value = story.title;
document.getElementById('storyCategory').value = story.category;
document.getElementById('storyContent').value = story.content;

editIndex = index;

window.scrollTo({top:0,behavior:'smooth'});
}

function deleteStory(index){
if(confirm('গল্প Delete করতে চান?')){
stories.splice(index,1);
localStorage.setItem('stories', JSON.stringify(stories));
renderStories();
}
}

function searchStories(){
const value = document.getElementById('searchInput').value.toLowerCase();

const filtered = stories.filter(story =>
story.title.toLowerCase().includes(value) ||
story.category.toLowerCase().includes(value) ||
story.content.toLowerCase().includes(value)
);

renderStories(filtered);
}

function filterCategory(category){
if(category === 'all'){
renderStories();
return;
}

const filtered = stories.filter(story => story.category === category);
renderStories(filtered);
}

function likeStory(index){
stories[index].likes = (stories[index].likes || 0) + 1;
localStorage.setItem('stories', JSON.stringify(stories));
renderStories();
}

function addComment(index){
const comment = prompt('কমেন্ট লিখুন');

if(!comment) return;

if(!stories[index].comments){
stories[index].comments = [];
}

stories[index].comments.push(comment);
localStorage.setItem('stories', JSON.stringify(stories));
renderStories();
}

function downloadPDF(){
window.print();
}

renderStories();
</script>

</body>
</html>

<script>
import { onMount } from "svelte";

const name = "Daniel Amund";
const title = "Full-Stack Web Developer";
const description = "Building fast web apps with SvelteKit, APIs and SQLite";
const email = "daniel@example.com";
const github = "https://github.com/DanielDev546";
const portfolio = "https://portfolio.example.com";

const skills = [
  "SvelteKit","Hono","SQLite","REST API","TypeScript",
  "Svelte","JavaScript","Node.js","Responsive Design","React"
];

const projects = [
{
name:"SVG Icon Generator",
description:"Dynamic SVG icon generator with live preview and customizable size, color, and stroke.",
technologies:["SvelteKit","TypeScript"]
},
{
name:"Todo App",
description:"Full-stack Todo app with SQLite and REST API integration.",
technologies:["SvelteKit","Hono","SQLite"]
},
{
name:"Portfolio Website",
description:"Single-page responsive portfolio showcasing projects and UI/UX skills.",
technologies:["SvelteKit","CSS"]
}
];

let todos = [];
let newTodo = "";
const accentColor = "#0077cc";

async function fetchTodos(){
try{
const res = await fetch('/api/todos');
todos = await res.json();
}catch(err){
console.error("Failed to fetch todos:",err);
}
}

async function addTodo(){
if(!newTodo.trim()) return;

try{
const res = await fetch('/api/todos',{
method:'POST',
headers:{'Content-Type':'application/json'},
body:JSON.stringify({title:newTodo})
});

const todo = await res.json();
todos=[...todos,todo];
newTodo="";
}catch(err){
console.error("Failed to add todo:",err);
}
}

async function toggleTodo(todo){
try{
const res = await fetch(`/api/todos/${todo.id}`,{
method:'PUT',
headers:{'Content-Type':'application/json'},
body:JSON.stringify({
title:todo.title,
completed:todo.completed ? 0 : 1
})
});

const updated = await res.json();
todos=todos.map(t=>t.id===updated.id ? updated : t);

}catch(err){
console.error("Failed to toggle todo:",err);
}
}

async function deleteTodo(id){
try{
await fetch(`/api/todos/${id}`,{method:'DELETE'});
todos=todos.filter(t=>t.id!==id);
}catch(err){
console.error("Failed to delete todo:",err);
}
}

let darkMode=false;

onMount(()=>{
darkMode=document.documentElement.classList.contains("dark");
const saved = localStorage.getItem("theme");
if(saved === "dark"){
document.documentElement.classList.add("dark");
darkMode = true;
}
fetchTodos();
});

function toggleTheme(){
darkMode=!darkMode;
document.documentElement.classList.toggle("dark",darkMode);
localStorage.setItem("theme", darkMode ? "dark" : "light");
}
</script>

<main>

<button class="theme-toggle" on:click={toggleTheme}>
{darkMode ? "Switch to Light Mode" : "Switch to Dark Mode"}
</button>

<header>
<h1>{name}</h1>
<p>{title}</p>
</header>

<section>
<h2>Skills</h2>
<ul class="skills">
{#each skills as skill}
<li>{skill}</li>
{/each}
</ul>
</section>

<section>
<h2>Projects</h2>

{#each projects as project}

<div class="project-card">

<h3>{project.name}</h3>
<p>{project.description}</p>
<p class="tech">Tech: {project.technologies.join(", ")}</p>

<button class="th">Live Demo</button>
<button class="ti">Github</button>

</div>

{/each}

</section>

<section>

<h2>Interactive Todo API Demo</h2>

<div class="todo-panel">

<div class="todo-input">

<input
type="text"
placeholder="New task..."
bind:value={newTodo}
/>

<button
on:click={addTodo}
style="background-color:{accentColor}"
>
Add
</button>

</div>

<ul class="todo-list">

{#each todos as todo}

<li class="todo-item">

<input
type="checkbox"
checked={todo.completed}
on:change={()=>toggleTodo(todo)}
/>

<span class:completed={todo.completed}>
{todo.title}
</span>

<button
class="delete"
on:click={()=>deleteTodo(todo.id)}
>
Delete
</button>

</li>

{/each}

</ul>

</div>

</section>

<section>

<h2>Contact</h2>

<p>Email: {email}</p>
<p>GitHub: <a href={github} target="_blank">{github}</a></p>
<p>Portfolio: <a href={portfolio} target="_blank">{portfolio}</a></p>

</section>

<br><br>

</main>

<style>

:global(body){
margin:0;
}

:global(html.dark),
:global(html.dark body){
background-color:#121212;
}

main{
max-width:900px;
margin:3rem auto;
background-color:#eee;
border-radius:4rem;
padding:1rem 2rem;
font-family:Arial,sans-serif;
line-height:1.6;
color:#111;
transition:background-color .3s,color .3s;
}

:global(html.dark) main{
background-color:#0e0d0d;
box-shadow:0px 5px 10px blue;
color:#eee;
}

.theme-toggle{
position:fixed;
top:20px;
right:20px;
background-color:#0e0d0d;
border:1px solid #eee;
color:white;
padding:6px 12px;
cursor:pointer;
border-radius:7px;
}

header{
text-align:center;
margin-bottom:40px;
}

h1{
font-size:2.5rem;
margin-bottom:5px;
}

h2{
font-size:1.5rem;
border-bottom:2px solid currentColor;
padding-bottom:5px;
margin-bottom:15px;
}

.skills{
list-style:none;
padding:0;
display:flex;
flex-wrap:wrap;
gap:8px;
}

.skills li{
background:#ccc;
color:#111;
padding:5px 10px;
border-radius:12px;
box-shadow:0px 0px 1.5px #eee;
font-weight:bold;
font-size:.9rem;
}

:global(html.dark) .skills li{
background:#111;
color:#eee;
}

.project-card{
border:1px solid #ccc;
border-radius:6px;
padding:10px 15px;
margin-bottom:15px;
transition:.2s ease;
}

.project-card:hover{
transform:translateY(-4px);
box-shadow:0 0px 10px blue;
}

.tech{
font-size:.85rem;
color:#555;
}

:global(html.dark) .tech{
color:#aaa;
}

.todo-panel{
border:1px solid #aaa;
padding:15px;
border-radius:6px;
margin-bottom:30px;
}

:global(html.dark) .todo-panel{
border-color:#555;
}

.todo-input{
display:flex;
gap:8px;
margin-bottom:15px;
}

.todo-input input{
flex:1;
padding:6px 8px;
font-size:1rem;
}

.todo-input button{
color:white;
border:none;
padding:6px 12px;
cursor:pointer;
border-radius:4px;
}

.todo-list{
list-style:none;
padding:0;
}

.todo-item{
display:flex;
align-items:center;
gap:10px;
margin-bottom:8px;
}

.todo-item .completed{
text-decoration:line-through;
color:#777;
}

:global(html.dark) .todo-item .completed{
color:#aaa;
}

.todo-item button.delete{
margin-left:auto;
background:#f44336;
color:white;
border:none;
padding:4px 8px;
cursor:pointer;
border-radius:4px;
}

.todo-item button.delete:hover{
background:#d32f2f;
}

.th{
border:1.5px solid blue;
padding:10px 15px;
color:hsl(0,0%,93%);
background:#000;
margin-left:5rem;
border-radius:8px;
cursor:pointer;
}

.ti{
border:1.5px solid black;
padding:10px 15px;
color:black;
font-weight:bold;
margin-left:7rem;
border-radius:8px;
cursor:pointer;
background:#eee;
}

@media(max-width:600px){

h1{font-size:2rem;}

h2{font-size:1.3rem;}

.skills{flex-direction:column;}

.todo-input{flex-direction:column;}

}

</style>
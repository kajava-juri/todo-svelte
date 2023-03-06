<script>
    export let todos = [
    {
        id: 1,
        title: "Buy groceries",
        description: "Milk, eggs, bread, and cheese",
        isDone: false
    },
    {
        id: 2,
        title: "Finish project report",
        description: "Research, write, and edit final draft",
        isDone: false
    },
    {
        id: 3,
        title: "Clean the house",
        description: "Vacuum, dust, and scrub bathrooms",
        isDone: true
    },
    {
        id: 4,
        title: "Schedule dentist appointment",
        description: "Checkup and cleaning",
        isDone: false
    },
    {
        id: 5,
        title: "Pay bills",
        description: "Electricity, water, and internet",
        isDone: true
    },
    {
        id: 6,
        title: "Take the dog for a walk",
        description: "30 minute walk around the neighborhood",
        isDone: false
    },
    {
        id: 7,
        title: "Call mom",
        description: "Catch up on family news and events",
        isDone: true
    },
    {
        id: 8,
        title: "Do laundry",
        description: "Wash, dry, and fold clothes",
        isDone: false
    },
    {
        id: 9,
        title: "Sign up for yoga class",
        description: "Find local yoga studio and enroll in beginner's class",
        isDone: false
    },
    {
        id: 10,
        title: "Read a book",
        description: "Choose a novel from the library and read for 30 minutes",
        isDone: true
    }
    ];

    let newTodoId = 11;

    import { invalid_attribute_name_character, update_await_block_branch } from "svelte/internal";
    import Modal from "../components/Modal.svelte";
    import Layout from "./+layout.svelte";

    const defaultTodoValues = {title: "", description: "", isDone: false};

    let createTodoValues = defaultTodoValues;

    let editModalOpen = false;
    let editTodoValues = defaultTodoValues;

    function handleEditButton(todo){
        editModalOpen = true;
        editTodoValues = {...todo}
    }

    function handleCloseModal(){
        editModalOpen = !editModalOpen;
        editTodoValues = defaultTodoValues;
    }

    function handleEditTodoInputChange(e){
        const { name, value, checked, type } = e.target;

        editTodoValues = {
          ...editTodoValues,
          [name]: type === "checkbox" ? checked : value,
        };
    };

    function EditTodo(e){

        e.preventDefault();

        todos = todos.map(todo => {
            if (todo.id == editTodoValues.id) {
                return editTodoValues;
            }
            return todo;
        })

        editTodoValues = defaultTodoValues;

        editModalOpen = false;
    }

    function handleDeleteButton(todoId){
        if(started){
            document.getElementById(todoId).remove();
        } else {
            todos = todos.filter( todo => todo.id !== todoId );
        }
        //setTimeout(()=>{console.log(todoHtmlCollection);}, 50)
    }

    function AddTodo(e){
        e.preventDefault();

        todos = [...todos, {...createTodoValues, id: newTodoId, isDone: false}];

        newTodoId++;
        createTodoValues = defaultTodoValues;
    }

    function handleNewTodoInputChange(e){
        const { name, value } = e.target;
    
        createTodoValues = {
        ...createTodoValues,
        [name]: value,
        }
    }
    //=============================================================================================================================
    //The bouncing todo items
    //=============================================================================================================================
    let parent;
    let todoItems = [];
    let todoHtmlCollection = [];

    let width;
    let height;

    function initiate(){
        for (let i = 0; i < todoItems.length; i++) {
            const todoItem = todoItems[i];
            if(todoItem === null) break;
            if(todoItem.classList.contains("bouncing")) continue;
            todoItem.classList.add("bouncing");
            todoItem.style.width = "fit-content";
            let { left, top } = todoItem.getBoundingClientRect()

            let x = left;
            let y = top;
            let xspeed = (Math.random() * 10) * (Math.random() < 0.5 ? -1 : 1);
            let yspeed = Math.random() * 10 * (Math.random() < 0.5 ? -1 : 1);

            todoItem.setAttribute("data-xspeed", xspeed);
            todoItem.setAttribute("data-yspeed", yspeed);
            todoItem.setAttribute("data-x", x);
            todoItem.setAttribute("data-y", y);
            todoItem.style.whiteSpace = "nowrap";
            todoItem.style.left = left + "px";
            todoItem.style.top = top + "px";
        }

        //this loop is to set absolute position after coordinates have been set
        //Because otherwise the elements will move up and the next element will have the same coordinates and they will stack up in the same position
        for (let i = 0; i < todoItems.length; i++) {
            todoItems[i].style.position = "absolute";
        }

        todoHtmlCollection = document.getElementsByClassName("todoItem");

        start();
    }

    function update(){
        for (let j = 0; j < todoHtmlCollection.length; j++) {
            const todo = todoHtmlCollection[j];

            let { x, y, xspeed, yspeed } = todo.dataset;

            todo.style.left = parseInt(x) + parseInt(xspeed) + "px";
            todo.setAttribute("data-x", parseInt(x) + parseInt(xspeed));

            todo.style.top = parseInt(y) + parseInt(yspeed) + "px";
            todo.setAttribute("data-y", parseInt(y) + parseInt(yspeed));

            checkHitBox(todo);

        }
    }
    function checkHitBox(element) {
        let { bottom, right, left, top } = element.getBoundingClientRect();
        let xspeed = parseInt(element.dataset.xspeed);
        let yspeed = parseInt(element.dataset.yspeed);
        if (right >= width || left <= 0) {
            element.setAttribute("data-xspeed", xspeed *= -1);
        }

        if (bottom >= height || top <= 0) {
            element.setAttribute("data-yspeed", yspeed *= -1);
        }
    }

    let started = false;

    function start(){

        if(!started){
            started = true;
            setInterval(() => update(), 20)
        }

    }
</script>


<svelte:window bind:innerHeight={height} bind:innerWidth={width}/>

<form on:submit={AddTodo}>
    <label for="title" style="color: white">Title</label>
    <input name="title" value={createTodoValues.title} on:change={handleNewTodoInputChange} type="text" required="required"/>

    <label for="description" style="color: white">Description</label>
    <input name="description" value={createTodoValues.description} on:change={handleNewTodoInputChange} type="text" required="required"/>

    <input type="submit" value="Submit"/>
</form>

<br/>

<button on:click={initiate} class="btn btn-danger btn-lg">Send todo items flying</button>
<!-- <button on:click={update} class="btn btn-warning btn-lg" style="z-index: 1; position:absolute; ">Update</button> -->


<ul>
    {#each todos as todo, index}
        <li class="todoItem" bind:this={todoItems[index]} id="{todo.id}">
            <p>Id: {todo.id}</p>
            <p>Title: {todo.title}</p>
            <p>Description: {todo.description}</p>
            <p>Done: {todo.isDone ? " true" : " false"}</p> 
            <button on:click={handleDeleteButton(todo.id)}>Delete</button>
            <button on:click={() => handleEditButton(todo)}>Edit</button>
        </li>
    {/each}
</ul>

<Modal open={editModalOpen} on:close={handleCloseModal}>
    <form on:submit={EditTodo}>

        <label for="title">Title: </label>
        <input type="text" name="title" value={editTodoValues.title} on:change={handleEditTodoInputChange}/>

        <label for="marked_as_done">Is done: </label>
        <input type="checkbox" name="isDone" checked={editTodoValues.isDone} on:change={handleEditTodoInputChange}/>

        <input type="submit" value="Submit"/>

    </form>
</Modal>

<style>
    p{
        color: white;
    }

    input[type=text] {
        width: 100%;
        padding: 12px 20px;
        margin: 8px 0;
        display: inline-block;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-sizing: border-box;
    }
    
    input[type=submit] {
        width: 100%;
        background-color: #ff4500;
        color: white;
        padding: 14px 20px;
        margin: 8px 0;
        border: none;
        border-radius: 4px;
        cursor: pointer;
    }
    
    input[type=submit]:hover {
        background-color: #cf3700;
    }

    .todoItem{
        margin-bottom: 16px;
        padding: 8px;
        border-bottom: solid orangered;
    }
    .todoItem:last-child{
        border-bottom: none;
    }

    :global(.positionAbsolute){
        position: absolute;
    }

</style>
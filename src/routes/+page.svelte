<script>
    export const todos = [
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

    import Modal from "../components/Modal.svelte";

    let editModalOpen = false;
    const defaultTodoValues = {title: "", isDone: false}
    let editTodoValues = defaultTodoValues;

    function handleEditButton(todo){
        editModalOpen = true;
        editTodoValues = {...todo}
        console.log(editTodoValues);
    }

    function handleCloseModal(){
        editModalOpen = !editModalOpen;
        editTodoValues = defaultTodoValues;
    }

    function handleEditTaskInputChange(e){
        const { name, value, checked, type } = e.target;

        editTodoValues = {
          ...editTodoValues,
          [name]: type === "checkbox" ? checked : value,
        };
    };

</script>

<ul>
    {#each todos as todo}
        <li>
            <p>Id: {todo.id}</p>
            <p>Title: {todo.title}</p>
            <p>Description: {todo.description}</p>
            <p>Done: {todo.isDone ? " true" : " false"}</p> 
            <button >Delete</button>
            <button on:click={() => handleEditButton(todo)}>Edit</button>
        </li>
    {/each}
</ul>

<Modal open={editModalOpen} on:close={handleCloseModal}>
    <form>

        <label for="title">Title: </label>
        <input type="text" name="title" value={editTodoValues.title}/>

        <label for="marked_as_done">Is done: </label>
        <input type="checkbox" name="marked_as_done" checked={editTodoValues.isDone} />

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
        background-color: #4CAF50;
        color: white;
        padding: 14px 20px;
        margin: 8px 0;
        border: none;
        border-radius: 4px;
        cursor: pointer;
    }
    
    input[type=submit]:hover {
        background-color: #45a049;
    }
</style>
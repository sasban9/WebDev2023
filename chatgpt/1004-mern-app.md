Sure! Let's create a small MERN (MongoDB, Express, React, Node.js) app. We’ll build a simple CRUD (Create, Read, Update, Delete) application for managing tasks. 

### **Overview**

1. **Backend (Node.js + Express + MongoDB)**:
   - Create a RESTful API to handle CRUD operations.
2. **Frontend (React)**:
   - Build a simple UI to interact with the API.

### **1. Set Up the Backend**

#### **a. Initialize the Project**

1. **Create a project directory**:
   ```bash
   mkdir mern-task-app
   cd mern-task-app
   ```

2. **Initialize a Node.js project**:
   ```bash
   npm init -y
   ```

3. **Install dependencies**:
   ```bash
   npm install express mongoose cors body-parser
   ```

4. **Create the folder structure**:
   ```bash
   mkdir server
   cd server
   mkdir models routes
   ```

#### **b. Create the Server**

1. **Create `server.js`**:
   ```javascript
   // server/server.js
   const express = require('express');
   const mongoose = require('mongoose');
   const cors = require('cors');
   const bodyParser = require('body-parser');

   const app = express();
   app.use(cors());
   app.use(bodyParser.json());

   // Connect to MongoDB
   mongoose.connect('mongodb://localhost/mern-task-app', {
     useNewUrlParser: true,
     useUnifiedTopology: true,
   });

   // Define the Task model
   const Task = mongoose.model('Task', new mongoose.Schema({
     title: String,
     completed: Boolean,
   }));

   // Routes
   app.get('/tasks', async (req, res) => {
     const tasks = await Task.find();
     res.json(tasks);
   });

   app.post('/tasks', async (req, res) => {
     const task = new Task(req.body);
     await task.save();
     res.json(task);
   });

   app.put('/tasks/:id', async (req, res) => {
     const task = await Task.findByIdAndUpdate(req.params.id, req.body, { new: true });
     res.json(task);
   });

   app.delete('/tasks/:id', async (req, res) => {
     await Task.findByIdAndDelete(req.params.id);
     res.sendStatus(204);
   });

   // Start the server
   const PORT = 5000;
   app.listen(PORT, () => console.log(`Server running on http://localhost:${PORT}`));
   ```

#### **c. Run the Backend**

1. **Start the MongoDB server**:
   ```bash
   mongod
   ```

2. **Run the Node.js server**:
   ```bash
   node server.js
   ```

### **2. Set Up the Frontend**

#### **a. Create a React App**

1. **Navigate to the root directory and create a React app**:
   ```bash
   npx create-react-app client
   cd client
   ```

2. **Install Axios for HTTP requests**:
   ```bash
   npm install axios
   ```

#### **b. Create React Components**

1. **Create a folder for components**:
   ```bash
   mkdir src/components
   ```

2. **Create `TaskList.js`**:
   ```javascript
   // client/src/components/TaskList.js
   import React, { useState, useEffect } from 'react';
   import axios from 'axios';

   function TaskList() {
     const [tasks, setTasks] = useState([]);
     const [newTask, setNewTask] = useState('');

     useEffect(() => {
       axios.get('http://localhost:5000/tasks').then((response) => {
         setTasks(response.data);
       });
     }, []);

     const addTask = () => {
       axios.post('http://localhost:5000/tasks', { title: newTask, completed: false }).then((response) => {
         setTasks([...tasks, response.data]);
         setNewTask('');
       });
     };

     const toggleCompletion = (id) => {
       const task = tasks.find(t => t._id === id);
       axios.put(`http://localhost:5000/tasks/${id}`, { completed: !task.completed }).then((response) => {
         setTasks(tasks.map(t => (t._id === id ? response.data : t)));
       });
     };

     const deleteTask = (id) => {
       axios.delete(`http://localhost:5000/tasks/${id}`).then(() => {
         setTasks(tasks.filter(t => t._id !== id));
       });
     };

     return (
       <div>
         <h1>Task List</h1>
         <input 
           type="text" 
           value={newTask} 
           onChange={(e) => setNewTask(e.target.value)} 
           placeholder="New task" 
         />
         <button onClick={addTask}>Add Task</button>
         <ul>
           {tasks.map(task => (
             <li key={task._id}>
               <span style={{ textDecoration: task.completed ? 'line-through' : 'none' }}>
                 {task.title}
               </span>
               <button onClick={() => toggleCompletion(task._id)}>
                 {task.completed ? 'Undo' : 'Complete'}
               </button>
               <button onClick={() => deleteTask(task._id)}>Delete</button>
             </li>
           ))}
         </ul>
       </div>
     );
   }

   export default TaskList;
   ```

3. **Update `App.js` to use `TaskList`**:
   ```javascript
   // client/src/App.js
   import React from 'react';
   import TaskList from './components/TaskList';

   function App() {
     return (
       <div className="App">
         <TaskList />
       </div>
     );
   }

   export default App;
   ```

#### **c. Run the Frontend**

1. **Start the React development server**:
   ```bash
   npm start
   ```

### **3. Testing the Application**

- Open your browser and go to `http://localhost:3000`.
- You should see the Task List UI.
- You can add, complete, and delete tasks. 

### **4. Additional Considerations**

1. **Error Handling**: Add error handling in your API requests and UI.
2. **Validation**: Validate user input on both the client and server sides.
3. **Deployment**: Deploy your app using platforms like Heroku, Vercel, or Netlify.

This example covers the basics of creating a MERN stack application. You can expand it with more features, such as user authentication, advanced styling, or additional functionalities. If you have any questions or need further details, feel free to ask!
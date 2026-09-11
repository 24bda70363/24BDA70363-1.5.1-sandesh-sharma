# 24BDA70363-1.5.1-sandesh-sharma
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RESTful API Dashboard</title>

    <style>
        * {
            box-sizing: border-box;
        }

        body {
            margin: 0;
            font-family: Arial, sans-serif;
            background: #f4f6f9;
            color: #222;
        }

        header {
            background: #1976d2;
            color: white;
            text-align: center;
            padding: 30px;
        }

        header h1 {
            margin: 0;
            font-size: 32px;
        }

        header p {
            margin-bottom: 0;
        }

        .container {
            width: 90%;
            max-width: 900px;
            margin: 30px auto;
        }

        .card {
            background: white;
            padding: 25px;
            margin-bottom: 20px;
            border-radius: 10px;
            box-shadow: 0 3px 12px rgba(0, 0, 0, 0.1);
        }

        h2 {
            color: #1976d2;
            margin-top: 0;
        }

        label {
            display: block;
            margin-top: 12px;
            font-weight: bold;
        }

        input {
            width: 100%;
            padding: 12px;
            margin-top: 5px;
            border: 1px solid #ccc;
            border-radius: 5px;
            font-size: 15px;
        }

        button {
            padding: 11px 18px;
            margin: 15px 5px 5px 0;
            border: none;
            border-radius: 5px;
            background: #1976d2;
            color: white;
            cursor: pointer;
            font-size: 14px;
        }

        button:hover {
            background: #125aa0;
        }

        .delete {
            background: #d32f2f;
        }

        .delete:hover {
            background: #a92323;
        }

        .clear {
            background: #555;
        }

        .clear:hover {
            background: #333;
        }

        #message {
            margin-top: 15px;
            padding: 12px;
            background: #e3f2fd;
            border-radius: 5px;
        }

        pre {
            background: #1e1e1e;
            color: #00ff66;
            padding: 20px;
            border-radius: 7px;
            min-height: 120px;
            overflow-x: auto;
            white-space: pre-wrap;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
        }

        th,
        td {
            padding: 12px;
            border: 1px solid #ddd;
            text-align: left;
        }

        th {
            background: #1976d2;
            color: white;
        }

        .empty {
            padding: 15px;
            text-align: center;
            color: #777;
        }
    </style>
</head>

<body>

<header>
    <h1>RESTful API Dashboard</h1>
    <p>Spring Boot API Project</p>
</header>

<div class="container">

    <!-- USER MANAGEMENT -->
    <div class="card">
        <h2>User Management</h2>

        <label for="userId">User ID</label>
        <input
            type="number"
            id="userId"
            placeholder="Enter ID for Get / Update / Delete"
        >

        <label for="name">Name</label>
        <input
            type="text"
            id="name"
            placeholder="Enter your name"
        >

        <label for="email">Email</label>
        <input
            type="email"
            id="email"
            placeholder="Enter your email"
        >

        <button onclick="createUser()">
            POST - Create User
        </button>

        <button onclick="updateUser()">
            PUT - Update User
        </button>

        <button class="delete" onclick="deleteUser()">
            DELETE - Delete User
        </button>
    </div>

    <!-- GET OPERATIONS -->
    <div class="card">
        <h2>GET Operations</h2>

        <button onclick="getAllUsers()">
            GET - All Users
        </button>

        <button onclick="getUserById()">
            GET - User By ID
        </button>

        <button class="clear" onclick="clearUsers()">
            Clear Data
        </button>

        <div id="message">
            Ready for operation...
        </div>
    </div>

    <!-- USERS -->
    <div class="card">
        <h2>Users</h2>

        <div id="userTable">
            <div class="empty">
                No users available.
            </div>
        </div>
    </div>

    <!-- API RESPONSE -->
    <div class="card">
        <h2>Standardized API Response</h2>

        <pre id="response">Waiting for API operation...</pre>
    </div>

</div>

<script>

    const STORAGE_KEY = "spring_boot_users";

    // Get users
    function getUsers() {
        return JSON.parse(
            localStorage.getItem(STORAGE_KEY) || "[]"
        );
    }

    // Save users
    function saveUsers(users) {
        localStorage.setItem(
            STORAGE_KEY,
            JSON.stringify(users)
        );
    }

    // Standardized API response
    function showResponse(status, message, data) {

        const apiResponse = {
            status: status,
            message: message,
            timestamp: new Date().toISOString(),
            data: data
        };

        document.getElementById("response").textContent =
            JSON.stringify(apiResponse, null, 4);
    }

    // Show message
    function showMessage(text) {
        document.getElementById("message").textContent = text;
    }

    // Validation
    function validateUser(name, email) {

        if (name.trim() === "") {
            showMessage("Name is required.");
            return false;
        }

        if (email.trim() === "") {
            showMessage("Email is required.");
            return false;
        }

        const emailPattern =
            /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

        if (!emailPattern.test(email.trim())) {
            showMessage("Please enter a valid email.");
            return false;
        }

        return true;
    }

    // POST - CREATE USER
    function createUser() {

        const name =
            document.getElementById("name").value;

        const email =
            document.getElementById("email").value;

        if (!validateUser(name, email)) {
            return;
        }

        const users = getUsers();

        const newUser = {
            id: users.length > 0
                ? Math.max(...users.map(user => user.id)) + 1
                : 1,
            name: name.trim(),
            email: email.trim()
        };

        users.push(newUser);

        saveUsers(users);

        showResponse(
            201,
            "User created successfully",
            newUser
        );

        showMessage("User created successfully.");

        displayUsers();

        clearForm();
    }

    // GET - ALL USERS
    function getAllUsers() {

        const users = getUsers();

        showResponse(
            200,
            "Users fetched successfully",
            users
        );

        showMessage("GET /api/users successful.");

        displayUsers();
    }

    // GET - USER BY ID
    function getUserById() {

        const id =
            Number(document.getElementById("userId").value);

        if (!id) {
            showMessage("Please enter User ID.");
            return;
        }

        const users = getUsers();

        const user =
            users.find(user => user.id === id);

        if (!user) {

            showResponse(
                404,
                "User not found",
                null
            );

            showMessage("User not found.");

            return;
        }

        showResponse(
            200,
            "User fetched successfully",
            user
        );

        showMessage("User found.");
    }

    // PUT - UPDATE USER
    function updateUser() {

        const id =
            Number(document.getElementById("userId").value);

        const name =
            document.getElementById("name").value;

        const email =
            document.getElementById("email").value;

        if (!id) {
            showMessage("Enter User ID.");
            return;
        }

        if (!validateUser(name, email)) {
            return;
        }

        const users = getUsers();

        const index =
            users.findIndex(user => user.id === id);

        if (index === -1) {

            showResponse(
                404,
                "User not found",
                null
            );

            showMessage("User not found.");

            return;
        }

        users[index] = {
            id: id,
            name: name.trim(),
            email: email.trim()
        };

        saveUsers(users);

        showResponse(
            200,
            "User updated successfully",
            users[index]
        );

        showMessage("User updated successfully.");

        displayUsers();
    }

    // DELETE USER
    function deleteUser() {

        const id =
            Number(document.getElementById("userId").value);

        if (!id) {
            showMessage("Enter User ID.");
            return;
        }

        const users = getUsers();

        const index =
            users.findIndex(user => user.id === id);

        if (index === -1) {

            showResponse(
                404,
                "User not found",
                null
            );

            showMessage("User not found.");

            return;
        }

        const deletedUser =
            users.splice(index, 1)[0];

        saveUsers(users);

        showResponse(
            200,
            "User deleted successfully",
            deletedUser
        );

        showMessage("User deleted successfully.");

        displayUsers();

        clearForm();
    }

    // DISPLAY USERS
    function displayUsers() {

        const users = getUsers();

        const table =
            document.getElementById("userTable");

        if (users.length === 0) {

            table.innerHTML =
                '<div class="empty">No users available.</div>';

            return;
        }

        let html = `
            <table>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                </tr>
        `;

        users.forEach(user => {

            html += `
                <tr>
                    <td>${user.id}</td>
                    <td>${user.name}</td>
                    <td>${user.email}</td>
                </tr>
            `;
        });

        html += "</table>";

        table.innerHTML = html;
    }

    // CLEAR FORM
    function clearForm() {

        document.getElementById("userId").value = "";
        document.getElementById("name").value = "";
        document.getElementById("email").value = "";
    }

    // CLEAR ALL DATA
    function clearUsers() {

        localStorage.removeItem(STORAGE_KEY);

        displayUsers();

        showResponse(
            200,
            "All demo data cleared",
            []
        );

        showMessage("All data cleared.");
    }

    // Load data
    displayUsers();

</script>

</body>
</html>

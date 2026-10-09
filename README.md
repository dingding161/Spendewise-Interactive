# Spendewise-Interactive
<h1>SpendWise Budget Tracker</h1>

<section>
    <h2>Set Your Budget</h2>

    <form id="budget-form">
        <label for="budget-input">Your budget (KES):</label>
        <input
            type="number"
            id="budget-input"
            min="0"
            step="0.01"
            required
        >
        <button type="submit">Set Budget</button>
    </form>

    <p id="budget-message">Please set your budget.</p>
</section>

<section>
    <h2>Budget Summary</h2>
    <p>Total Budget: <span id="total-budget">KES 0.00</span></p>
    <p>Total Expenses: <span id="total-expenses">KES 0.00</span></p>
    <p>Remaining Balance: <span id="remaining-balance">KES 0.00</span></p>
</section>

<section>
    <h2>Add Expense</h2>

    <form id="expense-form">
        <label for="expense-name">Expense Name:</label>
        <input type="text" id="expense-name" required>

        <label for="expense-amount">Amount (KES):</label>
        <input
            type="number"
            id="expense-amount"
            min="0.01"
            step="0.01"
            required
        >

        <label for="expense-category">Category:</label>
        <select id="expense-category" required>
            <option value="">Choose a category</option>
            <option value="Food">Food</option>
            <option value="Transport">Transport</option>
            <option value="Rent">Rent</option>
            <option value="Entertainment">Entertainment</option>
            <option value="Other">Other</option>
        </select>

        <button type="submit">Add Expense</button>
    </form>
</section>

<section>
    <h2>Your Expenses</h2>

    <p id="expense-count">Number of expenses: 0</p>

    <table>
        <thead>
            <tr>
                <th>Name</th>
                <th>Amount</th>
                <th>Category</th>
                <th>Action</th>
            </tr>
        </thead>
        <tbody id="expense-list"></tbody>
    </table>
</section>

<script src="script.js" defer></script>
// Store the budget and expenses
let budget = 0;
const expenses = [];

// Select HTML elements
const budgetForm = document.getElementById("budget-form");
const budgetInput = document.getElementById("budget-input");
const budgetMessage = document.getElementById("budget-message");

const totalBudgetElement = document.getElementById("total-budget");
const totalExpensesElement = document.getElementById("total-expenses");
const remainingBalanceElement =
    document.getElementById("remaining-balance");

const expenseForm = document.getElementById("expense-form");
const expenseName = document.getElementById("expense-name");
const expenseAmount = document.getElementById("expense-amount");
const expenseCategory = document.getElementById("expense-category");

const expenseList = document.getElementById("expense-list");
const expenseCount = document.getElementById("expense-count");

// Format money
function formatMoney(amount) {
    return "KES " + amount.toFixed(2);
}

// Calculate all expenses using a loop
function calculateTotalExpenses() {
    let total = 0;

    for (const expense of expenses) {
        total += expense.amount;
    }

    return total;
}

// Calculate the remaining balance
function calculateBalance() {
    return budget - calculateTotalExpenses();
}

// Make decisions using if and else
function updateBudgetMessage(balance) {
    if (budget === 0) {
        budgetMessage.textContent = "Please set your budget.";
    } else if (balance < 0) {
        budgetMessage.textContent =
            "Warning: You have exceeded your budget!";
    } else if (balance === 0) {
        budgetMessage.textContent =
            "You have used your entire budget.";
    } else {
        budgetMessage.textContent =
            "Good job! You are within your budget.";
    }
}

// Display expenses on the webpage
function displayExpenses() {
    expenseList.replaceChildren();

    if (expenses.length === 0) {
        const row = document.createElement("tr");
        const cell = document.createElement("td");

        cell.colSpan = 4;
        cell.textContent = "No expenses yet.";

        row.appendChild(cell);
        expenseList.appendChild(row);
    }

    // Loop through every expense
    for (const expense of expenses) {
        const row = document.createElement("tr");

        const nameCell = document.createElement("td");
        nameCell.textContent = expense.name;

        const amountCell = document.createElement("td");
        amountCell.textContent = formatMoney(expense.amount);

        const categoryCell = document.createElement("td");
        categoryCell.textContent = expense.category;

        const actionCell = document.createElement("td");
        const deleteButton = document.createElement("button");

        deleteButton.textContent = "Delete";
        deleteButton.type = "button";

        // Listen for a delete button click
        deleteButton.addEventListener("click", function () {
            const index = expenses.findIndex(
                item => item.id === expense.id
            );

            if (index !== -1) {
                expenses.splice(index, 1);
                updateDashboard();
            }
        });

        actionCell.appendChild(deleteButton);

        row.append(
            nameCell,
            amountCell,
            categoryCell,
            actionCell
        );

        expenseList.appendChild(row);
    }

    expenseCount.textContent =
        "Number of expenses: " + expenses.length;
}

// Update the dashboard
function updateDashboard() {
    const total = calculateTotalExpenses();
    const balance = budget - total;

    totalBudgetElement.textContent = formatMoney(budget);
    totalExpensesElement.textContent = formatMoney(total);
    remainingBalanceElement.textContent = formatMoney(balance);

    updateBudgetMessage(balance);
    displayExpenses();
}

// Listen for budget form submission
budgetForm.addEventListener("submit", function (event) {
    event.preventDefault();

    const newBudget = Number(budgetInput.value);

    if (
        budgetInput.value.trim() === "" ||
        !Number.isFinite(newBudget) ||
        newBudget < 0
    ) {
        budgetMessage.textContent =
            "Please enter a valid budget.";
        return;
    }

    budget = newBudget;
    updateDashboard();
});

// Listen for adding a new expense
expenseForm.addEventListener("submit", function (event) {
    event.preventDefault();

    const name = expenseName.value.trim();
    const amount = Number(expenseAmount.value);
    const category = expenseCategory.value;

    if (
        name === "" ||
        expenseAmount.value.trim() === "" ||
        !Number.isFinite(amount) ||
        amount <= 0 ||
        category === ""
    ) {
        alert("Please enter valid expense details.");
        return;
    }

    // Store the expense as an object in an array
    expenses.push({
        id: Date.now() + Math.random(),
        name: name,
        amount: amount,
        category: category
    });

    expenseForm.reset();
    updateDashboard();
});

// Show the initial dashboard
updateDashboard();

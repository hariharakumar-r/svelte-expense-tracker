<script>
  let name = '';
  let amount = '';
  let expenses = [];

  // Load expenses from LocalStorage on mount
  if (typeof window !== 'undefined') {
    const savedExpenses = localStorage.getItem('expenses');
    if (savedExpenses) {
      expenses = JSON.parse(savedExpenses);
    }
  }

  // Save expenses to LocalStorage whenever they change
  $: {
    if (typeof window !== 'undefined') {
      localStorage.setItem('expenses', JSON.stringify(expenses));
    }
  }

  // Add a new expense
  function addExpense(event) {
    event.preventDefault();
    if (name && amount && !isNaN(amount)) {
      expenses = [
        ...expenses,
        { id: Date.now(), name, amount: parseFloat(amount) }
      ];
      name = '';
      amount = '';
    }
  }

  // Delete an expense
  function deleteExpense(id) {
    expenses = expenses.filter(expense => expense.id !== id);
  }

  // Calculate total expenses
  $: total = expenses.reduce((sum, expense) => sum + expense.amount, 0).toFixed(2);
</script>

<main class="container">
  <h1>Expense Tracker</h1>
  <form on:submit={addExpense}>
    <input
      type="text"
      bind:value={name}
      placeholder="Expense name"
      required
    />
    <input
      type="number"
      bind:value={amount}
      placeholder="Amount"
      step="0.01"
      required
    />
    <button type="submit">Add Expense</button>
  </form>

  {#if expenses.length > 0}
    <h2>Total: ${total}</h2>
    <ul>
      {#each expenses as expense (expense.id)}
        <li>
          {expense.name}: ${expense.amount.toFixed(2)}
          <button class="outline" on:click={() => deleteExpense(expense.id)}>
            Delete
          </button>
        </li>
      {/each}
    </ul>
  {:else}
    <p>No expenses yet.</p>
  {/if}
</main>

<style>
  main {
    max-width: 600px;
    margin: 2rem auto;
  }
  form {
    display: grid;
    gap: 1rem;
    margin-bottom: 2rem;
  }
  ul {
    list-style: none;
    padding: 0;
  }
  li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0.5rem;
    border-bottom: 1px solid #eee;
  }
</style>
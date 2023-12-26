<script setup>
	import { ref, computed, onMounted } from "vue";
	import { useToast } from "vue-toastification";
	import TheHeader from "./components/TheHeader.vue";
	import TheBalance from "./components/TheBalance.vue";
	import TheIncomeExpense from "./components/TheIncomeExpense.vue";
	import TheTransactionsList from "./components/TheTransactionsList.vue";
	import TheTransactionsForm from "./components/TheTransactionsForm.vue";

	const toast = useToast();

	const transactions = ref([]);

	// Get total
	const total = computed(() => {
		return transactions.value.reduce((acc, transaction) => {
			return acc + transaction.amount;
		}, 0);
	});

	// Get income
	const income = computed(() => {
		return transactions.value
			.filter((transaction) => transaction.amount > 0)
			.reduce((acc, transaction) => acc + transaction.amount, 0)
			.toFixed(2);
	});

	// Get expenses
	const expenses = computed(() => {
		return transactions.value
			.filter((transaction) => transaction.amount < 0)
			.reduce((acc, transaction) => acc + transaction.amount, 0)
			.toFixed(2);
	});

	// Add transaction
	const handleTransactionSubmitted = (transactionData) => {
		transactions.value.push({
			id: generateUniqueId(),
			text: transactionData.text,
			amount: transactionData.amount,
		});

		saveTransactiontoLocalStorage();

		toast.success("Transaction added");
	};

	const generateUniqueId = () => {
		return Math.floor(Math.random() * 10000000);
	};

	// delete transaction
	const handletransactionDeleted = (id) => {
		transactions.value = transactions.value.filter(
			(transaction) => transaction.id !== id
		);

		saveTransactiontoLocalStorage();

		toast.success("Transaction deleted");
	};

	// save to localstorage
	const saveTransactiontoLocalStorage = () => {
		localStorage.setItem("transactions", JSON.stringify(transactions.value));
	};

	onMounted(() => {
		const savedTransactions = JSON.parse(localStorage.getItem("transactions"));
		if (savedTransactions) {
			transactions.value = savedTransactions;
		}
	});
</script>

<template>
	<TheHeader />
	<div class="container">
		<TheBalance :total="+total" />
		<TheIncomeExpense :income="+income" :expenses="+expenses" />
		<TheTransactionsList
			:transactions="transactions"
			@transactionDeleted="handletransactionDeleted" />
		<TheTransactionsForm @transaction-submitted="handleTransactionSubmitted" />
	</div>
</template>

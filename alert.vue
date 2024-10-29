<script>
transfer() {
    const amount = Number(this.transferAmount);
    const receiverAcc = this.accounts.find(acc => acc.username === this.transferTo);

    if (amount <= 0) {
      alert("Transfer failed: Amount must be greater than zero.");
      return;
    }

    if (!receiverAcc) {
      alert("Transfer failed: Recipient not found.");
      return;
    }

    if (receiverAcc.username === this.currentAccount.username) {
      alert("Transfer failed: You cannot transfer to your own account.");
      return;
    }

    if (this.calculateBalance() < amount) {
      alert("Transfer failed: Insufficient funds.");
      return;
    }
    this.currentAccount.movements.unshift(-amount);
    receiverAcc.movements.unshift(amount);

    const currentDate = new Date().toISOString();
    this.currentAccount.movementsDates.unshift(currentDate);
    receiverAcc.movementsDates.unshift(currentDate);
    this.withdrawalCount++;
    this.depositCount++; 

    this.transferAmount = '';
    this.transferTo = '';
    this.updateUI();
    alert(`Transfer of ${this.formatCurrency(amount)} to ${receiverAcc.owner} was successful.`);
  }

  requestLoan() {
  const amount = Number(this.loanAmount);
  if (amount <= 0) {
    alert("Please enter a valid loan amount.");
    return;
  }
  if (!this.currentAccount.movements.some(mov => mov >= (amount * 0.1))) {
    alert("Loan request denied. You must have at least one deposit that is 10% of the loan amount.");
    return;
  }


  setTimeout(() => {
    this.currentAccount.movements.unshift(amount);
    this.currentAccount.movementsDates.unshift(new Date().toISOString());
    this.sortMovements();
    this.updateUI(); 
    alert("Loan request approved!");
  }, 1000);

  this.loanAmount = '';
}

sortMovements() {
  const combined = this.currentAccount.movements.map((mov, index) => ({
    amount: mov,
    date: new Date(this.currentAccount.movementsDates[index])
  }));
  combined.sort((a, b) => b.date - a.date);

  this.currentAccount.movements = combined.map(item => item.amount);
  this.currentAccount.movementsDates = combined.map(item => item.date.toISOString());
}
</script>

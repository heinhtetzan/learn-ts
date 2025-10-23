# TypeScript Object-Oriented Programming (OOP) Complete Guide

## 🏗️ 1. Classes - Blueprint for Objects

**Description**: Classes are templates for creating objects. They encapsulate data (properties) and behavior (methods) together.

```typescript
class Car {
    // Properties (data)
    public brand: string;
    public model: string;
    private _mileage: number;
    protected vin: string;
    
    // Constructor - special method called when creating instances
    constructor(brand: string, model: string, vin: string) {
        this.brand = brand;
        this.model = model;
        this._mileage = 0;
        this.vin = vin;
    }
    
    // Methods (behavior)
    public drive(distance: number): void {
        this._mileage += distance;
        console.log(`Driving ${distance} miles. Total mileage: ${this._mileage}`);
    }
    
    public getInfo(): string {
        return `${this.brand} ${this.model} (VIN: ${this.vin})`;
    }
}

// Creating instances (objects) from the class
const myCar = new Car("Toyota", "Camry", "1HGBH41JXMN109186");
myCar.drive(50); // "Driving 50 miles. Total mileage: 50"
console.log(myCar.getInfo()); // "Toyota Camry (VIN: 1HGBH41JXMN109186)"
```

---

## 🔒 2. Access Modifiers - Controlling Visibility

**Description**: Access modifiers control the visibility and accessibility of class members.

```typescript
class BankAccount {
    // Public: Accessible from anywhere
    public readonly accountNumber: string;
    public accountHolder: string;
    
    // Private: Only accessible within this class
    private _balance: number;
    private _transactionHistory: string[] = [];
    
    // Protected: Accessible within this class and subclasses
    protected bankName: string = "Global Bank";
    
    constructor(accountNumber: string, accountHolder: string, initialBalance: number = 0) {
        this.accountNumber = accountNumber;
        this.accountHolder = accountHolder;
        this._balance = initialBalance;
        this._transactionHistory.push(`Account created with $${initialBalance}`);
    }
    
    // Public method - part of the interface
    public deposit(amount: number): void {
        if (amount <= 0) {
            throw new Error("Deposit amount must be positive");
        }
        this._balance += amount;
        this._transactionHistory.push(`Deposited: $${amount}`);
        console.log(`Successfully deposited $${amount}. New balance: $${this._balance}`);
    }
    
    public withdraw(amount: number): boolean {
        if (amount > 0 && amount <= this._balance) {
            this._balance -= amount;
            this._transactionHistory.push(`Withdrew: $${amount}`);
            console.log(`Successfully withdrew $${amount}. New balance: $${this._balance}`);
            return true;
        }
        console.log("Insufficient funds or invalid amount");
        return false;
    }
    
    // Public getter for private data
    public getBalance(): number {
        return this._balance;
    }
    
    public getTransactionHistory(): readonly string[] {
        // Return a copy to prevent external modification
        return [...this._transactionHistory];
    }
    
    // Private method - internal implementation detail
    private _validateAmount(amount: number): boolean {
        return amount > 0 && amount <= 100000; // Business rule
    }
}

// Usage
const account = new BankAccount("ACC123456", "John Doe", 1000);
account.deposit(500);    // ✅ Works - public method
account.withdraw(200);   // ✅ Works - public method
console.log(account.getBalance()); // ✅ Works - public method

// account._balance = 10000;        // ❌ Error - private property
// account._transactionHistory = []; // ❌ Error - private property
// account._validateAmount(500);     // ❌ Error - private method
```

---

## 👨‍👩‍👧‍👦 3. Inheritance - Creating Hierarchies

**Description**: Inheritance allows a class to inherit properties and methods from another class, promoting code reuse.

```typescript
// Base class (Parent)
class Animal {
    constructor(
        public name: string,
        protected age: number,
        private _species: string
    ) {}
    
    // Public method
    public speak(): void {
        console.log(`${this.name} makes a sound`);
    }
    
    public move(distance: number = 0): void {
        console.log(`${this.name} moved ${distance} meters`);
    }
    
    // Protected method - accessible in subclasses
    protected sleep(hours: number): void {
        console.log(`${this.name} slept for ${hours} hours`);
    }
    
    // Getter for private property
    public get species(): string {
        return this._species;
    }
}

// Derived class (Child) - inherits from Animal
class Dog extends Animal {
    private _breed: string;
    
    constructor(name: string, age: number, breed: string) {
        // Call parent constructor using super()
        super(name, age, "Canine");
        this._breed = breed;
    }
    
    // Method overriding - same method, different implementation
    public speak(): void {
        console.log(`${this.name} barks: Woof! Woof!`);
    }
    
    // New method specific to Dog
    public fetch(item: string): void {
        console.log(`${this.name} fetched the ${item}`);
    }
    
    // Using protected method from parent
    public takeNap(): void {
        this.sleep(2); // ✅ Can access protected method
    }
    
    public get breed(): string {
        return this._breed;
    }
}

// Another derived class
class Cat extends Animal {
    constructor(name: string, age: number) {
        super(name, age, "Feline");
    }
    
    // Method overriding
    public speak(): void {
        console.log(`${this.name} meows: Meow!`);
    }
    
    // New method specific to Cat
    public climbTree(): void {
        console.log(`${this.name} climbed the tree`);
    }
}

// Usage
const dog = new Dog("Buddy", 3, "Golden Retriever");
const cat = new Cat("Whiskers", 2);

dog.speak();    // "Buddy barks: Woof! Woof!"
cat.speak();    // "Whiskers meows: Meow!"

dog.fetch("ball");  // "Buddy fetched the ball"
cat.climbTree();    // "Whiskers climbed the tree"

// Both inherit from Animal
dog.move(10);   // "Buddy moved 10 meters"  
cat.move(5);    // "Whiskers moved 5 meters"

console.log(dog.species); // "Canine" - using getter from parent
```

---

## 📝 4. Abstract Classes - Incomplete Blueprints

**Description**: Abstract classes cannot be instantiated directly and serve as base classes. They can contain abstract methods that must be implemented by derived classes.

```typescript
// Abstract class - cannot create instances directly
abstract class Employee {
    constructor(
        public name: string,
        public id: number,
        protected baseSalary: number
    ) {}
    
    // Abstract method - must be implemented by derived classes
    abstract calculateSalary(): number;
    
    // Abstract method 
    abstract getRole(): string;
    
    // Concrete method - has implementation
    public getBasicInfo(): string {
        return `ID: ${this.id} | Name: ${this.name} | Role: ${this.getRole()}`;
    }
    
    // Concrete method
    protected calculateTax(income: number): number {
        return income * 0.2; // 20% tax
    }
}

// Concrete class - must implement abstract methods
class FullTimeEmployee extends Employee {
    private benefits: string[];
    
    constructor(name: string, id: number, baseSalary: number, benefits: string[] = []) {
        super(name, id, baseSalary);
        this.benefits = benefits;
    }
    
    // Implementing abstract method
    calculateSalary(): number {
        const bonus = this.baseSalary * 0.1; // 10% bonus
        const grossSalary = this.baseSalary + bonus;
        const tax = this.calculateTax(grossSalary);
        return grossSalary - tax;
    }
    
    // Implementing abstract method
    getRole(): string {
        return "Full-Time Employee";
    }
    
    // Additional method
    public getBenefits(): string[] {
        return [...this.benefits];
    }
}

class PartTimeEmployee extends Employee {
    private hoursWorked: number;
    private hourlyRate: number;
    
    constructor(name: string, id: number, hourlyRate: number, hoursWorked: number = 0) {
        super(name, id, 0); // baseSalary not used for part-time
        this.hourlyRate = hourlyRate;
        this.hoursWorked = hoursWorked;
    }
    
    calculateSalary(): number {
        const grossSalary = this.hourlyRate * this.hoursWorked;
        const tax = this.calculateTax(grossSalary);
        return grossSalary - tax;
    }
    
    getRole(): string {
        return "Part-Time Employee";
    }
    
    public logHours(hours: number): void {
        this.hoursWorked += hours;
        console.log(`Logged ${hours} hours. Total: ${this.hoursWorked} hours`);
    }
}

// Usage
// const employee = new Employee("John", 1, 5000); 
// ❌ Error: Cannot create an instance of an abstract class

const fullTimer = new FullTimeEmployee("Alice", 1, 5000, ["Health Insurance", "Paid Time Off"]);
const partTimer = new PartTimeEmployee("Bob", 2, 25);

partTimer.logHours(80); // "Logged 80 hours. Total: 80 hours"

console.log(fullTimer.getBasicInfo()); // "ID: 1 | Name: Alice | Role: Full-Time Employee"
console.log(partTimer.getBasicInfo()); // "ID: 2 | Name: Bob | Role: Part-Time Employee"

console.log(`Alice's salary: $${fullTimer.calculateSalary()}`); 
console.log(`Bob's salary: $${partTimer.calculateSalary()}`);
```

---

## 📜 5. Interfaces - Contracts for Classes

**Description**: Interfaces define the structure that classes must follow. They describe what a class should do, but not how it should do it.

```typescript
// Interface defining a contract
interface Vehicle {
    start(): void;
    stop(): void;
    readonly make: string;
    readonly model: string;
    fuelLevel: number;
}

interface Electric {
    batteryCapacity: number;
    charge(): void;
}

interface Maintenance {
    lastServiceDate: Date;
    performMaintenance(): void;
}

// Class implementing multiple interfaces
class ElectricCar implements Vehicle, Electric, Maintenance {
    // Vehicle interface properties
    public readonly make: string;
    public readonly model: string;
    public fuelLevel: number; // For electric, this represents battery percentage
    
    // Electric interface properties
    public batteryCapacity: number;
    
    // Maintenance interface properties
    public lastServiceDate: Date;
    
    constructor(make: string, model: string, batteryCapacity: number) {
        this.make = make;
        this.model = model;
        this.batteryCapacity = batteryCapacity;
        this.fuelLevel = 100; // Start with full battery
        this.lastServiceDate = new Date();
    }
    
    // Vehicle interface methods
    start(): void {
        if (this.fuelLevel > 0) {
            console.log(`${this.make} ${this.model} started silently`);
        } else {
            console.log("Battery too low to start");
        }
    }
    
    stop(): void {
        console.log(`${this.make} ${this.model} stopped`);
    }
    
    // Electric interface methods
    charge(): void {
        this.fuelLevel = 100;
        console.log("Vehicle fully charged");
    }
    
    // Maintenance interface methods
    performMaintenance(): void {
        this.lastServiceDate = new Date();
        console.log(`Maintenance performed on ${this.lastServiceDate.toLocaleDateString()}`);
    }
    
    // Additional methods specific to ElectricCar
    drive(distance: number): void {
        const batteryUsed = distance * 0.1; // Simulate battery usage
        if (batteryUsed <= this.fuelLevel) {
            this.fuelLevel -= batteryUsed;
            console.log(`Drove ${distance} km. Battery: ${this.fuelLevel}%`);
        } else {
            console.log("Not enough battery for this distance");
        }
    }
}

// Usage
const myElectricCar = new ElectricCar("Tesla", "Model 3", 75);
myElectricCar.start();           // "Tesla Model 3 started silently"
myElectricCar.drive(50);         // "Drove 50 km. Battery: 95%"
myElectricCar.drive(300);        // "Not enough battery for this distance"
myElectricCar.charge();          // "Vehicle fully charged"
myElectricCar.performMaintenance(); // "Maintenance performed on [current date]"

// myElectricCar.make = "New Make"; // ❌ Error: readonly property
```

---

## ⚡ 6. Getters and Setters - Controlled Access

**Description**: Getters and setters allow controlled access to private properties, enabling validation and computed properties.

```typescript
class SmartThermostat {
    private _temperature: number = 20;
    private _mode: 'heating' | 'cooling' | 'off' = 'off';
    private readonly minTemp: number = 10;
    private readonly maxTemp: number = 30;
    
    // Getter - accessed like a property
    get temperature(): number {
        return this._temperature;
    }
    
    // Setter - validates input
    set temperature(newTemp: number) {
        if (newTemp < this.minTemp || newTemp > this.maxTemp) {
            throw new Error(`Temperature must be between ${this.minTemp} and ${this.maxTemp}`);
        }
        this._temperature = newTemp;
        console.log(`Temperature set to ${newTemp}°C`);
    }
    
    // Getter for mode
    get mode(): string {
        return this._mode;
    }
    
    // Setter for mode with validation
    set mode(newMode: 'heating' | 'cooling' | 'off') {
        this._mode = newMode;
        console.log(`Mode changed to: ${newMode}`);
    }
    
    // Computed property - derived from other properties
    get status(): string {
        return `Thermostat is in ${this._mode} mode at ${this._temperature}°C`;
    }
    
    // Regular method
    public increaseTemp(amount: number = 1): void {
        this.temperature = this._temperature + amount; // Uses setter
    }
    
    public decreaseTemp(amount: number = 1): void {
        this.temperature = this._temperature - amount; // Uses setter
    }
}

// Usage
const thermostat = new SmartThermostat();

// Using setters (looks like property assignment)
thermostat.temperature = 22;   // "Temperature set to 22°C"
thermostat.mode = 'heating';   // "Mode changed to: heating"

// Using getters (looks like property access)
console.log(thermostat.temperature); // 22
console.log(thermostat.mode);        // "heating"
console.log(thermostat.status);      // "Thermostat is in heating mode at 22°C"

// Using methods
thermostat.increaseTemp(3);    // "Temperature set to 25°C"
thermostat.decreaseTemp();     // "Temperature set to 24°C"

// thermostat.temperature = 50; // ❌ Error: Temperature must be between 10 and 30
```

---

## 🔧 7. Static Members - Class-Level Properties/Methods

**Description**: Static members belong to the class itself rather than instances. They're useful for utility methods and shared data.

```typescript
class MathOperations {
    // Static property - shared across all instances
    public static readonly PI: number = 3.14159;
    public static instanceCount: number = 0;
    
    // Static method - called on the class, not instances
    public static calculateCircleArea(radius: number): number {
        return this.PI * radius * radius;
    }
    
    public static factorial(n: number): number {
        if (n <= 1) return 1;
        return n * this.factorial(n - 1);
    }
    
    // Static factory method
    public static createDefault(): MathOperations {
        return new MathOperations();
    }
    
    constructor() {
        MathOperations.instanceCount++;
    }
    
    // Instance method
    public add(a: number, b: number): number {
        return a + b;
    }
}

class DatabaseConnection {
    private static _instance: DatabaseConnection;
    private static _connectionCount: number = 0;
    private _isConnected: boolean = false;
    
    // Private constructor for Singleton pattern
    private constructor() {
        DatabaseConnection._connectionCount++;
    }
    
    // Static method to get singleton instance
    public static getInstance(): DatabaseConnection {
        if (!DatabaseConnection._instance) {
            DatabaseConnection._instance = new DatabaseConnection();
        }
        return DatabaseConnection._instance;
    }
    
    public static get connectionCount(): number {
        return DatabaseConnection._connectionCount;
    }
    
    public connect(): void {
        this._isConnected = true;
        console.log("Connected to database");
    }
    
    public disconnect(): void {
        this._isConnected = false;
        console.log("Disconnected from database");
    }
}

// Usage of static members
console.log(MathOperations.PI); // 3.14159 - accessed directly from class
console.log(MathOperations.calculateCircleArea(5)); // 78.53975
console.log(MathOperations.factorial(5)); // 120

const math1 = new MathOperations();
const math2 = new MathOperations();
console.log(MathOperations.instanceCount); // 2 - shared across instances

// Singleton pattern usage
const db1 = DatabaseConnection.getInstance();
const db2 = DatabaseConnection.getInstance();

db1.connect(); // "Connected to database"

console.log(db1 === db2); // true - same instance
console.log(DatabaseConnection.connectionCount); // 1 - only one instance created
```

---

## 🎯 The 4 Main OOP Principles

### 1. **Encapsulation** - Data Protection
**Description**: Bundling data and methods that operate on that data, while restricting direct access to some components.

```typescript
class SecureBankAccount {
    private _balance: number;
    private _accountNumber: string;
    private _transactionHistory: string[] = [];
    private _pin: string;
    
    constructor(accountNumber: string, initialBalance: number, pin: string) {
        this._accountNumber = accountNumber;
        this._balance = initialBalance;
        this._pin = pin;
        this._transactionHistory.push(`Account opened with $${initialBalance}`);
    }
    
    // Public interface - controlled access to private data
    public deposit(amount: number): void {
        this._validateAmount(amount);
        this._balance += amount;
        this._logTransaction(`DEPOSIT: +$${amount}`);
        console.log(`Deposited $${amount}. New balance: $${this._balance}`);
    }
    
    public withdraw(amount: number, pin: string): boolean {
        if (!this._authenticate(pin)) {
            console.log("Invalid PIN");
            return false;
        }
        
        if (!this._validateAmount(amount) || amount > this._balance) {
            console.log("Invalid amount or insufficient funds");
            return false;
        }
        
        this._balance -= amount;
        this._logTransaction(`WITHDRAWAL: -$${amount}`);
        console.log(`Withdrew $${amount}. New balance: $${this._balance}`);
        return true;
    }
    
    public getBalance(pin: string): number | null {
        if (!this._authenticate(pin)) {
            console.log("Access denied");
            return null;
        }
        return this._balance;
    }
    
    public getAccountInfo(pin: string): string | null {
        if (!this._authenticate(pin)) {
            return null;
        }
        return `Account: ${this._accountNumber} | Balance: $${this._balance}`;
    }
    
    // Private methods - implementation details hidden from outside
    private _authenticate(pin: string): boolean {
        return pin === this._pin;
    }
    
    private _validateAmount(amount: number): boolean {
        return amount > 0 && amount <= 10000; // Business rules
    }
    
    private _logTransaction(description: string): void {
        this._transactionHistory.push(`${new Date().toISOString()}: ${description}`);
    }
}

// Usage
const secureAccount = new SecureBankAccount("SECURE123", 1000, "1234");

secureAccount.deposit(500); // ✅ Works - no PIN needed for deposit
// secureAccount._balance = 10000; // ❌ Error - private property

console.log(secureAccount.getBalance("1234")); // ✅ 1500 - correct PIN
console.log(secureAccount.getBalance("wrong")); // ❌ "Access denied" - wrong PIN

secureAccount.withdraw(200, "1234"); // ✅ Works - correct PIN
secureAccount.withdraw(200, "wrong"); // ❌ "Invalid PIN" - wrong PIN
```

### 2. **Inheritance** - Code Reuse and Hierarchy
**Description**: Creating new classes based on existing classes, inheriting their properties and methods.

```typescript
// Base class
class Notification {
    constructor(
        protected recipient: string,
        protected message: string,
        protected timestamp: Date = new Date()
    ) {}
    
    public send(): void {
        console.log(`Sending notification to: ${this.recipient}`);
    }
    
    public getInfo(): string {
        return `To: ${this.recipient} | Message: ${this.message} | Time: ${this.timestamp.toLocaleString()}`;
    }
    
    protected formatMessage(): string {
        return this.message;
    }
}

// Derived classes with specialized behavior
class EmailNotification extends Notification {
    private subject: string;
    private from: string;
    
    constructor(recipient: string, message: string, subject: string, from: string = "noreply@company.com") {
        super(recipient, message);
        this.subject = subject;
        this.from = from;
    }
    
    // Method overriding
    public send(): void {
        console.log(`Sending email to: ${this.recipient}`);
        console.log(`From: ${this.from}`);
        console.log(`Subject: ${this.subject}`);
        console.log(`Body: ${this.formatMessage()}`);
        console.log("--- Email sent successfully ---");
    }
    
    // Additional method
    public scheduleSend(delayMinutes: number): void {
        const scheduledTime = new Date(this.timestamp.getTime() + delayMinutes * 60000);
        console.log(`Email scheduled for: ${scheduledTime.toLocaleString()}`);
    }
}

class SMSNotification extends Notification {
    private senderId: string;
    
    constructor(recipient: string, message: string, senderId: string = "COMPANY") {
        super(recipient, message);
        this.senderId = senderId;
    }
    
    // Method overriding
    public send(): void {
        console.log(`Sending SMS to: ${this.recipient}`);
        console.log(`From: ${this.senderId}`);
        console.log(`Message: ${this.formatMessage()}`);
        console.log("--- SMS sent successfully ---");
    }
    
    // Override protected method
    protected formatMessage(): string {
        // SMS has character limit
        return this.message.length > 160 ? this.message.substring(0, 157) + "..." : this.message;
    }
}

class PushNotification extends Notification {
    private deviceToken: string;
    private title: string;
    
    constructor(recipient: string, message: string, deviceToken: string, title: string) {
        super(recipient, message);
        this.deviceToken = deviceToken;
        this.title = title;
    }
    
    // Method overriding
    public send(): void {
        console.log(`Sending push notification to device: ${this.deviceToken}`);
        console.log(`Title: ${this.title}`);
        console.log(`Body: ${this.formatMessage()}`);
        console.log("--- Push notification sent successfully ---");
    }
    
    // Additional method
    public addActionButton(label: string): void {
        console.log(`Added action button: ${label}`);
    }
}

// Usage
const notifications: Notification[] = [
    new EmailNotification("user@example.com", "Welcome to our service!", "Welcome Email"),
    new SMSNotification("+1234567890", "Your verification code is 123456. Please use it within 5 minutes."),
    new PushNotification("user123", "You have a new message waiting for you", "device_token_abc", "New Message")
];

// Polymorphic behavior - same method, different implementations
notifications.forEach(notification => {
    console.log("\n" + "=".repeat(40));
    notification.send();
    console.log(notification.getInfo());
});
```

### 3. **Polymorphism** - Many Forms
**Description**: The ability to present the same interface for different underlying forms (data types).

```typescript
// Interface defining a common contract
interface PaymentProcessor {
    processPayment(amount: number): boolean;
    refundPayment(amount: number): boolean;
    getPaymentDetails(): string;
}

// Different implementations of the same interface
class CreditCardProcessor implements PaymentProcessor {
    constructor(
        private cardNumber: string,
        private cardHolder: string,
        private expiryDate: string,
        private cvv: string
    ) {}
    
    processPayment(amount: number): boolean {
        console.log(`Processing credit card payment of $${amount}`);
        console.log(`Card: ****${this.cardNumber.slice(-4)}`);
        console.log(`Holder: ${this.cardHolder}`);
        // Simulate payment processing logic
        const success = amount > 0 && amount <= 10000; // Business rules
        console.log(success ? "✅ Payment approved" : "❌ Payment declined");
        return success;
    }
    
    refundPayment(amount: number): boolean {
        console.log(`Refunding $${amount} to credit card ****${this.cardNumber.slice(-4)}`);
        // Simulate refund logic
        return true;
    }
    
    getPaymentDetails(): string {
        return `Credit Card: ****${this.cardNumber.slice(-4)} (${this.cardHolder})`;
    }
}

class PayPalProcessor implements PaymentProcessor {
    constructor(private email: string) {}
    
    processPayment(amount: number): boolean {
        console.log(`Processing PayPal payment of $${amount}`);
        console.log(`Email: ${this.email}`);
        // Simulate PayPal processing
        const success = amount > 0 && this.email.includes('@');
        console.log(success ? "✅ PayPal payment successful" : "❌ PayPal payment failed");
        return success;
    }
    
    refundPayment(amount: number): boolean {
        console.log(`Refunding $${amount} to PayPal account: ${this.email}`);
        return true;
    }
    
    getPaymentDetails(): string {
        return `PayPal: ${this.email}`;
    }
}

class CryptoProcessor implements PaymentProcessor {
    constructor(private walletAddress: string, private cryptocurrency: string = "Bitcoin") {}
    
    processPayment(amount: number): boolean {
        console.log(`Processing ${this.cryptocurrency} payment of $${amount}`);
        console.log(`Wallet: ${this.walletAddress}`);
        // Simulate crypto transaction
        const success = amount > 0 && this.walletAddress.length === 42;
        console.log(success ? "✅ Crypto transaction confirmed" : "❌ Crypto transaction failed");
        return success;
    }
    
    refundPayment(amount: number): boolean {
        console.log(`Refunding $${amount} in ${this.cryptocurrency} to wallet: ${this.walletAddress}`);
        console.log("⚠️  Note: Crypto refunds may take longer to process");
        return true;
    }
    
    getPaymentDetails(): string {
        return `${this.cryptocurrency} Wallet: ${this.walletAddress.substring(0, 8)}...`;
    }
}

// Function that works with any PaymentProcessor (polymorphism)
function handlePayment(processor: PaymentProcessor, amount: number): void {
    console.log("\n" + "💳".repeat(20));
    console.log("Initiating payment process...");
    console.log(`Payment Method: ${processor.getPaymentDetails()}`);
    console.log(`Amount: $${amount}`);
    
    const success = processor.processPayment(amount);
    
    if (success) {
        console.log("🎉 Payment completed successfully!");
        // Simulate order fulfillment
        console.log("📦 Order is being processed...");
    } else {
        console.log("😞 Payment failed. Please try again.");
    }
    console.log("💳".repeat(20));
}

// Usage - same function, different behaviors
const paymentMethods: PaymentProcessor[] = [
    new CreditCardProcessor("4111111111111111", "John Doe", "12/25", "123"),
    new PayPalProcessor("john.doe@example.com"),
    new CryptoProcessor("1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa")
];

const paymentAmount = 150;

// Polymorphic behavior - same interface, different implementations
paymentMethods.forEach(processor => {
    handlePayment(processor, paymentAmount);
});

// Another example of polymorphism with different types
function processRefund(processor: PaymentProcessor, amount: number): void {
    console.log(`\nProcessing refund via: ${processor.getPaymentDetails()}`);
    const refundSuccess = processor.refundPayment(amount);
    console.log(refundSuccess ? "✅ Refund processed" : "❌ Refund failed");
}

// Process refunds using the same interface
processRefund(paymentMethods[0], 50);  // Credit card refund
processRefund(paymentMethods[1], 75);  // PayPal refund
```

### 4. **Abstraction** - Hiding Complexity
**Description**: Showing only essential features and hiding implementation details.

```typescript
// Abstract class defining the template
abstract class DataExporter {
    constructor(protected data: any[]) {}
    
    // Template method - defines the algorithm skeleton
    public export(): void {
        console.log("Starting export process...");
        
        this.validateData();
        const processedData = this.processData();
        const formattedData = this.formatData(processedData);
        this.saveData(formattedData);
        
        console.log("Export completed successfully!");
        this.cleanup();
    }
    
    // Abstract methods - must be implemented by subclasses
    protected abstract formatData(data: any[]): string;
    protected abstract getFileExtension(): string;
    
    // Concrete methods with default implementation
    protected validateData(): void {
        if (!this.data || this.data.length === 0) {
            throw new Error("No data to export");
        }
        console.log("✅ Data validation passed");
    }
    
    protected processData(): any[] {
        console.log("🔄 Processing data...");
        // Default processing - can be overridden
        return this.data.map(item => ({ ...item, exportedAt: new Date() }));
    }
    
    protected saveData(formattedData: string): void {
        const filename = `export_${new Date().toISOString().split('T')[0]}.${this.getFileExtension()}`;
        console.log(`💾 Saving data to: ${filename}`);
        // In real implementation, this would write to file system
        console.log(`Data content:\n${formattedData}`);
    }
    
    protected cleanup(): void {
        console.log("🧹 Cleaning up temporary resources...");
    }
    
    // Hook method - subclasses can override if needed
    protected preExport(): void {
        // Default does nothing
    }
}

// Concrete implementations
class CSVExporter extends DataExporter {
    protected formatData(data: any[]): string {
        console.log("Formatting data as CSV...");
        
        if (data.length === 0) return "";
        
        // Extract headers
        const headers = Object.keys(data[0]);
        let csv = headers.join(',') + '\n';
        
        // Add rows
        data.forEach(item => {
            const row = headers.map(header => {
                const value = item[header];
                // Handle values that might contain commas
                return typeof value === 'string' && value.includes(',') ? `"${value}"` : value;
            }).join(',');
            csv += row + '\n';
        });
        
        return csv;
    }
    
    protected getFileExtension(): string {
        return "csv";
    }
    
    // Override hook method
    protected preExport(): void {
        console.log("📊 Preparing CSV-specific resources...");
    }
}

class JSONExporter extends DataExporter {
    protected formatData(data: any[]): string {
        console.log("Formatting data as JSON...");
        return JSON.stringify(data, null, 2);
    }
    
    protected getFileExtension(): string {
        return "json";
    }
    
    // Override processing method
    protected processData(): any[] {
        console.log("🔄 JSON-specific data processing...");
        // Add additional metadata for JSON export
        return this.data.map(item => ({
            ...item,
            exportedAt: new Date().toISOString(),
            format: 'json'
        }));
    }
}

class XMLExporter extends DataExporter {
    protected formatData(data: any[]): string {
        console.log("Formatting data as XML...");
        
        let xml = '<?xml version="1.0" encoding="UTF-8"?>\n';
        xml += '<data>\n';
        
        data.forEach(item => {
            xml += '  <record>\n';
            Object.entries(item).forEach(([key, value]) => {
                xml += `    <${key}>${value}</${key}>\n`;
            });
            xml += '  </record>\n';
        });
        
        xml += '</data>';
        return xml;
    }
    
    protected getFileExtension(): string {
        return "xml";
    }
}

// Usage - clients don't need to know implementation details
const sampleData = [
    { id: 1, name: "John Doe", age: 30, city: "New York" },
    { id: 2, name: "Jane Smith", age: 25, city: "Los Angeles" },
    { id: 3, name: "Bob Johnson", age: 35, city: "Chicago" }
];

const exporters: DataExporter[] = [
    new CSVExporter(sampleData),
    new JSONExporter(sampleData),
    new XMLExporter(sampleData)
];

console.log("🚀 Starting data export with different formats...\n");

// Same export method, different implementations
exporters.forEach((exporter, index) => {
    console.log(`\n${"═".repeat(50)}`);
    console.log(`Export ${index + 1}: ${exporter.constructor.name}`);
    console.log("═".repeat(50));
    
    try {
        exporter.export();
    } catch (error) {
        console.error(`❌ Export failed: ${error.message}`);
    }
});

// Client code only needs to know about the abstract interface
function createExporter(format: 'csv' | 'json' | 'xml', data: any[]): DataExporter {
    switch (format) {
        case 'csv': return new CSVExporter(data);
        case 'json': return new JSONExporter(data);
        case 'xml': return new XMLExporter(data);
        default: throw new Error(`Unsupported format: ${format}`);
    }
}

// Simple usage for clients
const clientExporter = createExporter('json', sampleData);
clientExporter.export(); // Client doesn't need to know the implementation details
```

---

## 🎯 Summary

| Feature | Purpose | Key Benefit |
|---------|---------|-------------|
| **Classes** | Create object blueprints | Code organization and reusability |
| **Access Modifiers** | Control visibility | Data protection and encapsulation |
| **Inheritance** | Create class hierarchies | Code reuse and logical organization |
| **Abstract Classes** | Define incomplete blueprints | Enforcement of structure, template pattern |
| **Interfaces** | Define contracts | Polymorphism and loose coupling |
| **Getters/Setters** | Controlled property access | Validation and computed properties |
| **Static Members** | Class-level properties/methods | Utility functions and shared data |

### The 4 OOP Principles:

1. **Encapsulation** - Protect data, expose controlled interface
2. **Inheritance** - Build upon existing functionality  
3. **Polymorphism** - Same interface, different implementations
4. **Abstraction** - Hide complexity, show essentials

These concepts work together to create maintainable, scalable, and robust TypeScript applications!
## Task 1:
<br>
###### SOLID Violations:

* SystemManager class has many responsabilities. ex. Order proccesing, invetory checkj, process payments, update order and also notify customer about order updated.
* OCP: The class SystemManager explicit checks the order type in order to determine the accurated method to invoke. So, if a new order type is introduced, the class must be modified in order to support the new one.
* DIP: Class uses convcrete implementations instead of interfaces.

<br>

```
class SystemManager {
    processOrder(order) {
        if (order.type == "standard") {
            verifyInventory(order);
            processStandardPayment(order);
        } else if (order.type == "express") {
            verifyInventory(order);
            processExpressPayment(order, "highPriority");
        }
        updateOrderStatus(order, "processed");
        notifyCustomer(order);
    }

    verifyInventory(order) {
        // Checks inventory levels
        if (inventory < order.quantity) {
            throw new Error("Out of stock");
        }
    }

    processStandardPayment(order) {
        // Handles standard payment processing
        if (paymentService.process(order.amount)) {
            return true;
        } else {
            throw new Error("Payment failed");
        }
    }

    processExpressPayment(order, priority) {
        // Handles express payment processing
        if (expressPaymentService.process(order.amount, priority)) {
            return true;
        } else {
            throw new Error("Express payment failed");
        }
    }

    updateOrderStatus(order, status) {
        // Updates the order status in the database
        database.updateOrderStatus(order.id, status);
    }

    notifyCustomer(order) {
        // Sends an email notification to the customer
        emailService.sendEmail(order.customerEmail, "Your order has been processed.");
    }
}
```

## Task 2:
<br>
**Single Responsability:** Creating different clasess / interfaces to manage specific funcitons.

**Open/Closed Principle (OCP):** Use a strategy pattern to handle different types of orders:
<br>
```
interface OrderProcessingStrategy {
    void process(Order order);
}

class StandardOrderProcessingStrategy implements OrderProcessingStrategy {
    public void process(Order order) {
        verifyInventory(order);
        processStandardPayment(order);
        updateOrderStatus(order, "processed");
        notifyCustomer(order);
    }
}

class ExpressOrderProcessingStrategy implements OrderProcessingStrategy {
    public void process(Order order) {
        verifyInventory(order);
        processExpressPayment(order, "highPriority");
        updateOrderStatus(order, "processed");
        notifyCustomer(order);
    }
}

class SystemManager {
    private Map<String, OrderProcessingStrategy> strategies;

    public SystemManager() {
        strategies = new HashMap<>();
        strategies.put("standard", new StandardOrderProcessingStrategy());
        strategies.put("express", new ExpressOrderProcessingStrategy());
    }

    public void processOrder(Order order) {
        OrderProcessingStrategy strategy = strategies.get(order.type);
        if (strategy != null) {
            strategy.process(order);
        } else {
            throw new IllegalArgumentException("Unknown order type: " + order.type);
        }
    }
}
```
<br>
**Dependency Inversion Principle (DIP): Using interfaces instead of concrete implementatios.**
<br>

```
interface PaymentProcessor {
    boolean process(double amount);
}

Class ExpressPaymentProcessor implements PaymentProcessor{
	boolean process(double amount){
    	//Process express payment
    }
}

Class StandarPaymentProcessor implements PaymentProcessor{
	boolean process(double amount){
    	//Process standard payment
    }
}

interface InventoryService {
    boolean verifyInventory(Order order);
}

interface DatabaseService {
    void updateOrderStatus(String orderId, String status);
}

interface Notifier {
    void sendEmail(String email, String message);
}

public class Order {
    private String id;
    private String type;
    private int quantity;
    private double amount;
    private String customerEmail;
    private String status;
}
```

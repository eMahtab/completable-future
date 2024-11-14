# CompletableFuture

```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureExample {
	
    public static void main(String[] args) {
        // Start both tasks asynchronously
        CompletableFuture<String> userFuture = CompletableFuture.supplyAsync(() -> {
            try { // Simulate an API call to fetch user data
            	System.out.println("userFuture being executed by :"+Thread.currentThread().getName());
                Thread.sleep(2000); // Simulate network delay
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "User Data: John Doe";
        });
        CompletableFuture<String> orderFuture = CompletableFuture.supplyAsync(() -> {
            try { // Simulate an API call to fetch user orders
            	System.out.println("orderFuture being executed by :"+Thread.currentThread().getName());
                Thread.sleep(3000); // Simulate network delay
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Order Data: [Order1, Order2, Order3]";
        });

        // Combine the results of both tasks
        CompletableFuture<String> combinedFuture = userFuture.thenCombine(orderFuture, (userData, orderData) -> {
            return userData + ", " + orderData;
        });

        // Print the combined result
        System.out.println("Fetching data asynchronously...");
        combinedFuture.thenAccept(result -> {
           System.out.println("Result: " + result);
        });
        System.out.println("Running the code in main thread");
        try {
        	Thread.sleep(10000); // this delay so that main thread don't finish and get destroyed before we receive result of async operations
        }catch(InterruptedException exp) {
        	exp.printStackTrace();
        }
        System.out.println("main method finished executing");
    }
}
```

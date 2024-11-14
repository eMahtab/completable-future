# CompletableFuture
A Future that may be explicitly completed (setting its value and status), and may be used as a CompletionStage, supporting dependent functions and actions that trigger upon its completion.
**CompletableFuture class was introduced with Java 1.8**


#### Example 1 : Combining the result of two async operations (CompletableFuture), thenAccept() non-blocking
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

### Code Execution Output
```
orderFuture being executed by :ForkJoinPool.commonPool-worker-2
Fetching data asynchronously...
userFuture being executed by :ForkJoinPool.commonPool-worker-1
Running the code in main thread
Result: User Data: John Doe, Order Data: [Order1, Order2, Order3]
main method finished executing
```

#### Example 2 : Combining the result of two async operations (CompletableFuture), get() blocking
```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;

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
        try {
              System.out.println("Result: " + combinedFuture.get());
        } catch (InterruptedException | ExecutionException e) {
              e.printStackTrace();
        }
        System.out.println("main method finished executing");
    }
}
```
### Code Execution Output
```
Fetching data asynchronously...
userFuture being executed by :ForkJoinPool.commonPool-worker-1
orderFuture being executed by :ForkJoinPool.commonPool-worker-2
Result: User Data: John Doe, Order Data: [Order1, Order2, Order3]
main method finished executing
```

### Example 3 : Chaining operations on CompletableFuture
```java
import java.util.concurrent.CompletableFuture;
public class CompletableFutureExample {
	public static void main(String[] args) {
		CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "Hello")
		        .thenApply(result -> result + " World")
		        .thenApply(result -> result.toUpperCase());

		future.thenAccept((result) -> System.out.println("Result :"+result)); // Output: HELLO WORLD
		try {
			Thread.sleep(3000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		System.out.println("Main method finished executing");
	}
}
```

### Code Execution Output:
```
Result :HELLO WORLD
Main method finished executing
```

### Example 4 : Waiting for all the async operations to complete using allOf and thenRun
If you want to wait till all of CompletableFuture instances, use **allOf()** to wait for all of them to complete.

```java
import java.util.concurrent.CompletableFuture;
public class CompletableFutureExample {
	public static void main(String[] args) {
		CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Task 1");
		CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "Task 2");
		CompletableFuture<String> future3 = CompletableFuture.supplyAsync(() -> "Task 3");

		CompletableFuture<Void> allFutures = CompletableFuture.allOf(future1, future2, future3);

		allFutures.thenRun(() -> {
		    try {
		        System.out.println(future1.get());
		        System.out.println(future2.get());
		        System.out.println(future3.get());
		    } catch (Exception e) {
		        e.printStackTrace();
		    }
		});
		try {
			Thread.sleep(5000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		System.out.println("Main method finished executing");
	}
}
```
#### Code Execution Output :
```
Task 1
Task 2
Task 3
Main method finished executing
```

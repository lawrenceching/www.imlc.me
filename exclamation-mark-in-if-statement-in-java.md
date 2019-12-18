# Shall I avoid Exclamation Mark(!) in If-Statement in Java

Today I read a post that we should not use "!" in If statement in order to gain a better Perfermance.
Because the "!" operator adds extra action for your program.
It sounds not a good idea.
Is it really truth?

I did a simple experiment.

I wrote 2 Java snippets:

```
// App.java
public final class App {
        public static void main(String[] args) {
                if(App.shouldPrintMessage()) {
                        System.out.println("Hello, wolrd");
                }
        }

        public static boolean shouldPrintMessage() {
                return true;
        }
}
```

Ran `javac App.java` and then I got `App.class`.
Ran `javap -c App.class` to view the bytecode.

Here it is:

```
public final class App {
  public App();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: invokestatic  #2                  // Method shouldPrintMessage:()Z
       3: ifeq          14                  // If-Equal !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
       6: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
       9: ldc           #4                  // String Hello, wolrd
      11: invokevirtual #5                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      14: return

  public static boolean shouldPrintMessage();
    Code:
       0: iconst_1
       1: ireturn
}
```

And then I added ! in If-Statement 

```
public final class App {
        public static void main(String[] args) {
                if(!App.shouldPrintMessage()) {
                        System.out.println("Hello, wolrd");
                }
        }

        public static boolean shouldPrintMessage() {
                return true;
        }
}
```

Checked the bytecode

```
public final class App {
  public App();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: invokestatic  #2                  // Method shouldPrintMessage:()Z
       3: ifne          14                  // If-Not-Equal !!!!!!!!!!!!!!!!!!!!!!!!!!!!!
       6: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
       9: ldc           #4                  // String Hello, wolrd
      11: invokevirtual #5                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      14: return

  public static boolean shouldPrintMessage();
    Code:
       0: iconst_1
       1: ireturn
}
```
As you can see, the main difference is the `ifeq` and the `ifne`.

---

Let me tried the example exactly provided in the post I read.

```

public final class App {
	public static void main(String[] args) {
		int a = 2;
		if(!(a <= 1)) {
			System.out.println("Hello, wolrd");
		}
	}

}

// vs

public final class App {
	public static void main(String[] args) {
		int a = 2;
		if(a > 1) {
			System.out.println("Hello, wolrd");
		}
	}
}
```

And here is the output

```
public final class App {
  public App();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: iconst_2
       1: istore_1
       2: iload_1
       3: iconst_1
       4: if_icmple     15
       7: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
      10: ldc           #3                  // String Hello, wolrd
      12: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      15: return
}

// vs

public final class App {
  public App();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: iconst_2
       1: istore_1
       2: iload_1
       3: iconst_1
       4: if_icmple     15
       7: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
      10: ldc           #3                  // String Hello, wolrd
      12: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      15: return
}
```
They're exactly the same in bytecode level. 

Two examples showed us that there is NO addtional operation even though we use "!" in If-Statement.
And futhermore, don't forget we have runtime optimazation in Java. In 2019, I quite believe the JVM is smart enough to resolve this problem.
So I think we can choose whatever we want in If-Statement from the readability perspective.

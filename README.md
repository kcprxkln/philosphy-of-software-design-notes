# Philosophy of Software Design notes

These notes are based on the book *A Philosophy of Software Design* by John Ousterhout. The book explores various aspects of software design, with a primary focus on managing and reducing complexity. 

## Overview
- [Symptoms of System Complexity](#symptoms-of-system-complexity)
- [What Creates Complexity?](#what-creates-complexity)
- [How to Limit Complexity?](#how-to-limit-complexity)
- [Potential Design Issues](#potential-design-issues)
- [Key Takeaways](#key-takeaways)

### Symptoms of System Complexity

Complexity manifests in three ways:
- **Change Amplification** - Seemingly simple change requires change in code in many places.
- **Cognitive Load** - How much does the developer need to know to acomplish task? The less the better.
- **Unknown Unknowns** - It is not obvious which parts of code need to be edited, or what additional information should the developer have.

### What Creates Complexity?

Complexity is incremental. The "get something to work" mentality of `Tactical Programming` may be effective in the short term, but it is ultimately short-sighted. Developers might introduce unnecessary complexities here and there just to accomplish tasks more quickly. These small complexities accumulate over time, making the system harder to maintain, understand, and work on.

### How to Limit Complexity?

To avoid this, developers should shift towards a `Strategic Programming` approach. This involves dedicating around 10-20% of their time to making thoughtful investments in code quality, architecture, and design. By doing so, they can ensure that the software remains flexible, maintainable, and scalable in the long run.

## Potential Design Issues 

🚩 **Shallow Module**

🚩 **Information Leakage**

🚩 **Temporal Decomposition**

🚩 **Overexposure**

🚩 **Pass-Through Methods**

🚩 **Repetition**

🚩 **Special-General Mixture**

🚩 **Conjoined Methods**

🚩 **Comment Repeats Code**

🚩 **Implementation Documentation Contaminates Interface**

🚩 **Vague Name**

🚩 **Hard to Pick Name**

🚩 **Hard to Describe**

🚩 **Nonobvious Code**

## Key Takeaways

### Modules Depth 

Most users of a specific module should not need to understand its internal implementation. They interact only with the abstraction provided by the module’s interface. When implementing a module, strive to hide unnecessary details to ensure that users have simple interfaces for common use cases, while still oferring sophisticated functionality behind the abstraction. 

A good example of a well-designed **Deep Module** is the file I/O mechanism provided by Unix and Linux. This system offers only a few system calls that handle a wide range of file operations. Many complexities are managed by the Unix file system implementation, so developers using these system calls do not need to know about the underlying details.

A **Shallow Module** is characterized by having a relatively complex interface with limited functionality. This means the module exposes many details or options that users must deal with, despite performing relatively simple operations under the hood. This can lead to increased complexity for users, as they must navigate a convoluted interface to access basic functionality.


### Information Hiding and Information Leakage

Each module should encapsulate its internal knowledge, which represents the design decisions of that module. This includes data structures used, algorithms related to the mechanism, as well as lower-level details such as page size and higher-level concepts like the assumption that most files are small. All of this information should be embedded in the module's implementation; however, it should not appear in the module's interface. We can call it **Information Hiding**. 

**Information Leakage** occurs when different modules become dependent on each other's implementation details. This situation arises when design decisions in one module force changes in another if a redesign is necessary. For example, consider two classes: one reads and one writes a specific file format. Since both are dependent on the file format, any change to the format would require redesigning both classes.

One cause of **Information Leakage** can be **Temporal Decomposition**. In Temporal Decomposition, the system's structure is designed based on the sequence of operations over time. Instead, the system should be designed based on the knowledge needed to perform specific tasks.


### Overexposure

An API should not require users to spend time learning about rarely used features. Instead, the class interfaces for common usage should be as simplified as possible.


### General-Special Purpose Implementation

Designing class as a **General-Purpose** one can result in a simpler interface with greater depth than the **Special-Purpose** class. Since building something too General-Purpose can lead to situation that it is difficult to use it for the current needs. Therefore, usually the best solution is to create General-Purpose interface that should be general enough to support multiple uses, with functionalities reflecting current needs.

Questions to ask yourself to find balance between those approaches:
- ***What is the simplest interface that could cover all my current needs?***
	 Fewer methods with the same capabilities typically indicate more general-purpose code.
- ***In How many situations will this method be used?***
	 A method designed for a single, specific use might be too special-purpose.
- ***Is the API easy to use for my current needs?***
	 If using the class for your purpose requires writing a lot of additional code, the class might be too general-purpose.


### Pass-Through Methods

**Pass-Through Method** is the method that does nothing except pass its arguments to another method. This typically indicates that there is no clean division of responsibilities between classes.
```
public class TextDocument ... {
	private TextArea textArea;
	...
	public Character getLastTypedCharacter() {
		return textArea.getLastTypedCharacter()
	}
}
```



### Decorators

Before creating decorator class, consider alternatives:
- ***Could you add the new functionality directly to the class rather than creating decorator?***
	 This makes sense if new functionality is relatively general-purpose or it's logically related to the class.
- ***If the new functionality has specialized usage, would it make sense to merge it with the class?***
- ***Could you merge the functionality into existing decorator?***
- ***Does new functionality really needs to wrap the existing class?***
	 Perhaps new functionality can be implemented as a standalone class.


### Pull Complexity Downwards

Most modules have more users than developers, so it's better for a module to have a simple interface than a simple implementation. A **Configuration Parameters** is a good example of moving complexity upwards. Instead of determining behavior internally, a class might export parameters that control its behavior. However, the thought process behind this approach is often flawed:
- Learning about configuration parameters increases the user's cognitive load.
- In many cases, the assumption that the user can fine-tune parameters better than the developer is incorrect.

Therefore always ask yourself - ***Will users (or higher-level) modules be able to determine a better value that I can determine here?***. If the answer is no, try to provide reasonable defaults. This is a great example of **Pulling Complexity Downwards** and making the system easier to use.

### Together or Apart?

**"Should I Implement them in one place or separately"** is a common question if it comes to the different functinalities implementation. Some important things about subdividing code:
- Some complexity comes just from number of components - the more of them, the harder is to keep track of them and to find a desired one.
- Subdivision can result in additional code to manage components.
- Subdivision can result in duplication. 

Therefore in situation that pieces of code are closely related, it is better to **bring them together**. Indications that two pieces of code are related:
- They share information.
- They are used together.
- It is hard to understand one part of code without looking at another.


### Conjoined Methods

**You should be able to understand each method separately.** If you can't understand the implementation of a method without understanding the implementation of another one, there's probably a design issue.


### Exceptions 

**Exception handling** is one of the worst sources software systems' complexity. Beside the amount of code that needs to be created for a specific exception, often Exceptions can't be easily tested. Since in the running systems, exceptions don't occur very often, **bugs can go undetected** for a long time.

In order to use exceptions effectively, we should reduce the number of places where exceptions have to be handled. We can do so by different methods:
- Define errors out of existence, 
- Mask exceptions, 
- Exception aggregation.


### Code Comments

`The overall idea behind comments is to capture information that was in the mind of the designer but couldn't be represented in the code.`

Correctly used comments can improve overall system design by reducing cognitive load while working with the code and explaining potential unknown unknowns.

We can divide comments on two categories. **Lower-level comments** add precision by describing the code meaning. **Higher-level comments** help reader understand the overall structure of the code.

**Comment Principles:**
- Avoid stating the obvious (things clear from the code itself).
- Use different terminology from the code names to avoid redundancy.
- Focus on _what_ the code does rather than _how_ it does it.

**Interface Documentation Principles**:
- Start with a high-level overview of what the method does.
- Document each argument and return value clearly.
- Note any side effects or system changes the method causes.
- Include details about exceptions that might be thrown.
- Describe any preconditions required for the method to work correctly.

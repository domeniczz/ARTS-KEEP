Year 2023, August, Week 35

## Algorithm

[Blind 75 Must Do](https://leetcode.com/list?selectedList=r327z1f1): [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters)

**Solution 1**:

```java
public static int lengthOfLongestSubstring(String s) {
    int maxLength = 0;
    int leftStart = 0;
    Set<Character> set = new HashSet<>();
    int i = 0;
    while (i < s.length()) {
        char c = s.charAt(i);
        if (!set.add(c)) {
            maxLength = maxLength < set.size() ? set.size() : maxLength;
            set.clear();
            leftStart++;
            i = leftStart - 1;
        }
        i++;
    }
    return maxLength < set.size() ? set.size() : maxLength;
}
```

Here we use [HashSet](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/HashSet.html) to store adjacent different characters. Left shift the start point & clear HashSet everytime a duplicate character is founded. A pretty straightforward solution.

**Solution 2**:

```java
public static int lengthOfLongestSubstring(String s) {
    int max = 0;
    int start = 0, end = 0;
    // initial values are 0
    int pos[] = new int[128];
    for (char c : s.toCharArray()) {
        // if the character has appeared before, start from the character
        start = Math.max(start, pos[c]);
        // mark the next position where a repeating character was last found
        // indicating that the character has already appeared
        pos[c] = end + 1;
        // calculate the max length
        max = Math.max(end - start + 1, max);
        ++end;
    }
    return max;
}
```

This solution is a bit tricky. First, we need to know that [ASCII characters](https://en.wikipedia.org/wiki/ASCII#Printable_characters) can be represented by numbers (0 ~ 128).

Variable `end` represents the pointer that keep moving right as we traverse the characters in the string, and variable `start` represents the start point of the substring. Thus, `end - start + 1` is the length of current substring.

While traversing the string, we mark the equivalent element, say `pos[100]` in the int array of the character, say "d", to it's next position (end + 1). This step is crucial, `+ 1` serves a dual purpose: 

1. Differentiate between a character that hasn't appeared yet and a character that already appeared.
2. By assigning `end + 1`, you're effectively marking where the next non-repeating substring should start if a repetition is found. This ensures `start` is moved just past the last repeating character.

## Review

### AI Podcast 1.0: Rise of the machines

> podcast: https://open.spotify.com/episode/4xd8DFAHSe3QgJKuIX1aPp
>
> podcast transcript: [Can ChatGPT write a podcast episode? Can AI take our jobs?](https://www.npr.org/transcripts/1178290105)

While the AGI, like [ChatGPT](https://chat.openai.com), can churn out plausible-sounding content like interview questions and titles on command, it becomes clear that a lot of human guidance, trial-and-error, and oversight is still needed. The hosts have an "ah-ha" moment when ChatGPT pulls a creative nugget from their source materials to craft a compelling narrative. Yet they're disappointed when the drama it writes is too earnest. This shows the technology's potential but also its current limitations.

The episode leaves me feeling that while AI can certainly augment and enhance human creativity right now, true imagination remains out of reach. However, the rapid progress suggests that capabilities once considered fundamentally human may someday be simulated.

### IoC: DI vs Service Locator

Martin Fowler: [Inversion of Control Containers and the Dependency Injection pattern](https://martinfowler.com/articles/injection.html)

This article introduced [dependency injection](https://www.wikiwand.com/en/Dependency_injection) & [service locator](https://www.wikiwand.com/en/Service_locator_pattern) design pattern.

Certainly, I will provide examples in Java for both Dependency Injection and Service Locator patterns, which are commonly used approaches to implement Inverse of Control.

#### Dependency Injection

> video: https://www.youtube.com/watch?v=J1f5b4vcxCQ

In dependency injection, an object receives its dependencies from an external entity. You usually inject the dependencies through a **constructor**, a setter, or a method.

**A basic example**:

In this example, `Notification` is the client class and `MessageService` is the dependency.

We inject an instance of `MessageService` into `Notification` using constructor injection.

```java
// Dependency
public interface MessageService {
    void sendMessage(String message);
}

// Concrete Implementation
public class EmailService implements MessageService {
    public void sendMessage(String message) {
        System.out.println("Sending email: " + message);
    }
}
```

```java
// Client class
public class Notification {
    private MessageService messageService;

    // constructor injection
    public Notification(MessageService messageService) {
        this.messageService = messageService;
    }

    public void notify(String message) {
        messageService.sendMessage(message);
    }
}
```

```java
// Main class
public class Main {
    public static void main(String[] args) {
        MessageService emailService = new EmailService();
        Notification notification = new Notification(emailService);
        // send a notification through email
        notification.notify("Dependency Injection example.");
    }
}
```

#### Service Locator

The service locator pattern serves as a **registry** that other classes can use to get their dependencies. Unlike Dependency Injection, the client class asks the Service Locator for its dependencies.

**A basic example**:

In this example, `ServiceLocator` holds the `MessageService` instances.

The client, `Notification`, asks `ServiceLocator` for the appropriate `MessageService` to use. 

```java
// Dependency
public interface MessageService {
    void sendMessage(String message);
}

// Concrete Implementation
public class EmailService implements MessageService {
    public void sendMessage(String message) {
        System.out.println("Sending email: " + message);
    }
}
```

```java
// Service Locator
public class ServiceLocator {
    // registry
    private static Map<String, MessageService> services = new HashMap<>();

    public static void registerService(String name, MessageService service) {
        services.put(name, service);
    }

    public static MessageService getService(String name) {
        return services.get(name);
    }
}
```

```java
// Client class
public class Notification {
    public void notify(String serviceName, String message) {
        MessageService service = ServiceLocator.getService(serviceName);
        service.sendMessage(message);
    }
}
```

```java
// Main class
public class Main {
    public static void main(String[] args) {
        ServiceLocator.registerService("email", new EmailService());
        Notification notification = new Notification();
        // send a notification through email
        notification.notify("email", "Service Locator example.");
    }
}
```

#### Difference

[Deciding which option to use](https://martinfowler.com/articles/injection.html#DecidingWhichOptionToUse)

**Dependency Injection**:
In this pattern, the dependency is provided to the class, usually through its constructor or a setter method. The class itself doesn't ask for the dependency. This enforces the **Inversion of Control** principle where the application class doesn't need to control the instantiation of dependencies.

**Service Locator**:
Here, the class explicitly requests its dependencies from a central location known as the Service Locator. The application code is dependent on the Service Locator to fetch its dependencies.

**How to Choose**:

Choose Dependency Injection for low coupling, improved testability, and better code readability.

Opt for Service Locator when you have control over the environment, or when you find Inversion of Control to be unnecessarily complex for your needs.

## Technique

`git push --force-with-lease` allows you to overwrite the remote branch with your local branch, but **only if** the remote branch has not changed since you last fetched it. It is a safer option than `git push -f`, which will force the push regardless of the state of the remote branch.

If you run `git push --force-with-lease` when the remote branch has changed since you last fetched it, you will get an error message like this:

```
To https://github.com/user/repo.git
 ! [rejected]        master -> master (stale info)
error: failed to push some refs to 'https://github.com/user/repo.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

## Thoughts

AI is on the rise, and people are concerned that AGIs will replace human jobs, much like machines replaced telephone operators in the 1920s. Fortunately, this hasn't happened yet. Platforms like ChatGPT are not yet capable of producing an entire podcast, as it's a complex project with multiple components and details. However, with human guidance to break down the project into smaller tasks and provide necessary resources for research, ChatGPT can generate several high-quality outputs, from which human editors can select the best.

I've been using [Github Copilot](https://github.com/features/copilot) and ChatGPT for a while to help me with coding. While they offer a modest boost to my productivity by generating basic, functional code snippets, they fall short when it comes to producing large blocks of bug-free, complex code. [A video on bilibili](https://www.bilibili.com/video/BV11N411z75Y/) underscores the point that AI requires explicit instructions for task completion; you can't simply request it to build an entire website without providing detailed guidance.

Compared to AI, the core competitiveness of us programmers lies in our ability to weigh the pros and cons among various implementation methods to make informed decisions. If we don't want to be replaced by AI, we cannot be content with being mere 'coding tools' that only write code based on clear requirements given by others.
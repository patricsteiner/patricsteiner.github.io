---
layout: post
title: State Machine in a DDD Context
image: machines.jpg
---

State machines are an important concept of computer science, specifically [automata theory](https://en.wikipedia.org/wiki/Automata_theory). But they are also quite common in our every day life and consequently equally common in software engineering problems. However, depending on the complexity of the domain and business logic, just implementing a simple [state pattern](https://sourcemaking.com/design_patterns/state) can turn out to be much more complicated than initially anticipated.

Consider the following scenario in a domain-driven context:

> An aggregate has a concrete state, and it depends on that current state of the aggregate, _how_ and _if_ certain operations can be performed.

This generally sounds like a good usecase to apply the state pattern.

**Let's use a concrete example and implement a solution for this use case:**

Imagine an application for a callcenter that manages the calls of every employee. Depending on the call state, employees have a different view and can interact differently with their application.
This application is built according to the principles of [Domain-Driven Design](https://en.wikipedia.org/wiki/Domain-driven_design) and the [Onion Architecture](https://jeffreypalermo.com/2008/07/the-onion-architecture-part-1/). The aggregate of interest will be the call of an employee.

```java
@Aggregate
public class Call {

    private CallState state;
    // additional properties & domain logic
    
}
```

In our callcenter domain there are many different events that can occur, such as incoming calls, outgoing calls, accepted calls, ended calls, etc.

```java
@DomainEvent
public class IncomingCallEvent implements Event {

    private final PhoneNumber phoneNumber;
    private final LocalDateTime initiatedAt;

    // all-args constructor + getters

}
```

But not every event is directly related to the user interactions with the telephony software. There are also other events that can occur when the user interacts with some business application, such as for instance:

```java
@DomainEvent
public class SelectCustomerEvent implements Event {

    private final CustomerNumber customerNumber;

    // all-args constructor + getter

}


@DomainEvent
public class PublishProtocolEvent implements Event { }
```

And many more.

Pretty straight-forward so far. Now let's see how we can represent the call state of an employee. Many Java developers like to use [enums to implement state machines](https://www.baeldung.com/java-enum-simple-state-machine). But since there is usually some business logic involved in the states, I prefer to have one class and file per state to get a better overview, instead of putting all the states and their transition logic in a single enum.

Eventually, this is how one exemplary state can be modeled:

```java
public final class CallAcceptedState implements CallState {

    public boolean supports(Event event) {
        return event instanceof EndCallEvent || event instanceof SelectCustomerEvent;
    }
    
    // code to handle events

}
```

Note that in a classical state pattern implementation, we usually put the code to handle an event directly in the state. But in this specific usecase, I found this to be quite cumbersome for several reasons:

1. **Code duplication:** Many events can be handled by many different states.
2. **Injecting dependencies:** A handler is allowed to mutate state, publish new events and even use interfaces of infrastructure services. Injecting all of these dependencies into the state is not very clean (1).
3. **Transaction boundary:** An aggregate must guarantee consistency of its data, therefore we should not mutate the state of the aggregate inside a child property.
4. **No single place for event handling:** For every event, I would like to have a good overview what its impact on the domain is (i.e. how and where it's handled).

(1) _Dependency injection frameworks would be an option, but I like the domain to be independent from everything, even DI frameworks (my only exception here is [Lombok](https://projectlombok.org/), since I consider it more as a language extension than a library or framework)._

For the reasons mentioned above, a better option is to extract the code to handle events in its own class. We can define a generic interface...

```java
public interface EventHandler<T extends Event> {

    void handle(Call context, T event);

}
```

... and then just implement the interface for every event in our domain. Let's look how a handler of the `IncomingCallEvent` can be implemented:

```java
public class IncomingCallEventHandler implements EventHandler<IncomingCallEvent> {

    private final EventPublisher eventPublisher;
    private final SomeOtherDependency someOtherDependency;

   	// all-args constructor

    @Override
    public void handle(Call context, IncomingCallEvent event) {
        // When the previous call already ended but user still didn't choose 
        // if he wants to submit or delete the call protocol, we just submit it
        // (which will transition to the IdleState).
        if (context.state() instanceof CallEndedState) {
            eventPublisher.publish(context.user().getId(), new SubmitProtocolEvent());
            eventPublisher.publish(context.user().getId(), event);
            return;
        }
        // Otherwise we are good to go. 
        // Handle handle the event normally and transition to the next state.
        context.initiateIncoming(event.getPhoneNumber(), event.getInitiatedAt());
        someOtherDependency.triggerSideEffect();
    }

}
```

By handling the events and state transitions in a seperate class, we: 
- avoid code duplication
- inject only the necessary dependencies in every handler
- assure all mutating calls are made by the aggregate root and can therefore guarantee integrity of the aggregate
- and have one single place to handle all events (we even see all the side effects!)

Awesome 😃✋

But one thing remains: Once an event is published, we need to delegate it to the corresponding EventHandler. To do this, we can use a simple map in the application layer that associates each event type with its handler.

```java
private Map<Class<? extends Event>, EventHandler<? extends Event>> eventHandlerRegistry = Map.of(
    IncomingCallEvent.class, new IncomingCallEventHandler(eventPublisher, someOtherDependency),
    OutgoingCallEvent.class, new OutgoingCallEventHandler(eventPublisher),
    // ...
);
```

Now whenever an event is published, we can lookup the handler in the map and just delegate the handling of the event to that handler:

```java
@DomainEventHandler
private void applyEvent(UserId userId, CtiEvent event) {
    var call = callRepository.find(userId).orElse(newCall(userOf(userId)));
    if (!call.supports(event)) { // delegates to state
        throw new UnsupportedEventException(event, call.state());
    }
    eventHandlerRegistry.get(event.getClass()).handle(call, event);
    callRepository.save(call);
}
```


## Summary
There [does][1] [not][2] [seem][3] to be _THE_ approach to build a state machine inside a DDD context. We started with the classical state pattern as a property of our `Call` aggregate and then modified and extended it to fit the rather complex needs of the example callcenter domain. We ended up with a pragmatic solution that is simple and yet powerful enough to handle all kinds of events dependent on the current aggregate state.

An alternative approach could be to model the aggregate itself as a state (so one aggregate per state), instead of using a property which holds the state. But since this mixes two concepts together, I think that could get out of hand quickly if there is a larger amount of states. In even more complex use-cases it might be a good idea to consider using a framework such as the [Spring State Machine](https://projects.spring.io/spring-statemachine/).

Thanks for reading and happy coding ☕👨‍💻


[1]: https://softwareengineering.stackexchange.com/questions/364910/how-to-implement-state-machine-pattern-on-aggregate-root
[2]: https://stackoverflow.com/questions/10011859/state-pattern-and-domain-driven-design
[3]: https://stackoverflow.com/questions/33593019/state-machines-for-entities-in-ddd-and-dependency-injection-context

**PS:**
_These DDD stereotype annotations I used are purely informational and do not add any logic to the code. I use them for [architecture governance](https://patricsteiner.github.io/unit-tests-for-software-architecure/) only at the moment._

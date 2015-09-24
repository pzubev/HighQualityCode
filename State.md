##Intent
- Allow an object to alter its behavior when its internal state changes. The object will appear to change its class.
- An object-oriented state machine
- wrapper + polymorphic wrappee + collaboration

##Problem

A monolithic object's behavior is a function of its state, and it must change its behavior at run-time depending on that state. Or, an application is characterixed by large and numerous case statements that vector flow of control based on the state of the application.

##Discussion
The State pattern is a solution to the problem of how to make behavior depend on state.

- Define a "context" class to present a single interface to the outside world.
- Define a State abstract base class.
- Represent the different "states" of the state machine as derived classes of the State base class.
- Define state-specific behavior in the appropriate State derived classes.
- Maintain a pointer to the current "state" in the "context" class.
- To change the state of the state machine, change the current "state" pointer.
The State pattern does not specify where the state transitions will be defined. The choices are two: the "context" object, or each individual State derived class. The advantage of the latter option is ease of adding new State derived classes. The disadvantage is each State derived class has knowledge of (coupling to) its siblings, which introduces dependencies between subclasses.

A table-driven approach to designing finite state machines does a good job of specifying state transitions, but it is difficult to add actions to accompany the state transitions. The pattern-based approach uses code (instead of data structures) to specify state transitions, but it does a good job of accomodating state transition actions.

##Structure

The state machine's interface is encapsulated in the "wrapper" class. The wrappee hierarchy's interface mirrors the wrapper's interface with the exception of one additional parameter. The extra parameter allows wrappee derived classes to call back to the wrapper class as necessary. Complexity that would otherwise drag down the wrapper class is neatly compartmented and encapsulated in a polymorphic hierarchy to which the wrapper object delegates.

![alt tag](https://sourcemaking.com/files/v2/content/patterns/State1-2x.png)

##State in C#ode

Allows an object to alter its behavior when its internal state changes. The object will appear to change its class.

This structural code demonstrates the State pattern which allows an object to behave differently depending on its internal state. The difference in behavior is delegated to objects that represent this state.

```c#
using System;

  class MainApp
  {
    static void Main()
    {
      // Setup context in a state 
      Context c = new Context(new ConcreteStateA());

      // Issue requests, which toggles state 
      c.Request();
      c.Request();
      c.Request();
      c.Request();

      // Wait for user 
      Console.Read();
    }
  }

  // "State" 
  abstract class State
  {
    public abstract void Handle(Context context);
  }

  // "ConcreteStateA" 
  class ConcreteStateA : State
  {
    public override void Handle(Context context)
    {
      context.State = new ConcreteStateB();
    }
  }

  // "ConcreteStateB" 
  class ConcreteStateB : State
  {
    public override void Handle(Context context)
    {
      context.State = new ConcreteStateA();
    }
  }

  // "Context" 
  class Context
  {
    private State state;

    // Constructor 
    public Context(State state)
    {
      this.State = state;
    }

    // Property 
    public State State
    {
      get{ return state; }
      set
      { 
        state = value; 
        Console.WriteLine("State: " + 
          state.GetType().Name);
      }
    }

    public void Request()
    {
      state.Handle(this);
    }
  }
  ```
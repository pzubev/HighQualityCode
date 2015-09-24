##Intent
- Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.
- Encapsulate the core (or common or engine) components in a Subject abstraction, and the variable (or optional or user interface) components in an Observer hierarchy.
- The "View" part of Model-View-Controller.

##Problem
A large monolithic design does not scale well as new graphing or monitoring requirements are levied.

##Discussion
Define an object that is the "keeper" of the data model and/or business logic (the Subject). Delegate all "view" functionality to decoupled and distinct Observer objects. Observers register themselves with the Subject as they are created. Whenever the Subject changes, it broadcasts to all registered Observers that it has changed, and each Observer queries the Subject for that subset of the Subject's state that it is responsible for monitoring.

This allows the number and "type" of "view" objects to be configured dynamically, instead of being statically specified at compile-time.

The protocol described above specifies a "pull" interaction model. Instead of the Subject "pushing" what has changed to all Observers, each Observer is responsible for "pulling" its particular "window of interest" from the Subject. The "push" model compromises reuse, while the "pull" model is less efficient.

Issues that are discussed, but left to the discretion of the designer, include: implementing event compression (only sending a single change broadcast after a series of consecutive changes has occurred), having a single Observer monitoring multiple Subjects, and ensuring that a Subject notify its Observers when it is about to go away.

The Observer pattern captures the lion's share of the Model-View-Controller architecture that has been a part of the Smalltalk community for years.

##Structure

![alt tag](https://sourcemaking.com/files/v2/content/patterns/Observer-2x.png)

Subject represents the core (or independent or common or engine) abstraction. Observer represents the variable (or dependent or optional or user interface) abstraction. The Subject prompts the Observer objects to do their thing. Each Observer can call back to the Subject as needed.

##Observer in Code

```c#
using System;
using System.Collections;

  class MainApp
  {
    static void Main()
    {
      // Configure Observer pattern 
      ConcreteSubject s = new ConcreteSubject();

      s.Attach(new ConcreteObserver(s,"X"));
      s.Attach(new ConcreteObserver(s,"Y"));
      s.Attach(new ConcreteObserver(s,"Z"));

      // Change subject and notify observers 
      s.SubjectState = "ABC";
      s.Notify();

      // Wait for user 
      Console.Read();
    }
  }

  // "Subject" 
  abstract class Subject
  {
    private ArrayList observers = new ArrayList();

    public void Attach(Observer observer)
    {
      observers.Add(observer);
    }

    public void Detach(Observer observer)
    {
      observers.Remove(observer);
    }

    public void Notify()
    {
      foreach (Observer o in observers)
      {
        o.Update();
      }
    }
  }

  // "ConcreteSubject" 
  class ConcreteSubject : Subject
  {
    private string subjectState;

    // Property 
    public string SubjectState
    {
      get{ return subjectState; }
      set{ subjectState = value; }
    }
  }

  // "Observer" 
  abstract class Observer
  {
    public abstract void Update();
  }

  // "ConcreteObserver" 
  class ConcreteObserver : Observer
  {
    private string name;
    private string observerState;
    private ConcreteSubject subject;

    // Constructor 
    public ConcreteObserver(
      ConcreteSubject subject, string name)
    {
      this.subject = subject;
      this.name = name;
    }

    public override void Update()
    {
      observerState = subject.SubjectState;
      Console.WriteLine("Observer {0}'s new state is {1}",
        name, observerState);
    }

    // Property 
    public ConcreteSubject Subject
    {
      get { return subject; }
      set { subject = value; }
    }
  }
  ```
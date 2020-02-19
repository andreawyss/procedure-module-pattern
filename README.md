# Procedure Module pattern

**"One module containing all phases of one message processing procedure."**

**Procedure Module** is the short name for: Message Processing Procedure Module.

The **Procedure Module** is a structural code organization pattern.

## Definitions

**Message**: Information sent to notify that something has occurred or to request that something occurs. In some contexts, Messages may also be called actions, commands or events.
Messages are identified by their _type_, and the information they carry is called the message _payload_.  
A Message may express the intent to do something but must never express how to do it.

**Procedure**: Steps performed in a certain order to obtain a result. A Procedure expresses how to do something.
In the context of processing Messages, when a certain type of Message is received, a certain Procedure will process that Message.  
One Procedure may process more than one type of Message. One Message may be processed by more than one Procedure.

## Phases of message processing

These are the Phases that a **Procedure Module** may contain:

- **Message Creation Phase**  
  This phase occurs before the Message can be dispatched.
  The Message with its payload is created.  
  The **Procedure Module** may expose a utility function to ease the creation of the Message and its Payload that the Procedure processes.

* **Validation Phase**  
  In this pre-processing Phase, the Procedure determine if the Message can be processed or not.  
  Some steps that may be performed in this Phase are:

  - Validation: Is the Message and its Payload valid?
  - Authorization: Is the user authorized to perform this Procedure?
  - Ask the user for confirmation before proceeding

* **Processing Phase**  
  This is the main phase where the work is performed.  
  The steps in this Phase are:

  - Set the "processing" state
  - Perform step(s)

* **Processing Failed Phase**  
  In this post-processing Phase, the Procedure performs the steps needs to report and mitigate failures occurred during the Processing Phase.  
  Possible steps in this Phase are:

  - Clear the "processing" state
  - Record error (Log, Track)
  - May restore the state to its initial condition (Recover/Undo)
  - May notify the user
  - May retry by dispatching a retry Message

* **Processing Succeeded Phase**  
  In this post-processing Phase, the Procedure performs the steps needs to record the success and the results of the Processing Phase.  
  Possible steps in this Phase are:

  - Clear the "processing" state
  - Update the model
  - May notify the user

* **Broadcast Update Phase**  
  In this last Phase, the Procedure broadcasts a Message on what was updated. This may cause other Procedures to perform additional work.  
  This Phase has a single step:

  - Dispatch 'item' updated Message

## Motivations for the "Procedure Module" pattern

- The Phases of message processing are coupled. Changes to one Phase may affect the following Phase(s).
- Placing all Phases of one message processing Procedure inside one Module increases the cohesion of the code.
- Add new Procedures, by adding new **Procedure Modules**, without the need to modify the existing ones.
- It scales well in large teams where several people can work on separate **Procedure Modules** with no or minimal merge conflicts.

## Analogy

The **Organize Code by Features** pattern tells us to group all Files that implement one Feature under one ancestor Folder.

The **Procedure Module** pattern tells us to put all Phases of one message processing Procedure inside one code Module.  
In this case the Feature is the Procedure.

## Testing

It is recommend to test the **Procedure Module** as a whole (black-box testing), instead of testing each Phase independently (white-box testing). Testing each Phase independently leads to implementation specific tests, which are of less value when refactoring code.

## Recommendations

### Avoid Orchestration

Orchestration occurs when one creates a "master" multi-step Procedure that controls other Procedures.
Procedures become tightly coupled with the “master” Procedure gaining too much responsibility and complexity. Adding new Procedures often requires changes to existing ones.
Only use Orchestration with truly sub-procedures where the "master-procedure" would not be complete without the "sub-procedure" and the "sub-procedure" will not make sense by itself.

### Embrace Choreography

Choreography occurs when one creates Procedures that observe the Updates broadcasted by other Procedures. Procedures are loosely coupled. It is simple to add new Procedures with no or minimal changes to existing ones.

## History

This pattern emerged in 2005-2006 while [Andrea Wyss](https://www.linkedin.com/in/awyss/) and [Trent DiBacco](https://www.linkedin.com/in/trent-dibacco-82840515/) worked on a Rich Internet Application written in [ActionScript](https://en.wikipedia.org/wiki/ActionScript).  
This pattern was then used in several [Apache Flex](https://en.wikipedia.org/wiki/Apache_Flex) Rich Internet Application projects and a few [Adobe AIR](https://get.adobe.com/air/) Mobile Apps.  
Since 2017, this pattern was used in several large SPAs (Single Pages Applications) written in [React](https://reactjs.org/) & [Redux](https://redux.js.org/).

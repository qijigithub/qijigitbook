# OOP design pattern

## Design Pattern Implementation Documents

### Brief Project Summary

My project catalogs Six Patterns: Strategy Pattern, Decorator Pattern, Observer Pattern, Command Pattern, Adapter Pattern, and Façade Pattern. I choose to different application to each explain each pattern. The coding software is IntelliJ.

### 1.Strategy pattern: behavioral Pattern

#### Explanation of problem and solution

At the begging, I want to write a program to plan travel for travelers. The Destination and the transportation are the first thing I need to consider about. Thus I write an abstract class called “traveler” which has the functions for setting destination and transportation so that different traveler could inherit this class.

However, I found different traveler has different destination. If I have 10 travel, 5 of them want to go 2 different destinations, I have to override that function 5 at least five times. So I need a solution which could make the destination interchangeable. I figured out Strategy Pattern: Using if in the situation that one problem is able to be solved by multiple similar algorithms. Here is my solution: I take the destination function out of Traveler class, and the strategy of each destination implemented destination interface are packaged into a single class and replaced arbitrarily when context\(traveler\) change.

Meanwhile, the transportation and the choices of partner have same problem with Destination. To be specific, Travelers may choose different transportation to go destination and they may want family members or friends to go travel with, even travel alone. Thus, same idea also be implemented in Transportation interface and Partner interface.

#### Project folder that contains the code

List of Java file for Strategy pattern:

\(strategy\)Transportation interface.java, train.java, plane.java, car.java \(strategy\)Destination interface.java, Japan. Java, NewYork.java, \(strategy\)Partner interface.java, Alone.java, WithFamily.java, \(context\)Traveler abstract class.java, Peter.java, Mary.java \(main function\) strategydemo.java

#### Implementation document and explanation

Run strategydemo.java document:

Traveler Peter will go travel alone and take airplane, the destination is Japan. Traveler Mary will go travel with family member by driving car, the destination is New York. Traveler Peter also go travel to New York and this time, he goes with his family by train.

### 2.Observer pattern：behavioral Pattern

#### Explanation of problem and solution

We get some information about some subject, I want to broadcast to every Observer followed me. I just use Observer and Subject to be the class name. I thought it is easier to catch the point. Wrong code:

```text
public class BadObserver {
        public void getchanged() {
            info1 = getInfo1();
            info2 = getInfo2();
Observer1.update(info1, info2);
Observer1.display(info1, info2);
Observer2.update(info1, info2): data1;
Observer2.display(data1);
Observer3.update(info1, info2): data2;
Observer3.display(data2);    
}
```

This code just finished the basic function, which is Notify Observer once subject get check. However, if we write code in this way, we cannot add observer when program start running. And One class has not only one responsibility, violating single responsibility principle. they all have an update\(\) method takes the info1 and info, too much needless repetitions appears. To solve the problem, we could separate informer and observer and encapsulate what changes to make code easier to change. Also, allowing multiple Observers and Subject, notifying all observers when subject get changes, that functions reduce the dependent relationship and connect them for as long as needed.

#### Project folder that contains the code

List of Java file for Observer pattern: \(Subject\)Subject interface.java, subject1.java \(Observer\)Observer interface.java, Observer1.java, Observer2.java, Observer3.java \(main function\) ObserverDemo.java

#### Implementation document and explanation

Run ObserverDemo.java document: Subject1 will be create and Observer1, Observer 2 and Observer 3 add themselves to Subject ArrayList. Once Subject1 set a change, it will notify all Observer in the Observer ArrayList. Each Observer will receive the update information and print it in their own way.

### 3.Decorator pattern：Structural Pattern

#### Explanation of problem and solution

Every time we play game, we know that the characters in game, besides the basic body, may have different custom, weapons and skills so that there are multiple characters with littler change. Wrong code:

```text
Public abstract class Character {
    Private String description;
    Public abstract getDescription(){Return description;}
 }
public class Man extends Character{}
public class Woman extends Character{}
public class WomanwithFancyCustom extends Character{}
public class MenwithFancyCustom extends Character{}
public class MenwithFancyCustomandAK47 extends Character{}
public class MenwithNormalCustomandAK47 extends Character{}
….
```

I could write thousands of the class… and if I add one new skills, I should write all the combination of skill and character again, that is very inefficient way to develop and one class may more than one responsibility. Second, if one of the custom change, all subclass will be change, violating open closed principle. That make the program is hard to maintain. Thus, I use decorator pattern to deal with it. The point that we can put the custom, weapon and skill as the decorator, attaching other responsibility to the object dynamically.

#### Project folder that contains the code

List of Java file for Decorator pattern: \(Character\)Woman.java, Man.java \(Decorator\)Decorator abstract class.java, CustomDec.java, WeaponDec.java, SkillDec.java \(main function\) DecoratorDemo.java

#### Implementation document and explanation

Run DecoratorDemo.java The basic character are man and woman,but the basic character could be add the function from from CustomDec, WeaponDec, and SkillDec class respectively. The prerequisite is decorator implements the same interface or abstract class as the Character they are going to decorate.

### 4.Command pattern：behavioral Pattern

#### Explanation of problem and solution

This time, I want to make a Car remote controller. The functions contained are lightOn\(\), lightOff\(\), and DoorOpen\(\).

Wrong code:

```text
Public Class RemoteController{
If (slot1==light){
lightOn();}
else(slot1==Door) {doorOpen();}
}
```

In this software system, behavior requesters and behavior implementers are usually a tightly coupled relationship. However, in some cases, such as the need to change remote controller, light or door, this type of incompatibility cannot be resisted. Coupling design is not suitable. At this situation, the decoupling is a good way to thinking. So the command pattern could be implement decoupling process. The idea is that creating an invoker invokes the receiver through the command, in the order: invoker → command→ receiver.

#### Project folder that contains the code

List of Java file for Command pattern

\(Invoker\)RemoteController.java \(Command\) CarlightOnCommand.java, CarlightOffCommand.java, CardoorOpenCommand.java \(receiver\)Cardoor.java, Carlight.java \(main function\) CommandDemo.java

#### Implementation document and explanation

Run CommandDemo.java document

After Remote controller button pressed, the command start execute, it will call receiver to action.

### 5. Adapter pattern：Structural Pattern

#### Explanation of problem and solution

The main solution is to often put some "objects" into the new environment in the software system, and the interfaces required by the new environment are not satisfied by current objects. The adapter pattern convert “new object” the customer wants so that it possible for those classes that originally could not work together due to incompatible interfaces.

#### Project folder that contains the code

List of Java file for adapter pattern

\(Target\) ChinaVolt.java, CWallOutlet.java \(Adaptee\)AmericVolt.java, AmericaLaptop.java \(adapter\)Voltadapter.java \(main function\) AdapterDemo.java

#### Implementation document and explanation

Run Adapter.java America Laptop volt is 110v, And China Wall Outlet volt is 220v. If you want to use China outlet, you have to use VoltAdapter, which transform 110v to 220v, thus it matches now.

### 6. Facade pattern：Structural Pattern

#### Explanation of problem and solution

To illustrate the facade pattern, let’s try to look into a small example. When I want to play game. There are several things I have to do: 1 turn on the TV, 2 tuner to HDMI mode channel, 3 turn on the xbox 4connect controller 5 dim the light 6 turn on the projector 6 load the game. Wrong code:

```text
Public static void main(String args[]){
TV.turnOn()
TV.HTMImode()
Xbox.turnOn()
Xbox.connectCtroller()
Light.dim()
Projecter. On()
Xbox.loadGame()
}
```

All that things should be done manually, whereas Façade pattern provides a combination for a set of interfaces in the subsystem and defines a high-level interface that makes this subsystem easier to use.

#### Project folder that contains the code

List of Java file for Facade pattern: \(Facade\)GameFacade.java \(Subsystem\) TV.java, Xbox.java.projector.java, light.java \(main function\) FaçadeDemo.java

#### Implementation document and explanation

Run FacadeDemo.java Façade will help you start game mode. The client does not need to know the complex link within the system. The entire system only needs to provide a "Façade Pattern".1 turn on the TV, 2 switch to HDMI mode channel, 3 turn on the xbox 4connect controller 5 dim the light 6 turn on the projector 6 load the game. And when you don’t want to play anymore. The program will help you close all of System.


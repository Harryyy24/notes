# notes


assignment 1 


Title: Echo Server
Aim:

To write a program for a concurrent echo client-server application using socket programming in Java.

🧠 Theory Explanation (Oral Exam Preparation)
1️⃣ Interprocess Communication (IPC)

Meaning: It is a mechanism that allows processes (running programs) to communicate with each other and share data.

Example: In a client-server setup, the client and server are two different processes — IPC allows them to exchange messages.

Techniques: Pipes, Message Queues, Shared Memory, and Sockets.

In this experiment: We use sockets as the IPC mechanism between client and server.

2️⃣ Socket

A socket is an endpoint of a two-way communication link between two programs running on a network.

It works as a door through which data is sent or received.

Each socket is associated with:

IP Address (identifies machine)

Port Number (identifies application/service)

📘 Types of Sockets in Java:

ServerSocket: used on the server side to listen for incoming connections.

Socket: used on the client side (and by the server once a client connects).

Example:

// Server side
ServerSocket serverSocket = new ServerSocket(5000);
Socket socket = serverSocket.accept(); // waits for client

// Client side
Socket socket = new Socket("127.0.0.1", 5000);

3️⃣ TCP vs UDP
Feature	TCP (Transmission Control Protocol)	UDP (User Datagram Protocol)
Type	Connection-oriented	Connection-less
Reliability	Reliable (acknowledgments, retransmission)	Unreliable (no guarantee)
Order of data	Maintains order	May arrive out of order
Use case	Chat apps, file transfer, web	Live streaming, games

👉 In Echo Server, we use TCP, because we need reliable, ordered communication.

4️⃣ TCP Socket Communication Steps
🔹 Server Side:

Create a ServerSocket with a specific port.

Wait (accept) for client requests.

When a client connects, create a Socket for communication.

Use InputStream and OutputStream to exchange data.

Close connections after communication.

🔹 Client Side:

Create a Socket specifying server IP and port.

Use InputStream and OutputStream to send and receive data.

Close connection when done.

5️⃣ Echo Server

The echo server simply receives a message from the client and sends back the same message.

Example:

Client → “Hello Server!”

Server → “Hello Server!”

This demonstrates bidirectional communication between client and server.

6️⃣ Concurrent Server

A concurrent server handles multiple clients simultaneously.

It uses multithreading:

Each new client connection is assigned to a separate thread.

This allows multiple clients to chat with the server independently at the same time.

Example Flow:

ServerSocket listens on port 5000.

Client A connects → handled by Thread 1

Client B connects → handled by Thread 2

Both clients can communicate without blocking each other.

7️⃣ Java Classes Used
Class	Description
ServerSocket	Listens for client requests on a specific port.
Socket	Creates a connection between client and server.
InputStream, OutputStream	Used to read/write data.
BufferedReader, PrintWriter	Easier handling of text data over sockets.
Thread	Allows handling multiple clients at once.
8️⃣ Tools / Environment

Java JDK 1.8+

Eclipse IDE

RMI Registry (not mandatory here, used for remote object communication)

Localhost (127.0.0.1) for local testing

💻 Steps to Perform the Practical (Implementation Guide)
(A) Server Program – Concurrent Echo Server
Step 1: Import required packages
import java.io.*;
import java.net.*;

Step 2: Create Server class
class ClientHandler extends Thread {
    Socket socket;

    ClientHandler(Socket socket) {
        this.socket = socket;
    }

    public void run() {
        try {
            BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
            String msg;

            while ((msg = in.readLine()) != null) {
                if (msg.equalsIgnoreCase("exit")) break;
                System.out.println("Client: " + msg);
                out.println("Echo: " + msg);  // sending message back
            }

            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

public class EchoServer {
    public static void main(String[] args) {
        try {
            ServerSocket server = new ServerSocket(5000);
            System.out.println("Server started... waiting for clients");

            while (true) {
                Socket socket = server.accept();
                System.out.println("Client connected!");
                new ClientHandler(socket).start(); // thread for each client
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

(B) Client Program – Echo Client
import java.io.*;
import java.net.*;

public class EchoClient {
    public static void main(String[] args) {
        try {
            Socket socket = new Socket("127.0.0.1", 5000);
            BufferedReader userInput = new BufferedReader(new InputStreamReader(System.in));
            BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            PrintWriter out = new PrintWriter(socket.getOutputStream(), true);

            String msg;
            System.out.println("Type message (type 'exit' to quit): ");
            while (true) {
                msg = userInput.readLine();
                out.println(msg);
                if (msg.equalsIgnoreCase("exit")) break;
                System.out.println("Server replied: " + in.readLine());
            }

            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

⚙️ Steps to Execute (in Eclipse or Terminal)

Open two terminals / run configurations:

One for EchoServer

One for EchoClient

Run the server first.

Output:
Server started... waiting for clients

Run the client next.

Output:
Type message (type 'exit' to quit):

Type messages in client console:

Client → “Hello”

Server receives and prints “Client: Hello”

Client receives “Echo: Hello”

Type exit to stop.

You can open multiple clients to test concurrent behavior.

✅ Conclusion

You successfully implemented a Concurrent Echo Client-Server Application using Java sockets.

The server can handle multiple clients simultaneously using threads.

This experiment demonstrates interprocess communication, network programming, and multithreading.

📊 Expected Viva Questions
Question	Short Answer
What is a socket?	Endpoint of two-way communication between two programs.
Which class is used for server-side socket?	ServerSocket
Which protocol is used here?	TCP
What is an echo server?	A server that returns the same message it receives.
What is a concurrent server?	A server that handles multiple clients simultaneously using threads.
Difference between TCP and UDP?	TCP is reliable and connection-oriented; UDP is fast but unreliable.
What are InputStream and OutputStream used for?	Reading and sending data through sockets.
What happens if client sends “exit”?	The connection is closed.

-----------------------------------------------------------------------------------------------------------------------------------


ASSIGNMENT 2 ---


🔹 PART 1: THEORY EXPLANATION (For Oral Exam)

Let’s go through all key theory points in a structured and simplified way (exactly how you can answer in the viva).

🔸 1. What is RMI?

RMI (Remote Method Invocation) allows a Java object running on one JVM (machine) to call methods on another Java object running on a different JVM — as if the object were local.

👉 In simple words:
It’s Java’s way of doing remote communication between programs using objects.

🔸 2. Why do we use RMI?

To build distributed applications.

To allow client-server communication using objects rather than raw data (like in sockets).

To make remote objects act like local objects for the client.

🔸 3. Basic Architecture of RMI
Components:
Component	Role
Client	Requests remote services
Server	Provides implementation of remote methods
RMI Registry	Acts like a phone directory that keeps mappings of remote object names to their actual references
Stub	A proxy object on the client side that sends the request to the server
Skeleton	(Older Java versions) Server-side proxy that receives the request from the stub and invokes the actual method on the remote object
Remote Object	The actual object residing on the server whose method is invoked remotely
🔸 4. Working of RMI (Step-by-Step Flow)

The server creates a remote object and registers it with the RMI registry under a specific name.

The client looks up that name in the RMI registry to get the stub (proxy).

When the client calls a method on the stub:

Stub marshals (serializes) parameters.

Sends them over the network to the server.

The server-side skeleton (or runtime) unmarshals (deserializes) data and calls the actual method.

The result is marshaled back and returned to the client.

📘 Analogy:
Stub = Client’s local “representative” of the server object.
Skeleton = Server’s “listener” that executes the method and returns result.

🔸 5. Important RMI Terminologies
Term	Explanation
Remote Interface	Defines methods that can be called remotely. Must extend java.rmi.Remote.
Remote Object	Implements the remote interface. Must extend UnicastRemoteObject.
Stub	Client-side proxy that communicates with the remote object.
Skeleton	Server-side proxy (used in older Java; now handled internally).
RMI Registry	Name service used to bind and look up remote objects. Started using rmiregistry.
🔸 6. Steps to Develop an RMI Application

Create a Remote Interface (e.g. Adder.java)

Implement the Remote Interface (e.g. AdderRemote.java)

Create Server Program (e.g. MyServer.java)

Create Client Program (e.g. MyClient.java)

Compile all classes using javac

Run RMI registry using start rmiregistry

Start the server

Run the client and test communication

🔸 7. Advantages of RMI

Object-oriented (uses Java objects directly)

Type-safe communication (no need to parse data)

Easier than raw socket programming

Built-in Java feature (no external library needed)

🔸 8. Real-World Example

Remote database access system

Distributed calculator app

Chat server between multiple clients

🔹 PART 2: PRACTICAL EXECUTION STEPS (With Your Code)

Your practical is based on multi-threaded client-server RMI communication (Addition example).

Here’s the full execution plan 👇

✅ Step 1: Create Files

Create these 4 Java files in the same folder:

Adder.java
AdderRemote.java
MyServer.java
MyClient.java

✅ Step 2: Compile All Files

In terminal or command prompt, run:

javac *.java


This generates .class files.

✅ Step 3: Start RMI Registry

In the same directory (where .class files exist), run:

start rmiregistry 3000


⚠️ Keep this window open — do not close it.

✅ Step 4: Start the Server

In a new terminal, run:

java MyServer


Expected output:

Server ready...

✅ Step 5: Start the Client

In another terminal, run:

java MyClient


Then enter two numbers, e.g.:

Enter first number: 5
Enter second number: 7


Expected output:

Sum from server: 12

✅ Step 6: Verify Server Console

Server window should display:

Client [127.0.0.1:some_port] requested: 5 + 7
Sum is: 12


This confirms successful RMI communication between client and server.

🔹 PART 3: ORAL EXAM PREPARATION (Short Answers)
Question	Short Answer
What is RMI?	It allows Java objects to invoke methods on remote objects located on another JVM.
What is a Stub?	A client-side proxy for a remote object. It forwards method calls to the remote server.
What is a Skeleton?	A server-side proxy that receives requests from the stub and invokes the real object (handled internally in new JDKs).
What is RMI Registry?	A bootstrap naming service used to bind and locate remote objects.
Which package is used in RMI?	java.rmi and java.rmi.server
What exception must every remote method throw?	RemoteException
What is the purpose of UnicastRemoteObject?	To make an object available to receive incoming remote calls.
What is the command to start RMI registry?	start rmiregistry
What is marshaling and unmarshaling?	Marshaling = sending (serializing) data, Unmarshaling = receiving (deserializing) data.
🔹 PART 4: Expected Output Summary

Client Side:

Enter first number: 10
Enter second number: 20
Sum from server: 30


Server Side:

Server ready...
Client [127.0.0.1:54321] requested: 10 + 20
Sum is: 30

✅ CONCLUSION (To Write in Journal / Speak in Oral)

“In this experiment, I learned how Remote Method Invocation (RMI) allows a Java object running on one machine to invoke methods on another Java object located remotely. I also understood the roles of Stub, Skeleton, and RMI Registry in enabling distributed client-server communication.”

-----------------------------------------------------------------------------------------------------------------------------------


ASSINGMENT 3 --



THEORY EXPLANATION (For Oral Exam)
1️⃣ What is CORBA?

Full Form: Common Object Request Broker Architecture

Definition:
CORBA is a middleware architecture developed by OMG (Object Management Group) that allows objects written in different languages and running on different machines to communicate with each other over a distributed network.

💡 Think of CORBA as a “translator” that lets Java, C++, and Python objects talk to each other as if they were on the same machine.

2️⃣ Why CORBA is Needed

In a distributed environment, different systems may use:

Different programming languages (Java, C++, .NET, Python)

Different OS (Windows, Linux, Mac)

Different network configurations

CORBA solves this by defining standards for communication and interoperability among them.

3️⃣ Key Component: ORB (Object Request Broker)

ORB = The heart of CORBA.

It acts like a messenger or broker between client and server.

It handles:

Object references

Communication (through IIOP)

Method invocation

Data marshalling (convert data to transferable form)

4️⃣ CORBA Architecture Overview
Component	Role
Client	Requests a service (invokes a method)
Server	Provides the implementation of the service
ORB	Mediates between Client & Server
IDL (Interface Definition Language)	Defines the interface contract
Stub (Client side)	Proxy for server object
Skeleton (Server side)	Proxy for actual object on server
POA (Portable Object Adapter)	Connects ORB requests to actual objects
IIOP (Internet Inter-ORB Protocol)	Standard protocol for ORB-to-ORB communication
5️⃣ How CORBA Works (Communication Steps)

Define an interface using IDL (Interface Definition Language).
→ Example: interface Hello { string sayHello(); }

Compile the IDL file using the idlj compiler.
→ This generates Java classes: interface, helper, holder, stub, skeleton.

Implement the server-side logic (in Java).
→ The actual business logic goes here.

Start the ORB and Naming Service
→ These allow the client to find the server object.

Write and run the client to call the remote object.

6️⃣ Role of IDL (Interface Definition Language)

Language-neutral way of defining object interfaces.

Defines methods, data types, and parameters.

Once compiled, generates code to enable communication between different languages.

Example:

module HelloApp {
    interface Hello {
        string sayHello();
    };
};


When compiled, generates:

Hello.java (interface)

HelloHelper.java

HelloHolder.java

_HelloStub.java

_HelloImplBase.java

7️⃣ POA (Portable Object Adapter)

Acts as a bridge between the ORB and the actual server object.

Assigns object IDs and manages object activation.

Ensures portability across different ORB implementations.

8️⃣ IIOP (Internet Inter-ORB Protocol)

Standard communication protocol used by CORBA.

Enables ORBs to communicate across the internet.

9️⃣ Java IDL

Java implementation of CORBA.

Provides tools:

idlj → IDL-to-Java compiler

orbd → ORB daemon (naming service)

orbd -ORBInitialPort → Starts naming service

Packages used: org.omg.CORBA, org.omg.PortableServer, etc.

🔟 CORBA vs RMI (Comparison)
Feature	CORBA	RMI
Language	Multi-language (C++, Java, Python)	Java-only
Interface	IDL	Java Interface
Communication	IIOP	JRMP
Platform	Cross-platform	Java-only
Object Passing	By reference	By reference & value
⚙️ STEPS TO PERFORM THE PRACTICAL

Let’s create a simple CORBA example — a Hello Service.

🔸 Step 1: Write the IDL File

Create a file named Hello.idl

module HelloApp {
    interface Hello {
        string sayHello();
    };
};

🔸 Step 2: Compile the IDL file

Use the idlj compiler:

idlj -fall Hello.idl


This generates:

HelloApp/
├── Hello.java
├── HelloHelper.java
├── HelloHolder.java
├── HelloOperations.java
├── HelloPOA.java
└── _HelloStub.java

🔸 Step 3: Write the Server Implementation

HelloServer.java

import HelloApp.*;
import org.omg.CORBA.*;
import org.omg.PortableServer.*;
import org.omg.PortableServer.POA;
import org.omg.CosNaming.*;
import org.omg.CosNaming.NamingContextPackage.*;

public class HelloServer {
    public static void main(String args[]) {
        try {
            ORB orb = ORB.init(args, null);
            POA rootpoa = POAHelper.narrow(orb.resolve_initial_references("RootPOA"));
            rootpoa.the_POAManager().activate();

            HelloImpl helloImpl = new HelloImpl();
            helloImpl.setORB(orb);

            org.omg.CORBA.Object ref = rootpoa.servant_to_reference(helloImpl);
            Hello href = HelloHelper.narrow(ref);

            org.omg.CORBA.Object objRef = orb.resolve_initial_references("NameService");
            NamingContextExt ncRef = NamingContextExtHelper.narrow(objRef);

            NameComponent path[] = ncRef.to_name("Hello");
            ncRef.rebind(path, href);

            System.out.println("HelloServer ready and waiting ...");

            orb.run();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}


HelloImpl.java

import HelloApp.*;
import org.omg.CORBA.*;

public class HelloImpl extends HelloPOA {
    private ORB orb;

    public void setORB(ORB orb_val) {
        orb = orb_val;
    }

    public String sayHello() {
        return "Hello from CORBA Server!";
    }
}

🔸 Step 4: Write the Client Code

HelloClient.java

import HelloApp.*;
import org.omg.CORBA.*;
import org.omg.CosNaming.*;
import org.omg.CosNaming.NamingContextPackage.*;

public class HelloClient {
    public static void main(String args[]) {
        try {
            ORB orb = ORB.init(args, null);
            org.omg.CORBA.Object objRef = orb.resolve_initial_references("NameService");
            NamingContextExt ncRef = NamingContextExtHelper.narrow(objRef);

            Hello helloRef = HelloHelper.narrow(ncRef.resolve_str("Hello"));

            System.out.println("Message from Server: " + helloRef.sayHello());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

🔸 Step 5: Start Naming Service
orbd -ORBInitialPort 1050 &

🔸 Step 6: Run the Server
java HelloServer -ORBInitialPort 1050 -ORBInitialHost localhost

🔸 Step 7: Run the Client
java HelloClient -ORBInitialPort 1050 -ORBInitialHost localhost

✅ Output:
HelloServer ready and waiting ...
Message from Server: Hello from CORBA Server!

🎓 CONCLUSION

CORBA provides network transparency.

Java provides implementation transparency.

CORBA enables communication between applications written in different languages.

Java IDL simplifies building distributed, interoperable systems.

🎯 EXPECTED VIVA QUESTIONS
Question	Short Answer
What is CORBA?	Common Object Request Broker Architecture — middleware for distributed communication.
What is ORB?	Object Request Broker — mediates between client and server objects.
What is IDL?	Interface Definition Language — defines interfaces for distributed objects.
What is POA?	Portable Object Adapter — connects requests to correct object implementations.
What is IIOP?	Internet Inter-ORB Protocol — used for communication between ORBs.
What is difference between RMI and CORBA?	RMI = Java only; CORBA = multi-language and cross-platform.
What is stub and skeleton?	Stub = client-side proxy; Skeleton = server-side proxy.
What are IDL-generated files?	Interface, Helper, Holder, Stub, Skeleton.
Which tool generates stubs/skeletons?	idlj (IDL-to-Java compiler).
What is the function of orbd?	Starts the naming service for CORBA.



-----------------------------------------------------------------------------------------------------------------------------------


ASSIGNMENT 4 :--



🧭 Experiment: Clock Synchronization using Berkeley Algorithm
🔹 Aim

To implement Berkeley’s Algorithm for clock synchronization in a distributed system.

🔹 Objectives

To understand the basics of physical and logical clocks in distributed systems.

To implement an n-node distributed system that synchronizes all nodes’ clocks using Berkeley’s algorithm.

🔹 Theory (For Oral + Journal Explanation)

Let’s go through the theoretical concepts one by one — these are the same that examiners usually ask in orals 👇

🔸 1. What is Clock Synchronization?

In a distributed system, multiple computers (nodes) have their own local clocks.
Because of hardware differences and network delays, their clocks drift over time (i.e., show slightly different times).

To ensure proper coordination (like ordering events, logs, transactions, etc.), all nodes must agree on a common time.
This process is called clock synchronization.

🔸 2. Types of Clocks
Type	Description
Physical Clock	Refers to the actual system time (real-time clock) of each machine.
Logical Clock	Used to order events without needing real-time accuracy (e.g., Lamport timestamps).

👉 In this experiment, we are using physical clocks and synchronizing them using Berkeley’s Algorithm.

🔸 3. What is Berkeley’s Algorithm?

It is a clock synchronization algorithm designed for distributed systems where no node has an accurate UTC time source.

Introduced by Gusella and Zatti (1989), Berkeley’s Algorithm ensures that:

All nodes agree on a common average time, and

The average time difference between nodes is minimized.

🔸 4. Basic Idea Behind Berkeley’s Algorithm

✅ Key Points:

One node acts as the master (coordinator).

Other nodes act as slaves.

The master collects clock times from all slaves.

The master computes the average time considering its own clock.

The master sends time adjustments to all nodes so that every clock is synchronized to the new average.

🔸 5. Steps of Berkeley’s Algorithm (in simple terms)
Step	Description
1. Master Election	One node is selected as the master (leader) — either preassigned or chosen by election algorithms like Bully or Ring.
2. Polling	The master node sends a request to all slaves asking for their current time.
3. Response Collection	Each slave responds with its local time.
4. Delay Calculation	Master measures round-trip delay to estimate the real time at each slave (using Cristian’s algorithm).
5. Average Time Calculation	The master averages all collected times (including its own).
6. Adjustment Broadcast	The master sends back to each node the amount of offset it should add or subtract from its clock.
7. Synchronization	Each node adjusts its clock accordingly.
🔸 6. Example
Node	Local Time	Offset (ms)
Master	10:00:00	+0
Node 1	10:00:30	+30s
Node 2	09:59:50	-10s
Node 3	10:00:20	+20s

➡ Average offset = (+30 -10 +20 +0) / 4 = +10s
➡ Each node adjusts its clock to 10 seconds ahead of its current time → all become synchronized.

🔸 7. Advantages

Easy to implement.

Works even if there’s no global UTC time source.

Provides average synchronization, not dependent on a single machine.

🔸 8. Disadvantages / Limitations

One node (master) is a single point of failure.

Assumes network delay is symmetric.

Slight inaccuracies remain due to message latency.

🔸 9. Scope for Improvement (as in your manual)

Improve accuracy using Cristian’s algorithm.

Ignore outlier nodes that are too far from the mean time.

Preselect a backup master in case the main one fails.

Instead of sending total synchronized time, send relative time difference to reduce latency.

🔸 10. Conclusion

The Berkeley algorithm is a simple, decentralized, and effective clock synchronization method for distributed systems.
It ensures that all nodes in the system agree on a common time reference even without a central time server.

🔹 Algorithm (Exam Ready)
Step 1: Select one node as the master and others as slaves.
Step 2: Master sends a request to all slaves to share their local time.
Step 3: Each slave sends its local time to the master.
Step 4: Master collects all times and calculates the average time.
Step 5: Master calculates the offset for each node.
Step 6: Master sends offset adjustments to all slaves.
Step 7: Each node adjusts its clock using the received offset.
Step 8: All nodes are now synchronized to the average time.

🔹 Explanation of Your Code
File: Berkeley.java
Classes Used:

Node → Represents each node (master or slave)

id: Node number

clockOffsetMs: How far this node’s time is from the real clock

currentTime(): Returns current simulated time for the node

Key Operations:

Creates 5 nodes (1 master, 4 slaves) with different time offsets.

Master collects all node times (simulating request/response).

Sorts times and removes outliers (optional improvement).

Calculates average time.

Each node adjusts its clock to match the average.

Displays times before and after synchronization.

💡 Output Behavior

Before Synchronization:

Node 0 Time: 10:15:45 (Offset: 0 ms)
Node 1 Time: 10:15:45 (Offset: 500 ms)
Node 2 Time: 10:15:44 (Offset: -800 ms)
Node 3 Time: 10:15:46 (Offset: 1200 ms)
Node 4 Time: 10:15:44 (Offset: -300 ms)


After Synchronization:

Node 0 Time: 10:15:45 (Offset: +100 ms)
Node 1 Time: 10:15:45 (Offset: +100 ms)
Node 2 Time: 10:15:45 (Offset: +100 ms)
Node 3 Time: 10:15:45 (Offset: +100 ms)
Node 4 Time: 10:15:45 (Offset: +100 ms)


All nodes now show approximately the same time.

🔹 Steps to Perform the Practical
🧩 Step 1: Create Java File

Create a folder named fourth_updated and inside it, save the file as Berkeley.java.

🧩 Step 2: Compile

In terminal:

javac fourth_updated/Berkeley.java

🧩 Step 3: Run
java fourth_updated.Berkeley

🧩 Step 4: Observe Output

You will see:

Times before synchronization

Times after synchronization

Average (reference) time

🧩 Step 5: Viva Preparation (Quick Q&A)
Question	Answer
What is clock synchronization?	Adjusting clocks of different systems to a common time.
What is the main idea of Berkeley’s algorithm?	The master averages the time from all nodes and sends corrections.
Why is Berkeley’s algorithm needed?	To coordinate time in systems without a global clock.
What happens if the master fails?	A backup master is elected.
What is the role of Cristian’s algorithm?	To calculate accurate delay between master and slaves.
What kind of clock does Berkeley’s algorithm use?	Physical clocks.
Is it centralized or distributed?	Semi-centralized (master coordinates but averages everyone’s time).
✅ Conclusion (Write in Journal)

Berkeley’s algorithm synchronizes clocks in distributed systems by calculating the average of all participating clocks.
It improves coordination among distributed nodes, allowing them to operate with minimal time difference even without an external UTC reference.

-----------------------------------------------------------------------------------------------------------------------------------



ASSIGNMENT 5:--



🧠 THEORY EXPLANATION (for Oral Exam)
🔹 What is an Election Algorithm?

In a distributed system, multiple processes (nodes) run independently.
Sometimes, one process must act as a coordinator (leader) — for example, to manage resources or synchronize actions.

But what if that coordinator fails or crashes?
Then the system must elect a new coordinator automatically — that’s where Election Algorithms come in.

Election algorithms ensure that:

Only one process becomes the leader.

All nodes agree on who the new leader is.

The algorithm works even if some nodes fail or recover later.

⚙️ 1. Bully Algorithm (Centralized, All-to-All Communication)
🔸 Concept:

Every process in the system has a unique process ID (PID).

Higher the ID → Higher priority.

The process with the highest ID among active processes becomes the coordinator.

🔸 Assumptions:

Every process knows the IDs of all others.

Communication is reliable.

Any process can directly send messages to any other process.

🔸 Steps (Working of Bully Algorithm):
🧩 Case 1: Coordinator Fails

Suppose the current coordinator stops responding.

Any process P that detects this failure starts an election.

P sends an ELECTION message to all processes with higher IDs.

If no higher-ID process responds →
🔹 P becomes the new coordinator and broadcasts a COORDINATOR message.

If a higher-ID process responds →
🔹 P stops its election and waits for that process to finish its election.

Eventually, the highest active process ID wins and becomes coordinator.

🧩 Case 2: A Failed Process Recovers

When a process comes back online, it starts an election.

If it has the highest ID, it wins automatically.

🔸 Why called Bully Algorithm?

Because the highest ID process "bullies" others — it always becomes the coordinator!

🔸 Advantages:

✅ Simple and easy to understand.
✅ Ensures the most powerful (highest ID) node becomes the leader.

🔸 Disadvantages:

❌ Too many messages exchanged (O(n²) complexity).
❌ Works only in systems with full connectivity.

⚙️ 2. Ring Algorithm (Decentralized, Circular Communication)
🔸 Concept:

All processes are logically arranged in a ring (circular order).

Each process can only communicate with its next process in the ring.

Coordinator = process with highest ID.

🔸 Assumptions:

Each process knows its successor in the ring.

Communication links are unidirectional (clockwise or counterclockwise).

Every process has a unique ID.

🔸 Steps (Working of Ring Algorithm):
🧩 Case 1: Coordinator Fails

Any process (say P) notices that the coordinator isn’t responding.

P starts an election by sending an ELECTION message (with its ID) to its next active process.

Each active process adds its own ID to the message and passes it along the ring.

When the message returns to the initiator →
🔹 The initiator determines the highest ID in the message list = new coordinator.

The initiator now sends a COORDINATOR message around the ring announcing the winner.

🔸 Example:

Let’s say 5 processes: {1, 2, 3, 4, 5} in a ring.
Coordinator = Process 5.

If 5 crashes → Process 2 detects failure:

2 → sends “ELECTION(2)” → 3 → 4 → 5(dead) → skips → 1 → back to 2.

Highest ID = 4 → 4 becomes the new coordinator.

2 sends “COORDINATOR(4)” → all know the new leader.

🔸 Advantages:

✅ Fewer messages (O(n)).
✅ Works even in large rings.
✅ No need for all-to-all connectivity.

🔸 Disadvantages:

❌ Slower than Bully in detecting failure.
❌ Must maintain a logical ring order.

🧩 Comparison Table:
Feature	Bully Algorithm	Ring Algorithm
Structure	All-to-All	Ring (one-way)
Communication	Direct to all higher processes	One-way along ring
Winner	Highest ID	Highest ID
Fault Tolerance	Needs election if coordinator fails	Needs election if coordinator fails
Message Complexity	O(n²)	O(n)
Speed	Faster (direct)	Slower (round-trip)
🧪 PRACTICAL STEPS (How to Perform in Lab)
🔸 For Bully Algorithm

Open Eclipse / IntelliJ / Terminal.

Create a Java file → BullyAlgorithm.java

Copy the code you provided.

Run the program.

Choose options from menu:

1. Crash process
2. Recover process
3. Start election
4. Exit


Example flow:

Crash highest process (coordinator)

Start election from a lower process

Observe that the highest alive process becomes coordinator

Recover crashed process and observe automatic re-election

🔸 For Ring Algorithm

Create a Java file → RingElection.java

Enter number of processes, e.g., 5

Assign IDs (e.g., 10, 20, 30, 40, 50)

The last process is made inactive (simulating coordinator failure)

Choose:

1. Start Election
2. Exit


Start election with any active process ID.

Observe:

Each process receives message

IDs circulate

Highest ID becomes new coordinator

Coordinator announcement sent across the ring

🧾 ORAL EXAM HINT QUESTIONS
Question	Answer
What is the need for Election Algorithm?	To select a new coordinator when the current one fails.
Why is it called Bully Algorithm?	Because the process with the highest ID “bullies” and becomes coordinator.
Difference between Bully and Ring Algorithm?	Bully uses all-to-all communication; Ring passes message in circular order.
What happens if coordinator recovers?	It holds a new election; if it’s the highest ID, it becomes coordinator again.
What are assumptions in Ring Algorithm?	Processes are arranged in a ring; each knows its successor; unidirectional communication.


----------------------------------------------------------------------------------------------------------------------------------



ASSIGNMENT 6 :---



🧠 THEORY EXPLANATION (Oral Exam Focus)
🔹 Introduction

MapReduce is a distributed programming model introduced by Google for processing large-scale data across clusters of computers in parallel.
It simplifies data distribution, fault tolerance, and load balancing automatically.

It divides a task into two main stages:
👉 Map — Divide and process chunks of data.
👉 Reduce — Aggregate results and produce the final output.

Used in: Big Data analytics, search indexing, word count, log analysis, etc.

🔹 Architecture & Execution Flow of MapReduce
Phase	Description
1. Input Splitting	Input data (e.g., text files) is split into Input Splits and distributed to different nodes for parallel processing. Each split is given to a separate Mapper.
2. Map Phase	Each Mapper processes its split line by line. It tokenizes text into words and emits (word, 1) pairs — meaning each occurrence of a word is counted as one.
3. Intermediate Storage & Partitioning	Mapper output is stored locally and partitioned by key (word) using a hash function. The same words go to the same Reducer.
4. Shuffle & Sort Phase	Intermediate data is transferred to Reducers. The framework groups and sorts all identical keys together so each Reducer receives all counts for a particular word.
5. Reduce Phase	Reducer takes each unique key (word) and list of values (1s), sums them up to get the total frequency of that word.
6. Output Phase	Final results (word, total count) are stored in the distributed file system as the final output.
🔹 Example (How Word Count Works)
Input Text:
Data is power
Data drives technology

Map Output:
(data, 1)
(is, 1)
(power, 1)
(data, 1)
(drives, 1)
(technology, 1)

Shuffle & Sort Output:
data → [1, 1]
is → [1]
power → [1]
drives → [1]
technology → [1]

Reduce Output (Final):
data → 2
is → 1
power → 1
drives → 1
technology → 1

🔹 Advantages of MapReduce

Handles very large datasets efficiently

Fault tolerant (failed nodes reprocessed automatically)

Parallel and scalable processing

Simplifies distributed computation using only Map & Reduce logic

⚙️ ALGORITHM (For Viva or Write-up)

Input: A collection of text documents
Output: Word frequency count

Steps:

Splitting: Divide the dataset into smaller chunks.

Map Function:

Read text line by line.

Tokenize into words.

Emit (word, 1).

Shuffle/Sort:

Group identical keys (same word).

Collect all values for that word.

Reduce Function:

For each key, sum all values.

Emit (word, total_count).

Output: Store the final word counts.

🧪 PRACTICAL STEPS (To Perform in Lab)
🖥️ Tool Setup:

Java version: JDK 1.8

IDE: Eclipse (EE) or Command line

Alternative (Python): Run directly in terminal or IDE

⚙️ Java Execution Steps:

Open Eclipse → File → New → Java Project
Name it: MapReduceWordCount

Create a Class:
File → New → Class → MapReduceWordCount

Paste the Java code (provided by you).

Run the program:
Right-click → Run As → Java Application.

Observe Output in Console:

Input Documents printed

Mapper phase output (word,1)

Grouped words (Shuffle/Sort)

Final reduced output showing word counts.

⚙️ Python Execution Steps:

Save the Python file as mapReduce.py.

Run in terminal or IDE:

python mapReduce.py


Enter sentences (each line is one document).

Type END to stop input.

Observe the 3 phases in order:

Map phase → emits (word, 1)

Shuffle/Sort → groups by word

Reduce phase → sums all counts

Final output shows words with total frequency in descending order.

🔍 CODE EXPLANATION (Short, Oral-ready)
🟦 Java Code: MapReduceWordCount.java
Part	Purpose
mapper()	Takes text input, converts to lowercase, removes punctuation, splits into words, emits (word,1).
reducer()	Takes each word and its list of 1s, sums them up → returns (word, total count).
main()	Simulates full MapReduce process: Map → Shuffle/Sort → Reduce → Final Output.

🧩 Simulation Flow in Output:

--- 1. Map Phase ---
(word, 1)
(word, 1)
...
--- 2. Shuffle & Sort ---
Grouped by word
--- 3. Reduce Phase ---
(word, total_count)
--- Final Word Counts ---

🟨 Python Code: mapReduce.py
Function	Explanation
mapper(document)	Tokenizes text, removes punctuation, yields (word,1).
reducer(key, values)	Sums values for each word and yields (word, total_count).
main()	Takes user input, simulates MapReduce flow with clear print stages.

✅ Output Example:

=== MapReduce Word Count ===
Enter text:
Data science is great
END
--- Map Phase ---
('data', 1)
('science', 1)
('is', 1)
('great', 1)
--- Shuffle & Sort ---
Grouped: ('data', [1])
...
--- Reduce Phase ---
('data', 1)
...
Final Word Count Results:
data 1
science 1
is 1
great 1

🏁 CONCLUSION

The Word Count MapReduce application efficiently counts word occurrences from large data using parallel computation.

It demonstrates distributed data handling, partitioning, and aggregation concepts.

This practical reinforces understanding of MapReduce’s power in Big Data and Distributed Systems.

📘 ORAL EXAM QUICK ANSWERS (Rapid-Fire)
Question	Answer
What is MapReduce?	A distributed data processing model that splits a task into Map and Reduce phases for parallel execution.
What does the Map function do?	Converts input data into key-value pairs (word, 1).
What does the Reduce function do?	Aggregates values for each key (sums counts).
What is the Shuffle phase?	Transfers, groups, and sorts intermediate data between Map and Reduce.
What is Input Splitting?	Dividing large input data into smaller chunks for each Mapper.
Why is MapReduce fault-tolerant?	Because failed tasks are automatically re-executed by the framework.
What is the output of Word Count?	(word, frequency) pairs.
Name the two main user-defined functions in MapReduce.	map() and reduce().



----------------------------------------------------------------------------------------------------------------------------------


ASSIGNMENT 7 :--


🎯 Title: Termination Detection in Distributed Systems (Dijkstra–Scholten Algorithm)
🧠 Theory Explanation (for Oral Exam)
1️⃣ Concept of Termination Detection

In a distributed system, many processes (or nodes) execute concurrently and communicate via messages.
The termination problem is:

How can the system determine that the entire distributed computation has finished,
i.e., no process is active and no messages are still in transit?

A system is terminated when:

All processes are passive, and

There are no messages in transit between processes.

Challenge:
In an asynchronous system, there’s no global clock, and messages can be delayed — so even if all processes seem passive, a delayed message might reactivate a process later. Hence, local observations are insufficient to detect global termination.

2️⃣ Why is this Problem Important?

Termination detection is vital in distributed applications such as:

Distributed garbage collection

Graph algorithms (like spanning tree or shortest path)

Iterative computation (e.g., PageRank or data aggregation)

Without proper termination detection, a system might:

Stop prematurely (false termination), or

Keep running forever waiting for work that’s already done.

3️⃣ The Dijkstra–Scholten Algorithm Overview

The Dijkstra–Scholten Algorithm (1980) provides a control-based solution for detecting termination in an asynchronous distributed system.

It works by:

Creating a logical spanning tree (rooted at the initiator process).

Tracking message dependencies and balances.

Using REPLY (control) messages to confirm when a process and all its descendants are passive.

This algorithm never uses a global clock or shared memory.
It relies solely on message passing and local bookkeeping.

4️⃣ Key Concepts
✅ A. Computation Messages

These are normal application messages that carry actual work (e.g., tasks, jobs).

Sending such a message implies the sender might expect further activity.

✅ B. Control (REPLY) Messages

These are special messages used for termination detection.

A process sends a REPLY to its parent when it becomes passive and all its work (and its children's work) is done.

✅ C. Computation Spanning Tree

When the computation starts, the initiator (root) is active.

Whenever a process P sends its first computation message to process Q:

Q becomes a child of P.

P becomes the parent of Q.

This parent-child relation forms a spanning tree.

This tree helps backtrack control messages (REPLY) upwards toward the root.

5️⃣ Local State Tracking

Each process maintains:

Component	Meaning
State	Active or Passive
Parent	The process from which it received the first message
Children list	All processes to which it sent computation messages
Deficit Counter	The number of outstanding (unreplied) messages sent to children
6️⃣ The Algorithm Steps (Detailed)
🧩 Step 1: Initiation

The root process (R) starts the computation and detection.

It is initially active and has no parent.

🧩 Step 2: Message Sending

When an active process P sends a computation message to process Q:

Q sets P as its parent (if Q had no parent already).

P increments its deficit counter (since it expects a reply later).

🧩 Step 3: Becoming Passive

A process becomes passive when:

It has no more work to do locally.

It waits for replies from its children (if any).

🧩 Step 4: Receiving Replies

When a child process finishes and sends a REPLY (SIGNAL) message to its parent:

The parent decrements its deficit counter.

Removes that child from its children list.

🧩 Step 5: Global Termination

The root process declares termination only when:

It is passive.

Its deficit counter = 0.

It has received REPLY from all children.

Only then do we know all processes are passive and no messages are in transit.

7️⃣ Advantages
Benefit	Description
Accuracy	Detects termination only when all nodes are done.
No false positives	Avoids premature termination even with asynchronous communication.
Scalable	Works for any number of nodes.
No global clock needed	Entirely message-based control.
8️⃣ Real-world Example (Simplified)

Let’s say we have 3 processes:

P₀ (root/initiator), P₁, and P₂.

🟩 Steps:

P₀ starts computation and sends a message to P₁ → P₁ sets P₀ as parent.

P₁ sends message to P₂ → P₂ sets P₁ as parent.

Once P₂ finishes work → sends REPLY to P₁.

P₁ receives reply, becomes passive, sends REPLY to P₀.

P₀ receives all replies → declares global termination.

⚙️ Algorithm Summary (for Oral Answer)

Algorithm: Dijkstra–Scholten Termination Detection

Root initiates the detection.

Each process keeps track of:

Active/passive state

Parent process

Message count (deficit)

When sending a computation message:

Increment counter.

Receiver sets sender as parent.

When becoming passive:

If counter = 0 → send REPLY to parent.

Root detects termination when:

Passive, counter = 0, and all replies received.

Step 3: Understand the Code Flow
Code Section	Explanation
Process extends Thread	Represents a process (node) in the distributed system.
messageQueue	Shared queue simulating message passing between processes.
Message class	Defines message structure: senderId, receiverId, type (COMPUTATION or SIGNAL).
sendMessages()	Simulates sending computation messages to random other processes.
processMessages()	Receives messages and acts based on type.
becomePassive()	When process finishes its work, it sends a SIGNAL to its parent.
terminated flag	Global flag that ends simulation when root detects termination.



----------------------------------------------------------------------------------------------------------------------------------


ASSIGNMENT 8 --



🧠 1. Theory Explanation (Oral Exam Ready Notes)
🔹 Title:

Distributed Name Resolution Service Implementation

Aim:

To develop a client-server application where a central Name Server maps logical service names (like database-service) to physical addresses (IP + Port).

Objectives:

Understand the need for naming services in distributed systems.

Implement a centralized Name Server using socket programming.

Design the communication protocol for service lookup requests and responses between client and server.

Related Theory:
1️⃣ What is Name Resolution?

In a distributed system:

Services run on different machines (nodes).

Each service is accessed through an IP address and port number.

Humans can’t easily remember IPs (like 142.250.183.206), so we use logical names (like www.google.com).

👉 Name Resolution = converting a name → IP address + Port.

This is exactly what DNS (Domain Name System) does on the Internet.

2️⃣ Why Name Server is Needed:

It acts as a central directory where all service names and their corresponding addresses are stored.

Clients don’t need to know IP addresses — only the Name Server address.

Enables loose coupling — services can move to a different host/IP without client-side code change.

3️⃣ Components:
A. Name Server (Server-Side)

Maintains a database/dictionary:

service_name → (IP, Port)


Waits for client queries (using sockets).

When a client sends a request with a service name:

Looks up its table.

Sends the resolved IP + port (if found) or a “not found” message.

B. Client (Client-Side)

Sends the service name query to the Name Server.

Receives a response:

If found → uses that IP:Port to connect to actual service.

If not found → shows error.

4️⃣ Communication Flow (Protocol):
Client → [Query: service_name] → Name Server
Name Server → [Response: IP:Port or NOT_FOUND] → Client

5️⃣ Internal Working Steps:

Server Initialization

Start a socket on a fixed port (e.g., 5000).

Maintain a mapping of names to IP addresses (hardcoded/static).

Client Request

Client connects to the server.

Sends a service name string (e.g., "www.google.com
").

Server Lookup

Reads the service name.

Searches the mapping (or uses InetAddress.getByName()).

Response

If found → sends resolved IP.

If not found → sends error message.

Client Action

Receives and displays the IP or error.

6️⃣ Example Scenario:
Client: "www.google.com"
↓
Name Server: Resolves → "142.250.183.206"
↓
Client: Displays → "Resolved IP Address: 142.250.183.206"

Algorithm (Simplified Version):

Server:

Start server socket at a known port.

Wait for client connection.

Read service name from client.

Try to resolve name (using DNS or map).

Send IP if found else send "Not Found".

Close client connection.

Client:

Connect to Name Server.

Take input (service name).

Send it to server.

Wait for response.

Display response (IP or error).

Close connection.

Scope for Improvement:

Client-Side Caching – store recent resolutions to reduce future queries.

Dynamic Registration – allow new services to register/unregister dynamically.

Redundancy – maintain backup name servers for reliability.

TTL (Time-to-Live) – specify how long a client can use a cached mapping before requerying.

Conclusion:

This practical demonstrates:

The core concept of distributed naming.

How to decouple names from IPs using a centralized directory.

Foundation for real-world systems like DNS.

Outcomes:

✅ You understood how name resolution works in distributed systems.
✅ You implemented client-server communication using sockets in Java.
✅ You can now extend this model to more complex systems (like DNS or service discovery in microservices).

🧩 2. Steps to Perform the Practical (Lab Procedure)
Step 1: Create two Java files

DynamicNameServer.java

DynamicNameClient.java

Step 2: Compile both files
javac DynamicNameServer.java
javac DynamicNameClient.java

Step 3: Run the Name Server

In Terminal 1 / Command Prompt 1:

java DynamicNameServer


✅ Output example:

Name Server started on port 5000

Step 4: Run the Client

In Terminal 2 / Command Prompt 2:

java DynamicNameClient

Step 5: Enter a Domain Name

When prompted:

Enter domain name (e.g., www.google.com): www.google.com


✅ Output:

Server Response: Resolved IP Address: 142.250.183.206


If an invalid domain is given:

Enter domain name (e.g., www.google.com): abc.unknown
Server Response: Error: Could not resolve domain abc.unknown

Step 6: Observe the Server Terminal
Client connected: /127.0.0.1
Resolved www.google.com -> 142.250.183.206

Step 7: Stop Execution

Press Ctrl + C on both terminals to stop the server and client.

✅ Practical Verification Checklist (for Viva):
Question	Expected Answer
What is the purpose of a Name Server?	To resolve logical service names into physical IP addresses and ports.
What is the difference between Name Server and DNS?	DNS is a large-scale real-world implementation of a name server on the internet.
Why use socket programming here?	It enables client-server communication over the network.
What happens if the domain is not found?	The server sends an error message to the client.
Can this be extended to dynamic registration?	Yes, by allowing services to register/unregister with the Name Server dynamically.



----------------------------------------------------------------------------------------------------------------------------------


ASSIGNMENT 9 :--


🧠 THEORY EXPLANATION (for oral exam)
1️⃣ What is a Web Service?

A Web Service is a software system designed to support interoperable machine-to-machine communication over a network.
It uses standard protocols like HTTP and XML/JSON for communication.

Think of it like:

A web service = a function or operation you can call over the internet using HTTP.

Key properties:

Discoverable (can be found by others via registry)

Uses standard XML/JSON for messaging

Accessible over internet/intranet

Platform independent (Java ↔ .NET ↔ Python)

Self-describing using XML or WSDL

2️⃣ Types of Web Services
🔹 SOAP (Simple Object Access Protocol)

Protocol-based (strict rules)

Uses XML for request & response.

Runs over HTTP (mostly via POST).

Needs a WSDL (Web Services Description Language) to describe operations.

Platform-independent → Java client ↔ .NET server possible.

Heavyweight but secure and standardized.

Example: Bank or Payment Gateway web services.

🔹 REST (Representational State Transfer)

Architectural style, not a protocol.

Uses standard HTTP methods:

GET – retrieve data

POST – create new resource

PUT – update resource

DELETE – remove resource

Uses JSON or XML.

Lightweight and faster than SOAP.

Widely used in modern apps (e.g., Google APIs, GitHub API).

3️⃣ Web Service Architecture

Three main components:

Component	Role
Service Provider	Hosts and provides the web service. (Server)
Service Requestor (Client)	Consumes or calls the web service.
Service Registry	Acts like a directory — lists available services (UDDI).
4️⃣ SOAP Web Service Components
Component	Description
SOAP	XML-based protocol for communication
WSDL	Describes the service: methods, parameters, return types
UDDI	Directory service for discovering web services
SOAP Request-Response Flow:

Client creates SOAP XML request.

Sends HTTP POST to server.

Web service decodes request, executes logic.

Sends SOAP XML response back.

Client reads response.

5️⃣ RESTful Web Services

REST treats every piece of data as a Resource, identified by a URI.
Uses stateless communication (no session stored on server).

Example:

GET http://localhost:8080/api/student/101


→ Returns JSON data for student 101.

Advantages:

Lightweight

Easy to test (even in browser/Postman)

Uses JSON instead of XML

Ideal for mobile & cloud applications

6️⃣ SOAP vs REST Comparison
Feature	SOAP	REST
Type	Protocol	Architectural style
Data Format	XML only	XML/JSON/Text
Speed	Slower	Faster
State	Stateful	Stateless
Security	Built-in WS-Security	Relies on HTTPS
Use Case	Enterprise / Banking	Web / Mobile Apps
7️⃣ Service-Oriented Architecture (SOA) Concept

Web Services are the building blocks of SOA — an approach where:

Each function (service) is independent.

Services communicate over network using standard protocols.

Promotes reusability and interoperability.

⚙️ STEPS TO PERFORM THE PRACTICAL

Aim: Create a web service and a distributed client application to consume it.

🧩 Example: Add Two Numbers (Simple SOAP Web Service)
🔸 Step 1: Create a New Project

Open NetBeans IDE

File → New Project → Java Web → Web Application

Name: AdditionService

Server: GlassFish Server

Java EE version: Java EE 7 or 8

🔸 Step 2: Create the Web Service

Right-click Source Packages → New → Web Service

Name: AdditionService

Package: com.example

Add code:

package com.example;

import javax.jws.WebMethod;
import javax.jws.WebService;

@WebService
public class AdditionService {

    @WebMethod
    public int addNumbers(int a, int b) {
        return a + b;
    }
}

🔸 Step 3: Deploy the Service

Right-click project → Run (or Deploy)

The WSDL will be generated at:

http://localhost:8080/AdditionService/AdditionService?wsdl

🔸 Step 4: Create Client Application

File → New Project → Java Application

Name: AdditionClient

Right-click on the client project → New → Web Service Client

Enter WSDL URL from above step → Finish.

Use code:

package com.client;

import com.example.AdditionService;
import com.example.AdditionService_Service;

public class AdditionClient {
    public static void main(String[] args) {
        AdditionService_Service service = new AdditionService_Service();
        AdditionService port = service.getAdditionServicePort();

        int result = port.addNumbers(10, 20);
        System.out.println("Sum = " + result);
    }
}

🔸 Step 5: Run Client

Output:

Sum = 30

✅ Outcome

You successfully created a SOAP-based web service.

You wrote a distributed Java client to consume it.

💡 Optional Extension (for bonus marks)

You can also implement the same logic using a RESTful web service using JAX-RS:

@Path("/add")
public class AddRestService {
    @GET
    @Produces(MediaType.TEXT_PLAIN)
    public String add(@QueryParam("a") int a, @QueryParam("b") int b) {
        return "Sum = " + (a + b);
    }
}


Access in browser:

http://localhost:8080/AddRestService/add?a=10&b=20

🎯 VIVA QUESTIONS (most asked)
Question	Expected Answer
What is a Web Service?	It’s a software component accessible over the internet using standard protocols like HTTP.
Difference between SOAP and REST?	SOAP is a protocol using XML; REST is an architectural style using HTTP methods and JSON/XML.
What is WSDL?	Web Services Description Language — XML file describing methods and parameters.
What is UDDI?	Universal Description Discovery and Integration — a registry for web services.
What are HTTP methods in REST?	GET, POST, PUT, DELETE.
Why REST is faster than SOAP?	REST uses lightweight data (JSON) and less overhead.
What tools are used here?	


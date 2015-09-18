Example for Websockets using Java SE
====================================

Instead of using Java EE and all the stuff it comes with, one might want a smaller implimentation of websockets, perhaps for a standalone program or an embedded application.  This example will show a minimum of coding to get a jetty server running, serving up websockets, and and a client to interact.  The client will be a web browser, so the code is just some html and javascript.  The server is a few POJOs that will launch Jetty and configure a service for it.

This code is based on a WebSocket tutuorial found here:
http://jansipke.nl/websocket-tutorial-with-java-server-jetty-and-javascript-client/

Some people, myself included, believe annotations are black magic that only hide what's really going on.  I want a clear picture of exactly what's happening and how it's happening.  However, Jetty documentation isn't to the point were this is a clear cut issue regading websockets.  It makes a number of references to factories, servlets, and connection handlers but doesn't really connect all the dots.  Even if all the dots were connected, there is a lot of ancillary background things that annotations provide that would need to be done manually, and seem to distract from the point of just using a websocket.  While this presents a possible issue of, "Well I want to use websockets in my aplpication but I can't get annotations to work, how in the world do I do this manually?" The learning of this one aspect is easier with annotations. 

This was all done on a linux system.  Pulling jetty as well as setting classpaths on windows or mac should follow similar steps.  I believe in you!

External Dependency
===================
Jetty: jetty v9.3.3

Building
========
Could all this be put into a script? Yes, but then what would you learn?

1. grab jetty

        wget http://central.maven.org/maven2/org/eclipse/jetty/aggregate/jetty-all/9.3.3.v20150827/jetty-all-9.3.3.v20150827-uber.jar -O jetty-all-uber.jar

2. add jetty to the classpath

        . ./setupClasspath

3. compile the server

        cd server
        javac Main.java
        javac MyWebSocketHandler.java

Running
=======
1. run the server
    
        cd server
        java Main &

2. launch a browser looking at the index.html from the client folder
    
        cd ../client
        iceweasel index.html


So What?
========
What's happening here?  The Main class launches jetty with a websocket handler.  You can see status messages from Jetty

    2015-09-18 17:28:49.548:INFO:oejs.Server:main: jetty-9.3.z-SNAPSHOT
    2015-09-18 17:28:49.579:INFO:oejs.ServerConnector:main: Started ServerConnector@484b61fc{HTTP/1.1,[http/1.1]}{0.0.0.0:9000}
    2015-09-18 17:28:49.579:INFO:oejs.Server:main: Started @223ms

This tells you that jetty is up and running at port 9000.

When you launch the browser looking at the index.html, the javascript code it contains will attempt to connect to localhost port 9000.
When it connects the `onopen` event handler gets called and there is an alert in the browser.  At this point the javascript sends a message to the server, which you can see printed out on the terminal.  The first line is the prinout from the websocket handler to indicate a connection, the second is the message sent from the javascript!  Cool right?

    Connect: /127.0.0.1
    Message: Hello Server

You'll see in the `MyWebSocketHandler` class that the Connect message is generated in the `onConnect` handler, while the `onMessage` handler is printing out the second line.

Of course websockets are about bidirectional communication, so the websocket handler, when connected sends a message to the client when it connects with this bit of code:
    session.getRemote().sendString("Hello Webbrowser");

The java script recieving this message through the `onmessage` event handler then pops up another alert indicating what it had recieved.

If you wait long enough on the browser, you will eventually see the alert that says Closed that is generated from the `onclose` event handler.  Websockets eat up a precious resource of a port on a server, so by default they timeout after some time and close automatically.  Of course it's best practice to close them as soon as you are done with them, by using the `close`method.  The other way the socket could get close is by a loss of communication to the server, like if you kill the server before the web browser.

What do I do now?
=================
This is just the tip of the websocket iceberg.  Arrays can also be sent and recieved as well as Blobs (a File is a deriviteve of Blob!).

Maybe you could try creating a div tag in the index.html, and motify the javascript to write the hello message to the div tag instead of using an alert?
Maybe try using a form to gather two numbers, send them to the server, and have the server add them and return them?
Maybe try passing an image file from the server, recieving it as a blob and displaying it on the web page? hint: http://stackoverflow.com/questions/11089732/display-image-from-blob-using-javascript-and-websockets

References
==========
Websocket javascript API: https://developer.mozilla.org/en-US/docs/Web/API/WebSocket







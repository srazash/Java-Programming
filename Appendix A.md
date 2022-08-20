# Appendix A: Working with Java in the shell

![Computer Cat](./img/computercat.gif)

There are a handful of useful CLI utilities to be aware of when working with Java in the shell.

## javac: The Java compiler

`javac` the Java compiler, this is invoked when you want to compile your .java source code files into Java binaries (.class files).

Here we have a very simple project:

```
../java-project/
└── Hello.java

0 directories, 1 file
```

There's one a sinfle .java source file, `Hello.java`, which contains:

```java
public class Hello {
	public static void main(String[] args) {
		System.out.println("Hi!");
	}
}
```

To compile this we would run:

```
javac Hello.java
```

As with the majority of CLI tools and programs, so long as there are no errors, our .java file will compile with no output whatsoever (no news is good news), we can confirm our source code has compiled by checking for an outputted .class file:

```
../java-project/
├── Hello.class
└── Hello.java

0 directories, 2 files
```

Now we have a valid .class file in the form of `Hello.class`, this is Java's equivalent to a binary file that would be produced by the C/C++ compiler containing machine code that is run by the CPU, in this case our .class file contains **bytecode** that is run by the Java Virtual Machine (JVM) in the `java` tool.

## java: Invoking the The Java Virtual Machine

⚠️ Unlike the `javac` command which requires full name of the .java file we want to compile, including the extension, the `java` command wants the name of the class contained in the .class file. This means a couple of things:

1. The class we defined in our .java source code file will produce a .class file of the same name.
2. To run our compiled .class file we only provide the **class name** not the full file name.
3. Additionally - to be able to run, the .class file's name **must** match the name of the class in the .java source code file when it was compiled.

This does make sense despite being somewhat counter intuative, if we compiled a C/C++ programe, we could call the binary anything we wanted to, but Java has a very specific structure that must be followed when working with compiled .class files. This restriction becomes more relaxed when we package our compiled .class files into a .jar file, which will be touched on later.

Returning to our Java project, we now have a compiled .class file:

```
../java-project/
├── Hello.class
└── Hello.java

0 directories, 2 files
```

This means we have valid Java bytecode that can be run in the JVM, keeping what we discussed in the prior paragraphs in mind we would do so by running:

```
java Hello
```

Which will output:

```
Hi!
```

If we did try to pass java the .class file:

```
java Hello.class
```

We would instead see:

```
Error: Could not find or load main class Hello.class
Caused by: java.lang.ClassNotFoundException: Hello.class
```

This can be a bit confusing if it's the first time you see it. You have compiled you .java code, right? And there were no errors with that, so what's the issue?

Take a closer look at the wording of the error: **Could not find or load main class Hello.class**

It's not looking for a .class file, it's looking for the **main class**, that is the Java class containing the main method. It's referencing the class in our source code! Which makes much more sense when we think of it that way. This is one a Java's excentricities, but it's one that's easy enough to work with when we know what is actually going on.

So what if we do want to rename our compiled code? Put a version number on it? Maybe we want to share this code as HelloV1, in anticipation of the possiblitities of a V2 further down the line. there's also the question of what we do with a project tha has more than one Java class, because that would prodcue multiple .class files, maybe ecven dozens or hundred, depending on the projects complexity.

Java let's us bundle our compiled code into a JAR file (.jar) using the `jar` command.

## jar: The Java Archive Builder

JAR files let us bundle Java projects into a convenient package that is easy to share, these are made up of .class files (compiled Java code) and metadata that the JVM reads so it knows how to run the contained classes bundled together using the `jar` command.

Here we'll use our very simple Java project and create a JAR to share. Our project looks like this:

```
../java-project/
├── Hello.class
└── Hello.java

0 directories, 2 files
```

We have a compiled .class file that does work, so how do we bundle this into a JAR file?

Firstly, we need to create some useful metadata, in a project this simple we only need to worry about telling Java which is our main class, to do this we need a META-INF directory to contain our metadata:

```
mkdir META-INF
```

And inside the META-INF directory we need a MANIFEST.MF file which needs to define our main class:

```
Main-Class: Hello
```

Now we have everything we need to apckage up Hello.class into a JAR file that will work:

```
../java-project/
├── Hello.class
├── Hello.java
└── META-INF
    └── MANIFEST.MF

1 directory, 3 files
```

To build the JAR file we invoke:

```
jar -cvfm MyHelloApp.jar META-INF/MANIFEST.MF Hello.class
```

There's a few things going on here:

1. We're invoking the `jar` command.
2. We giving it the options `-cvfm` --> compress, verbose, output file, metadata (manifest)
3. We provide it an output file name
4. We provide it the location of our metadata/manifest file
5. We procide it our class file

The output of this command is:

```
added manifest
adding: Hello.class(in = 407) (out= 279)(deflated 31%)
```

So what happened?

We asked `jar` to create us a compressed JAR file (a JAR file is basically a ZIP file), to provide us verbose output (as shown above), to bundle in the metadata (manifest) we provided and to bundle in teh class file we defined. The result is a JAR file we can run with the `java` command:

```
../java-project/
├── Hello.class
├── Hello.java
├── META-INF
│   └── MANIFEST.MF
└── MyHelloApp.jar

1 directory, 4 files
```

We now have **MyHelloApp.jar**, first thing to note is the JAR files **doesn't** match our class name, like we needed when running the .class file directly. Tha's because our metadata/manifest defines that for us. A second thing to note is that this may seem like a lot of work just for a single .class, but if our project had many .class files, this would make things mcuh easier when we wanted to share our program to other computers or people.

To run a JAR file we do need tell the JAVA that is what we are using:

```
java -jar MyHelloApp.jar
```

Again, we've invoked the `java` command, but this time we give it an option, `-jar` whcih simply informs the JVM that we're giving it a bundled JAR file instead of a class. After this, we just point it to a JAR file and let it do it's thing.

Running this will output:

```
Hi!
```

Which means it has found our Hello class in the JAR and run it!

## Final thoughts

It's worth keeping in mind that we've barely scratched the surface of each of the commands we've worked with, it'd be worthwhile running each one with the `--help` option to see what they can do, as well as look up the official documentation for each:

- [javac](https://docs.oracle.com/en/java/javase/18/docs/specs/man/javac.html)
- [java](https://docs.oracle.com/en/java/javase/18/docs/specs/man/java.html)
- [jar](https://docs.oracle.com/en/java/javase/18/docs/specs/man/jar.html)
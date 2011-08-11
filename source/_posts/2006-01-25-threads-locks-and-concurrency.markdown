---
layout: post
title: "Threads, locks and concurrency"
date: 2006-01-25 22:23:56
comments: true
categories:
---

Just working through An Introduction to [Programming with C# Threads](http://research.microsoft.com/~birrell/papers/ThreadsCSharp.pdf) by Andrew D. Birrell. I'm finding the best way to understand all of this is to write code that breaks and then fix it. Not quite sure how to use unit tests with threading problems yet, so I'll just show the before and after code.

If you have more than one thread running and they have access to the same class, then it is quite possible that eventually both threads will change a field or call a method at the same time. Depending on the field or method, this can cause unexpected errors. This code demonstrates the problem:

	using System;
	using System.Collections.Generic;
	using System.Text;
	using System.Threading;
	using System.Diagnostics;
	namespace Threads
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            Thread inc1 = new Thread(new ThreadStart(Incrementer.loop));
	            Thread inc2 = new Thread(new ThreadStart(Incrementer.loop)); 
	            inc1.Start();
	            inc2.Start();
	        }
	    }   
	    class Incrementer
	    {
	        private static int _firstvalue = 0;
	        private static int _secondvalue = 0;
	        public static void add()
	        {
	            _firstvalue++;
	            _secondvalue++;
	            Debug.Assert(_firstvalue == _secondvalue, "Values are not the same");
	        }
	        public static void loop()
	        {
	            while (true)
	            {
	                Incrementer.add();
	            }
	        }
	    }   
	}

At some point the two values will differ and the assertion will be incorrect. Maybe this doesn't matter in the context of this particular class, but if it does, then weirdness could ensue!

The simple solution is to use a lock. The lock statement needs an object. Because the fields in question are static the lock is best placed on a Type object reflecting the type of the class in question.

	private static int _firstvalue = 0; //protected by typeof(Incrementer)
	private static int _secondvalue = 0; //protected by typeof(Incrementer)
	public static void add()
	{
	    lock (typeof(Incrementer))
	    {
	        _firstvalue++;
	        _secondvalue++;
	        Debug.Assert(_firstvalue == _secondvalue, "Values are not the same");
	    }
	}

Now any thread calling the add method of the Incrementer class must wait for any other thread to finish its work in the lock block. This is known as mutual exlusion. Andrew Birrell recommends specifying how each field is locked with a comment next to its definition.

All of this seems very relevant today with the reaching of practical limits of processor cooling and the 4GHz speed barrier that doesn't look libe being overcome anytime soon. The practical result is that Intel, Motorola, AMD and co. are releasing multicore chips which is bringing concurrency to the forefront of programming design. Before, applications could depend on compiler optimisations and increasing processor speeds for their performance gains. Now, programmers will have to start looking at building threading into all types of application. Microsoft is pre-empting this big change with their [Windows Workflow Foundation](http://msdn.microsoft.com/en-gb/magazine/cc163661.aspx) which has great scope to make concurrent programming more simply available.

This [article](http://www.gotw.ca/publications/concurrency-ddj.htm) gives a nice insight to what the future trends might be.
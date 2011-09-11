---
layout: post
title: "The Singleton"
date: 2006-01-27 16:47:13
comments: true
categories: "Code"
---

I recently had a phone interview for a developer job with an interesting sounding company working on a desktop application with some venture money in Clerkenwell. It was my first interview for 4 years and I fluffed it up. The difficulty was describing code verbally rather than through a computer screen, or more specifically an Integrated Development Environment. The challenge that I messed up was to describe the mechanics and problems associated with the simplest of the ["Gang of Four"](http://c2.com/cgi/wiki?GangOfFour) Design Patterns, the Singleton.

To rectify this inability to articulate a simple programming paradigm, I'm going to practice here!

The Singleton is an object that, once created, can be guaranteed to be unique. Only one instance of the class can exist and a factory method deals with either the instantiation of the object or referral to the existing instance. The constructor is made private so that no clients can cheekily break the Singleton by building a new instance themselves. The Singleton is useful when it would be wasteful to have lots of references to hungry resources. Database connections is a good example. It also solves the Highlander problem: "There can be only one!". For instance, there can only be one football in a soccer game.

Here is the bog standard Singleton pattern in all it's glory: 

{% codeblock  %}
using System;
using System.Collections.Generic;
using System.Text;
using NUnit.Framework;

namespace Singleton
{
    class Program
    {
        static void Main() { }
    }
    
    class Singleton
    {
        private static Singleton _instance = null;
        private int _value;
        public static Singleton Create()
        {
            if (_instance == null)
            {
                _instance = new Singleton();
            }
            return _instance;
        }
        private Singleton() { }
        public int Value
        {
            get { return this._value; }
            set { this._value = value; }
        }
    }
    [TestFixture]
    public class SingletonTests
    {
        [Test]
        public void TestSingletonUniqueness()
        {
            Singleton s1 = Singleton.Create();
            Singleton s2 = Singleton.Create();
            s1.Value = 12;
            Assert.AreEqual(s1.Value, s2.Value);
        }
        [Test]
        public void TestSingletonNoPublicConstructor()
        {
            Type singleton = typeof(Singleton);
            ConstructorInfo[] constructors = singleton.GetConstructors();
            Assert.AreEqual(0, constructors.Length);
        }
    }
}
{% endcodeblock %}

The initialisation of _instance and the factory method could be handled this way:

    class Singleton
    {
        private static readonly Singleton _instance = new Singleton();
        private int _value;
        public static Singleton Create()
        {
            return _instance;
        }
    }
    
This is slightly more elegant. The instance is only created when referenced by the Create method and so demonstrates lazy instantiation and therefore memory savings. This second example is also safe in a multi-threaded environment. The first example could allow two or more threads to call the Create method at the same time and then there could be more than one instance of the Singleton, which would be incorrect! This can be gotten around by having a lock object to check against. See the [previous concurrency post](/2006/threads-locks-and-concurrency/).

A much better discussion of these issues can be found on [MSDN](http://msdn.microsoft.com/en-us/library/ms998558.aspx) and [here](http://www.yoda.arachsys.com/csharp/singleton.html).

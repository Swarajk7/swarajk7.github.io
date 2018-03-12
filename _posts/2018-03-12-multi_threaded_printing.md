---
layout: post
title: "Multithreaded solution for printing sequence 010203040506 (Careercup)"
author: "Swaraj"
---

### Print series 010203040506 using multiple threads.
1st thread will print only 0, 2nd thread will print only even numbers and 3rd thread print only odd numbers.
[CareerCup Link](https://www.careercup.com/question?id=5082791237648384)

In this blog, I will introduce to thread, mutex and condition_variable headers in C++ 11 by walking you through a code. 
Some familiarity with threads, mutex is assumed to understand the solution. Objective is to understand how to use these basic building blocks to design a solution. Take a loo at how condition variables and how cv.notify_all(), cv.wait(lock, predicate) works before proceeding.

<b> Let's understand the Headers</b>
        

        #include<iostream> 
        
        #include<thread> 
        //After C++ 11, we don't need to create threads with pthread POSIX, 
        //we can use thread class to create and deal with threads
        
        #include<mutex> 
        //mutex - mutual exclusion objects can be accessed by one thread at a time.
        
        #include<condition_variable>
        /* this is a nice construct that we can use to signal other threads 
         * based on a given condition.
         * it removes the need of polling based check using while loop.
         * go through link below to understand conditional variable
         * as we see , It reduces the CPU usage and hence a better alternative. */

[Condition-Variable](https://goo.gl/W3VWYQ) 

<b> Let's design the functions</b> <br>
* We will design 3 functions. print0, printOdd and printEven.
* Each will deal with printing 0, even and odd respectively.
* we have to order these events. 0 - odd - 0 - even - 0 - odd - etc
* so we need a signal mechansim to signal between threads. 
* So I will use a mutex and a condition variable.
* I will use 2 variables : zero (which singals should I print zero now) <br> and val which signals what is the next value to print.


Logic is at one point of time, only one of the three conditions will be satisfied.

if zero is true => it will trigger zero thread. <br>
zero is false and val is odd => it will trigger odd thread. <br>
zero is false and val is even => it will trigger even thread. <br>

Each thread will print something and change state of zero and val to trigger other threads. <br>


Rest of the code. Please follow the comments.

        using namespace std;
        mutex m;
        //object of mutex class
        condition_variable cv;
        //cv will be used to notify other threads.

        int val = 0;
        bool zero = true;

        // condition which should satisfy for printing next odd value.
        // odd thread will wait for this condition and mutex m.
        bool isOdd() {
            return val%2==1 && !zero;
        }

        // condition which should satisfy for printing next even value.
        // even thread will wait for this condition and mutex m.
        bool isEven() {
            return val%2==0 && !zero;
        }

        // condition which should satisfy for printing zero value.
        // zero thread will wait for this condition and mutex m.
        bool isZero() {
            return zero;
        }


        // print0 function will be called from thread zero.
        void print0() {
            // we need to print till 6.
            while(val<6) {
                // unique_lock is a way to lock mutex.
                // It locks the mutex in constructor
                // And unlocks in destructor, so it is a safe implementations. 
                // It will never face the issue of resource leak.
                unique_lock<mutex> lck(m);

                // Now here we take advantage of condition variable.
                // cv will wait till mutex is free and isZero condition is satisfied.
                // Once wait is finished, this thread will get the lock.
                cv.wait(lck, isZero);

                // print 0 and increment count.
                cout<<0;
                val += 1;

                // set zero to false. 
                // It means next thread should be either even or odd based on val.
                zero = false;

                //unlock the lck. and notify all other process that mutex is unlocked.
                lck.unlock();
                cv.notify_all();
            }
        }

        // print Even numbers called by even thread.
        void printEven() {
            // loop till val is less than 6.
            while(val<6) {
                unique_lock<mutex> lck(m);

                // Even will wait till isEven condition is satisfied.
                // and triggered by zero thread using notify_all()
                cv.wait(lck, isEven);

                //print val.
                cout<<val;

                // set zero to true, so that 0 thread will be triggered again.
                zero = true;

                lck.unlock();
                cv.notify_all();
            }
        }

        void printOdd() {
            // loop till val is less than 5.
            // Follow Up : Analyze what happens if you loop till 6?

            while(val<5) {
                unique_lock<mutex> lck(m);

                // if zero is true and val is odd.
                // this will be satisfied when notify_all() is called by zero thred.
                cv.wait(lck, isOdd);

                cout<<val;

                // set zero to true, so that 0 thread will be triggered again.
                zero = true;

                lck.unlock();
                cv.notify_all();
            }
        }

        int main() {
            // create the threads.
            thread t1(print0);
            thread t2(printOdd);
            thread t3(printEven);

            // wait for threads to join here.
            t1.join();
            t2.join();
            t3.join();
        }

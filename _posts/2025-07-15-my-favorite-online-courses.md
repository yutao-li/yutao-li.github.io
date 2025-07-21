---
title: My Favorite Online Courses
date: 2025-07-15 23:09 -0700
description: Review my completed online courses and recommend the greatest ones
math: true
---

Looking back, throughout my undergraduate and graduate years, I have completed dozens of online courses from various platforms, including Edx, Udemy, Udacity, Coursera, Stanford Lagunita (now deprecated), and some from China. Most of the courses are CS-related. While many were good, a select few were so exceptional that they became ingrained in my mind. Therefore, in this post, I want to introduce and promote these remarkable courses.

_(Disclaimer: I'm deliberately excluding AI/ML courses simply because I am no longer interested in that area, and they are popular enough.)_

A personal take: What makes a course great, in my view, is an equal emphasis on lectures and assignments. Many online courses have excellent lectures but lack well-crafted assignments that hone hands-on skills or challenge students to think deeper. A course focused on theory without solid or challenging assignments won't substantially improve one's practical skills in that area.

This brings me to my first recommendation: [Algorithms from Princeton University on Coursera](https://www.coursera.org/learn/algorithms-part1).

### Algorithms from Princeton University

#### World-Class Lectures

As the title suggests, the course teaches fundamental algorithms and data structures. It’s taught by the renowned Professor Robert Sedgewick. He teaches the class in such an intelligible and concise way that I binge-watched the whole series without even realizing it. The lectures are enhanced by brilliant animations of algorithms and data structures, which transform complex ideas into a pleasant and intuitive viewing experience. Even now, I vividly remember the animation of the red-black tree, which, combined with Robert’s apt illustration, made the entire concept click.

#### Assignments That Make You Think

While the lectures are top-notch, the assignments are where the course truly shines. They are not your typical "implement this algorithm" tasks. Each assignment is a self-contained project with successive steps of incremental difficulty designed to push you to think deeply about efficiency, particularly time and space complexity. Merely getting the code to work is not enough; passing all the tests requires a truly optimized solution, and the sense of accomplishment is immense.

One of my favorite assignments was the **8-Puzzle problem**, which you can find here: [8-Puzzle Specification](https://coursera.cs.princeton.edu/algs4/assignments/8puzzle/specification.php).

#### The 8-Puzzle Challenge and the Memory Explosion

The 8-puzzle is a classic sliding puzzle on a 3x3 grid. The goal is to find the shortest sequence of moves to rearrange scrambled tiles into a sorted order. A standard approach is the A* search algorithm, which intelligently explores possible board states. However, this leads to a critical challenge: as the search explores deeper, the number of board states stored in memory can grow exponentially.

The course materials highlight this very issue. A naive implementation using a standard `int[][]` array in Java can be surprisingly memory-hungry. For an `n`x`n` grid, the memory footprint is about $24 + 32n + 4n^2$ bytes. Storing thousands of these boards can quickly exhaust available memory.

#### A Memory-Efficient Solution

This memory constraint is what makes the assignment so thought-provoking. It forces you to move beyond the basic algorithm and devise a more efficient data representation. The problem specification itself hints at this: `in principle, you need only about lg((n^2)!) bits to represent it; when n equals 3, this is only 19 bits`.

Inspired by this, I designed my own highly memory-efficient `Board` class, which you can find in my GitHub repository: **[8puzzle/Board.java](https://github.com/yutao-li/8puzzle/blob/master/8puzzle/Board.java)**.

The core idea is to treat each board state as a unique permutation of the numbers 0 through 8. Since there are $9!$ possible permutations, we can assign a unique integer index to each one. This index, which requires only 19 bits, is all we need to store. My implementation achieves this by converting the permutation's rank in lexicographical order into a compact `byte[]` array. The methods `permuToBits` and `bitsToPermu` handle the conversion to and from this compressed format.

This is the kind of deep, practical learning that makes Princeton's Algorithms course so valuable. The assignments don't just ask you to regurgitate lecture content; they challenge you to confront and solve real-world engineering problems. If you're looking for a course that will genuinely sharpen your problem-solving skills, I cannot recommend this one enough.

### Algorithms Specialization from Stanford University

Another course that has earned a permanent spot on my recommendation list is the **[Algorithms Specialization on Coursera](https://www.coursera.org/specializations/algorithms)**, taught by Professor Tim Roughgarden from Stanford University. I consider it a perfect complement to Princeton's offering. While the Princeton course excels in building deep implementation skills, the Stanford specialization focuses more on the theoretical underpinnings, emphasizing the big picture and conceptual understanding over low-level implementation details. The two series also cover largely different sets of algorithms and data structures, giving you a more complete and well-rounded perspective on the field.

The specialization is a four-course series that delves into advanced topics like the Bellman-Ford and Floyd-Warshall algorithms, NP-completeness, and strategies for handling computationally intractable problems.

#### An Interesting Theoretical Puzzle: 2-SAT

While the programming assignments are generally not as extensive as those in the Princeton course, some are still quite interesting. A standout example for me was the assignment on the 2-satisfiability (2-SAT) problem, which is part of the final course, "[Shortest Paths Revisited, NP-Complete Problems and What To Do About Them](https://www.coursera.org/learn/algorithms-npcomplete/assignment-submission/WuAkl/programming-assignment-4/view-submission)".

The assignment challenges you to determine if several large 2-SAT instances are satisfiable. The standard textbook approach involves building an "implication graph" and finding its strongly connected components (SCCs)—if any variable and its negation land in the same SCC, the formula is unsatisfiable.

However, what makes this assignment truly engaging, apart from the theory, is the opportunity to move beyond a basic implementation and create a highly optimized one. The problem instances can be quite large, so a solution must be efficient in both time and memory.

Inspired by this challenge, I developed my own efficient implementation, which you can find here: **[2sat.py](https://github.com/yutao-li/efficient-2SAT-solution/blob/main/2sat.py)**. My solution is based on Tarjan's algorithm for finding SCCs, but with a key memory optimization. A textbook implementation of Tarjan's algorithm typically uses separate arrays for discovery times and "low-link" values. My version cleverly avoids the extra array for low-link values. Instead, it computes the low-link value on-the-fly within the recursive calls of the depth-first search and stores it back into the discovery time array itself. This small but significant change reduces the memory footprint, a practical concern when dealing with graphs that can have hundreds of thousands of vertices, as is the case in this assignment.

It's this kind of subtle, practical optimization that reinforces theoretical concepts in a meaningful way, bridging the gap between an algorithm on a whiteboard and a robust, working program.

For anyone looking to build a robust and comprehensive understanding of algorithms, I highly recommend taking both the Princeton and Stanford specializations. Together, they provide a powerful combination of hands-on implementation skills and deep theoretical knowledge.

### Programming Languages from the University of Washington

Another standout course series that fundamentally shaped my understanding of coding is **[Programming Languages](https://www.coursera.org/learn/programming-languages/home/info)** by Dan Grossman at the University of Washington. This is not a single course, but a comprehensive three-part series (Parts A, B, and C) that takes you on a deep dive into the principles of programming language design and implementation, with a strong emphasis on functional programming.

#### A Fresh Perspective on Programming

The true goal of this series isn't to make you an expert in any single language. Instead, it uses ML, Racket, and Ruby as vehicles to teach you how languages "fit together". This approach provides a framework that helps you write more correct and elegant programs, regardless of the language you are using. The focus on functional programming is particularly enlightening, revealing how to build software that is robust, reusable, and composable.

The series is broken into three distinct parts, which is a welcome design choice given the challenging material.

* **Part A:** Focuses on core concepts in functional programming using ML. You'll learn about features like datatype bindings, pattern matching, tail recursion, and higher-order functions.
* **Part B:** Shifts to Racket, a dialect of Lisp, to explore concepts like delayed evaluation, streams, and memoization. It also covers the fascinating topic of implementing interpreters and function closures.
* **Part C:** Uses Ruby to explore object-oriented programming, comparing and contrasting it with the functional paradigm. It masterfully connects different programming paradigms, covering topics like dynamic dispatch, mixins, and subtyping.

#### Challenging Assignments That Build Mastery

What truly sets this series apart, in my opinion, is the quality of its assignments. They are not trivial exercises; they are meticulously designed to compel you to develop strong logical reasoning and hone your programming skills. The course also features a set of challenging problems that push you to think even more deeply about the material. I've compiled my solutions to some of these challenges in my GitHub repository, which you can find here: **[programming-languages-coursera](https://github.com/yutao-li/programming-languages-coursera)**.

Here are a few examples of the kinds of stimulating problems you'll encounter:

* **Generating All Divisors from Prime Factors:** This challenge requires writing a function, `all_products`, that takes a list of prime factors for a number (e.g., `[(2,2),(5,1)]` for 20) and generates a list of all of that number's divisors (e.g., `[1,2,4,5,10,20]`). The twist is that the resulting list must be generated in increasing order directly by your recursive process, without sorting it afterwards. This problem demands careful recursive thinking and list manipulation. My implementation is in **[challenge1.sml](https://github.com/yutao-li/programming-languages-coursera/blob/master/challenge1.sml)**.

* **Advanced Pattern-Matching Type Checking:** In another challenge, you have to write a function that type-checks a list of patterns in a custom language. This requires you to implement a type-inference-like system that can determine the most lenient, yet valid, type that all patterns in a list can share. You can see my take on this in **[challenge2.sml](https://github.com/yutao-li/programming-languages-coursera/blob/master/challenge2.sml)**.

* **Implementing `foldl` with `foldr`:** A classic functional programming puzzle that requires a clever use of higher-order functions. You are asked to implement the `foldl` function by using `foldr`. This seemingly simple task forces you to deeply understand how these fundamental operations work. My solution can be found in **[challenge3.sml](https://github.com/yutao-li/programming-languages-coursera/blob/master/challenge3.sml)**.

* **Language Simplification with Lambda Calculus:** A fascinating problem in Part B asks you to refactor a mini-language called MUPL by removing language features and replacing them with function-based equivalents, drawing on ideas from lambda calculus. For example, you replace `let` expressions and even data structures like pairs with closures and function calls. This exercise brilliantly demonstrates the expressive power of functions. My solution is in **[challenge4.rkt](https://github.com/yutao-li/programming-languages-coursera/blob/master/challenge4.rkt)**.

These assignments are where the theoretical concepts from the lectures are forged into practical, durable skills. They don't just test your knowledge; they transform the way you think about programming.

This series is a masterclass in programming language theory and practice. The way it systematically builds concepts upon one another across different languages provides a level of insight that is hard to find elsewhere. I highly recommend it to any developer looking to deepen their fundamental understanding of programming.

_(Note: Courses recommend next won’t have coding assignments, but they are good in other aspects.)_

### Introduction to Operating Systems from Georgia Tech (on Udacity)

The next course I want to recommend is **[Introduction to Operating Systems](https://www.udacity.com/course/computer-networks--ud923)**. This course is the publicly available version of Georgia Tech's graduate-level course, **[CS 6200](https://omscs.gatech.edu/cs-6200-introduction-operating-systems)**.

While the courses I've praised so far are notable for their hands-on coding assignments, this one's strength lies elsewhere. It primarily uses quizzes and problem sets for evaluation, with no major coding projects. Because of this, I found it to be an excellent theoretical complement to a more project-intensive OS course.

I've taken a few operating systems courses, like Berkeley's CS 162, and this is the best I have found for mastering the theory. The explanations are concise and penetrative, and the lectures manage to cover nearly every essential topic in a modern OS in a visually engaging and intelligible way. Notably, the use of a digital whiteboard in the videos really helps with the illustrations. The course teaches the basic operating system abstractions and their implementations, with a focus on concurrency, resource management, and distributed services. The topics range from processes, threads, scheduling, and memory management to distributed file systems and datacenter technologies.

If you are looking for a course that provides a deep and clear conceptual understanding of how operating systems work, I cannot recommend this one highly enough.

### Databases from Stanford University

Next, I want to talk about databases. My first encounter with the subject was through Stanford's "Databases" course, which I originally took on their Lagunita platform. That platform has since been retired, and the course material has been moved to edX. What was once a single, massive course is now offered as a series of five self-paced courses, with **[Databases: Relational Databases and SQL](https://www.edx.org/learn/relational-databases/stanford-university-databases-relational-databases-and-sql)** being the most popular starting point.

This course is exceptional for its theory teaching—it's concise, comprehensive, and highly intelligible. I remember it having quite a lot of quizzes and interactive exercises, which really help solidify the concepts. However, it lacks a significant coding project.

For those interested in diving deeper and gaining hands-on implementation skills, I recommend complementing the Stanford course with **CMU's 15-445: Database Systems**. This is a project-heavy course where you get to build key components of a real, disk-oriented relational database called Bustub. It's a challenging but incredibly rewarding experience that will give you a deep, practical understanding of how database systems work from the inside out.

### The Complete Networking Fundamentals Course on Udemy

Finally, I want to recommend **[The Complete Networking Fundamentals Course. Your CCNA start](https://www.udemy.com/course/complete-networking-fundamentals-course-ccna-start/?couponCode=MT150725G1)** on Udemy. This course is a departure from many traditional networking courses taught in schools. Instead of focusing purely on theory, it emphasizes industrial practice, covering the topics in the new Cisco CCNA 200-301 exam.

A key feature of this course is its focus on using software widely employed in the industry, such as Graphical Network Simulator-3 (GNS3), to build networks in a simulated environment. GNS3 is a powerful network software emulator that allows the combination of virtual and real devices to simulate complex networks and is used by large companies like Exxon, Walmart, and AT&T. Using these tools helps in illustrating various network topologies.

This hands-on approach is incredibly effective for understanding how networks are connected and organized, and how traffic flows. You will gain a lot of practical experience tinkering with these software tools throughout the course. For example, the course provides access to online simulations where you can practice on simulated Cisco routers and switches and learn how to configure devices using commands like `enable`, `configure terminal`, and `router ospf`.

For anyone who has studied networking from a more theoretical perspective in a university setting, this course is an excellent complement. It provides invaluable, hands-on experience that bridges the gap between abstract concepts and real-world network engineering.

## Conclusion

That’s all I have for now. Each of these courses stands out for its powerful combination of insightful theory and meticulously designed assignments that forge real, practical skills. It is this focus on hands-on practice that transforms a good course into a great one. I hope this list helps you find a learning experience that you find equally rewarding.

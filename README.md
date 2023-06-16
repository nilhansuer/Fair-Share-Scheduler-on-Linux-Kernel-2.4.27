# Fair-Share-Scheduler-on-Linux-Kernel-2.4.27

# 1. INTRODUCTION 
In operating systems, a scheduler is a vital component that oversees the execution of 
processes or threads on a computer system's CPU (Central Processing Unit). Its main objective 
is to distribute the CPU's time among various processes or threads, aiming for efficient and 
equitable utilization of system resources.
The scheduler's role involves determining which processes or threads are allowed to run, 
when they can run, and for how long. This decision-making process relies on specific scheduling 
algorithms, which may vary depending on the operating system and its configuration. The chosen 
scheduling algorithm seeks to optimize system performance, responsiveness, throughput, 
fairness, or a combination of these factors.
Having the best possible outcome and fairness throughout the systems has always been 
the priority. For this project we tried to see if we could create a fairer scheduling algorithm than 
the default Linux 2.4.27 scheduler. We observed our experiments average CPU utilization and 
MSE calculation for several users with several process’. And then, we compared the test cases 
between each other. To achieve a fair full system we made the required adjustments to this kernel 
and explained this whole procedure from to start to finish.


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

# 2. DESIGN and IMPLEMENTATION
The scheduling algorithm that we will develop and use is called a Fair-Share Scheduling. We'll 
talk over the intricacies of the Default and Fair-Share Scheduler designs.
# 2.1. DESIGN
The order in which processes are scheduled under the "SCHED_OTHER" scheduling policy is 
determined by the scheduler in the Linux 2.4.20 Kernel using counter and nice values.
The counter value represents the amount of CPU-Time that a process has consumed. Based on 
its static priority, a process is given a counter value when it is first created. The counter value 
drops as the operation progresses.
The process is preempted (temporarily paused) and put back in the run queue when the counter 
value hits zero. The epoch ends and new counter values are generated for each process once all 
the counter values for processes in the run queue are zero.

A user or a program can alter the nice value to change the priority of a process in relation to other 
processes. lesser lovely values indicate higher priorities, while higher nice values indicate lesser 
priorities.
● Higher nice → low priority (nice : 0 – 20) 
● Lower nice → high priority (nice : -19 – 0) 
● Medium priority → nice=0 (in fork, a process gets 0 value for nice)
The periodic process selection mechanism computes a weight value using the goodness function 
in "sched.c". A process's priority is represented by its weight value.
● If counter value of a process is 0 then weight is directly = 0 
● Processes with SCHED_OTHER → weight = 20-nice+counter 
● Processes with RT_PRIORITY → weight = 1000 + rt_priority
When the counter value is reset, it is modified in accordance with the pleasant value of the 
process. This effectively enables users and programs to change the priority of processes by 
allowing the Kernel to allocate more CPU time to processes with lower nice values and less CPU 
time to processes with higher nice values. We were entrusted with implementing a chance-based 
scheduling system. The "Fair-Share Scheduler" algorithm.

#2.2. IMPLEMENTATION

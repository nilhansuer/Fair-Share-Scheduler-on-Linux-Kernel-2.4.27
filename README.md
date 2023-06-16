# Fair-Share-Scheduler-on-Linux-Kernel-2.4.27
# ABSTRACT
 Abstract In this project, we develop a new fair-share scheduling algorithm for the Linux kernel 2.4.27 and evaluate its fairness performance in comparison tonthe standard Linux scheduler. In a VirtualBox VM, all coding and testing are carried out. operating a 2.4.27 kernel.

# 1. INTRODUCTION 
In operating systems, a scheduler is a vital component that oversees the execution of processes or threads on a computer system's CPU (Central Processing Unit). Its main objective is to distribute the CPU's time among various processes or threads, aiming for efficient and equitable utilization of system resources.
The scheduler's role involves determining which processes or threads are allowed to run, when they can run, and for how long. This decision-making process relies on specific scheduling algorithms, which may vary depending on the operating system and its configuration. The chosen scheduling algorithm seeks to optimize system performance, responsiveness, throughput, fairness, or a combination of these factors. Having the best possible outcome and fairness throughout the systems has always been the priority. For this project we tried to see if we could create a fairer scheduling algorithm than the default Linux 2.4.27 scheduler. We observed our experiments average CPU utilization and MSE calculation for several users with several process’. And then, we compared the test cases between each other. To achieve a fair full system we made the required adjustments to this kernel and explained this whole procedure from to start to finish.

# 2. DESIGN and IMPLEMENTATION
The scheduling algorithm that we will develop and use is called a Fair-Share Scheduling. We'll talk over the intricacies of the Default and Fair-Share Scheduler designs.
# 2.1. DESIGN
The order in which processes are scheduled under the "SCHED_OTHER" scheduling policy is determined by the scheduler in the Linux 2.4.20 Kernel using counter and nice values.The counter value represents the amount of CPU-Time that a process has consumed. Based on its static priority, a process is given a counter value when it is first created. The counter value drops as the operation progresses.The process is preempted (temporarily paused) and put back in the run queue when the counter value hits zero. The epoch ends and new counter values are generated for each process once all the counter values for processes in the run queue are zero.
A user or a program can alter the nice value to change the priority of a process in relation to other processes. lesser lovely values indicate higher priorities, while higher nice values indicate lesser priorities.
● Higher nice → low priority (nice : 0 – 20) 
● Lower nice → high priority (nice : -19 – 0) 
● Medium priority → nice=0 (in fork, a process gets 0 value for nice)
The periodic process selection mechanism computes a weight value using the goodness function 
in "sched.c". A process's priority is represented by its weight value.
● If counter value of a process is 0 then weight is directly = 0 
● Processes with SCHED_OTHER → weight = 20-nice+counter 
● Processes with RT_PRIORITY → weight = 1000 + rt_priority
When the counter value is reset, it is modified in accordance with the pleasant value of the process. This effectively enables users and programs to change the priority of processes by allowing the Kernel to allocate more CPU time to processes with lower nice values and less CPU time to processes with higher nice values. We were entrusted with implementing a chance-based scheduling system. The "Fair-Share Scheduler" algorithm.

# 2.2. IMPLEMENTATION

![Ekran görüntüsü 2023-06-16 195730](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/ae7048fd-8e2f-4d3e-99d8-69829d0070c9)

Figure 1 shows how we introduced a straightforward system call to the kernel that modifies the value of the "phase2switchflag" int variable, which we specified above. We may divide the functionality of the default scheduler using if else statements and switch  between the default scheduler and our scheduler whenever we want by using the  "phase2switchflag" flag in the "sched.c" file.

![Ekran görüntüsü 2023-06-16 200030](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/3394fc65-1963-4099-b3de-9be9a912147d)


In Figure 2 are the three functions we’ve implemented in “sched.c”. The functions timer_bh, tqueue_bh, and immediate_bh are declared with extern keyword, indicating that they are defined elsewhere and their definitions can be found in another file or module. timer_bh(void): This function is likely related to handling timer events or tasks. It  might be responsible for processing or managing timers in the system. tqueue_bh(void): This function might be associated with a task queue. It could be  responsible for processing tasks or events from a queue. immediate_bh(void): The name suggests that this function is associated with 
 immediate or urgent processing. It may handle time-sensitive or high-priority tasks. It's important to note that the actual functionality and purpose of these functions can only be  determined by examining the implementation or the codebase where they are defined.


 ![image](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/6cd74c0d-40d1-4ea0-a69e-12e01db0be4b)

 When phase2switchflag is true and c is zero, the activities in this code fragment are carried out. After releasing the run queue lock and updating all task counts according to their "nice" values, it reacquires the run queue lock and jumps to a label to resume scheduling. The overall context  of the code and the scheduling method being used determine the precise function and setting of these operations.


 ![image](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/0817adad-cd37-446e-b3ac-ccd2ab17c1c6)

 We initialized an array called “user_process_count” out of the “schedule” function which has 10 elements and keep the number of processes of users.

![image](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/ff21b678-783e-42bc-98c6-6691ea3ff583)

In Figure 6, the else part is active when the scheduler is switched to the fair-share scheduler. In the first “for_each_task” block, number of processes of the users is initialized to the “user_process_count” array. We created the indexes of the array by taking the mode of the userIDs. Then in the second “for_each_task” block, we checked if the related indexes of the  array is equal to 0. If it is not, then number of processes is mutliplied and initialized to a  variable called “process_multiplication”. Then we multiplied it by 6 to make the values bigger without altering the results. At the end, we distributed the “p->counter” values by dividing “process_multiplication” by the number of processes of the related user to make the distribution fair. 

![image](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/2d7f6ddd-81a9-4846-b6c6-c65bb32ee85f)

The program switches between default and fair-share scheduler with respect to the input values. It switches to the fair-share scheduler if the user types "2" and it switches to the default schedular if the user types "1".

# 3. TESTS and RESULTS
# 3.1 TESTS
In the testing part, we collected CPU Utilizition values for the processes in each case. There were 5 tests that consist of different number of users and processes. With the use of “ top –n 100 –d 1 –b > d1t1.txt” command, we received data. Each “top” command took 1 second to collect the data. We have totally 5000 samples. There were 5 testcases and each of them had 10 tests. All the tests were repeated for both the default and our Fair-Share scheduler. After all our data had been collected, we calculated the Average CPU Utilization and Mean Square Error (MSE) for each process of each test


# 3.1.1. TEST 1

![image](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/97255136-a123-4a2d-b50d-ed6c6c311b8a)


# 3.1.2. TEST 2

![image](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/c7313ab8-7d49-4441-94a7-135c676f19c6)

# 3.1.3. TEST 3

![image](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/1c9a78e8-3bbd-457b-b0fc-ea05a6a277d5)


# 3.1.4. TEST 4

![image](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/6d00a095-04b6-4216-8f49-d78dd00847cf)
![image](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/9f70b8fa-202d-44be-bfb5-26b065bd7961)


# 3.1.5. TEST 5

![image](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/6d70dcc9-8104-4d84-813d-8bc1a180b6ce)
![image](https://github.com/nilhansuer/Fair-Share-Scheduler-on-Linux-Kernel-2.4.27/assets/78359573/799eaf9d-de20-405e-9b98-a509da071d90)


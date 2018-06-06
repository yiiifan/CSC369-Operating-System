# CSC369-Operating-System

## Assignment 1 Hijacking System Calls and Monitoring Processes

Assignment Goals:
hijacking (intercepting) system calls by writing and installing a very basic kernel module to the Linux kernel.
 
hijacking:
	a new system call: my_syscall
	my_syscall + command -->intercept pre_existing system call
the intercepted system call would log a message before its task
	log message when a certain set of PIDs
	monitor a list of PIDs + add/remove PIDs 
stop hijacking: 
	invoke the interceptor(my_syscall)+ REQUEST_SYSCALL_RELEASE

Function List:

System Call Table Stuff(Symbol that allows access to the kernel system call table)

	extern void* sys_call_table[];
	void set_addr_rw(unsigned long addr)/* before replacing a syscall
	set_addr_ro(unsigned long /*restore sys_call table(default)


Data structures and bookkeeping

	struct pid_list; /*monitored pid list for each intercepted system_call
		pid_t pid + list_head list;
	struct mytable; /*store info about intercepted/replaced system calls

  typedef struct {

	  /* Original system call */
	  asmlinkage long (*f)(struct pt_regs);

	  /* Status: 1=intercepted, 0=not intercepted */
	  int intercepted;
	  /* Are any PIDs being monitored for this syscall? */
	  int monitored;
	  /* List of monitored PIDs */
	  int listcount;

	  struct list_head my_list;
  }mytable;


	mytable table[NR_syscalls+1];/*entry for each system call

	spinlock_t pidlist_lock = SPIN_LOCK_UNLOCKED;
	spinlock_t calltable_lock = SPIN_LOCK_UNLOCKED;
	static int check_pid_monitored(int sysc, pid_t pid);

LIST OPERATIONS(manipulating the list of pids)

	static int add_pid_sysc(pid_t pid, int sysc);
	static int del_pid_sysc(pid_t pid, int sysc);
	static int del_pid(pid_t pid);
	static void destroy_list(int sysc);
	static int check_pid_from_list(pid_t pid1, pid_t pid2);/*if two pids have same owner
	
Intercepting exit_group

	void (*orig_exit_group)(int);
	
	[TODO] void my_exit_group(int status);//status??
	[TODO] asmlinkage long interceptor(struct pt_regs reg);
	[TODO] asmlinkage long my_syscall(int cmd, int syscall, int pid);
         REQUEST_SYSCALL_INTERCEPT to intercept the 'syscall' argument
         REQUEST_SYSCALL_RELEASE to de-intercept the 'syscall' argument
         REQUEST_START_MONITORING to start monitoring for 'pid' whenever it issues 'syscall' 
         REQUEST_STOP_MONITORING to stop monitoring for 'pid'

	long (*orig_custom_syscall)(void);

	[TODO]static int init_function(void);
	[TODO]static void exit_function(void);

	module_init(init_function);
	module_exit(exit_function);














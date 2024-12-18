# Process management

Any command/program running on the computer is refered to as a process.
Here are some important concepts that can help you in your work.

- [Overview and check processes](#overview-and-check-processes)
- [Running process when you log off](#running-process-when-you-log-off)
- [Scheduling: timing and queues](#scheduling-timing-your-runs)

## Overview and check processes

If you want to check a complete overview use `htop` or `top`.

- `htop` gives a more "graphical" overview; you can quit with `F10`
- `top` is a bit harder to read; you can quit with `q`

If you want to check if a process is still running or you want to find
its PID (process ID), then you can use `ps aux` and filter for the command or program.

```bash
# Check for a SPAdes run
ps aux | grep spades
# If you want to see the header then do the following
ps aux | grep -P "PID|spades"
```

## Running process when you log off

There are two main ways: disowning or using `tmux` (or `screen`).

The reason why your process stop when you log off or disconnect from the server is that process are organized in a hierarchy, and your session is the parrent of the processes that you start up. When a process is **terminated** (killed - `Ctrl` + `c`; not paused - `Ctrl` + `z`), then all child processes are also killed. This also useful for complex processes that start multiple other processes.

If you set up a `tmux` session (check out the [tmux page for a brief intro](tmux.md)), then it will run independent from your terminal session.
That way you can join it (attach to it) later. This is ideal if your connection is somewhat instable or you don't want to worry about disowning.

States of processes:

- **Running** – here it’s either running (it is the current process in the system) or it’s ready to run (it’s waiting to be assigned to one of the CPUs).
- **Waiting** – in this state, a process is waiting for an event to occur or for a system resource. Additionally, the kernel also differentiates between two types of waiting processes; interruptible waiting processes – can be interrupted by signals and uninterruptible waiting processes – are waiting directly on hardware conditions and cannot be interrupted by any event/signal.
- **Stopped** – in this state, a process has been stopped, usually by receiving a signal. For instance, a process that is being debugged.
- **Zombie** – here, a process is dead, it has been halted but it’s still has an entry in the process table.
- **Terminated** - this is printed when you `kill` a process that is otherwise listed by `jobs`.

To `disown` a job (job IDs start with `%`) or process (using PID),
run `disown <ID>`. If it was among your `jobs`, then it will no longer
be listed there. Make sure that the process is running in the background
before you disown it, otherwise it will just hang suspended.

## Scheduling (timing) your runs

If you have a large run, then it may make sense to time it to the off hours of the server (evening or weekend). Or you can use a queue for scheduling your runs. So they run after one another.

### SLURM

For an example let's suppose we have all the commands that you want
to run in a file called `run.sh`

```bash
# Run it at 19:00
srun --begin=19:00 bash run.sh
```

> You can check the options [here](https://slurm.schedmd.com/srun.html). There are multiple ways to define start time.

Here is a test. So you can see how it works.

```
# Content of run.sh
date
echo "Test run using SLURM"
```

Then run the following:

```bash
date; srun --begin=now+30 bash run.sh >test.log
```

This will print the date on your terminal then schedule the run 30 seconds from now. Check `test.log` and compare the time between what is on the terminal and in the file.

### `at`

**_Currently not working._**

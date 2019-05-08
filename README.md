# Results Watcher

This is an example watcher that is optimized to record results. For example, 
let's say that we are running jobs on an HPC resource. 
Each job would finish with a result value (string, or json equivalent)
and then call export variables to the environment, and call 
watchme to record the result for some task:

```bash
export WATCHMEENV_density=0.45
export WATCHME_weight=32
watchme run results-watcher task-hpc-job
```

And then you see the results as they are here - the variables are written to
files with equivalent names:

```bash
$ tree
.
├── README.md
├── task-hpc-job
│   ├── density
│   ├── TIMESTAMP
│   └── weight
└── watchme.cfg

1 directory, 5 files
```

This means that you can use a single timepoint (multiple files for one commit)
as an entry, possibly adding a `WATCHMEENV_ID` to give the timepoint an id, OR
you can have a single file represent a single metric changing over time.
When time is ready for export, it's done via .git with a simple command:

```bash
$ watchme export results-watcher task-hpc-job density
git log --all --oneline --pretty=tformat:"%H" --grep "ADD results" 384d7bdc6e54af6266377b30ff0d47a40c4fc28d..732dee443caa19f0e50ec1e9b89ca3a542459cc7 -- task-hpc-job/density
{
    "commits": [
        "732dee443caa19f0e50ec1e9b89ca3a542459cc7",
        "c0861ed8ebe473cc3efa1db5f84e10d05d61bbc8"
    ],
    "dates": [
        "2019-05-08 15:16:14 -0400",
        "2019-05-08 15:11:32 -0400"
    ],
    "content": [
        "0.55",
        "0.45"
    ]
}
```

And that's it! Watchme handles commits, so all you would need to do (as I've
just done) is create the repository on GitHub, and then push. For more 
details about watchme and the results tasks, see [the docs](https://vsoch.github.io/watchme/watchers/results/#1-the-from-env-task).

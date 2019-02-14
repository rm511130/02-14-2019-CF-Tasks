# 02-14-2019 Training 

## CF-Tasks
Link to the "How to use CF TASKS" [documentation](https://docs.cloudfoundry.org/devguide/using-tasks.html).

In contrast to a long running process (LRP), tasks run for a finite amount of time, then stop. Tasks run in their own containers and are designed to use minimal resources. After a task runs, Cloud Foundry destroys the container running the task.

As a single-use object, a task can be checked for its state and for a success or failure message.

Tasks can be used to perform one-off jobs, which include the following:

- Migrating a database
- Sending an email
- Running a batch job
- Running a data processing script
- Processing images
- Optimizing a search index
- Uploading data
- Backing-up data
- Downloading content

So let's play with `cf tasks`

## Lab 

**1. We'll start by cloning and pushing a simple Chess App**

```
$ cd /work # my workspace
$ git clone https://github.com/rm511130/chess; cd chess; cf push
```

**2. Let's take a look at the structure of the project we just cf pushed**

```
$ cd /work/chess/tasks
$ cat my_task.sh 
```

You should see this: 

```
#
# to be executed from a command prompt
# using the command:  $ cf run-task chess "/home/vcap/app/htdocs/tasks/my_task.sh" --name job_example
#
echo "Executing the MY_TASK shell script"
date
pwd
df -h
echo "End of MY_TASK shell script"
#
# you can also try something like this: 
# $ cf run-task chess "for i in { 1..5 }; do curl -k https://chess.apps.pcf4u.com; done" --name curl_repeat
# 
```

You can also peruse the structure of the Chess project in github. More specifically, we're interested in the [my_task.sh](https://github.com/rm511130/chess/blob/master/tasks/my_task.sh) file.

**3. Let's take a look inside the container that is running the Chess App you pushed in step 1**

```
$ cf ssh chess

vcap@af0fe8a0:~$ ls -las ./app/htdocs/tasks/my_task.sh 

4 -rwxr-xr-x 1 vcap vcap 93 Sep 14 15:17 ./app/htdocs/tasks/my_task.sh

vcap@af0fe8a0:~$ cat ./app/htdocs/tasks/my_task.sh

echo "Executing the MY_TASK shell script"
date
pwd
df -h
echo "End of MY_TASK shell script”

vcap@af0fe8a0:~$ exit
```

As you can see, the contents of the `/tasks` folder can be found in the container that is running the Chess App.

**4. Let's run `cf tasks` **

Back to your CF CLI command prompt, execute the following command:

```
$ cf run-task chess "/home/vcap/app/htdocs/tasks/my_task.sh" --name job_example

Creating task for app chess in org demo / space demo as rmeira@pivotal.io...
OK

Task has been submitted successfully for execution.
task name:   job_example
task id:     1
```

**5. What happened? Let's check **

```
$ cf tasks chess
Getting tasks for app chess in org demo / space demo as rmeira@pivotal.io...
OK

id   name          state       start time                      command
1    job_example   SUCCEEDED   Thu, 14 Feb 2019 14:58:41 UTC   /home/vcap/app/htdocs/tasks/my_task.sh
```

**6. Where's the output? **

```
   2019-02-14T09:58:42.73-0500 [CELL/0] OUT Cell 11c6701a-565d-47d9-a5b1-4fd0b656c5db successfully created container for instance 39c17cc8-961e-4168-87aa-4fc4ee61f868
   2019-02-14T09:58:52.53-0500 [APP/TASK/job_example/0] OUT Executing the MY_TASK shell script
   2019-02-14T09:58:52.53-0500 [APP/TASK/job_example/0] OUT Thu Feb 14 14:58:52 UTC 2019
   2019-02-14T09:58:52.53-0500 [APP/TASK/job_example/0] OUT /home/vcap/app
   2019-02-14T09:58:52.54-0500 [APP/TASK/job_example/0] OUT Filesystem      Size  Used Avail Use% Mounted on
   2019-02-14T09:58:52.54-0500 [APP/TASK/job_example/0] OUT overlay         1.0G  221M  804M  22% /
   2019-02-14T09:58:52.54-0500 [APP/TASK/job_example/0] OUT tmpfs           7.9G     0  7.9G   0% /dev/shm
   2019-02-14T09:58:52.54-0500 [APP/TASK/job_example/0] OUT /dev/sdb2       111G   21G   85G  20% /tmp/garden-init
   2019-02-14T09:58:52.54-0500 [APP/TASK/job_example/0] OUT tmpfs           7.9G     0  7.9G   0% /sys/fs/cgroup
   2019-02-14T09:58:52.54-0500 [APP/TASK/job_example/0] OUT tmpfs           2.8M  104K  2.7M   4% /etc/cf-instance-credentials
   2019-02-14T09:58:52.54-0500 [APP/TASK/job_example/0] OUT udev            7.9G     0  7.9G   0% /dev/tty
   2019-02-14T09:58:52.54-0500 [APP/TASK/job_example/0] OUT tmpfs           7.9G     0  7.9G   0% /proc/scsi
   2019-02-14T09:58:52.54-0500 [APP/TASK/job_example/0] OUT tmpfs           7.9G     0  7.9G   0% /sys/firmware
   2019-02-14T09:58:52.54-0500 [APP/TASK/job_example/0] OUT End of MY_TASK shell script
   2019-02-14T09:58:52.54-0500 [APP/TASK/job_example/0] OUT Exit status 0
   2019-02-14T09:58:53.22-0500 [CELL/0] OUT Cell 11c6701a-565d-47d9-a5b1-4fd0b656c5db stopping instance 39c17cc8-961e-4168-87aa-4fc4ee61f868
   2019-02-14T09:58:53.22-0500 [CELL/0] OUT Cell 11c6701a-565d-47d9-a5b1-4fd0b656c5db destroying container for instance 39c17cc8-961e-4168-87aa-4fc4ee61f868
   2019-02-14T09:58:54.71-0500 [CELL/0] OUT Cell 11c6701a-565d-47d9-a5b1-4fd0b656c5db successfully destroyed container for instance 39c17cc8-961e-4168-87aa-4fc4ee61f868
```   

As you can see, the logs show the results of the execution of `my_task.sh`

**7. How else can I use `cf tasks`**

Take a look at the example below:






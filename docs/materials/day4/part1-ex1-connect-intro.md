Thursday Exercise 1.1: Getting Acquainted with OSG Connect
==========================================================


In this exercise, you'll get acquainted with the OSG Connect submit server (user-training.osgconnect.net), and the OSG Connect helpdesk.

Get Acquainted with the OSG Connect Submit Server (user-training.osgconnect.net)
--------------------------------------------------------------------------------

SSH to `username@user-training.osgconnect.net` in a command-line terminal.

### Look around your 'home' directory

You'll notice the 'public', and 'stash' directories, which may be used in various specific OSG Connect guides. You'll use these spaces for different reasons. **Make sure to read the OSG Connect helpdesk guides** on [data management](https://support.opensciencegrid.org/support/solutions/articles/12000006512-guidelines-for-data-managment-in-osg-storage-and-transfer) and [storage solutions](https://support.opensciencegrid.org/support/solutions/articles/12000002985-storage-solutions-on-osg-home-stash-and-public).

### Run `condor_status`

Running `condor_status` by itself on the OSG Connect submit host will give no output, but you can otherwise check with:

``` console
%UCL_PROMPT_SHORT% <strong>condor_status -pool osg-flock.grid.iu.edu</strong>
```

Visit the Helpdesk Materials
----------------------------

Visit the Help desk page (<https://support.opensciencegrid.org/>) and take a look at the knowledge base articles. You will be using some of these in the next exercises.

### Move on to the next exercise when you're ready to submit jobs!


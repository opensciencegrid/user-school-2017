Challenge 1
-----------

Do you have any extra computation that needs to be done? Real work, from your life outside this summer school? If so, try it out on our HTCondor pool. Can't think of something? How about one of the existing distributed computing programs like [distributed.net](http://www.distributed.net), [SETI@home](http://setiathome.ssl.berkeley.edu/), [Einstien@Home](http://www.einsteinathome.org/) or others that you know. We prefer that you do your own work rather than one of these projects, but they are options.

Challenge 2
-----------

Try to generate other Mandelbrot images. Some possible locations to look at with goatbroat:

``` console
goatbrot -i 1000 -o ex1.ppm -c 0.0016437219722,-0.8224676332988 -w 2e-11 -s 1000,1000
goatbrot -i 1000 -o ex2.ppm -c 0.3958608398437499,-0.13431445312500012 -w 0.0002197265625 -s 1000,1000
goatbrot -i 1000 -o ex3.ppm -c 0.3965859374999999,-0.13378125000000013 -w 0.003515625  -s 1000,1000
```

You can convert ppm files with `convert`, like so:

``` console
convert ex1.ppm ex1.jpg
```

Now make a movie! Make a series of images where you zoom into a point in the Mandelbrot set gradually. (Those points above may work well.) Assemble these images with the "convert" tool which will let you convert a set of JPEG files into an MPEG movie.

Challenge 3
-----------

Try out [Pegasus](OSGSS2012ExtraPegasus). Pegasus is a workflow manager that uses DAGMan and can work in a grid environment and/or run across different types of clusters (with other queueing software). It will create the DAGs from abstract DAG descriptions and ensure they are appropriate for the location of the data and computation.

Links to more information:

-   [Pegasus Website](http://pegasus.isi.edu)
-   [Pegasus Documentation](http://pegasus.isi.edu/wms/docs/4.0/)
-   [Useful Tutorial](https://github.com/OSGConnect/tutorial-pegasus)
-   [Pegasus on OSG Connect (covered Thursday)](https://pegasus.isi.edu/documentation/tutorial.php)

If you have any questions or problems, please feel free to contact the Pegasus team by emailing <pegasus-support@isi.edu>

Challenge 4
-----------

Try out [Makeflow](OSGSS2012ExtraMakeflow). You can think of it acting like DAGMan, but there several intriguing differences. Now that you understand DAGMan, you should think about how it compares to Makeflow. Why would you use DAGMan or Makeflow?

These extra exercises for Makeflow were kindly provided by Doug Thain.

Note: Be Nice
-------------

Please be polite. Computers in our Condor pool will run multiple jobs at a time, and these computers are shared with your fellow students. If you have jobs that will use significant computational power or memory, limit your jobs to be kind to your neighbors, unless you run your jobs during off-hours.


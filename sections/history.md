# A brief history


![Doug Cutting](images/doug-cutting.jpg)

Note: Doug Cutting had a vision.

To search the entire internet on an open platform.

Worked on open source search engine.

In a way, was one of the first people to really visualize GIFEE


![Lucene](images/lucene.png)

Note: Way to search text

Solr and Elasticsearch both heavily utilize Lucene

When you can search the web you need...


![Nutch](images/nutch.svg)

Note: A way to crawl the web

Nutch was designed to be fast!

But it was slow and didn't scale


###...meanwhile, at

![Google](images/google.png)

Note: Two very influential papers came out


![File System Paper](images/google-paper-fs.png)

Note: Described Google's successfully running file system

Dealt with fault tolerance and component failures

Dealt with huge files


![Map Reduce Paper](images/google-paper-map-reduce.png)

Note: Described Google's successfully running computation engine

Used to power Google's web crawler


Doug Cutting wrote open source versions of both, and called the result Hadoop, after his son's favorite toy.


### A Decade Later...

Hadoop still alive and kicking

HDFS and MapReduce still heavily used

The YARN Scheduler was added to the core project

Many, many, many subprojects exist
ScalaQueryPlayIteratees
=======================
[![Build Status](https://travis-ci.org/youdevise/scalaquery-play-iteratees.png)](https://travis-ci.org/youdevise/scalaquery-play-iteratees)

Summary
-------
Easily create a Play 2.0.x [Enumerator](http://www.playframework.com/documentation/2.0.x/Enumerators)
producing chunked results from any [ScalaQuery 0.10.0-M1](https://github.com/slick/slick/tree/0.10.0-M1)
query!
 *  Stream chunked results from any `Query` as an `Enumerator`! 
 *  Easily plug into Play 2.0.x streaming, such as
    [Comet](http://www.playframework.com/documentation/2.0.x/ScalaComet)
 *  Underlying database transaction provides read consistency across async chunked reads
 *  Easy logging callbacks 
 *  Robust error handling
 *  Well-tested library extracted from production code

Usage
-----

```scala
//
// Controller
//
def listRecordsViaComet = Action { request =>
  val pipeline = (
    Records.enumerateAllInChunksOfTwo
      &> Enumeratee.map(toJson(_))
      &> Comet(callback = "parent.cometMessage"))

  Ok.stream(pipeline)
}

//
// Model
//
class Records extends Table[Record]("records") {
  def mkQuery = for { r <- this } yield r
  
  def enumerateAllInChunksOfTwo = 
    enumerateScalaQuery(profile, Right(database), mkQuery, maybeChunkSize = Some(2))
}
```

See the included [sample play app](sample) for a working example.

To depend on the latest published jar, simple add the following dependency in **project/Build.scala**: `"com.timgroup" %% "scalaquery-play-iteratees" % "0.9.0"`

Status
------

This code is working in production code based on Play 2.0.8, ScalaQuery 0.10.0-M1, on Scala 2.9.2. 

TODO
----

Create a branch to begin to port to latest versions of Play, SLICK, and Scala.

Copyright
---------

See [MIT-LICENSE.txt](MIT-LICENSE.txt) for details.

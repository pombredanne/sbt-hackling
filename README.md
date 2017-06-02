[![Gitter chat](https://badges.gitter.im/libling.svg)](https://gitter.im/libling/Lobby)
[![Build Status](https://travis-ci.org/libling/sbt-hackling.svg?branch=master)](https://travis-ci.org/libling/sbt-hackling)

# sbt-hackling

Hackathon implementation of the Libling concept.

## Libling

Libling is a way to add dependencies to your project as plain Scala sources. 
It is intended for simple snippets of code or small libraries (for now).

## Motivation

* Publishing simple libraries is unnecessarily hard in JVM land. Why not just push to GitHub and be done with it?
* even when code is source compatible with multiple Scala versions, need to cross-build it for multiple scala versions
* with scala.js and scala native, even more cross building is necessary
* versioning is hacky. a git commit graph is closer to what's going on, a commit hash uniquely identifies a version and its history
* take advantage of distributed VCS possibilities. cloning a repo mirrors all the versions so far, can be cached. Does not require single source of truth centralized infrastructure such as Maven Central.

## To use a libling

Add this plugin:

    addSbtPlugin("libling" % "sbt-hackling" % "1.0")

Add a libling source dependency:
       
    sourceDependencies += ...
    
## To build a libling

    sbt new libling/libling.g8
    
### structure

A libling is just a bunch of code in a certain structure. When you include a libling into your sbt project, 
some code gets dumped into your `/target/libling` directory and becomes part of your build.

* `README.md` documentation root. can link to further docs in `/doc` directory.
* `/src`
    contains plain Scala sources, no main/ test/ etc
    always included on libling resolution
    we'll figure out later how to build more complicated stuff
* `/doc`
    .md sources - included by default.
* `/libling` (TODO)
    * `lock`: the libling's own libling dependencies, including transitive dependencies.
    Commit hashes only. These are generated by the `liblingUpdate` task and should not be manually edited.
    A libling can only depend on other liblings. We'll figure out how to handle necessary binary deps (e.g. scala version) later. HOCON format.
    * `meta`: useful metadata where to look up the commit hashes. Can be generated by `liblingGenerateMetadata` task. HOCON format.
* `/test`
    * tests go in here. not included as dependency by default
    
## TODO

* dependency lock file as part of the libling
* transitive resolve of liblings, via dependency lock file
* resolve commits by tags and branches
* evicting conflicting revisions coming from the same tree
    
    
## Inspirations

* [microlib](https://github.com/jessitron/microlib): same basic idea in Clojure
* [Gistard](https://gist.github.com/viktorklang/a09aad920c1a4072cfe6): depend on gists

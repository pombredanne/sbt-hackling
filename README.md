[![Gitter chat](https://badges.gitter.im/libling.svg)](https://gitter.im/libling/Lobby)
[![Build Status](https://travis-ci.org/libling/sbt-hackling.svg?branch=master)](https://travis-ci.org/libling/sbt-hackling)

# sbt-hackling

Hackathon implementation of the Libling concept. It's a prototype. It works, but exactly what and how works might break at any time.

## Libling

Libling is a way to add dependencies to your project as plain Scala sources. 
It is intended for simple snippets of code or small libraries (for now).

## Motivation

* Publishing simple libraries is unnecessarily hard in JVM land. Why not just push to GitHub and be done with it?
* Even when code is source compatible with multiple Scala versions, need to cross-build it. Makes builds more complicated!
* With scala.js and Scala Native, even more cross building is necessary!
* Classical major.minor.bugfix versioning is a hack! A git commit graph is closer to what's going on, a commit hash uniquely identifies a version and its history.
* take advantage of distributed VCS possibilities. cloning a repo mirrors all the versions so far, can be cached. Does not require single source of truth centralized infrastructure such as Maven Central.

Actually the latter two are the true motivation and the first three are a way to justify a practical experiment

## Features

* declare dependencies on liblings in your `build.sbt` via git commit hash
* a commit hash may be resolved from remote and local repos
* dependencies are resolved transitively
* repos are cached locally
* install them as sources, including markdown docs

## Still kind of missing features

* friendly version resolution via tags and branches
* friendly DSL
* finding and resolving dependency conflicts by eviction or something

## To use a libling

Add this plugin:

    resolvers += Resolver.bintrayIvyRepo("jastice","sbt-plugins")
    addSbtPlugin("libling" % "sbt-hackling" % "0.2")

Add a libling source dependency:
       
    sourceDependencies += Dependency(
            Version("<git commit hash>"), 
            Repositories(uri("<git repository uri>")))
            
On the sbt shell:

    > liblingInstall
    
This will:

* cache repos locally in `<user home>/.libling/cache`
* copy sources and docs out of the repos into your project under `target/libling/`
* generate a `/libling` directory in your project that contains a lock file that exactly identifies the commit hashes you depend on and the repos where they were found


## Examples
    
* [Skeleton](https://github.com/libling/libling-skeleton) - a minimal libling example
* [Libling with dependencies](https://github.com/libling/libling-with-dependencies) - includes Skeleton as a dependency
* [Libling with transitive dependencies](https://github.com/libling/libling-with-transitive-dependencies) - includes the libling with dependencies directly and the Skeleton transitively

    
## To create a libling

    sbt new libling/libling.g8
    
See [libling.g8](https://github.com/libling/libling.g8) for details.
    
    
### structure

A libling is just a bunch of code in a certain structure. When you include a libling into your sbt project, 
some code gets dumped into your `/target/libling` directory and becomes part of your build.

* `README.md` documentation root. can link to further docs in `/doc` directory.
* `/src`
    Contains plain Scala sources, no main/ test/ etc. Always included on libling resolution. We'll figure out later how to build more complicated stuff.
* `/doc`
    .md sources - included by default.
* `/libling`
    * `lock`: the libling's own libling dependencies, including transitive dependencies.
    Commit hashes only. These are generated by the `liblingUpdate` task and should not be manually edited.
    A libling can only depend on other liblings. We'll figure out how to handle necessary binary deps (e.g. scala version) later. HOCON format.
    * `meta`: useful metadata where to look up the commit hashes. Can be generated by `liblingGenerateMetadata` task. HOCON format.
* `/test`
    * tests go in here. Not included as dependency by default.
    
    
## Inspirations

* [microlib](https://github.com/jessitron/microlib): same basic idea in Clojure
* [Gistard](https://gist.github.com/viktorklang/a09aad920c1a4072cfe6): depend on gists

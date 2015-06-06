## Overview ##

jblogbackup is a Java command line tool which allows you to backup your [Blogger](http://www.blogger.com) blog content including the following:
  * posts
  * comments
  * images (including the larger click-through images)

Blog content is downloaded and processed into one directory which contains the following:
  * index.html : all blog posts and comments in one HTML file
  * styles.css : CSS style sheet
  * images/ directory : contains all downloaded images using the same file name as [Blogger](http://www.blogger.com). The larger "click-through" images are suffixed with `_fullsize`.
  * log.html : a processing log of warnings and errors
  * atom/ directory : contains the atom posts (entries.xml) and comments (comments.xml)

To see what a jblogbackup backup looks like check out
  * the jblogbackup [Test Blog](http://jblogbackup-test.blogspot.com) and
  * its corresponding [Backup](http://www.mycreativecup.com/jblogbackup-test)

## Feature Highlights ##

  * Downloads all [Blogger](http://www.blogger.com) images including the larger "click-through" images
  * Only uses the [Blogger](http://www.blogger.com) public API so no authentication required
  * A zip file of the backup directory can be created by setting `jblogbackup.createZipFile=true` or using the command line option `--zip <zipfile>`
  * Image downloading is parallelized

## Requirements ##

  * Java 6 JRE or JDK

## How to Install and Run a Backup ##

  1. Be sure to have Java 6 installed. We assume its installed in directory `{JAVA_HOME}`
  1. Download jblogbackup-onejar-{version}.jar to directory `{JBLOGBACKUP_HOME}`
  1. Figure out the name of the [Blogger](http://www.blogger.com) blog you want to backup. The name can be found in the blog's URL as follows: `http://{blog-name}.blogspot.com`
  1. From your OS command line
    * `cd {JBLOGBACKUP_HOME}`
    * `{JAVA_HOME}/java -jar jblogbackup-onejar-{version}.jar --blogname {blog-name}`
  1. This will create the blog backup in directory `{JBLOGBACKUP_HOME}/{blog-name}`

Additional Parameters:
  * To create a zip file at `{JBLOGBACKUP_HOME}/{blog-name}/{blog-name}.zip` add the `--zip` command line switch
  * If you run this backup periodically you can add the `--clean` switch to delete all files in the `{JBLOGBACKUP_HOME}/{blog-name}` directory when the backup starts.
  * Add `--appendtimestamp` to append a timestamp to your backup directory e.g. `{JBLOGBACKUP_HOME}/{blog-name}_ddMMMyy-HHmmss`
  * To specify default settings, create a Java properties file at `{JBLOGBACKUP_HOME}/jblogbackup.properties` with one property per line e.g. `jblogbackup.{paramname}={paramvalue}`. That way you won't have to specify all your desired parameters via the command line when running the command above from the `{JBLOGBACKUP_HOME}` directory. For example:
```
jblogbackup.appendTimestampToBackupDir = true
jblogbackup.cleanBackupDir = true
jblogbackup.createZipFile = true 
```
  * Use the `--printconfig` command line switch to print out your current configuration WITHOUT running the backup. This is useful if you are using a combination of command line switches and jblogbackup.properties files:
```
$ java -jar jblogbackup-onejar-1.0.jar --blogname buzz --backupdir /tmp --printconfig 
PARAMETERS
Blog Name: buzz
Backup Directory Path: /tmp
Append Timestamp To Backup Dir: false
Clean Backup Dir: false
Create Zip File: false
Zip File Path: //tmp.zip
Number of Image Download Threads: 4
Force GC to Limit Open Files: false
```

## Command Line Interface ##

```
usage: jblogbackup.CmdLineTool
 -?                             Print this message.
    --appendtimestamp           If specified, append timestamp to the
                                backup directory in the form "_ddMMMyy-HHmmss"
 -b,--backupdir <backupdir>     Path to the backup directory. Will be
                                created if it does not exist. Defaults to blogname/ in the current working
                                directory.
 -c,--clean                     If specified, all files in the backup
                                directory are deleted before the backup starts
    --dontclean                 If specified, the backup directory will
                                NOT be cleaned
    --dontzip                   Do not create a zip file of the backup
                                directory.
    --forcegc                   Force garbage collection to limit number
                                of open files. Only needed if you see java.io.FileNotFoundException due to
                                "Too many open files"
 -h,--help                      Print this message.
 -help                          Print this message.
    --imgthreads <numThreads>   Number of image download threads. It is
                                NOT recommended to exceed 4!
    --log-debug                 Emit debug, info, warning and error
                                messages. Sets Log4J root category level to debug.
    --log-error                 Emit error messages. Sets Log4J root
                                category level to error.
    --log-info                  Emit info, warning and error messages.
                                Sets Log4J root category level to info.
    --log-warn                  Emit warning and error messages. Sets
                                Log4J root category level to warn.
 -n,--blogname <blogname>       Blog name. REQUIRED.
    --noprops                   Do not load any properties from
                                jblogbackup.properties files or Java system properties.
 -p,--printconfig               Print configuration and exit.
 -v,--version                   Print version information and exit.
 -z,--zip <zipfile>             Create a zip file of the backup directory.
                                If zipfile path is not specified then backupdir/../backupdir.zip is used.
```

## Parameters ##

jblogbackup reads its parameters from multiple sources with the following precedence:
  * the command line
  * java system properties (i.e. -Djblogbackup.{paramname}={paramvalue})
  * jblogbackup.properties in the current working directory (i.e. ${user.dir}/jblogbackup.properties)
  * jblogbackup.properties in the user's directory (i.e. ${user.home}/jblogbackup.properties)

This allows one to specify default values in various properties files.

| **Paramter**                                     | **Default Value**            | **Command Line Switches**    | **Property**                           | **Description** |
|:-------------------------------------------------|:-----------------------------|:-----------------------------|:---------------------------------------|:----------------|
| Blogger blog name **REQUIRED**                   | None                         | -n,--blogname `<blogname>`   | jblogbackup.blogName                   | Blogger blog name. You can find the blog name of your Blogger blog in the blog URL http://{blogname}.blogspot.com. |
| Backup directory path                            | `<blogname>`/ in the current working directory          | -b,--backupdir `<backupdir>` | jblogbackup.backupDir                  | Path to the backup directory. Will be created if it does not exist. Defaults to `<blogname>`/ in the current working directory. |
| Append timestamp to backup directory?            | false                        | --appendtimestamp            | jblogbackup.appendTimestampToBackupDir | If specified, append timestamp to the backup directory in the form `_ddMMMyy-HHmmss` |
| Clean backup directory before starting backup?   | false                        | -c,--clean,--dontclean       | jblogbackup.cleanBackupDir             | If specified, all files in the backup directory are deleted before the backup starts. |
| Create zip file of the backup directory?         | false                        | -z,--zip,--dontzip           | jblogbackup.createZipFile              | If specified, create a zip file of the backup directory. If no zip file path is specified then `<backupdir>`.zip will be created in the same directory which contains `<backupdir>`. |
| Zip file path                                    | `<backupdir>`/../`<backupdir>`.zip | -z,--zip `<zipfile>`         | jblogbackup.zipFilePath                | Path to the zip file. |
| Number of image download threads                 | 4                            | --imgthreads `<numThreads>`  | jblogbackup.numImageDownloadThreads    | Number of image download threads. It is NOT recommended to exceed 4!  |
| Force garbage collection to limit open files     | false                        | --forcegc                    | jblogbackup.forceGcToLimitOpenFiles    | Force garbage collection to limit number of open files. Only needed if you see java.io.FileNotFoundException due to "Too many open files". This may be needed on some hosted servers where the maximum number of open files (`ulimit -n`) has low limit e.g. 100.  |

## Known Limitations ##

  * There is no attempt to backup the look and feel of the blog.
  * The blog is saved as one large HTML file index.html
  * Blog atom posts are downloaded all at once i.e. no support for paginating through posts. This may or may not be needed to support very large blogs.
  * Currently no support for incremental backups.
  * Only supports [Blogger](http://www.blogger.com). Note that the code is mostly generic and not too much work would be needed to support other blogging software (like WordPress). To support other blogging software, one has to implement:
    * a new factory for downloading the Atom or RSS and converting that into java objects (those java objects are used by Velocity when generating index.html and log.html)
    * custom content handlers for things like click through images etc
  * Blogger videos are NOT downloaded. The backed up entry will contain the original Blogger video object markup with a reference back to Blogger. This really needs to be fixed as jblogbackup should download all of one's content!





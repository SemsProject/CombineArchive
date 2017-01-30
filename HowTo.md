/HowTo use /CombineArchive 
---------------------------
Using the our /CombineArchive library is not very complicated and can be separated in the sollowing steps:

* [Opening or Creating a /CombineArchive](//HowTo#OpeningorCreatingaCombineArchive): create a new /CombineArchive or open an existing one.
* [Reading the contents of a /CombineArchive](//HowTo#ReadingthecontentsofaCombineArchive): scan the contents of a /CombineArchive and read its files and meta data
* [Updating the /CombineArchive](//HowTo#UpdatingtheCombineArchive): add, remove, or modify the entities in a /CombineArchive
* [Finalising the /CombineArchive](//HowTo#FinalisingtheCombineArchive): write the manifest of a /CombineArchive and save it

In addition, a complete example is available in [source:src/main/java/de/unirostock/sems/cbarchive/Example.java Example.java].

### Opening or Creating a /CombineArchive 
To open a /CombineArchive just pass the path to the archive to the /CombineArchive constructor ([source](https://sems.uni-rostock.de/trac/combinearchive/browser/src/main/java/de/unirostock/sems/cbarchive///CombineArchive.java#L116), [JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html/#/CombineArchive%28java.io.File%29)):

```
#!java
CombineArchive ca = new CombineArchive (new File ("/path/to/archive.omex"));
```

If ```/path/to/archive.omex``` does not exist an empty /CombineArchive aill be created.

### Reading the contents of a /CombineArchive 
In addition to the methods described in the following these functions provide some information about the entries stored in a /CombineArchive:
* ```getNumEntries()``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html//#getNumEntries%28%29)): returns the number of entries stored in the archive
* ```getMainEntry()``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html//#getMainEntry%28%29)): returns the main entry, as defined in the manifest of the archive
* ```getNumEntriesWithFormat(URI format)``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html//#getNumEntriesWithFormat(java.net.URI))): returns the number of entries in the archive sharing a certain format
* ```HasEntriesWithFormat(URI format)` ([http://jdoc.sems.uni-rostock.de/CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html#HasEntriesWithFormat(java.net.URI) JavaDoc]): returns `true``` if there is at least one entry with a certain format
(see also /CombineFormats)

#### Iterate over all Entries in an Archive 
The method ```getEntries ()``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html//#getEntries%28%29)) returns a list of files stored in the archive. To iterate over all entries you could use the following snippet:

```
#!java
for (ArchiveEntry entry : ca.getEntries ())
{
	System.out.println (">>> file name in archive: " + entry.getFileName ()
		+ "  -- apparently of format: " + entry.getFormat ());
}
```

#### Get an Entry by Location 
Passing a path to ```getEntry (String location)``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html//#getEntry%28java.lang.String%29)) you can retrieve the file that is stored at this location:

```
#!java
ArchiveEntry myEntry = ca.getEntry ("/path/to/entry");
```

Paths should always start with a ```/``` and, thus, start absolute from the root of the archive.

#### Receive the Meta Data describing an Entry 
To iterate over the meta data descriptions stored for an entry ```entry` in the archive you might want to use the `getDescriptions ()``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/ArchiveEntry.html//#getDescriptions%28%29)) method:

```
#!java
for (MetaDataObject description : entry.getDescriptions ())
{
	System.out.println ("+ found some meta data about "
		+ description.getAbout ());
}
```

To learn more about meta data have a look at [None](//MetaDataObject)s.

#### Extract a File from the /CombineArchive 
Given an entry in ```entry` you can extract it using `extractFile(File target)``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/ArchiveEntry.html//#extractFile%28java.io.File%29)):

```
#!java
File tmpEntryExtract = new File ("/path/to/desired/destination");
entry.extractFile (tmpEntryExtract);
```

Afterwards, you'll find the fill in ```/path/to/desired/destination```.

#### Read a File from the /CombineArchive without extracting it 
To read a file you do not have to extract it. Instead, you can access its input stream directly:

```
#!java
InputStream myReader = Files.newInputStream (entry.getPath (), StandardOpenOption.READ);
// do whatever you want with the stream
myReader.close ();
```

This of course will on the fly decompress it from the ZIP container. Thus, it is fine for just taking a small look into a file, but not the best way to open all files repeatedly.

#### Extract the whole /CombineArchive 
The archive can be extracted by passing the destination to the ```extractTo(File destination)``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html//#extractTo%28java.io.File%29)) method:

```
#!java
File destination = new File ("/path/to/myDestination");
ca.extractTo (destination);
```

This will extract all files stored in the archive to ```/path/to/myDestination```.


### Updating the /CombineArchive 
It is of course also possible to manipulate archives.

#### Adding a File to an Archive 
There are multiple options to add another file to a /CombineArchive. The following two sections will cover the main methods.

##### Specifying the Target Name 
This is the simplest method. Just pass a file and a target location to ```addEntry(File toInsert, String targetName, URI format)``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html//#addEntry(java.io.File,%20java.lang.String,%20java.net.URI))):

```
#!java
ArchiveEntry CellMLFile = ca.addEntry (
	new File ("/tmp/base/path/subdir/file.cellml"),
	"/target/dir/file.cellml", 
	new URI ("http://identifiers.org/combine.specifications/cellml.1.0"));
```

After doing so you'll find the CellML file in ```/target/dir/file.cellml``` of the /CombineArchive.

(To learn more about formats see combine-ext:wiki, especially http://sems.uni-rostock.de/trac/combine-ext/wiki//CombineFormatizer)

##### Providing a Base Path 
The function ```addEntry(File baseDir, File file, URI format)` ([http://jdoc.sems.uni-rostock.de/CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html#addEntry(java.io.File,%20java.io.File,%20java.net.URI) JavaDoc]) will add an entry stored in `file` from the subtree starting in `baseDir```. For example:

```
#!java
ArchiveEntry SBMLFile = ca.addEntry (
	new File ("/tmp/base/path"),
	new File ("/tmp/base/path/sub/dir/file.sbml"),
	new URI ("http://identifiers.org/combine.specifications/sbml"));
```

will add an SBML file which is located in ```/tmp/base/path/sub/dir/file.sbml`. The base path is set to `/tmp/base/path`, that means the entry can afterwards be found in `/sub/dir/file.sbml``` in the /CombineArchive.

While this method seems to be more complicated and less useful, there are cases when it eases your work. Imagine you want to create an archive of all files in ```/home/user/latest/research` recursively. Using this method you could simply iterate through all files and directories always passing `/home/user/latest/research``` as base path to the creation of entries. This way, you can quickly zip subtree on your file system.

(To learn more about formats see combine-ext:wiki, especially http://sems.uni-rostock.de/trac/combine-ext/wiki//CombineFormatizer)

#### Setting the Main Entry 
You can immediately define the main entry of an archive when you add it. There are versions of the previously described ```addEntry (...)``` functions which take another boolean to specify if it's the main entry.

In addition, you can set a main entry afterwards by calling the ```setMainEntry (ArchiveEntry mainEntry)``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html//#setMainEntry%28de.unirostock.sems.cbarchive.//ArchiveEntry%29)) method, passing the main entry:

```
#!java
ca.setMainEntry (entry);
```

#### Removing a File from an Archive 
You can remove a file from an archive by specifying its location in ```removeEntry(String location)``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html//#removeEntry%28java.lang.String%29)):

```
#!java
ca.removeEntry("/path/to/the/entry");
```


Additionally, you can simply pass the [ArchiveEntry](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/ArchiveEntry.html) to ```removeEntry(ArchiveEntry entry)``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html//#removeEntry%28de.unirostock.sems.cbarchive.//ArchiveEntry%29)):

```
#!java
ca.removeEntry(entry);
```


#### Adding Meta Data to an Entry in the Archive 
To describe an ```entry` in the archive simply pass a MetaDataObject to the `addDescription(MetaDataObject description)``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/ArchiveEntry.html//#addDescription%28de.unirostock.sems.cbarchive.meta.//MetaDataObject%29)) function:


```
#!java
entry.addDescription (someMetaData);
```

Read more about [None](//MetaDataObject)s.


If you have a file containing multiple description elements for a single archive entry you may just pass the file to the ```addAllDescriptions (File metaDataFile)``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/ArchiveEntry.html//#addAllDescriptions%28java.io.File%29)) function:

```
#!java
entry.addAllDescriptions (new File ("/path/to/rdf/file"));
```

This will read the file ```/path/to/rdf/file` and add all `rdf:Description` subtrees to `entry```.


#### Removing Meta Data of an Entry in the Archive 
You can of course also delete some meta data for an entry in the archive. Just pass the description to the ```removeDescription (MetaDataObject toDelete)``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/ArchiveEntry.html//#removeDescription%28de.unirostock.sems.cbarchive.meta.//MetaDataObject%29)) function:

```
#!java
entry.removeDescription (someMetaData);
```

Read more about [None](//MetaDataObject)s.

### Finalising the /CombineArchive 
While we are working directly in the /CombineArchive you need to finalise the archive after you finished working with it. That means, you need to call the following functions:

```
#!java
ca.pack ();
ca.close ();
```

* ```pack ()` ([http://jdoc.sems.uni-rostock.de/CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html#pack%28%29 JavaDoc]) will generate the manifest of the archive an write the meta data file. Thus, you do not need to call it if you did not modify the archive. By default, this method will write the meta data of all files into a single meta-data-file in the archive. There is, however, also demand for a functionality to generate multiple meta data files, one for every file in the archive. Thus, you may use the function `pack (boolean multipleMetaFiles)` ([http://jdoc.sems.uni-rostock.de/CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html#pack%28boolean%29 JavaDoc]). Passing `true``` to the call we will generate multiple meta data files.
* ```close ()``` ([JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/CombineArchive.html#close%28%29)) then closes the /CombineArchive and writes it to the disk.

### Full Example 
A complete example is available at [source:src/main/java/de/unirostock/sems/cbarchive/Example.java Example.java].
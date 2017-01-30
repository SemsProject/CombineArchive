[None](//MetaDataObject)s in [None](//CombineArchive)s 
-------------------------------------------------------
[None](//MetaDataObject)s store meta data about entries in a /CombineArchive.

All meta data in a /CombineArchive is usually stored in files with names like ```/metadata(-.+)?.rdf``` in the root of the archive.
These files contain RDF describing the entries available from the archive.
Go to [http://co.mbine.org/documents/archive co.mbine.org] to learn more about the meta data used in [None](//CombineArchive)s.

This library contains an abstract class /MetaDataObject ([ source](/src/main/java/de/unirostock/sems/cbarchive/meta//MetaDataObject.java), [JavaDoc](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/meta/MetaDataObject.html)) which represents instances of RDF description blocks, such as:

```
#!xml
<rdf:Description rdf:about="/some/file">
	<!-- some describing subtree -->
</rdf:Description>
```

There are two main subclasses extending the /MetaDataObject:
* The [Default/MetaDataObject](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/meta/DefaultMetaDataObject.html) is used to represent any kind of meta data that is not further known to the library
* The [Omex/MetaDataObject](http://jdoc.sems.uni-rostock.de///CombineArchive/de/unirostock/sems/cbarchive/meta/OmexMetaDataObject.html) represents meta data supposed to be the default meta format for [None](//CombineArchive)s.

However, your able to retrieve the plain XML subtree in any case using the ```getXmlDescription()` ([http://jdoc.sems.uni-rostock.de/CombineArchive/de/unirostock/sems/cbarchive/meta/MetaDataObject.html#getXmlDescription%28%29 JavaDoc]) method. This function will return the `rdf:Description``` element, so you can try to parse the subtree on your own.

### Write your own /MetaDataObject 
If you're developing a different kind of format to describe your files we'd like to encourage you to write your own subclass of the /MetaDataObject. Just extend the class (see [ source](/src/main/java/de/unirostock/sems/cbarchive/meta//MetaDataObject.java)) and add a function ```tryToRead (Element element, ArchiveEntry about, String fragmentIdentifier)` that takes the `rdf:Description``` element, the [ArchiveEntry](https://sems.uni-rostock.de/trac/combinearchive/browser/src/main/java/de/unirostock/sems/cbarchive///ArchiveEntry.java) that is being described and an optional fragment identifier (which might be null). To get an idea you may want to have a look at the sources of [ /DefaultMetaDataObject](/src/main/java/de/unirostock/sems/cbarchive/meta//DefaultMetaDataObject.java) and [ /OmexMetaDataObject](/src/main/java/de/unirostock/sems/cbarchive/meta//OmexMetaDataObject.java).

Afterwards add a small snippet to the [Meta/DataFile reader](/src/main/java/de/unirostock/sems/cbarchive/meta//MetaDataFile.java@be56ff2#L139) to try to parse meta data given your format.

If you finished implementing support for your meta data format please send your code to one of us. We'll be happy to support your format officially, so others may also benefit from the data that is stored in your descriptions.


Also, feel free to fork this project on [GitHub](https://github.com/binfalse///CombineArchive) :)
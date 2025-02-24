.. _import-metadata:

===============
Import metadata
===============

Archivematica can recognize descriptive, rights, and/or event metadata that is
included with your transfer. You can import metadata by including a directory
called ``metadata`` in your transfer. The directory can contain any type of
metadata that you wish to preserve alongside your digital objects. The Process
Metadata Directory Microservice will perform a number of preservation actions on
objects in this directory.

Archivematica supports conventions for importing descriptive, rights, and event
metadata that will transpose the contents of the metadata files into the AIP
METS file. If you are using :ref:`AtoM <atom-setup>` or :ref:`ArchivesSpace
<archivesspace-setup>`, metadata that is transposed to the METS can be passed on
to these access systems for further use.

Metadata that is not able to be transposed to the METS - for example,
preservation metadata created by tool like BitCurator prior to ingest into
Archivematica - will be preserved alongside the materials in the transfer.

Descriptive, rights, and/or event metadata can be added to standard, unzipped,
zipped, and disk image :ref:`transfer types <transfer-types>`.

Metadata in the METS file is searchable in the :ref:`Archival Storage
<archival-storage>` tab.

*On this page:*

* :ref:`Importing descriptive metadata with metadata.csv <metadata.csv>`

  * :ref:`Adding descriptive metadata to individual objects <object-metadata>`
  * :ref:`Adding descriptive metadata to a directory <directory-metadata>`
  * :ref:`Adding non-Dublin Core metadata <non-dc-metadata>`
  * :ref:`Adding metadata using JSON files <metadata-json>`

* :ref:`Importing and validating XML metadata with source-metadata.csv <metadata-xml-validation>`
* :ref:`Importing rights metadata with rights.csv <rights.csv>`
* :ref:`Importing event metadata with premis.xml <premis.xml>`
* :ref:`Adding metadata to bags <metadata-bags>`
* :ref:`Importing other types of metadata <other-metadata>`
* :ref:`Importing structural metadata with mets_structmap.xml <structmap.xml>`

.. seealso::

   :ref:`Create a transfer with submission documentation <create-submission>`

.. _metadata.csv:

Importing descriptive metadata with metadata.csv
------------------------------------------------

Archivematica natively supports Dublin Core's `DCMI Metadata Terms`_, which
includes the basic 15 elements from the `Dublin Core Metadata Element Set`_.
Using the metadata.csv method, users can also include :ref:`non-Dublin Core
metadata <non-dc-metadata>`. Archivematica is able to pass Dublin Core metadata
to AtoM or ArchivesSpace, but not non-Dublin Core metadata.

.. note::

   Note: Archivematica can only read CSV files that have been encoded in UTF-8.
   Metadata saved with a file encoding other than UTF-8 may not map correctly
   to the METS file, or may cause an error and not be added at all.

Descriptive metadata can be applied to individual objects within a transfer, to
directories within the transfer, or both.

.. _object-metadata:

Adding descriptive metadata to individual objects
+++++++++++++++++++++++++++++++++++++++++++++++++

Metadata can be applied to individual objects within a transfer. For example,
you could apply item-level descriptive metadata to an individual image or
multimedia file. The following steps will take you through the basic workflow
for creating a metadata.csv file that will apply metadata to individual objects.

#. Create a transfer directory containing the digital objects you would like to
   preserve (for more information on creating a basic transfer for
   Archivematica, see :ref:`Basic transfers <basic-transfers>`.) As an example,
   the following directory tree displays a basic transfer called
   ``metadataTransfer``. One digital object sits within the top-level directory,
   while another object is nested within a subdirectory.::

    metadataTransfer/
    ├── audio
    │   └── bird.mp3
    └── beihai.tif

   Note that this transfer does not currently contain any metadata.

#. Create a CSV file called metadata.csv. The example below can be used as
   a template for Dublin Core metadata. The filename column must come first and
   the filename path must always start with ``objects/``. Note that if you add
   column headings that are not in the ``dc.element`` or ``dcterms.element``
   formats, Archivematica will not indicate that the metadata is Dublin Core.

   .. csv-table::
      :file: _csv/individual-metadata.csv
      :header-rows: 1

   Note that empty columns (e.g. ``dc.contributor``) were left in to demonstrate
   the range of possible basic Dublin Core values. You do not need to keep empty
   columns.

#. Create a subdirectory called ``metadata`` and place the metadata.csv file
   inside it.::

    metadataTransfer/
    ├── audio
    │   └── bird.mp3
    ├── beihai.tif
    └── metadata
       └── metadata.csv

#. :ref:`Process the transfer <process-transfer>` as usual.

Any Dublin Core metadata formatted as in the example above will be transposed to
the METS file. The METS file for the above example includes two descriptive
metadata sections (``<dmdSec>``), one for each file in the transfer. These
contain the Dublin Core metadata parsed from the metadata.csv; they include both
``dc.element`` and ``dcterms.element`` metadata. Note in the metadata wrapper
(``<mdWrap>``) that they are given an MDTYPE of "DC". If there had been
non-Dublin Core metadata in the metadata.csv, there would be a separate
``<mdWrap>`` for the file with an MDTYPE of "OTHER".

.. literalinclude:: scripts/individual-mets.xml
   :language: xml

.. _directory-metadata:

Adding descriptive metadata to a directory
++++++++++++++++++++++++++++++++++++++++++

Metadata can also be applied to a directory within the transfer. For example,
you could apply directory-level descriptive metadata to folder containing the
pages of a book, where the metadata describes the book itself rather than the
individual pages. The following steps will take you through the basic workflow
for creating a metadata.csv file that will apply metadata to a directory of
objects.

Note that the metadata.csv can contain directory-level metadata, individual
object metadata, or a combination of both.

#. Create a transfer directory containing the digital objects you would like to
   preserve, organized as required within subdirectories (for more information
   on creating a basic transfer for Archivematica, see
   :ref:`Basic transfers <basic-transfers>`.) As an example, the following
   directory tree displays a basic transfer called ``directoryTransfer``. The
   transfer contains two subfolders, which each contain two images.::

    directoryTransfer/
    ├── CoastNews-1964-01-02
    │   ├── page-01.jpg
    │   └── page-02.jpg
    └── CoastNews-1964-01-09
        ├── page-01.jpg
        └── page-02.jpg

   Note that this transfer does not currently contain any metadata.

#. Create a CSV file called metadata.csv. The example below can be used as
   a template for Dublin Core metadata. The filename column must come first and
   the filename path must always start with ``objects/``. In the example below,
   the filename column contains the directory path, instead of a filename path.

   .. csv-table::
      :file: _csv/directory-metadata.csv
      :header-rows: 1

#. Create a subdirectory called ``metadata`` and place the metadata.csv file
   inside it.::

     directoryTransfer/
     ├── CoastNews-1964-01-02
     │   ├── page-01.jpg
     │   └── page-02.jpg
     ├── CoastNews-1964-01-09
     │   ├── page-01.jpg
     │   └── page-02.jpg
     └── metadata
         └── metadata.csv

#. :ref:`Process the transfer <process-transfer>` as usual.

The METS file that results from the above example includes a ``<dmdSec>`` for
each directory, rather than each individual file. These contain the Dublin Core
metadata parsed from the metadata.csv.

.. literalinclude:: scripts/directory-mets.xml
   :language: xml

.. _non-dc-metadata:

Adding non-Dublin Core descriptive metadata
+++++++++++++++++++++++++++++++++++++++++++

You may wish to capture metadata elements that are not supported by the Dublin
Core Metadata Elements Set. As shown in the examples above, Dublin Core metadata
is written to the ``<dmdSec>`` of the METS file with the metadata type indicated
as ``MDTYPE="DC"``. Non-Dublin Core metadata will be written into a separate
``<dmdSec>`` as ``MDTYPE="OTHER"``.

Here is an example of a metadata.csv file containing a mix of Dublin Core and
non-Dublin Core descriptive metadata.

.. csv-table::
   :file: _csv/mixed-metadata.csv
   :header-rows: 1

This results in a METS file containing two ``<dmdSec>`` sections for each
directory - two with ``MDTYPE="DC"`` and two with ``MDTYPE="OTHER"``.

.. literalinclude:: scripts/mixed-metadata-mets.xml
   :language: xml

.. _metadata-json:

Adding metadata using JSON files
++++++++++++++++++++++++++++++++

It is possible to use a JSON file rather than a CSV to import Dublin Core
metadata. The JSON file is added to the metadata directory as above.

#. Create a transfer directory containing the digital objects you would like to
   preserve (for more information on creating a basic transfer for
   Archivematica, see :ref:`Basic transfers <basic-transfers>`.) As an example,
   the following directory tree displays a basic transfer called
   ``jsonMetadataTransfer``. One digital object sits within the top-level
   directory, while another object is nested within a subdirectory.::

    jsonMetadataTransfer/
    ├── audio
    │   └── bird.mp3
    └── beihai.tif

   Note that this transfer does not currently contain any metadata.

#. Create a JSON file called metadata.json. The example below can be used as
   a template for Dublin Core metadata. The filename field must come first and
   the filename path must always start with ``objects/``. Note that if you add
   fields that are not in the ``dc.element`` or ``dcterms.element`` formats,
   Archivematica will not indicate that the metadata is Dublin Core.

   .. literalinclude:: scripts/metadata.json
      :language: json

   Repeating fields can be listed in an array, as with ``dc.subject`` in the
   example above.

#. Create a subdirectory called ``metadata`` and place the metadata.json file
   inside it.::

    metadataTransfer/
    ├── audio
    │   └── bird.mp3
    ├── beihai.tif
    └── metadata
       └── metadata.json

#. :ref:`Process the transfer <process-transfer>` as usual.

The METS file will be populated exactly as in the CSV examples above.

.. _metadata-xml-validation:

Importing and validating XML metadata with source-metadata.csv
--------------------------------------------------------------

Users can add metadata files in XML format to their transfer, and have that
metadata related to objects in the transfer and parsed into the METS file in
the AIP. If formatted correctly, Archivematica can also validate the metadata
to ensure it conforms with a local or linked standard.

Setting up
++++++++++

This feature is disabled by default. It's still considered experimental and
its validation rules need to be configured according to the user needs before
using it.

The `Metadata XML validation variables <https://github.com/artefactual/archivematica/tree/179247f9e5508defd563e4eae3c73ea8e8b674f3/src/MCPClient/install#metadata-xml-validation-variables>`_
section of the MCPClient installation documentation explains how to enable
and configure this feature. You can also check the
`sample configuration files <https://github.com/artefactual/archivematica-sampledata/tree/b01539902198a9e4a15f76add7b6b129eecf6b7b/xml-validation>`_
used in the Archivematica Automated User Acceptance Tests (AMAUATs).

Adding and validating metadata
++++++++++++++++++++++++++++++

#. Create a transfer directory containing the digital objects you would like to
   preserve (for more information on creating a basic transfer for
   Archivematica, see :ref:`Basic transfers <basic-transfers>`.) As an example,
   the following directory tree displays a basic transfer called
   ``metadataTransfer``. One digital object sits within the top-level directory,
   while another object is nested within a subdirectory.::

    metadataTransfer/
    ├── audio
    │   └── bird.mp3
    └── beihai.tif

   Note that this transfer does not currently contain any metadata.

#. Create a CSV file called ``source-metadata.csv``. It must contain three
   columns in the following order:

   * ``filename``: a relative path of a digital object or a directory in the
     transfer. It must start with ``objects/``.
   * ``metadata``: a path of an XML file in the ``metadata`` directory with
     metadata about the digital object or directory specified in the
     ``filename`` column. Optionally, this XML file can be validated
     using an XML schema and its contents are included in the METS file
     of the AIP.
   * ``type``: a unique identifier for the metadata content. This identifier
     is used during metadata reingests to update or delete existing metadata.

   This is the ``source-metadata.csv`` file we are going to use for the
   ``metadataTransfer`` directory from above.

   .. csv-table::
      :file: _csv/source-metadata.csv
      :header-rows: 1

#. Create the two metadata XML files listed in the ``metadata`` column of the
   ``source-metadata.csv`` file: ``beihai.xml`` and ``bird-dc.xml``.

   The ``beihai.xml`` metadata file contains unstructured text represented
   as a single top level ``<metadata>`` element with no XML namespace wrapping
   `character data <https://www.w3.org/TR/REC-xml/#sec-cdata-sect>`_ like
   this:

   .. literalinclude:: scripts/metadata-xml-unstructured.xml
      :language: xml

   And the ``bird-dc.xml`` metadata file contains
   `OAI-PMH <https://www.openarchives.org/pmh/>`_ Dublin Core metadata that
   could be validated against the
   `OAI-PMH Version 2.0 specification <https://www.openarchives.org/OAI/openarchivesprotocol.html>`_
   and it looks like this:

   .. literalinclude:: scripts/metadata-xml-dc.xml
      :language: xml

#. Create a subdirectory called ``metadata`` and place the ``source-metadata.csv``
   file and the two metadata XML files inside.::

    metadataTransfer/
    ├── audio
    │   └── bird.mp3
    ├── beihai.tif
    └── metadata
        ├── source-metadata.csv
        ├── beihai.xml
        └── bird-dc.xml

#. :ref:`Process the transfer <process-transfer>` as usual.

The METS file for the above example includes two descriptive metadata sections
(``<dmdSec>``), one for each file in the transfer. Both sections are given
attributes that specify their creation time and their status is set as
``original``. And the contents of the metadata XML files in the ``metadata``
directory have been transposed here.

.. literalinclude:: scripts/metadata-xml-dmdsecs.xml
   :language: xml

Note in the metadata wrapper (``<mdWrap>``) that they are given an MDTYPE of
"OTHER" and an MDOTHERTYPE with the value of the ``type`` cell from the
``source-metadata.csv`` file. This is useful for updating or deleting the
metadata files in the AIP during metadata reingests.

Updating metadata through metadata reingests
++++++++++++++++++++++++++++++++++++++++++++

You can update the metadata contained in the METS file by reingesting your
AIP and adding a new ``source-metadata.csv`` file and updated metadata XML
files from one of your transfer source locations before approving the reingest.

#. This is the new ``source-metadata.csv`` file we are going to use to
   update the metadata associated with the ``beihai.tif`` digital object
   in the previous transfer.

   .. csv-table::
      :file: _csv/source-metadata-update.csv
      :header-rows: 1

   It's important that in the ``type`` column we reuse the identifier
   (i.e. ``local``) that was set as the  ``OTHERMDTYPE`` attribute of the
   ``<dmdSec>``.

#. Create the new metadata XML file listed in the ``metadata`` column of the
   ``source-metadata.csv`` file: ``beihai-v2.xml``.

   The ``beihai-v2.xml`` metadata file contains the same structure as before
   with a couple of additional sentences:

   .. literalinclude:: scripts/metadata-xml-unstructured-update.xml
      :language: xml

#. :ref:`Start a metadata reingest <reingest>` for the previous AIP.

#. Before approving the reingest :ref:`upload the new CSV file and metadata XML file through the user interface <metadata-csv-ui>`.

#. Approve and finish the metadata reingest.

The METS file now includes a new descriptive metadata section (``<dmdSec>``)
for the ``beihai.tif`` digital object. It also contains an attribute that
specifies its creation time and its status is set as ``update``.

.. literalinclude:: scripts/metadata-xml-dmdsecs-update.xml
   :language: xml

Note that the initial descriptive metadata section for the object is still
there but its status has been updated to ``original-superseded``. Also, the
descriptive metadata section for the other digital object in the AIP has been
left intact in the process.

Deleting metadata through metadata reingests
++++++++++++++++++++++++++++++++++++++++++++

You can delete the metadata XML files contained in the AIP and ... in the METS
file by reingesting your AIP and adding a new ``source-metadata.csv`` file from
one of your transfer source locations before approving the reingest.

#. This is the new ``source-metadata.csv`` file we are going to use to
   delete the metadata associated with the ``audio/bird.mp3`` digital object
   in the previous transfer. Here we assume this is the second metadata reingest
   of the AIP after the one in the previous section.

   .. csv-table::
      :file: _csv/source-metadata-delete.csv
      :header-rows: 1

   It's important that in the ``type`` column we reuse the identifier
   (i.e. ``dc-xml``) that was set as the  ``OTHERMDTYPE`` attribute of the
   ``<dmdSec>``. Note that we signal the deletion request by leaving the
   ``metadata`` value empty.

#. :ref:`Start a metadata reingest <reingest>` for the previous AIP.

#. Before approving the reingest :ref:`upload the new CSV file through the user interface <metadata-csv-ui>`.

#. Approve and finish the metadata reingest.

In the METS file the status attribute of the descriptive metadata section
(``<dmdSec>``) of the ``audio/bird.mp3`` digital object has been updated
from ``original`` to ``deleted``.

.. literalinclude:: scripts/metadata-xml-dmdsecs-delete.xml
   :language: xml

After this the ``bird-dc.xml`` file has been deleted from the
``objects/metadata`` directory in the AIP. And note that the descriptive
metadata sections for the other digital object in have been left intact in
the process.

.. _rights.csv:

Importing rights metadata with rights.csv
-----------------------------------------

Rights information can be associated with objects or directories in a transfer
by creating a rights.csv file, similar to the metadata.csv file used for
descriptive metadata. Archivematica implements :ref:`PREMIS metadata in
Archivematica <premis-template>`, including PREMIS rights, as used here. Rights
metadata added using the rights.csv will be transposed to the METS file.

Rights metadata can be applied to individual objects within a transfer, to
directories within the transfer, or both.

#. Create a transfer directory containing the digital objects you would like to
   preserve. As an example, the following directory tree displays a basic
   transfer called ``rightsTransfer``. One digital object sits within the
   top-level directory, while another object is nested within a subdirectory.::

    rightsTransfer/
    ├── audio
    │   └── bird.mp3
    └── beihai.tif

#. Create a file called rights.csv. The example below can be used as
   a template for PREMIS rights metadata. The filename column must come first
   and the filename path must always start with ``objects/``.

   .. csv-table::
      :file: _csv/rights.csv
      :header-rows: 1

   Note that to enter multiple rights acts for the same basis, you must create
   two separate rows, as with rows 1 and 2 in the example above.

#. Create a subdirectory called ``metadata`` and place the rights.csv file
   inside it.::

    rightsTransfer/
    ├── audio
    │   └── bird.mp3
    ├── beihai.tif
    └── metadata
        └── rights.csv

#. :ref:`Process the transfer <process-transfer>` as usual.

Any PREMIS rights metadata formatted as in the example above will be transposed
to the METS file. The METS file for the above example includes three rights
metadata sections (``<mets:rightsMD>``).

.. literalinclude:: scripts/rights-mets.xml
   :language: xml

.. _premis.xml:

Importing event metadata with premis.xml
----------------------------------------

In some cases, digital preservation events may occur on digital objects
prior to their transfer into Archivematica. You can log information about these
events using the PREMIS standard in a XML file.

#. Create a transfer directory containing the digital objects you would like to
   preserve. As an example, the following directory tree displays a basic
   transfer called ``eventsTransfer``. One digital object sits within the
   objects subdirectory, while a premis.xml file sits in the metadata
   subdirectory.::

    eventsTransfer/
    ├── objects
    │   └── bird.mp3
    └── metadata
        └── premis.xml

#. To create the premis.xml file containing the event information you can use
   the example below as a template.

   .. literalinclude:: scripts/premis.xml
      :language: xml

#. Each premis.xml file should contain one or more ``premis:object``,
   ``premis:event`` and ``premis:agent`` entity. Each entity should have its own
   local identifier value for linking within the file. These will be converted
   to Archivematica UUIDs upon import. One Object can be linked to many Events.
   Each Event should be linked to one Agent.

#. Note that the ``premis:originalName`` value is what links the ``premis:object``
   to the ``objects\bird.mp3`` file. Objects are linked to Events via the
   ``premis:linkingEventIdentifierValue`` property.

#. Note that the ``premis:eventDateTime`` value should be in ISO 8601 format
   (e.g. 2019-07-04T22:46:07.773391+00:00)

#. :ref:`Process the transfer <process-transfer>` as usual. Check the
   ``Characterize and extract metadata`` microservice on the Transfer tab and
   look at the status of the ``Load PREMIS events from metadata/premis.xml`` job
   to determine whether the import was successful.

.. _metadata-bags:

Adding metadata to bags
-----------------------

Metadata can also be added to transfers packaged in accordance with the Library
of Congress `BagIt`_ specification (zipped or unzipped). Special care needs to
be taken in order to ensure that Archivematica can recognize the metadata files
and process them properly.

#. Create your metadata files. In this example, we will create a metadata.csv to
   add descriptive metadata to the individual objects in the bag.

   .. csv-table::
      :file: _csv/bag-metadata.csv
      :header-rows: 1

   Note that the filename path anticipates the structure of the bag that we will
   create below by including the payload directory (``data``) in the path. If
   your transfer includes subdirectories, those should be reflected in the path
   as well.

#. Assemble your transfer materials. In this example, there are two objects, one
   of which is contained in a subdirectory, as well as the metadata directory
   containing a metadata.csv file.::

    to-be-transferred/
    ├── audio
    │   └── bird.mp3
    ├── beihai.tif
    └── metadata
        └── metadata.csv

#. Create a bag containing the material that you would like to preserve as well
   as the metadata. The digital objects and the metadata directory have been
   added to the payload directory (``data``).::

     bagTest/
     ├── bag-info.txt
     ├── bagit.txt
     ├── data
     │   ├── audio
     │   │   └── bird.mp3
     │   ├── beihai.tif
     │   └── metadata
     │       └── metadata.csv
     ├── manifest-md5.txt
     └── tagmanifest-md5.txt

   Note that when the metadata.csv was created in step 1, above, this structure
   had to be anticipated. Material that you add to a bag will always be inside
   the payload directory, so the filename path must always begin with ``data``.

   The bagging program has also created the required bag files.

#. :ref:`Process the transfer <process-transfer>` using the Unzipped Bag or
   Zipped Bag transfer type.

The METS file in the resulting AIP will include the metadata in the ``<dmdSec>``
as in the non-bagged examples above.

The above method can be used for metadata.csv files that apply metadata to
a directory, for rights.csv files, and for other types of metadata that you wish
to preserve.

.. note::
   Submission documentation can also be added to a bag using the above method.
   The ``submissionDocumentation`` directory should be nested inside the
   metadata directory. See :ref:`Submission documentation <create-submission>`
   for more information.

.. _other-metadata:

Importing other types of metadata
---------------------------------

You can include other types of metadata files in the ``metadata`` directory
other than the metadata.csv and rights.csv files. For example, you may want to
preserve the output of a specialized analysis tool alongside the file. You can
place these files in the metadata directory.::

  rightsTransfer/
  ├── video.mp4
  └── metadata
      ├── qctools.xml.gz
      └── rights.csv

Archivematica will run various preservation tasks on this file, just as it would
on the metadata.csv or rights.csv. In the METS, the file will be listed in the
file section (``<fileSec>``) alongside any other metadata files, under the file
group (``fileGrp``) usage heading ``metadata``.

.. literalinclude:: scripts/metadata-filesec.xml
   :language: xml

.. _structmap.xml:

Importing structural metadata with mets_structmap.xml
-----------------------------------------------------

The files transferred to Archivematica may have a coherent hierarchical
or logical structure (e.g. sections of a book or parts of an audio file) that
has already been described in a METS structural map. Users can import these by
including a file called ``mets_structmap.xml`` in their transfer's
``metadata`` directory. The files referenced in this structural map should be
included in the transfer's ``objects`` directory.

Archivematica will merge this structural map into the archival information
package's METS file by assigning it a unique structural map ID. It will also
update the file pointers (``mets:fptr``) to use the UUIDs created by
Archivematica for the files in its archival information packages.

Note that Archivematica requires that ``CONTENTIDS`` attributes in ``mets:fptr``
elements must be used with the ``objects/`` prefix to correctly map files to
IDs.

**Example mets_structmap.xml**

Using a minimal structural map example for an audio file:

.. literalinclude:: scripts/transfer-minimal-structmap.xml
   :language: xml

The resulting output in the Archivematica AIP METS file will be:

.. literalinclude:: scripts/structmap-in-mets.xml
   :language: xml

Here is another example of a custom METS structural map for a simple book. The
transfer's ``objects/`` directory contains all of the digital files used to
compile the book (e.g. cover.jpg, inside_cover.jpg, page_01.jpg, etc.) The
transfer's ``metadata/`` directory contains the following ``mets_structmap.xml``
file to define the structure of the book. Upon import, Archivematica will add a
unique ID to the ``structMap`` element and update all the ``FILEID`` attributes
to match the UUID value for these files in the Archivematica AIP.

.. literalinclude:: scripts/book-structmap.xml
   :language: xml

The resulting output in the Archivematica AIP METS file will be:

.. literalinclude:: scripts/mets-book-structmap.xml
   :language: xml

:ref:`Back to the top <import-metadata>`

.. _DCMI Metadata Terms: https://www.dublincore.org/specifications/dublin-core/dcmi-terms/
.. _Dublin Core Metadata Element Set: https://www.dublincore.org/specifications/dublin-core/dces/
.. _`BagIt`: https://tools.ietf.org/html/rfc8493

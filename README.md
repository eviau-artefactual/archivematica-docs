# Archivematica documentation

[Archivematica](https://www.archivematica.org/en/) is a web- and
standards-based, open-source application which allows your institution to
preserve long-term access to trustworthy, authentic and reliable digital
content.

Our target users are archivists, librarians, and anyone working to preserve
digital objects.

You are free to copy, modify, and distribute the Archivematica documentation
with attribution under the terms of the Creative Commons Attribution Share Alike
4.0 (CC-BY-SA-4.0) license. See the [LICENCE](LICENCE) file for details.

## Building the documentation locally

To build a local, offline version of the documentation:

* Decide where the documentation will be stored on your computer.
* In a Terminal window, use the `cd` command to navigate to this location.
* Create a local copy of the documentation by running:

```bash
git clone https://github.com/artefactual/archivematica-docs.git
```

* Move to the documentation repository with:

```bash
cd archivematica-docs
```

* Create a Python virtual environment to contain all the required tools:

```bash
python3 -m venv .env
```

* Activate the virtual environment:

```bash
source .env/bin/activate
```

* Install the requirements:

```bash
pip install -r requirements.txt
```

* Build the documentation:

```bash
sphinx-build -D language=en ./ _build/html/en # for English

sphinx-build -D language=es ./ _build/html/es # for *español* (Spanish)

sphinx-build -D language=fr ./ _build/html/fr # for *français* (French)

sphinx-build -D language=pt_BR ./ _build/html/pt_BR # for *português do Brasil* (Brazilian Portuguese)
```

* Access the documentation:

```bash
python3 -m http.server --d _build/html
```

The HTML files for the documentation will be in
`archivematica-docs/_build/html/`.
You can open the files in a browser of your choice, without having any access
to the Internet, by accessing `http://localhost:8000`.

While this offline version will not have the Archivematica web theme, you will
gain access to improved search features.

This local version will contain only the Archivematica documentation; notably
the Archivematica Storage Service docs will be missing. Instructions on how to
build a local, offline version of the Archivematica Storage Service is
available on its [GitHub repo](https://github.com/artefactual/archivematica-storage-service-docs).

## Contributing

Thank you for considering a contribution to the Archivematica documentation! For
more information on contributing, please see the [Archivematica docs Github
wiki](https://github.com/artefactual/archivematica-docs/wiki). The wiki
describes the change submission process for the Archivematica docs and includes
a style guide for new contributions. Following these guidelines helps us assess
your changes faster and makes it easier for us to merge your submission.

Note that some documentation lives in separate repositories:

* [archivematica-docs](https://github.com/artefactual/archivematica-docs):
  This repository! Contains the main user and administrator documentation.
* [archivematica-storage-service-docs](https://github.com/artefactual/archivematica-storage-service-docs):
  Contains documentation for the Storage Service, Archivematica's
  archival storage management system.

### Security

If you have a security concern about Archivematica or any of its companion
repositories, please see the
[Archivematica security policy](https://github.com/artefactual/archivematica/security/policy)
for information on how to safely report a vulnerability.

# Data Services Suite for Wikibase
This is a container-based suite of applications providing data services to an existing Wikibase installation. It is **based** on [Wikibase Suite](https://github.com/wmde/wikibase-release-pipeline).
I ndetails, this suite offer a containerized versions of the follwing tools:
* SPARQL endpoint
* QuickStatements
* ElasticSearch

This suite is aimed to users with existing Wikibase instance who needs to quickly deply additional services that are somehow complicated to be setup as standalone applications.
This suite uses Docker containers of the applications with heavily-customized configurations files in order to adapt the suite to own needs.

This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Get started
This readme file is a general introduction to Data Services Suite,  detailed instructions are available in the *deploy*-specific [`README.md`](deploy/README.md) file, more in-depth technical documentation is abailable in the subfolder [`/docs`](https://github.com/lucamauri/data-services-wikibase/tree/main/docs) of this repository. 

## Why not a fork?

## Acknowledgements
First and foremost, an acknowldgement to [Wikimedia Deutschland e. V.](https://github.com/wmde) for their work on [Wikibase Suite](https://github.com/wmde/wikibase-release-pipeline) that represents the foundation of this project.

Additionally:
 - [Awesome Readme Templates](https://awesomeopensource.com/project/elangosundar/awesome-README-templates)
 - [Awesome README](https://github.com/matiassingers/awesome-readme)
 - [How to write a Good readme](https://bulldogjob.com/news/449-how-to-write-a-good-readme-for-your-github-project)

## License
This software is licensed under the [GNU General Public License v3.0 (GPLv3)](https://www.gnu.org/licenses/gpl-3.0.en.html)

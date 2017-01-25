# OpenLMIS Module Template
This template is meant to be a starting point for developing a new 
OpenLMIS 3.x Extension Module.

## Prerequisites
* Docker 1.11+
* Docker Compose 1.6+

## Starting a new module
1. Fork/clone this repository from GitHub.

 ```shell
 git clone https://github.com/OpenLMIS/openlmis-template-module.git <openlmis-your-module-name>
 ```
2. Respectively change all instances of `openlmis-template-module` and `template-module` within the project 
to `openlmis-your-module-name` and `your-module-name`.
3. Change project name and description in `gradle.properties`
4. Declare the service to be extended as a dependency using maven, e.g.:

 ```
dependencies {
    compile "org.openlmis:openlmis-requisition:3.0.0-SNAPSHOT"
}
 ```
5. Develop w/ Docker by running `docker-compose run --service-ports <your-module-name>`. 
6. You should now be in an interactive shell inside the newly created development environment. 
7. Add java code to the template. See [Adding extension points](#addingextensions).
8. Assemble the outputs of project and create jar file by running `gradle assemble`
9. Copy the generated jar to the `extensions` volume of extended service and add your module as a dependency.
[Read more](https://github.com/OpenLMIS/openlmis-example-extensions#adding-extension-points)

### <a name="addingextensions">Adding extension points</a>
1. Annotate your implementation of the extension point with @Component and put its name in the value.
```
@Component("CustomExtendableComponent")
public class CustomExtendableComponent implements ExtendableComponent {
...
```
2. Update `extensions.properties` in the `extensions` volume of corresponding service with your components's id, e.g.:
```
# Extension configuration of some service
ExtendableComponent=CustomExtendableComponent
```

### Publishing the module to maven
1. The module needs to be signed with GnuPG key before publishing. Follow the instructions 
[here](http://blog.sonatype.com/2010/01/how-to-generate-pgp-signatures-with-maven/) if your don't have one.
2. Move your secret keyring (`secring.gpg`) to the `.signing` directory.
3. Edit `.env` file and change `SIGNING_KEYID` and `SIGNING_PASSWORD` with your key id and password respectively.
4. By default, the template module is configured to publish to Sonatype OSSRH. 
You can modify the repository urls inside `uploadArchives` gradle task.
5. Put your repository id and password in the `.env` file.
6. Run the builder. It should assemble the outputs, sign archives and publish your artifacts.
```shell
> docker-compose -f docker-compose.builder.yml run builder
```

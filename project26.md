# Setting up private repositories and Preparing CI pipelines with Jenkins
In this project, we have quite a lot of exciting things to do. Lets get right into it.

As a follow up from previous project, the first thing you will need to do is create a repository in Artifactory which will serve as a private docker registry. Having a private registry in an enterprise means that there is more control over the containers deployed there. You have control over the secured traffic to the registry, control over storage and you can rely more on the availability of your images.

In Artifactory, you can setup either local, remote or virtual repositories. It doesn’t matter if it is for docker, helm, or standard software binaries.

Artifactory hosts two kinds of repositories: local and remote. Both local and remote repositories can be aggregated under virtual repositories, in order to create controlled domains for artifacts resolution and search, as we will see in the next sections.

#### Local Repositories

Local repositories are physical locally-managed repositories that one can deploy artifacts into. Artifacts under a local repository are directly accessible via a URL pattern such as 
* https://tooling.artifactory.sandbox.svc.darey.io/artifactory/<local-repository-name>/<artifact-path>.

#### Remote Repositories

A remote repositories serves as a caching proxy for a repository managed at a remote URL (including other Artifactory remote repository URLs). Artifacts are stored and updated in remote repositories according to various configuration parameters that control the caching and proxying behavior. You can remove cached artifacts from remote repository caches but you cannot manually deploy anything into a remote repository.

Artifacts under a remote repository are directly accessible via a URL pattern of

https://tooling.artifactory.sandbox.svc.darey.io/artifactory/<remote-repository-name>/<artifact-path> or
https://tooling.artifactory.sandbox.svc.darey.io/artifactory/<remote-repository-name>-cache/<artifact-path>
The second URL will only serve already cached artifacts while the first one will fetch a remote artifact in the cache URL (2nd one) only if it is not already stored in the first URL.

#### Virtual Repositories
A virtual repository (or “repository group”, if you prefer this term) aggregates several repositories under a common URL. The repository is virtual in the sense that you can resolve and get artifacts from it but you cannot deploy anything to it.

By default, Artifactory uses a global virtual repository that is available at https://tooling.artifactory.sandbox.svc.darey.io/artifactory/repo This repository contains all local and remote repositories.

### Lets create a local repository for docker.
1. Click on administration, Repositoris , and **Add repositories as shown in the image below.


2. Since we are exploring Local Repositories, select Local Repository


3. Type Docker in the search box and select the Docker icon
4. In the Repository Key box, type in the name of the repository you wh=ish to create. For example tooling. So that all the docker images for tooling app can be pushed there.


5. Click on the Create Local Repository button.


6. Now you can see that a Docker Repository for tooling has been created.


7. Create a second Local Repository for Jenkins

Lets create a Virtual Repository
Remember, a virtual repository aggregates several repositories under a common URL. You can get artifacts from it but you cannot deploy anything to it.

Select virtual Repository 
Name the virtual Repository as you deem fit, click on the create Virtual Repository button


Now that the virtual repository is created, it is time to add local repositories to it. You can see in the image below that it is a virtual repo, and it will only be used by Docker.


Scroll down the page to see the local repositories. This is where you select which local repository that will be part of the virtual repository. Click on the double arrows to move them.


Once moved, you will see them in the included items section.




To see the address of the virtual repository, simply click on the square icon at the top left, click on Artifacts and select the repository you wish to see more information on.


Push docker images to the repository
You can either pull and push docker images to the local repository for each application, or simply pull from the virtual repository.

Lets get docker images from docker hub and push to our private registry.



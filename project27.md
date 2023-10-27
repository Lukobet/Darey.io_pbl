# Deploy and Manage Business Application in Kubernetes – Helm | Kustomize | GitOps
Based on your experience on previous projects, you already have a grasp of Helm by now. But you should also know that there are multiple choices available when it comes to deploying applications into kubernetes. So, Helm should not always be your default choice. It is important to be aware of the options available, know the pros and cons, and choose what works for your team or project.

Lets quickly discuss the different options, and make an informed decision before starting the actual work.

1. Write YAML files and deploy with kubectl – This is the easiest method where you write YAML for deployments, services, ingress, and all of that, and then deploy with kubectl apply -f <YAML-FILE>. This is usually the default way when getting started with kubernetes. OR during development and exploration. But it is not sufficient or reliable when it comes to managing the infrastructure in production. It is hard work to keep track of multiple yaml files. You can imagine what will be the fate of your project if there are tens or hundreds of microservices/applications that needs to be managed across multiple environments. With this type of approach, you will end up in a PEBKAC Situation

2. Use a templating engine like HELM – You already know about Helm. Its value propositions to install applications, manage the lifecycle of those applications across multiple environments, and customise applications through templating. Without going deeper into its obvious benefits, it also has its downsides too. for example;

* Helm only adds value when you install community components. Tools you can easily find on artifacthub.io Otherwise you need to write yaml anyway.
* Leads to too much logic in the templates (This is not good for inexperienced kubernetes users, and a problem to hiring managers)
* Learning another DevOps tool. Always be careful about introducing yet another tool into your team/project. Similar to number 3 above, it slows down the project.
* Everyone’s Kubernetes cluster is different. Your needs are different. You might need to make a change to the way the chart is deployed – like changing a small piece of configuration in the templates folder. But, like a remote control, you’ve only got a limited set of controls which are exposed to you (the values that are exposed in the values.yaml file). If you want to make any deeper changes to the Helm chart, you’ll need to fork it and change it yourself. (This is a bit like taking a remote control apart, and adding a new button!)

3. Kustomize Overlays – To overcome the challenges of Helm identified above, using a tool that is already embeded as part of kubectl; which you are already familiar with makes more sense for most use cases. You will get to see how Kustomize works shortly. For now, understand that another option is to use Overlays, instead of a templating engine. The downside to this is that it does not manage the lifecycle of your aplications like Helm is able to do. Therefore, you will need to use other methods alongside Kustomize for this.
Before we make a final decision on how we will realistically manage deployments into Kubernetes, lets see how Kustomize works.

# How Kustomize Works
Kustomize uses a file called kustomization.yaml that contains declarative specifications to what resources need to be imported from what manifest files and what changes need to be made. Once it has processed the resources, it emits them to the standard output, which can be stored in a file or directly used with kubectl to apply it to a particular cluster.

One of the excellent use cases of Kustomize is to manage Kubernetes resources for multiple environments. For Kustomize to work in that scenario, you would need a base directory that would contain all manifest files with all the common elements and an overlays directory that contains all the differences for a particular environment.

To understand better, let’s look at a hands-on example.

Installing Kustomize
Kustomize comes pre bundled with kubectl version >= 1.14. You can check your version using kubectl version. If version is 1.14 or greater there’s no need to take any steps.

For a stand alone Kustomize installation(aka Kustomize cli), go through the official documentation to install Kustomize – Here

Working with Kustomize
kustomize is a command line tool supporting template-free, structured customization of declarative configuration targeted to k8s-style objects.

Targeted to k8s means that kustomize has some understanding of API resources, k8s concepts like names, labels, namespaces, etc. and the semantics of resource patching.

kustomize is an implementation of Declarative Application Management (DAM). A set of best practices around managing k8s clusters.

It is a configuration management solution that leverages layering to preserve the base settings of your applications and components by overlaying declarative yaml artifacts (called patches) that selectively override default settings without actually changing the original files.

Kustomize relies on the following system of configuration management layering to achieve reusability:

Base Layer – Specifies the most common resources
Patch Layers – Specifies use case specific resources
Let’s step through how Kustomize works using a deployment scenario involving 3 different environments: dev, sit, and prod. In this example we’ll use service, deployment, and namespace resources. For the dev environment, there wont be any specific changes as it will use the same configuration from the base setting. In sit and prod environments, the replica settings will be different.

Using the tooling app for this example, create a folder structure as below.

└── tooling-app-kustomize
    ├── base
    │   ├── deployment.yaml
    │   ├── kustomization.yaml
    │   └── service.yaml
    └── overlays
        ├── dev
        │   ├── kustomization.yaml
        │   └── namespace.yaml
        ├── prod
        │   ├── deployment.yaml
        │   ├── kustomization.yaml
        │   └── namespace.yaml
        └── sit
            ├── deployment.yaml
            ├── kustomization.yaml
            └── namespace.yaml
Now, lets walk through the content of each file.

Base directory
tooling-app-kustomize/base/deployment.yaml – This is a standard kubernetes yaml file for a deployment.

apiVersion: apps/v1
kind: Deployment
metadata:
  name: tooling-deployment
  labels:
    app: tooling
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tooling
  template:
    metadata:
      labels:
        app: tooling
    spec:
      containers:
      - name: tooling
        image: dareyregistry/tooling-app:1.0.2
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
tooling-app-kustomize/base/service.yaml – This is a standard kubernetes yaml file for a service.

apiVersion: v1
kind: Service
metadata:
  name: tooling-service
  labels:
    app: tooling
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    name: http
  type: ClusterIP
  selector:
    app: tooling
tooling-app-kustomize/base/kustomization.yaml – This is a Kustomization declaritive yaml that is used to let kustomize know what resources to create and monitor in kubernetes.

apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - deployment.yaml
  - service.yaml
The resources being monitored here are deployment and services. You can simply add more to the list as you wish.

It is assumed that we will need to deploy kubernetes resources across multiple environments, as a standard practice in most cases. Hence, to deploy resources into the Dev environment, lets see what the layout and file contents will look like.

DEV Environment
In the overlays folder – This is where you manage multiple environments. In each environment, there is a Kustomize file that tells Kustomize where to find the base setting, and how to patch the environment using the base as the starting point.

In the dev environment for example, the namespace for dev is created, and the deployment is patched to use a replica count of “3” different from the base setting of “1”. So Kustomize will simply create all the resources in base, in addition to whatever is specified in the dev directory. We will discuss patching a little further in the following section.

Lets have a look at what each file contains.

tooling-app-kustomize/overlays/dev/namespace.yaml

apiVersion: v1
kind: Namespace
metadata:
  name: dev-tooling
tooling-app-kustomize/overlays/dev/deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: tooling-deployment
spec:
  replicas: 3
tooling-app-kustomize/overlays/dev/kustomization.yaml

apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: dev
bases:
- ../../base

commonLabels:
  env: dev-tooling

resources:
  - namespace.yaml
The Kustomization file for dev here specifies that the base configuration should be applied, and include the yaml file(s) specified in the resources section. It also indicates what namespace the configuration should be applied to.

In summary it specifies the following;

The apiversion
The Kind of resource (kustomization)
The namespace where this kustomizaton will create or patch resources
The location of the base folder, where the base configuration can be found.
The resource(s) to be created – Such as a namespace or deployment
A commonLabel field which ensures that kubernetes labels and selectors are automatically injected into the resources being created. such as below;

Generally, A kustomization file contains fields falling into four categories (although not all have been used in the example above):

resources – what existing resources are to be customized. Example fields: resources, crds.

generators – what new resources should be created. Example fields: configMapGenerator (legacy), secretGenerator (legacy), generators (v2.1).

transformers – what to do to the aforementioned resources. Example fields: namePrefix, nameSuffix, images, commonLabels, etc. and the more general transformers (v2.1) field.

meta – fields which may influence all or some of the above. Example fields: vars, namespace, apiVersion, kind, etc.

Patching configuration with Kustomize

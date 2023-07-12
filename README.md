# DevOps Project
This repository contains a mini project which I made to study some tools from the DevOps world.

CI/CD, or continuous integration and continuous delivery, is an essential part of the modern software development life cycle. Coupled with GitOps, CI/CD allows developers to release high-quality software almost as soon as they commit code to a repository such as GitHub.

The core of this project is to create a CI/CD pipeline using GitHub Action and ArgoCD to implement a GitOps scenario. The image below shows us a GitOps scenario that contains two repositories. The first repository is where we have the code of our application and the second is where we have our infrastructure repository.  

![Architecture diagram](core.png)

The repository that contains the code is: https://github.com/leohauschild/simple-voting-app  
And the second repository which contains the infrastructure code is the same as where I am writing.
# Infrastructure

[Cluster Provision](/provision):  
* For this project, I am using [Kind](https://kind.sigs.k8s.io/) to implement a local Kubernetes Cluster.
* I am using [Terraform](https://www.terraform.io/) to deploy the Kind local Custer.
* On the same Terraform code, I am using [Helm provider](https://registry.terraform.io/providers/hashicorp/helm/latest/docs) on Terraform to install ArgoCD.
* After Terraform deploys all of my resources I start the configurations of [ArgoCD using their CLI](https://argo-cd.readthedocs.io/en/stable/getting_started/).

# CI Workflow
The CI workflow in this [repository](https://github.com/leohauschild/simple-voting-app) is designed to automate the process of building and deploying container images for each microservice. Whenever a code change is pushed or a pull request is opened, the following steps are executed:

- **Code Change Detection:** GitHub Actions automatically detects changes in the codebase and triggers the workflow.  
- **Build Process:** The workflow starts by building a new container image for each modified microservice. This step ensures that the image reflects the latest changes in the codebase.  
- **Image Tagging:** The newly built container images are tagged with a unique identifier, using [semantic versioning](https://semver.org/) (like v1.0.1). This tagging facilitates tracking and differentiation of images.  
- **Push to Container Registry:** The workflow pushes the newly built images to a container registry. This step ensures that the images are accessible and ready for deployment.

# Continuous Deployment (CD) with GitOps and Argo CD
In addition to Continuous Integration (CI), I have implemented Continuous Deployment (CD) to automate the process of deploying our microservices using GitOps principles and Argo CD.

## Deployment Workflow
My CD workflow is designed to seamlessly deploy the microservices whenever there is a change in the container image tags. Here's a high-level overview of the process:

- **Image Tag Change Detection:** When a new container image is built and tagged during the CI process, the CD workflow is triggered to initiate the deployment.  
- **GitOps Approach:** We leverage GitOps principles, with the help of Argo CD, to manage and automate our deployments. Argo CD uses a declarative approach, where the desired state of the deployment is stored in a Git repository.
- **Git Repository Update:** The CD workflow automatically updates the Git repository with the new image tag for the respective microservice.
- **GitOps Synchronization:** Argo CD continuously monitors the Git repository and detects changes in the desired state. Upon detecting a change, Argo CD automatically synchronizes the deployment to match the desired state, triggering the deployment of the new image.
- **Deployment Rollout:** Argo CD orchestrates the rollout of the new image to the target environment, ensuring that the changes are applied gradually and in a controlled manner. This allows for easy rollbacks in case of any issues or failures.
- **Verification and Validation:** Throughout the deployment process, Argo CD monitors the health and status of the deployed microservices, performing validation checks to ensure that the deployment is successful.


# Integrating Overleaf with a CV Repository and a Web Page

This guide aims to describe how to push a CV to a GitHub repository, and update the PDF on the repository. Note that Overleaf does not synchronize PDFs to a GitHub repo, therefore they need to be recompiled there. The guide also includes pushing the compiled PDF to another web page repository. In this example, I assumed the web page is hosted as part of a GitHub organization, and the CV repo is owned separately.

Pushing changes from the Overleaf project to the CV repository in GitHub will trigger:

* Update the GitHub repo.
* Compile PDF using the xu-cheng/latex-action@v3 action. The compiled PDF will be inside a Docker image, this needs to be moved back to the repository itself.
* Copy the PDF back to the CV repo using the mikeal/publish-to-github-action@master action.
* Push the PDF to a GitHub page hosted separately as an Organization Web Page, to the desired directory.
* The Organization Web Page will be rebuilt by the GitHub Pages.

# Disclaimers

* The documentation provided here is unofficial and probably not the best there can be.
* This guide does not necessarily represent best practices:
  - Certified ["It works on my machine"](https://blog.codinghorror.com/the-works-on-my-machine-certification-program/)
* Provided code snippets are likely not optimal, but they do the job.
  - Yes, [it works on my machine](https://blog.codinghorror.com/the-works-on-my-machine-certification-program/)

# The Set Up

* Create a GitHub repository for your CV.
* First, in Overleaf, go to the project that you created to edit your CV. Set up the GitHub synchronization by [following the official guide](https://www.overleaf.com/learn/how-to/GitHub_Synchronization).
* Create an access token in your GitHub account. You probably already have done this for other repositories, but a separate token for the CV may be a good idea, with limited permissions. If you forgot how to do this:
  * Go to Settings - Developer Settings - Personal Access Tokens - Tokens (classic)
  * Generate New Token: give "repo" and "workflow" permissions. Give it a meaningful name, e.g., "CV_TOKEN".
  * Copy the token, you will need this.
* Go to your CV repository Settings - Security - Secrets and Variables - Actions.
  * Create a New Repository Secret. Give a meaningful name again, CV_TOKEN should work. Paste the token you created in the previous step to the Secret field.
* Create the directory ".github/workflows" in your CV repository, and create a YAML file: makefile.yml or main.yml. Other names will probably work.
* Here is my [makefile.yml](https://github.com/calkan/cv/blob/master/.github/workflows/makefile.yml). Copy it and edit it that will work in your repository setting.

---
Title: Delete Azure DevOps project wiki using Az CLI
Published: 2021-12-09 22:00:00 +0000
Tags: 
  - Azure DevOps
  - Az CLI
---

If you use [Azure DevOps](https://dev.azure.com), then I'm sure that you come across with this screen when you select the Wiki option in the Overview left icon:

![Azure DevOps wiki screen](/images/azure-devops-wiki-create-screen.png)

For those that are not familiar with this feature, if you select the option "Create project wiki", Azure DevOps will immediately, and without requiring any kind of confirmation, present you with an online editor where you can right away start writing and publishing content to your newly created wiki. 

If on the other hand you select the "Publish code as wiki" option, you'll be presented with a screen where you can select a repository, branch and folder, that will serve as the root folder for your wiki. This is a great scenario because in the same DevOps project you can have multiple wikis, hosted in different repos or in different folders of a single repo.

One thing that both options have in common is that behind the scenes the wiki is just markdown files. With the second option you chose where those files are stored but with the first one DevOps will create a default wiki repository that you apparently can't control in any way using the DevOps interface.

In my specific case that was a problem because I accidently clicked on the first option, and I was left without any apparent way to undo my mistake and to go back to the "Publish code as wiki" scenario. Everytime I went back to the wiki screen I could only see the editor and no options to remove the wiki from the project.

With a bit of research I was able to find a very simple solution for this problem, and [Azure CLI](https://docs.microsoft.com/en-us/cli/azure) was again my best friend. 

Using this tool, and the respective [DevOps extension](https://docs.microsoft.com/en-us/cli/azure/devops/extension?view=azure-cli-latest), you can easily find the ID of that default repository using the following command: 

```shell
 az devops wiki list -p "{repository-name}" --organization "https://dev.azure.com/{organization-name}"
```

The output of that command will give you the details of the default repo that was created to host the wiki, including the necessary Id that you can now use to permanently remove that repository from your project:

```shell
az repos delete -p "{repository-name}" --organization "https://dev.azure.com/{organization-name}" --id {repository-id}
```

After running the above, you can now go back to Azure DevOps and to the wiki screen and you'll again be presented with the two options to choose from.

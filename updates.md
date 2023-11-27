# Updates



### November 2023 (2.17.0)

Welcome to the 2.17.0 release of Terrakube. There are a couple of updates in this version that we hope you'll like, some of the key highlights include:

#### Submodules view

You can now view the submodules of your Terraform Module in the open registry. When you select a submodule, you will see its details, such as inputs, outputs and resources.

![image](https://github.com/AzBuilder/terrakube/assets/27365102/5ef297db-f65c-452f-aea7-bc0ee07a232b)

![image](https://github.com/AzBuilder/terrakube/assets/27365102/6a82dc09-1935-44b3-ae09-fa5797c69a8e)

#### Workspace Overview

We have enhanced the workspace overview to give you more information at a glance. You can now see the Terraform version, the repository, and the list of resources and outputs for each workspace.

![image](https://github.com/AzBuilder/terrakube/assets/27365102/5910b408-b941-4168-967d-d7405fd7541b)

![image](https://github.com/AzBuilder/terrakube/assets/27365102/0567a0f2-3159-4563-b6f9-a160c804302a)

#### Team API Tokens

You can now generate team API tokens using the Terrakube UI and specify their expiration time in days/minutes/hours. This gives you more flexibility and control over your team’s access to Terrakube. To learn more, please visit our [documentation](https://docs.terrakube.io/user-guide/organizations/api-tokens#user-api-tokens-1).

![image](https://github.com/AzBuilder/terrakube/assets/27365102/185b5ec3-c11f-4005-9e57-05d236ca99b3)

![image](https://github.com/AzBuilder/terrakube/assets/27365102/baf010ef-d190-4894-89ab-36f0db4fc033)

### Terraform provider for Terrakube

You can use the [Terrakube provider](https://registry.terraform.io/providers/AzBuilder/terrakube/latest/docs) for Terraform to manage modules, organizations and so on.

```
provider "terrakube" {
  endpoint = "http://terrakube-api.minikube.net"
  token    = "(PERSONAL ACCESS TOKEN OR TEAM TOKEN)"
}

data "terrakube_organization" "org" {
  name = "simple"
}

data "terrakube_vcs" "vcs" {
  name            = "sample_vcs"
  organization_id = data.terrakube_organization.org.id
}

data "terrakube_ssh" "ssh" {
  name            = "sample_ssh"
  organization_id = data.terrakube_organization.org.id
}

resource "terrakube_team" "team" {
  name             = "TERRAKUBE_SUPER_ADMIN"
  organization_id  = data.terrakube_vcs.vcs.organization_id
  manage_workspace = false
  manage_module    = false
  manage_provider  = true
  manage_vcs       = true
  manage_template  = true
}

resource "terrakube_module" "module1" {
  name            = "module_public_connection"
  organization_id = data.terrakube_ssh.ssh.organization_id
  description     = "module_public_connection"
  provider_name   = "aws"
  source          = "https://github.com/terraform-aws-modules/terraform-aws-vpc.git"
}
```

#### Bug fixes

We’ve addressed some issues reported by the community and fixed some vulnerabilities. You can see the full change log for this version here https://github.com/AzBuilder/terrakube/releases/tag/2.17.0

### October 2023 (2.16.0)

Welcome to the 2.16.0 release of Terrakube. There are a couple of updates in this version that we hope you'll like, some of the key highlights include:

#### Improvement in Workspaces List

We have added more filters to workspaces, so you can easily find the one you need. You can now use the tags or description of the workspaces to narrow down your search. Additionally, you can see the tags of each workspace on its card, without having to enter it. This way, you can quickly identify the workspaces that are relevant to you.

![image](https://github.com/AzBuilder/terrakube/assets/27365102/08efcfaa-cf37-4c46-8919-2f34b4e057fc)

#### Team API Tokens

In the previous version, you could only generate API tokens that were associated with a user account. Now, you can also generate API tokens that are linked to a specific team. This gives you more flexibility and control over your API access and permissions. To generate a team API token, you need to use the API for now, but we are working on adding this feature to the UI in the next version. You can find more information on how to use team API tokens in our [documentation](https://docs.terrakube.io/user-guide/organizations/team-tokens)

```
POST {{terrakubeApi}}/access-token/v1/teams
Authorization: Bearer (PERSONAL ACCESS TOKEN)
Request Body:
{
  "days": "1",
  "group": "TERRAKUBE_ADMIN",
  "description": "Sample Team Token"
}
```

#### Bug fixes and Performance

We’ve addressed some issues reported by the community regarding performance,bugs and security. You can see the full change log for this version here https://github.com/AzBuilder/terrakube/releases/tag/2.16.0



### August 2023 (2.15.0)

Welcome to the 2.15.0 release of Terrakube. There are a couple of updates in this version that we hope you'll like, some of the key highlights include:

#### Local Execution Mode

In the previous version, all executions were run remotely in Terrakube. However, starting from this version, you can choose to define the execution mode as either local or remote. By default, all workspaces run remotely. If you want to disable remote execution for a workspace, you can change its execution mode to “Local”. This mode allows you to perform Terraform runs locally using the CLI-driven run workflow.

![image](https://github.com/AzBuilder/terrakube/assets/27365102/d0e40b6c-0830-4153-abe3-3ce43c14e97c)

#### Accessing State from Other Workspaces

Now its possible to use `terraform_remote_state` datasource to acces state between workspaces. For further details check [our documentation](https://docs.terrakube.io/user-guide/workspaces/share-workspace-state)

```
data "terraform_remote_state" "remote_creation_time" {
  backend = "remote"
  config = {
    organization = "simple"
    hostname = "8080-azbuilder-terrakube-vg8s9w8fhaj.ws-us102.gitpod.io"
    workspaces = {
      name = "simple_tag1"
    }
  }
}
```

#### Support Cloud Block with Tags

In our previous version, we introduced support for adding tags to Workspaces. Starting from this version, you can use these tags within the cloud block to apply Terraform operations across multiple workspaces that share the same tags. You can find more details in the [CLI-Driven workflow documentation](https://docs.terrakube.io/user-guide/workspaces/cli-driven-workflow)

```
terraform {
  cloud {
    organization = "simple"
    hostname = "8080-azbuilder-terrakube-md8dylvuzta.ws-us101.gitpod.io"

    workspaces {
      tags = ["networking", "development"]
    }
  }
}
```

#### Search modules

We’ve added a search function to the Modules pages, making it easier to find modules.

#### Bug fixes

We’ve addressed some issues reported by the community, so Terrakube is becoming more stable. You can see the full change log for this version here https://github.com/AzBuilder/terrakube/releases/tag/2.15.0

Thanks for all the community support and contributions. Special thanks to @klinux for implementing the search feature inside Modules.

###

### Jun 2023 (2.14.0)

Welcome to the 2.14.0 release of Terrakube. There are a couple of updates in this version that we hope you'll like, some of the key highlights include:

#### Terraform cloud block support

This new version lets you use the [terraform cloud block](https://developer.hashicorp.com/terraform/cli/cloud/settings#the-cloud-block) to execute remote operations using the [CLI driven run workflow](https://docs.terrakube.io/user-guide/workspaces/cli-driven-workflow).

```
terraform {
  cloud {
    hostname = "8080-azbuilder-terrakube-q8aleg88vlc.ws-us92.gitpod.io"
    organization = "migrate-org"
    workspaces {
      name = "migrate-state"
    }
  }
}
```

#### Enhanced UI error messages for Authorization errors

We have enhanced the UI to handle authorization errors more gracefully.

![image](https://github.com/AzBuilder/terrakube/assets/27365102/fd556517-e663-42d4-ba46-f20199cb322e)

#### Workspace Tags

We have enabled tagging functionality within the workspaces. In an upcoming release, you will be able to use these tags in the cloud block feature. For more details please check the [terrakube documentation](https://docs.terrakube.io/user-guide/organizations/tags).

![image](https://github.com/AzBuilder/terrakube/assets/27365102/07551e69-a52f-45e8-ba64-cbd8f8acf663)

![image](https://github.com/AzBuilder/terrakube/assets/27365102/78cecea5-503d-4672-a46b-04e48831ea87)

#### Overview Workspace page

With Terrakube’s new workspace Overview page, you can access all the essential information for each of your workspaces in a simple and convenient panel. Initially we are including the last run and workspace tags. We will add more information in upcoming releases.

![image](https://github.com/AzBuilder/terrakube/assets/27365102/7d5fff7d-eed6-48a9-b1bc-8a4f114224f7)

#### Improved CLI run workflow logs

From this version onwards, you can view the logs in real time when you use the Terraform CLI remote backend or the cloud block.

![image](https://github.com/AzBuilder/terrakube/assets/27365102/d3082abe-5ed1-4221-88c9-9eaa13dbe5e8)

#### SSH key injection

Before, Terrakube only used SSH keys to clone private git repos. Now, Terrakube will inject the keys into the workspace. This way, if your terraform modules use git as source, Terraform will access the modules with the keys. Check the [documentation](https://docs.terrakube.io/user-guide/vcs-providers/ssh#ssh-key-injection) for further details.

For the full changelog for this version please check https://github.com/AzBuilder/terrakube/releases/tag/2.14.0



### May 2023 (2.13.0)

Welcome to the 2.13.0 release of Terrakube. There are a couple of updates in this version that we hope you'll like, some of the key highlights include:

#### Terraform init -migrate-state support

Now you can easily migrate your Terraform state from other tools like TFE or Terraform Cloud to Terrakube. The migration command is supported by using the terraform CLI.

```
terraform login 8080-azbuilder-terrakube-q8aleg88vlc.ws-us92.gitpod.io
terraform init -migrate-state
```

Check out our [documentation](https://docs.terrakube.org/user-guide/remote-state-migration) for further details about the process.

#### Improved Log streaming in the UI

Starting with this version, you can see the logs in the Terrakube UI in almost real time, so you can easily follow up on your Terraform changes.

![image](https://github.com/AzBuilder/terrakube/assets/27365102/069d08dd-9ba4-4b1c-b98d-549661a59077)

Also we optimized the code and updated most libs to the latest versions For the full changelog for this version please check https://github.com/AzBuilder/terrakube/releases/tag/2.13.0



### Mar 2023 (2.12.0)

Welcome to the 2.12.0 release of Terrakube. There are a couple of updates in this version that we hope you'll like, some of the key highlights include:

#### Search options in Workspaces

Use the new controls in the workspaces list to filter by status or search by name.

![image](https://user-images.githubusercontent.com/27365102/227623916-7d6633bf-835f-4b6e-8aba-0f35fd31dc50.png)

#### Simplified process to add a new VCS provider

You can now connect to a vcs provider in one step, instead of creating it first and then updating the call back url.

![image](https://user-images.githubusercontent.com/27365102/227625007-37fa43e1-9946-4b05-9a0a-2eb02006d682.png)

#### Open Telemetry

We added Open Telemetry to the api, registry and executor components for monitoring Terrakube. In future versions, we will also provide Grafana templates for custom metrics like jobs execution and workspaces usage. Check the [documentation](https://docs.terrakube.org/getting-started/deployment/open-telemetry) for further information.

![image](https://user-images.githubusercontent.com/4461895/223861732-82bf163b-f3c8-4463-af51-3953c7ae9f76.png)

#### Improvement to Templates

* In the previous version we introduced the use of `importCommands`, and now improve reutilization we are adding `inputsTerraform` and `inputsEnv` so basically now you can send parameters to your templates in an easy way. For example:

```yaml
flow:
  - type: "terraformPlan"
    step: 100
    importComands:
      repository: "https://github.com/AzBuilder/terrakube-extensions"
      folder: "templates/terratag"
      branch: "import-template"
      inputsEnv:
        INPUT_IMPORT1: $IMPORT1
        INPUT_IMPORT2: $IMPORT2
      inputsTerraform:
        INPUT_IMPORT1: $IMPORT1
        INPUT_IMPORT2: $IMPORT2
  - type: "terraformApply"
    step: 200
```

Check the [documentation](https://docs.terrakube.org/user-guide/organizations/templates/filter-gloval-variables-in-jobs) for more details.

* And if you want to create a schedule for a template, you can do it easily now with `scheduleTemplates`.

```yaml
flow:
  - type: "customScripts"
    step: 100
    inputsTerraform:
      SIZE: $SIZE_SMALL
    commands:
      - runtime: "BASH"
        priority: 100
        before: true
        script: |
          echo "SIZE : $SIZE"
  - type: "scheduleTemplates"
    step: 200
    templates:
      - name: "schedule1"
        schedule: "0 0/5 * ? * * *"
      - name: "schedule2"
        schedule: "0 0/10 * ? * * *"
```

Check [here](https://docs.terrakube.org/user-guide/organizations/templates/template-scheduling-in-jobs) for more details.

#### Restrict Terraform Versions or Provide your own Terraform build

You can now use a custom Terraform CLI build or restrict the Terraform versions in your organization with terrakube. Check the [documentation](https://docs.terrakube.org/getting-started/deployment/custom-terraform-cli-builds) for more details.

#### Terrakube Installation

We improved the helm chart to make the installation easy and now you can quickly install Terrakube for a Sandbox Test environment in [Minikube](https://docs.terrakube.org/getting-started/deployment/minikube). If you prefer you can now try Terrakube using [Docker compose](https://docs.terrakube.org/getting-started/docker-compose)

Thanks for the community contribution, specially thanks to @Diliz and @jstewart612 for their contributions in the Helm Chart. For the full changelog for this version please check https://github.com/AzBuilder/terrakube/releases/tag/2.12.0

And if you have any idea or suggestion don't hesitate to let us know.



### February 2023 (2.11.0)

Welcome to the 2.11.0 release of Terrakube. There are a couple of updates in this version that we hope you'll like, some of the key highlights include:

#### Adding Global Variables to Organization Settings

Global Variables allow you to define and apply variables one time across all workspaces within an organization. So you can set your cloud provider credentials and reuse it in all the workspaces inside the same organization. See more details in [our documentation](https://docs.terrakube.org/user-guide/organizations/global-variables)

![image](https://user-images.githubusercontent.com/27365102/221226933-ee19e11e-ed97-448f-8213-5c4848d5289b.png)

#### Adding CLI and API Driven workflows to workspaces

[CLI-driven](https://docs.terrakube.org/user-guide/workspaces/creating-workspaces#cli-driven-workflow) and [API-driven](https://docs.terrakube.org/user-guide/workspaces/creating-workspaces#cli-driven-workflow) workflows are available now. This mean you can create a workspace in the UI and then connect to the same using the Terraform cli or the Terrakube API. Or if you prefer you can go ahead and connect to your Terrakube organization locally from the Terraform cli. Check the [CLI-driven worflow documentation](https://docs.terrakube.org/user-guide/workspaces/cli-driven-workflow) for further details.

For CLI or API driven workspaces you will see the steps directly in the overview tab:

![image](https://user-images.githubusercontent.com/27365102/221228244-89f6993c-2fbb-481b-ad45-b0520e5f2fb9.png)

And in the list of workspaces you will see a badge so you can easily identify CLI and API driven workspaces

![image](https://user-images.githubusercontent.com/27365102/221229865-885cf590-706b-4a3f-bef2-fb14707570aa.png)

#### Improved error handling for invalid templates

If you have an error in your template yaml syntax will be easy to identify it. You will see a more clear message about the job execution failure so you can easily see if the error is related with the execution or with the template configuration.

![image](https://user-images.githubusercontent.com/27365102/221230638-d65335cd-29c5-4454-b6cf-4b9665b4cc1e.png)

#### Import scripts inside the templates

If you have some scripts that you want to reuse, you can import it using the new `importCommand` this will improve the reuse of templates, so you can share some commons steps with the community in your github repos. See [Terrakube docs](https://docs.terrakube.org/user-guide/organizations/templates/import-templates) for more details in how to import scripts

```yaml
flow:
  - type: "terraformPlan"
    step: 100
    importComands:
      repository: "https://github.com/AzBuilder/terrakube-extensions"
      folder: "templates/terratag"
      branch: "import-template"
  - type: "terraformApply"
    step: 200
```

#### Adding UI templates support in workspaces

UI templates extend the idea of templates to the UI, so you can customize how each step will look like, in your custom flow inside Terrakube. Basically you can use standard HTML to build the way in which Terrakube will present the specific step

For example you can present a more user friendly information when extracting data from infracost result. And in the UI if you have a custom Template for that step you will se the Structured UI by default, but you can switch the view to the standard terminal output to see the full logs for that step.

Check some examples:

![image](https://user-images.githubusercontent.com/27365102/221233136-f1efde52-88db-4407-af41-fab66d8db460.png) ![image](https://user-images.githubusercontent.com/27365102/221233363-d23d52d9-269c-4274-872c-03c2f204a2ef.png)

UI templates will allow to create better UI experiences. And brings new ways to customize the way you use Terrakube in your organization. In the upcoming releases we will create some standards templates that you can reuse for the terraform plan and apply. And also we will provide a Terrakube extensions that allows to create UI templates easily.

#### Improving organization selection on initial login

When you login to Terrakube for the first time and if you have multiple organizations assigned, then you will see a screen to select your organization.

For the full changelog for this version please check https://github.com/AzBuilder/terrakube/releases/tag/2.11.0

And if you have any idea or suggestion don't hesitate to let us know.
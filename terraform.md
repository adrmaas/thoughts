# On Terraform

## Terraform Repositories
Terraform and terraform repository layout is an interesting subject.  And the difficulty of moving terraform
state and refactoring terraform code make it important to get it right the first time.

An organization I worked at once had a great point about organizational structure being an unreliable thing:

> teams, owners, and org structure will change over time

For that reason I favour:
- ownership of terraform root modules/configurations should not be reflected in the directory structure
- ownership of terraform root modules/configurations should not be reflected in the state key
- project ownership should only be recorded as meta data to the project

Environment is another element I often see being included in terraform repository layout.  And environments
are also subject to change over time.  Again I would be careful on how environments are represented in the
directory structure of terraform repositories and projects.

Given the above restrictions I've come to think of the following as the optimal layout for terraform repostories:
```
/
  apps/            # directory for projects/root modules/configurations
    sample-app/    # a root module
      stage/       # if environments are needed for a project, include them in the project directory
      prod/        # don't insist on them (if you don't have lower environments how are you testing?)
  modules/         # reusable generic modules if you're doing mono-repo
  bin/             # any shared tooling
```
Avoid the complexity of refactoring an over organized repository that encodes changing information in it's
directory structure.  Record ownership in meta-data.  Include environment when needed.  

Don't mix root modules/configurations with resuable modules.  Module cross dependencies can become a plane
of hell.

Don't use root modules/configurations sub/private modules if it can be avoided.  This can lead to a profusion
of code variations that become difficult to support.

External module repostories can be defined by varying git tag wild cards.  This enables external module
repositories as a great way to gate functionality at different rates to projects that have different risk
profiles/operational requirements but shared needs.

## Terraform Testing
Don't be an animal: do testing of modules.  One approach is through sample configurations which can be
destroyed, rebuilt, and upgraded.  Building your configurations from scratch is important. It's possible
to evolve terraform modules/configurations into a state where they can not be created from nothing due to
state graph dependencies that develop over time.

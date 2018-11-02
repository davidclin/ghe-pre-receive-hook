# ghe-pre-receive-hook
GHE Pre-receive hook example

The following pre-receive hook prevents users from pushing changes directly to master or creating a new branch off master.
Users MUST submit changes via Pull Request.

```
#!/bin/bash

zero_commit="0000000000000000000000000000000000000000"
DEFAULT_BRANCH=$(git symbolic-ref HEAD)

##############################################################
# This blocks creation of new branches on the default branch #
##############################################################

while read oldrev newrev refname; do
  # Only check new branches ($oldrev is zero commit)
  if [[ $oldrev == $zero_commit && $refname =~ ^refs/heads/ ]]; then
      echo "***********************************************************************************************************"
      echo "Creation of new branch $refname is not allowed. Changes to the default branch must be made by Pull Request."
      echo "***********************************************************************************************************"
      exit 1
  fi

#######################################################################
# This restricts changes on the default branch to those made with the #
# GUI Pull Request Merge button, or the Pull Request Merge API.       #
#######################################################################

  if [[ "${refname}" != "${DEFAULT_BRANCH:=refs/heads/master}" ]]; then
    continue
  else
    if [[ "${GITHUB_VIA}" != 'pull request merge button' && \
          "${GITHUB_VIA}" != 'pull request merge api' ]]; then
      echo "************************************************************************************************************"
      echo "Changes to the default branch must be made by Pull Request. Direct pushes, edits, or merges are not allowed."
      echo "************************************************************************************************************"
      exit 1
    else
      continue
    fi
  fi
done

exit 0
```

# Useful Resources
About pre-receive hooks [(link)](https://help.github.com/enterprise/2.13/admin/guides/developer-workflow/about-pre-receive-hooks/)<br>
Managing pre-receive hooks on the GitHub Enterprise appliance [(link)](https://help.github.com/enterprise/2.15/admin/guides/developer-workflow/managing-pre-receive-hooks-on-the-github-enterprise-appliance/)<br>
GitHub Sample Pre-receive Hooks [(link)](https://github.com/github/platform-samples/tree/master/pre-receive-hooks)<br>
Creating a pre-receive hook script [(link)](https://help.github.com/enterprise/2.13/admin/guides/developer-workflow/creating-a-pre-receive-hook-script/)


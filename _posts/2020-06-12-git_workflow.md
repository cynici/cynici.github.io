# Git squash rebase Workflow

Rebase-style instead of merge-style - changes on your branch
are squashed into a single commit, then rebased on top of
the latest origin/master, before being pushed back to origin/master.
This workflow results in a master branch that contains no merges,
just a single line of commits.

You are concerned with two branches:
- your adhoc branch
- main (or master)

There are no git-flow style `develop`, `release`, `hotfix`, etc branches.
The main branch is expected (must be) deployable at any time.
Commits should only end up in main if they leave main in a deployable
state.

Don't push something to main if you don't intend to deploy it to
production relatively soon!

## Procedure

Configure git push to use upstream, `git config --global push.default upstream` Otherwise you'll get an error on coe-release

1. git clone {repo\_url}
1. Start a new branch tracking main, `code-on FEAT-xyz` (use issue reference)
1. Stage and commit locally, `git commit -a -m "meaningful commit message"`. Repeat as necessary.
1. Push to remote with the same branch name as the current local branch. Create remote branch if it doesn't exist.  `code-push`
1. Create a pull request in Github.com parlance (or merge request in Gitlab)
1. Repeat git commit, code-push as necessary to gain PR approval
1. After PR is approved, rebase your branch on top of the latest main `git pull --rebase` (equivalent to git fetch + git rebase)
1. Force update remote adhoc branch, `code-push -f`
1. Release to remote main, remove loxal and remote adhoc branch `code-release`

### references

- Detailed description of squash rebase workflow, <https://medium.com/swlh/squash-and-rebase-git-basics-5cb1be1e0dac>
- Difference between 'git merge' and 'git rebase' <https://stackoverflow.com/q/16666089>
- Squash commit and rebase <https://medium.com/swlh/squash-and-rebase-git-basics-5cb1be1e0dac>, differs from this article


## Shell aliases, functions

```
function info() {
  echo "$(tput setaf 7)$1$(tput sgr0)"
}
function code-on() {
  # Checkout a new branch for a Jira issuea
  # params:
  #   $1 jira issue key
  #   $2 tracking branch (default: main)
  (
    set -e
    local upstream=${2:=main}
    local branch="${USER}-$1"
    info "creating local branch '$branch' which tracks 'origin/${upstream}'"
    (
      set -x
      git checkout -b $branch origin/${upstream}
    )
  )
}
function code-push() {
  # Push branch to the matching branch on the remote
  # Use this to update pull requests.
  (
    set -e
    local branch=`git rev-parse --abbrev-ref HEAD`
    [[ "$branch" == "main" ]] && echo "should be on feature branch" && return 1
    info "pushing to remote branch 'origin/$branch'"
    (
      set -x
      git push origin HEAD:$branch $@
    )
  )
}
function link-pr-jira() {
  # Tell company XXXXX Jira that we've created the PR and link it
  # Params:
  #   $1 The pull request url
  (
  local pr=$1
  if [ -z $pr ]; then
    info "pull request link not given, not linking to jira"
    return
  fi
  set -e
  local repo=`git remote -v | grep fetch | grep -o 'XXXXX/[^.]*' | cut -d '/' -f2`
  local branch=`git rev-parse --abbrev-ref HEAD`
  local issue=${branch#*-}
  local jirabase="https://jira.XXXXX.com/jira/rest/api/latest"
  local pr_description=${pr#*github.com/}
  info "linking pull request $pr to jira issue $issue"
  (
    set -x
    curl --netrc -X POST -H "Content-Type: application/json" $jirabase/issue/$issue/remotelink --data-raw "{\"object\": {\"url\":\"$pr\", \"title\": \"$pr_description\"}}"
  )
  )
}
function code-pr() {
  # Create a pull request from your branch to the upstream
  # Requires hub (tool from github)
  # Automatically uses the last commit message as the pr message
  (
  set -e
  local branch=`git rev-parse --abbrev-ref HEAD`
  local upstream=`git rev-parse --abbrev-ref HEAD@{upstream}`
  local short_upstream=${upstream#origin/}
  [[ "$branch" == "main" ]] && echo "should be on feature branch" && return 1
  code-push
  info "creating pull request from '$branch' to '$short_upstream'"
  (
    set -x
    local pr=`hub pull-request -h $branch -b $short_upstream -F <(git log -1 --pretty=%B)| grep -o 'http[^ ]*'`
    set +x
    #link-pr-jira "$pr"
  )
  )
}
function code-release() {
  # This will just push the code to the remote branch
  # and also to the upstream. Pushing to both locations
  # will cause the existing pr to close.
  # This will also remove the local and remote branches
  (
  set -e
  local branch=`git rev-parse --abbrev-ref HEAD`
  local upstream=`git rev-parse --abbrev-ref HEAD@{upstream}`
  [[ "$branch" == "main" ]] && echo "should be on feature branch" && return 1
  git fetch
  code-push
  info "preparing release of 'origin/$branch' into '$upstream'"
  git status
  sleep 3
  info "pushing to remote branch '$upstream'"
  (
    set -x
    git push origin HEAD:main
  )
  info "cleaning up"
  (
    set -x
    git checkout main
    git reset --hard origin/main
    git push origin :${branch}
    git branch -d ${branch}
  )
  )
}
function code-abandon-local() {
  (
  set -e
  local branch=`git rev-parse --abbrev-ref HEAD`
  local upstream=`git rev-parse --abbrev-ref HEAD@{upstream}`
  [[ "$branch" == "main" ]] && echo "should be on feature branch" && return 1
  [[ -z "$upstream" ]] && echo "sanity failure; upstream is empty?" && return 1
    (
      set -x
      git checkout main
      git branch -d ${branch}
    )
  )
}
```

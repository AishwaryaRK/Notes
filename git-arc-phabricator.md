# git + arc + phabricator

### new branch

    arc feature <<branch_name>>

differential / diff == merge request

### rebase

    git fetch origin
    git rebase origin/master

### filter commit logs based on author

    git log --author="user_name"

### revert commit

    arc feature <<branch_name>>
    git revert <<commit-id>>
    arc diff

### lint

    arc lint

### test

    arc unit

### push + PR

    arc diff
    arc diff --preview

- specify custom reviewers
- summary of all the changes in the diff (like a PR description)

### merge to master

    arc land
    
    arc patch
    arc diff --update <<Diff ID>>

### see your revisions pending for review

    arc list

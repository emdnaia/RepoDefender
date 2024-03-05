# RepoDefender
Defend against repository takedowns on Github

0) Create multiple accounts on GH
1) Get https://github.com/cli/cli/blob/trunk/docs/install_linux.md
2) Authorize your accounts each via
`gh auth login`
3) Fork & clone targets
```
#!/bin/bash
 
# Replace 'user_or_organization' with the actual GitHub username or organization name
USER_OR_ORG="target-account"
 
# Replace 'limit' with the number of repositories you want to list. Use a high number to list all.
LIMIT=100
 
# List repositories, fork them, and then clone
gh repo list $USER_OR_ORG --limit $LIMIT --json nameWithOwner -q ".[].nameWithOwner" | while read repo; do
  # Fork the repository to your account
  echo "Forking $repo..."
  gh repo fork "$repo" --clone=false
 
  # Construct your fork's SSH URL assuming your GitHub username is 'your_username'
  # Replace 'your_username' with your actual GitHub username
  # Note: Replace ':' with '/' in the SSH URL for the correct format
  FORK_SSH_URL="git@github.com:$(echo $repo | sed 's/:/\//')"
 
  echo "Cloning $FORK_SSH_URL..."
  git clone $FORK_SSH_URL
done
```
4) Out of all your new forked accounts, it makes sense to make some private too
```
#!/bin/bash
 
# Your GitHub username
USERNAME="your-account"
 
# List all repositories for the user, filter for forks, and update to private
gh repo list $USERNAME --json nameWithOwner,isFork --limit 100 | jq -r '.[] | select(.isFork==true) | .nameWithOwner' | while read repo; do
  echo "Setting $repo to private..."
  gh repo edit "$repo" --visibility private
done
```

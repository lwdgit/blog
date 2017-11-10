git filter-branch --force --index-filter 'git rm --cached -r --ignore-unmatch test.js' --prune-empty --tag-name-filter cat -- --all
git reflog expire --expire=now --all
git gc --prune=now
git gc --aggressive --prune=now
git push --all --prune --force

spectacle
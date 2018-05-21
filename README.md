# KomodoPlatform-wiki

This is a cloned wiki of the repository https://github.com/KomodoPlatform/komodo for easy contribution of edits.
To understand the process, see https://gist.github.com/larrybotha/10650410 and https://stackoverflow.com/a/11481887/9551761

#The process for a person suggesting an edit
-------------------------------------------
+ Fork this repo
+ Make edits in your forked copy
+ Submit pull request to this repo
+ I will review and merge the edits.
+ Later, maintainer of the KomodoPlatform's official wiki will merge changes from this repo to the official wiki i.e., https://github.com/KomodoPlatform/komodo/wiki

#The process for maintainer of KomodoPlatform's official wiki:
-------------------------------------------------------------
+ Clone the official wiki `git clone https://github.com/KomodoPlatform/komodo.wiki.git`
+ Add a new remote: `git remote add edited_repo https://github.com/gcharang/komodo-wiki.git`
+ `git fetch edited_repo master`
+ Compare the latest commit from origin with the latest commit from edited_repo
+ git merge or rebase
+ Remove this `README.md` file and `commit`
+ `git push -u origin master` to commit to the official repository  

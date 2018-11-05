MYSQL
============

## MYSQL 8? need 5.7?


Looks like the prior installation of mysql8 creates bad DB files, which must be deleted manually [re: stackoverflow post](https://apple.stackexchange.com/questions/328439/brew-install-mysql5-7-results-in-missing-mysql-bin)

Solution! (Warning, removes all previous databases if any)

``` sh
rm -rf /usr/local/var/mysql  # WARNING: this deletes all your data!

brew uninstall mysql@5.7
brew install mysql@5.7
```
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

## Windows (installing 5.7)

1) Install mysql 5.7 server, remember password (at the end it will ask you to name service `MySQL57`)
2) Run windows + r, run 'services.msc' and scroll down to `MySQL57` or whatever you named it
3) Right click > run, if not running..
4) Add `C:\Program Files\MySQL\MySQL Server 5.7\bin` to your PATH variable so you can easily type `mysql` in console (and make sure its above cygwin or any other mysql versions if you have multiple)
5) Restart console, try `mysql -uroot` and password if supplied
6) You might have to initialize the db folder..do so by `mysqld --console --initialize-insecure` in a admin ready console.
7) now try `mysql -uroot` and password if supplied
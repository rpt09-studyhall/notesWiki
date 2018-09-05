CRON NOTES
============

Open up a cron file for editing
--------------------------------
``` 
  $> crontab -e
```

Editing the cron file to run a node job
--------------------------------------

Note: `--cronMode` is a user defined flag that can be accessed in your script by lookng up `process.argv[2]`.
This can be useful for only doing a routine if run in a special mode (like cron vs. a typical module)

```
# this runs html fetcher and passes in a flag. it runs it every minute
*/1 * * * * /usr/local/bin/node  '[ABS_PATH_TO]/workers/htmlfetcher.js' --cronMode
```

To see if you have any cron jobs running 
--------------------------------------
```
$> crontab -l
```

Removing a cron job
--------------------------------------
Adding a `#` or deleting the line from the cron job file is an easy way to kill the job


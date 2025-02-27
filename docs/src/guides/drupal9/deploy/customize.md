---
title: "Customize Drupal 9 for Platform.sh"
sidebarTitle: "Customize"
weight: -90
description: |
    Add some helpful dependencies, and modify your Drupal site to read from a Platform.sh environment.
---

Now that your code contains all of the configuration to deploy on Platform.sh,
it's time to make your Drupal site itself ready to run on a Platform.sh environment.
There are a number of additional steps that are either required or recommended, depending on how well you want to optimize your site.

## Install the Config Reader

{{% guides/config-reader-info lang="php" %}}

## `settings.php`

`settings.php` is the main Drupal environment-configuration file.
In a stock Drupal installation it contains the database credentials, various other settings, and an enormous amount of comments.

In the Drupal 9 template, the [`settings.php`](https://github.com/platformsh-templates/drupal9/blob/master/web/sites/default/settings.php) file
is mostly replaced with a stub that contains only the most basic configuration
and then includes a `settings.platformsh.php` and `settings.local.php` file, if they exist.
The latter is a common Drupal pattern, and the `settings.local.php` file should never be committed to Git.
It contains configuration that's specific to your local development environment,
such as a local development database.

The `settings.platformsh.php` file contains glue code that configures Drupal
based on the information available in Platform.sh's environment variables.
That includes the database credentials, Redis caching, and file system paths.

The file itself is a bit long, but reasonably self-explanatory.

{{< readFile file="static/files/fetch/config-examples/drupal9" highlight="php" >}}

If you add additional services to your application, such as Solr, Elasticsearch, or RabbitMQ,
you would add configuration for those services to the `settings.platformsh.php` file as well.

## `.environment`

Platform.sh runs `source .environment` in the [app root](../../../create-apps/app-reference.md#root-directory)
when a project starts, before cron commands are run, and when you log into an environment over SSH.
That gives you a place to do extra environment variable setup before the app runs,
including modifying the system `$PATH` and other shell level customizations.

The Drupal template includes a small [`.environment` file](https://github.com/platformsh-templates/drupal9/blob/master/.environment).
This modifies the `$PATH` to include the `vendor/bin` directory,
where command line tools like Drush are stored.

You need the file or one like it if you plan to run `drush` as a command,
such as in a cron task like the one in the [app configuration from the previous step](./configure.md#configure-apps-in-platformappyaml).
If you don't include the file, you get a [command not found error](../../../development/troubleshoot.md#command-not-found).

```text {location=".environment"}
# Allow executable app dependencies from Composer to be run from the path.
if [ -n "$PLATFORM_APP_DIR" -a -f "$PLATFORM_APP_DIR"/composer.json ] ; then
  bin=$(composer config bin-dir --working-dir="$PLATFORM_APP_DIR" --no-interaction 2>/dev/null)
  export PATH="${PLATFORM_APP_DIR}/${bin:-vendor/bin}:${PATH}"
fi
```

## Drush configuration

Drush requires a YAML file that declares what its URL is.
That value varies depending on the branch you're on, so it can't be included in a static file.
Instead, the `drush` directory includes a [short script](https://github.com/platformsh-templates/drupal9/blob/master/drush/platformsh_generate_drush_yml.php)
that generates that file on each deploy, writing it to `.drush/drush.yml`.
That allows Drush to run successfully on Platform.sh, such as to run cron tasks.

The script contents aren't especially interesting.
For the most part, you can download it from the template,
place it in a `drush` directory in your project so they can be called from the deploy hook, and then forget about it.

{{< guide-buttons next="Deploy Drupal 9" >}}

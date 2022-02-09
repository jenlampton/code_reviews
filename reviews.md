# Common things I include in code reviews

## This document is intendeded to make the process of writing code reviews faster.

### `README.md` suggestions

* Add the required sections into the README.md file. See [this example](https://github.com/backdrop-ops/contrib/blob/main/examples/README.md) for all sections that are required, but I know Maintainers and License are not optional.
* Fix the markdown formatting in the README.md file. (This file is parsed and used to generate the project page on backdropcms.org, see the example file for formatting suggestions)
* Remove the duplicate README.txt file (only the .md version is necessary)


### `.info` file suggestions

* The `version = 1.x` line should be removed -- this will be added automatically by the backdropcms.org packaging script when a release is created. (This makes it easier on contrib devs because you don't need to remember to update it with every release)
* The` project` line should be removed. (This line will be added by the backdrop packager once your module gets its first official release. Though it's not problematic to have it, it's not necessary since what you put on that line will be overridden by the line that is automatically added.)


###  `.install` file suggestions

* All update functions numbered 7000 and above can be removed. Update functions for Backdrop will be numbered 1000 and above, and all drupal 7 updates should have been run in Drupal (before upgrading to Backdrop).


### `.module` file suggestions

* The `hook_help` can be removed. (That hook does not exist in Backdrop.)


### `.admin.inc` file suggestions

* The settings form callback could open with `$form = array('#config' => 's3fs.settings');` instead of ` $form = array();`. This `#config` item allows `system_settings_form()` to save all the values from this form into the specified config file. Then, you could delete the `s3fs_settings_form_submit()` function entirely.


### Location of files

* (tiny nit) the `s3fs.tests.info` file should be moved inside the `tests` directory (and the line `file = tests/s3fs.test` within, should be updates to `file = s3fs.test` so that it is still relative).


## general coding standards

* All function definitions should end the line with the opening `{` rather than placing that character on the next line. (This will decrease line-count and keep git diffs cleaner.)
* You can remove the blank line at the top of all PHP files. These were necessary in Drupal for the CVS version control system, but are no longer necessary now that Drupal (&Backdrop) use Git.
* Files that contain PHP classes should be moved into an `includes` directory.
* Remove the `.gitattributes` file from the repo, and ignore it with `.gitignore`

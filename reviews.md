# Common things I include in code reviews

I'm also going to do a quick code review, but items below are suggestions. Take or leave them as you see fit! We do this review specifically looking for things that may be done differently between Backdrop and Drupal. We hope that this review might help with the transition for those new to Backdrop :)


### `README.md` suggestions

* Add the required sections into the README.md file. See [this example](https://github.com/backdrop-ops/contrib/blob/main/examples/README.md) for all sections that are required, but I know Maintainers and License are not optional.
* Fix the markdown formatting in the README.md file. (This file is parsed and used to generate the project page on backdropcms.org, see the example file for formatting suggestions)
* Remove the duplicate README.txt file (only the .md version is necessary)
* For the section on Issues, it would be helpful if "the issue queue" was linked to the specific GitHub queue for this module (I would assume you are already planning to do this once the link to the queue is confirmed, but noting it here just in case)
* Please use a URL to a GitHub profile for all current maintainers.
* If possible, please link the names directly in the Credits section, rather than printing the complete URL. For example: `Ported to Backdrop CMS by Greg OToole https://github.com/gto1` would become
`Ported to Backdrop CMS by [Greg OToole](https://github.com/gto1)`


### `.info` file suggestions

* The `version = 1.x` line should be removed -- this will be added automatically by the backdropcms.org packaging script when a release is created. (This makes it easier on contrib devs because you don't need to remember to update it with every release)
* The` project` line should be removed. (This line will be added by the backdrop packager once your module gets its first official release. Though it's not problematic to have it, it's not necessary since what you put on that line will be overridden by the line that is automatically added.)
* In the `.info` file you have defined a `package` but this is usually only used if your module comes as part of a suite of modules that you would like grouped together on the modules page. Since this is a stand-alone module, that line can be left off if you like. (But if you want it separated from other modules on the page, you are also welcome to leave it as-is.)


###  `.install` file suggestions

* In `hook_uninstall()` it's not necessary to delete the module's own config files. As long as a module properly implements `hook_config_info()` that cleanup will happen automatucally.  If, however, a module writes to config files provided by other modules, it should remove its own values from those other files.
* All update functions numbered 7000 and above can be removed. Update functions for Backdrop will be numbered 1000 and above, and all drupal 7 updates should have been run in Drupal (before upgrading to Backdrop).
* The numbers for the updates should ideally be something like `1001` and `1002`, indicating that they are both for Backdrop 1.x (in the range 1000-1999) and that they are both for the same version of your module (in the range of 1000 - 1099). Jumping 100 numbers or more in the update function name usually indicates a jump from one branch of the module to another branch (allowing room for more updates on the 1.x branch, added after those added on the 2.x branch, but to be executed before).
* Update functions in the `.install` file look like they are setting or changing config values. For people who install this module for the first time, the initial config values will be set when the config file is copied from the `config` directory into their sites active directory, and these updates won't be run.


### `.module` file suggestions

* The `hook_help` implementation can be removed. (That hook does not exist in Backdrop.)
* An implementation of `hook_form_alter()` will be executed on every form in Backdrop. Since it looks like this change should only affect node forms, this function could be replaced with an implementation of `hook_form_BASE_FORM_alter()` for better performance. I would rename the function to `content_limiter_form_node_form_alter()`.


### `.admin.inc` file suggestions

* The settings form callback could open with `$form = array('#config' => 's3fs.settings');` instead of ` $form = array();`. This `#config` item allows `system_settings_form()` to save all the values from this form into the specified config file. Then, you could delete the `s3fs_settings_form_submit()` function entirely.
* The function `config_get()` does not include a default value like `variable_get()` did, instead defaults are set in the `.json` file included in the `/config/` directory.
* Config keys don't need to be namespaced to begin with the module name `sendgrid_` since they will be saved in a `.json` file that contains the module name. (Many people like to leave the namespaced versions in anyway)


### `.tokens.inc` suggestions

* The `@file` docblock still references "Token module" but token is no longer a stand-alone module in Backdrop, it has been integrated into core.


### Template files `.tpl.php`

* In Drupal classes were converted from an array into a string via the process layer, which has been removed from Backdrop. In Backdrop, we make that conversion directly in the template file, so that front-end developers can have an easier time changing them. You may need to update the .tpl.php file so that <div class="<?php print $classes; ?>"... becomes <div class="<?php print implode(' ', $classes); ?>"... and <?php print $attributes; ?> becomes <?php print backdrop_attributes($attributes); ?> in order for your classes and attributes to print correctly. If it's working as expected now, these may have been flattened to strings too early, and you might want to rework that code to leave them as arrays until the template file.

### Theme functions

* The `hook_theme()` implementation should remain in `example.module` but all of the theme functions themselves are usually placed into a `example.theme.inc` file in Backdrop. That file name can be added into the `hook_theme()` definition as `'file' => 'example.theme.inc',` Since not all theme functions are needed at all times on all pages, having this code separated from the rest of the module code might offer a very minor performance and/or memory and/or processing benefit (but also maybe not), but more importantly, should make it easier for front-end developers to find the code they may wish override.

### Location of files in the module

* We recommend all `.tpl.php` files to be placed inside a `/templates/` subdirectory in the module.
* We recommend that all `.css` files go inside a  `/css/` subdirectory. (It doesn't matter so much when you only have one js file, but if there were a handful this would help keep things tidy)
* We recommend that all `.js` files go inside a  `/js/` subdirectory. (It doesn't matter so much when you only have one js file, but if there were a handful this would help keep things tidy)
* We recommend that all `.test` files go inside a  `/tests/` subdirectory. Test modules should also be placed inside that directory, but also in a directory matching the name of the module.
* The `tests.info` file should also be moved within the `/tests/` subdirectory (and the `file` lines in the `.tests.info` file should be updated to remove the `tests` directory - so that they are still relative).
* Files that contain PHP classes should be placed into an `/includes/` subdirectory. This makes it easier for developers to scan the list of files and directories in a module and immediately know the module contains classes, without even needing to open any files.

### Test files

* Test dependencies should be defined in the tests themselves, so the line `test_dependencies[] = maillog` can be removed from the `.info` file. Since this is a different module entirely, it might be helpful to list this as a dependency in the README.md also.

## General coding standards

* We recommend adding a `@file` docblock at the top of each file with a brief description of what's inside.
* Blank lines after the opening `<?php` tag can be removed if you want. The extra line breaks were required in Drupal because of the previous CVS version control system, but are not needed anymore.
* Indentation should be 2 spaces, instead of 4. (this increases the number of characters before the 80 character wrap)
* The opening tag for a function should remain on the same line as the function definition, rather than placing that character on the next line. (This will decrease line-count and keep git diffs cleaner.)
* An else statement should appear on the line following the closing tag for the if statement that proceeds it, not the same line. (this makes adding or removing else statements a cleaner operation because no changes will be necessary to the if statement above)
* For an `if` statement, our standard is to put a space after the `if` and before the following opening parenthasis. So, `if($disable_limiting` would become `if ($disable_limiting`. This also makes the code easier for other people to read.
* When we concatenate strings with the `.` operator, we also surround the dot with spaces on both sides to make the code easier for people to read.
* Wherever possible, strings should be wrapped in single quotes `'` instead of double quotes `"` so they will be evaluated faster (PHP will not search the string for variable replacements if it uses single quotes)
* We do not recommend using inline styles because people often use themes that will override the CSS, and a theme would not be able to override this style if it was added inline. If you need to add styles, we recommend using the `#attached` property, and adding a stylesheet containing the neccessary CSS.
* Array syntax still needs to be compatible with PHP 5.6 until Backdrop 2.x, so `[]` needs to be replaced with `array()`.
* Inline code comments should start with `//` followed by a space, and end with a period `.`.
* For new functions, `@params` and `@return` should apear in the docblock. (This is not necessary for hook implementations.)


## Translated strings

* Building strings that can be translated into any language is done using the `t()` function. The first parameter is the message containing markers for variable replacements, and the second is an array of the variables to insert. Something like
```
t('Content limiting detected ('.$delete_quantity.' deleted)')
```
should be replaced with
```
t('Content limiting detected (:quantity deleted)', array(':quantity' => $delete_quantity))
```
* For watchdog messages, the use of the `t()` function is assumed, so what you pass in is the message containing markers for variable replacements, and also the array of the variables.  Something like
```
watchdog('content', $delete_quantity.' '.$type.' nodes deleted by content limiter.');
```
should probably be replaced with
```
$message = ':quantity :type nodes deleted by content limiter.';
$replacements = array(':quantity' => $delete_quantity, ':type' => $type);
watchdog('content_limiter', $message, $replacements);
```


# Text for the invitation to join the contrib group!

I've reviewed this module and since it meets all 3 requirements, **an invitation to join the Backdrop Contrib group is on the way!** Once you have accepted the invitation, feel free to [transfer the repository](https://docs.backdropcms.org/documentation/after-your-application-is-accepted) into the backdrop-contrib group at any time (ask here if you have questions).


# Text for the top of the code review comment

I'm also going to do a quick code review for the module, but items in this comment are only suggestions, take or leave them as you please. We do this review specifically looking for things that may be done differently between Backdrop and Drupal in the hopes that this information might help with the transition for those new to Backdrop :)

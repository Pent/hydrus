---
title: Changelog
---

# changelog

!!! note
    This is the new changelog, only the most recent builds. For all versions, see the [old changelog](old_changelog.html).

## [Version 566](https://github.com/hydrusnetwork/hydrus/releases/tag/v566)

### incremental tagging

* when you boot a 'manage tags' dialog on multiple files, a new `±` button now lets you do 'incremental tagging'. this is where you, let's say for twenty files, tag them from page:1->page:20. this has been a long time in the works, but now we have thumbnail reorganisation tech, it is now sensible to do.
* the dialog lets you set a namespace (or none), start point (e.g. you can start tagging at page:19 if you are doing the second chapter etc...), the step (you can count by +2 every file, instead of +1, or even -1 to decrement), the subtag prefix (so you can say 'page:insert-4' or something), and the subtag suffix (for, say, 'page:2 (wip)')
* the last namespace is remembered between dialog opens, and if the first file in the selection has a number tag in that namespace, that is the number the 'start' will initialise with. a bit of overlap/prep may save time here!
* the prefix and suffix are remembered between dialog opens
* a status text gives you a live preview of what you will be adding and says whether any of the files already have exactly those tags or have different tags under the same namespace (which would be possible conflicts, suggesting you are not lined up correct)

### misc

* added import support for .docx, .xlsx, and .pptx files (the Microsoft Open XML Formats). they get icons, not much else. they are secretly zips, so **on update, you will be asked if you want to scan your existing zips for these formats**
* when you move a window to another screen in a maximised state (e.g. on Windows you can do this with win+shift+arrow), the system that remembers window coordinates will now register and save this. the 'restore' window size is preserved from whatever it was on the previous screen while the 'restore' position will try to stay the same on the new monitor (e.g. if it was at (200, 400) on the old monitor, it will try to do the same on the new) as long as the window fits, otherwise it is moved to (20,20) on the new screen
* the 'edit string converter' panel no longer requires you to enter an example text that can be converted. you can see the error on the dialog, so if you don't want to fix it, or you just need to nip in and out testing things, it is now up to you
* if the database takes a long time to update, the 'just woke up from sleep' state should no longer trigger. the system thought the long weird early delay was the computer going to sleep
* the system that gives a popup and then a dialog when you have 165+ (and then 500+ or so) pages open is now removed. this was always a wx thing primarily, and Qt is much happier about having a whole load of UI elements. the main problem here is now memory blot and UI-update lag. this is now in the user's hands alone, no more bothering from me (unless it becomes a new problem, and I'll figure out a better warning test/system)

### boring code cleanup

* neatened how some manage tags ui is initialised. there's a hair of a chance this fixes the 'the manage tags dialog taglist is cut off sometimes' bug
* neatened how some pending content updates are held in manage tags
* manage tags dialogs now receive their media list in the same order as the underlying thumbnail selection, ha ha ha
* untangled some of the presentation import options. stuff like 'is new or in inbox' gets slightly better description labels and cleaner actual logic code
* fixed some type issues, some typo'd pubsubs, and other misc linting
* tried last week's aborted github build update again. the build is now Node 20 compatible

## [Version 565](https://github.com/hydrusnetwork/hydrus/releases/tag/v565)

### tag sorting bonanza

* _options->sort/collect_ now offers four places to customise default tag sort. instead of having one default sort for everything, there's now sort for search pages, media viewers, and the manage tags dialogs launched off of them

### tag filter

* when you copy namespaces from the tag filter list, it now copies the actual underlying data text like `character:`, which you can paste in elsewhere, rather than the pretty `"character" tags` display text
* brushed up some of the UI and help text on the tag filter UI
* fixed a couple places where the tag copy menus were trying to let you copy an empty string, which ended up with `-invalid label-`
* fixed some extremely janked-out logic in the tag filter that was sending `(un)namespaced tags` to the 'except for these' advanced whitelist in many cases. it was technically ok, but not ideal and overall inhuman

### concatenated source urls

* on rule34.xxx and probably some other places, when the file has multiple source urls, the gelbooru-style parsers were pulling the urls in the format [ A, B, C, 'A B C' ], adding this weird extra string concatenation that is obviously invalid. I fixed the parsers so it won't happen again
* **on update, you are going to get a couple of yes/no dialogs asking if you want to scan for and delete existing instances of these URLs**. if you have a big client, it will take some time to do this scan. the yes/no dialogs will auto-yes after ten minutes, so if you are doing a headless update via docker or something, please be patient--it will go through

### note sidecars

* the note->media sidecar exporter module now has a 'forced name' input. if you want to parse a single note from a .txt or .json that doesn't have a name, you can now force it
* the sidecard txt separator dropdown in the .txt importer module now has a 'four pipes (||||)' entry in the dropdown as a quick-select beside 'newline'. four pipes is a useful separator of multi-line notes content since it almost certainly won't come up in a normal note
* some tooltips and stuff are updated around here to better explain what the hell is going on
* added a unit test to test the forced name

### misc

* to help the recent shortcuts change that merged `numpad` variants of + and left arrow and so on into being seen as the `unmodified` variants, if you have a saved shortcut that _is_ still the `numpad` variant, it will now match the `unmodified` input when the merge mode is on. just means you don't have to remap everything with this mode on--everything merged matches everything
* added 'copy file known urls' to the 'media' shortcut set
* I forgot to mention last week that we figured out more native global menubar tech (where the top menubar of the program will embed into your OS's top system menubar) in last week's release, for non-macOS (some versions of Linux) users. the new checkbox is under _gui->Use Native MenuBar_. it defaults to on for macOS and off for everyone else, but feel free to try it. there was a related 'my menubar is now messed up, why?' bug that hit some people in v564 that is fixed today. sorry if you got boshed by this, since it was tricky to manually fix. in future, note you can hit ctrl+p in a default client to bring up the command palette, and then you can type 'options' and can open the options that way, if your menubar isn't working!
* fixed the `ideal usage` calculation in _database->move media files_ when there are three or more competing storage locations with two or more having a max size that is exceeded by their weight, and one or more having a max size that is only exceeded by their weight a little bit. due to a mistake in how total remaining weight was calculated in the little behind the scenes elimination game here, a location in this situation was exceeding its max size amount by a multiple of `1/(1-total_normalised_weight_of_restricted_locations)`, typically +10-30%. thank you for the report here, it was interesting to figure out!
* I removed a hack that made the repositories (like the PTR) work for users running super old versions of the client. the hack has now been in place for more than a year. if you run into repository syncing problems, please update to after v511!
* fixed a dumb status line in the 'check for missing/invalid files' checker thah was double-counting bad files in the popup
* fixed some media duration 'second' components being rendered with extraneous .0, like '30.0 seconds'
* fixed a db routine that fetches a huge table in pieces to not repeat a few rows when the ids it is fetching are non-contiguous, and to report the correct quantity of work done as a result (it was saying like 17,563/17,562)
* the new _help->about_ Qt platformName addition will now say if the actual platformName differs from the running platformName (e.g. if it was set otherwise with a Qt launch parameter)

### client api

* just a small thing, but the under-documented `/manage_database/get_client_options` call now says the four types of default tag sort. I left the old key, `default_tag_sort`, in so as not to break stuff, but it is just a copy of the `search_page` variant in the new `default_tag_sort_xxx` foursome
* client api version is now 62

## [Version 564](https://github.com/hydrusnetwork/hydrus/releases/tag/v564)

### more macOS work

* thanks to a user, we have more macOS features:
* macOS users get a new shortcut action, default Space, that uses Quick Look to preview a thumbnail like you can in Finder. **all existing users will get the new shortcut!**
* the hydrus .app now has the version number in Get Info
* **macOS users who run from source should rebuild their venvs this week!** if you don't, then trying this new Quick Look feature will just give you an error notification

### new fuzzy operator math in system predicates

* the system predicates for width, height, num_notes, num_words, num_urls, num_frames, duration, and framerate now support two different kinds of approximate equals, ≈: absolute (±x), and percentage (±x%). previously, the ≈ secretly just did ±15% in all cases (issue #1468)
* all `system:framerate=x` searches are now converted to `±5%`, which is what they were behind the scenes. `!=` framerate stuff is no longer supported, so if you happened to use it, it is now converted to `<` just as a valid fallback
* `system:duration` gets the same thing, `±5%`. it wasn't doing this behind the scenes before, but it should have been!
* `system:duration` also now allows hours and minutes input, if you need longer!
* for now, the parsing system is not updated to specify the % or absolute ± values. it will remain the same as the old system, with ±15% as the default for a `~=` input
* there's still a little borked logic in these combined types. if you search `< 3 URLs`, that will return files with 0 URLs, and same for `num_notes`, but if you search `< 200px width` or any of the others I changed this week, that won't return a PDF that has no width (although it will return a damaged file that reports 0 width specifically). I am going to think about this, since there isn't an easy one-size-fits-all-solution to marry what is technically correct with what is actually convenient. I'll probably add a checkbox that says whether to include 'Null' values or not and default that True/False depending on the situation; let me know what you think!

### misc

* I have taken out Space as the default for archive/delete filter 'keep' and duplicate filter 'this is better, delete other'. Space is now exclusively, by default, media pause/play. **I am going to set this to existing users too, deleting/overwriting what Space does for you, if you are still set to the defaults**
* integer percentages are now rendered without the trailing `.0`. `15%`, not `15.0%`
* when you 'open externally', 'open in web browser', or 'open path' from a thumbnail, the preview viewer now pauses rather than clears completely
* fixed the edit shortcut panel ALWAYS showing the new (home/end/left/right/to focus) dropdown for thumbnail dropdown, arrgh
* I fixed a stupid typo that was breaking file repository file deletes
* `help->about` now shows the Qt platformName
* added a note about bad Wayland support to the Linux 'installing' help document
* the guy who wrote the `Fixing_Hydrus_Random_Crashes_Under_Linux` document has updated it with new information, particularly related to running hydrus fast using virtual memory on small, underpowered computers

### client api

* thanks to a user, the undocumented API call that returns info on importer pages now includes the sha256 file hash in each import object Object
* although it is a tiny change, let's nonetheless update the Client API version to 61

### boring predicate overhaul work

* updated the `NumberTest` object to hold specific percentage and absolute ± values
* updated the `NumberTest` object to render itself to any number format, for instance pixels vs kilobytes vs a time delta
* updated the `Predicate` object for system preds width, height, num_notes, num_words, num_urls, num_frames, duration, and framerate to store their operator and value as a `NumberTest`, and updated predicate string rendering, parsing, editing, database-level predicate handling
* wrote new widgets to edit `NumberTest`s of various sorts and spammed them to these (operator, value) system predicate UI panels. we are finally clearing out some 8+-year-old jank here
* rewrote the `num_notes` database search logic to use `NumberTest`s
* the system preds for height, width, and framerate now say 'has x' and 'no x' when set to `>0` or `=0`, although what these really mean is not perfectly defined

## [Version 563](https://github.com/hydrusnetwork/hydrus/releases/tag/v563)

### macOS improvements

* Thanks to a user, we have multiple improvements for macOS!
* There is a new icon for the macOS .app build of hydrus
* The macOS app will now appear as "Hydrus" in the menu bar instead of "Hydrus Network"
* - Use the native global menu bar on macOS and some Linux desktop environments
* - "options" will now appear as "Preferences..." and be under the Hydrus menu on macOS
* - "exit" will now appear as "Quit Hydrus" and be under the Hydrus menu on macOS
* "exit and force shutdown maintenance", "restart", and "shortcuts" will now be under the Hydrus menu on macOS
* The hydrus system tray icon is now enabled for macOS and "minimise to system tray" will be in the Hydrus menu when in advanced mode
* macOS debug dialog menus are now disabled by default
* The macOS build of hydrus now uses pyoxidizer 0.24.0 and Python 3.10
* The command palette and hyperlinks colors in the default Qt stylesheet now use palette based colors that should change based on the Qt style
* one thing hydev did: on macOS, Cmd+W _should_ now close any dialog or non-main-gui window, just like the Escape key

### shortcuts

* by default, Alt+Home/End/Left/Right now does the new thumbnail rearranging. **assuming they do not conflict with an existing mapping, all users will recieve this on update**
* by default, the shortcuts system now converts all non-number 'numpad' inputs (e.g. 'numpad Home', 'numpad Return', 'numpad Left') to just be normal inputs. a bunch of different keyboards have whack numpad assignments for non-numpad keys, so if it isn't a number, let's not, by default, make a fuss over the distinction. you can return to the old behaviour by unchecking the new checkbox under _file->shortcuts_
* the default shortcuts now no longer spam numpad variants anywhere. existing users can delete the surplus mappings (under 'thumbnails' and maybe some of the 'media' sets) if they like

### some UI QoL

* the _tag service_ menu button that appears in the autocomplete panel and sometimes some other places in advanced mode now shows a proper check mark in its menu beside its current value
* the _location context_ menu button on the other side of an autocomplete panel and some other places also now shows a check mark in its menu beside its current value
* the `OR` button on search autocomplete that creates new OR predicates now inherits the current file search domain. it was previously defaulting at all times to the fallback file domain and 'all known tags'
* the current search predicates list also now inherits the file search domain when you edit an OR predicate currently in use, same deal
* removed the 'favourites' submenu from the taglist menu when no tags are selected
* in any import context, the file log's arrow menu now supports deleting all the 'unknown' (outstanding, unstarted) items or setting them all to 'skipped'. the 'abort imports' button (with the stop icon) in HDD and urls import pages is removed

### misc

* fixed yet another dumb problem with the datetime control's paste button--although the paste was now 'working' on the UI side, the control wasn't saving that result on dialog ok. the fixes both the datetime button and the modified/file service time multi-column list editing
* a core asynchronous thread-checking timer in the program has been rewritten from a 20ms-resolution busy-wait to a <1ms proper wait/notify system. a bunch of stuff that works in a thread is now much faster to recognise that blocking UI work is done, and it is more thread-polite about how it does it!
* in the `setup_venv` scripts, if it needs to delete an old venv directory but fails to do so, the script now dumps out with an error saying 'hey, you probably have it open in a terminal/IDE, please close that and try again'. previously, it would just charge on and produce an odd file permission error as, e.g., the new venv setup tried to overwrite the in-use python exe
* added a `help->debug->gui->isolate existing mpv widgets` command to force regeneration of mpv windows and help test-out/hack-fix various 'every other of my mpv views has no audio' and 'my mpv loses xxx property after a system sleep/wake cycle' problems. if I've been working with you on this stuff, please give it a go and let me know if new mpv window creation is good or what!
* added a `BUGFIX: Disable off-screen window rescue` checkbox to `options->gui` that stops windows that think they are spawning off-screen from repositioning to a known safe screen. several Qt versions have had trouble with enumerating all the screens in a multiple monitor setup and thus the safe coordinate space, so if you have been hit by false positives here, you can now turn it off! (issue #1511)
* fixed another couple instances of error texts with empty formatting braces `{}`

### tag repository

* mapping petitions fetched from the server will now max out at 500k mapping rows or 10k unique tags or ten seconds of construction time. we had a 250k-unique-tag petition this last week and it broke something, so I'm slapping a bunch of safety rails on. let me know if these are too strict, too liberal, or if it messes with the fetch workflow at all--I don't _think_ it will, but we'll see

### build stuff

* now they have had time to breathe, I optimised the recently split Github build scripts. the 'send to an ubuntu runner and then upload' step is now removed from all three, so they are natively uploaded in the first runner step. it works just a little nicer and faster now, although it did require learning how to truncate and export a variable to the Github Environment Variables file in Powershell, aiiieeeee
* also, Github is moving from Node 16 to Node 20 soon, and I have moved two of the four actions we rely on to their newer v20 versions. a third action should be ready to update next week, and another, a general download file function, I have replaced with curl (for macOS) and Powershell's magical Invoke-WebRequest adventure

## [Version 562](https://github.com/hydrusnetwork/hydrus/releases/tag/v562)

### misc

* page tab drag and drops will now not start unless the click has lasted more than 100ms
* same for thumbnail drag and drop--it perviously did a 20 pixel deadzone, but time checks detect accidental/spastic clicks better and stops false negatives when you start dragging on certain edges
* added a 'BUGFIX: disable page tab drag and drop' setting to _options->gui pages_. while adding this, I may have accidentally fixed the issue I wanted to investigate (rare hangs on page DnD)
* the manage tags dialog now shows the current count of tags for each page tab, and, if there are outstanding changes, shows an asterisk
* the `migrate database` dialog is renamed `move media files`

### fixes

* fixed the basic copy/paste in the single 'edit datetime' panel, wich was often raising a dumb error. this thing also now exports millisecond data (issue #1520)
* I am pretty sure I fixed the column-resizing problem in the very new PySide6 (Qt) 6.6.1, which it seems AUR users were recently updated to in an automatic OS update. all columns were setting to 100px width on initialisation. I think it is now safe to try out 6.6.1. I am still not sure why it was doing this, but some extra safeguards seem to have fixed it and also not broken things for <=6.6.0, so let me know what you run into! if you were affected by this, recall that you can right-click on any multi-column list header and say 'reset widths' to get something sensible back here
* when exporting files, the max size is now clipped another 84 characters (64 + 20 more, which usually ends up about 150 characters max for the output filename), in order to give padding for longer sidecar suffixes and also avoid going right to the filesystem limit, which broadly isn't sensible
* I think I fixed an issue where the mouse could stay hidden, perhaps, just on Wayland, after closing the media viewer with your keyboard (issue #1518)
* fixed inc/dec ratings in the media viewer not updating their tooltips on new media correctly
* if you hit 'open this location' on the export files window and the location does not exist, you now get a nice messagebox rather than a semi-silent error

### analyze

* background: some databases that process the PTR superfast or otherwise import a lot of data to a new file domain sometimes encounter massively massively slow tag update actions (typically tag-delete when the tags involved have siblings/parents), so I want to make the critical 'ANALYZE' call more timely
* the 'analyze' database maintenance call will be soft-called far more regularly during normal repository processing, not just on first sync
* sped up how some pre-analyze calculation is done
* the size limit for automatic database analyze maintenance is raised from 100k rows to 10M
* I hope to do more work here in future, probably making a review panel like we did for vacuum
* if your repository processing sometimes hangs your whole damn client for 10-15 minutes, hit _database->db maintenance->analyze->full_! this job may take 30-60 minutes to finish

### boring code cleanup

* finished the HG->CG.client_controller refactor I started last week. this was a thousand lines changed from one braindead format to another, but it will be a useful step towards untangling the hell-nest import hierarchy
* did a scattering of the clientinterface typing, getting a feel for where I want to take this
* deleted the old in-client server-test's 'boot' variant; this is no longer used and was always super hacky to maintain
* I removed an old basic error raising routine that would sometimes kick in when a hash definition is missing. this routine now always fills in the missing data with garbage and does its best to recover the invalid situation automatically, with decent logging, while still informing the user that things are well busted m8. it isn't the user's job to fix this, and there is no good fix anyway, so no point halting work and giving it to the user to figure out!

## [Version 561](https://github.com/hydrusnetwork/hydrus/releases/tag/v561)

### rearranging thumbnails

* on the thumbnail menu, there is a new 'move' submenu. you can move the current selection of files to the start or end of the media list, or to one before or after the earliest selected file, or to the file you right-clicked on to create the menu, or to the first file's position if the selection is not contiguous. if the selection is non-contiguous, it will be made so in the move
* added these rearrange commands to the shortcuts system, as 'move thumbnails' under the 'thumbnails' set. I wasn't sure whether to add some default shortcuts, like ctrl+numpad 7/3/4/6 for home/end/left/right or something--let me know what you think

### misc

* thanks to user help, fixed a stupid typo from last week that caused some bad errors (including crashes, in some cases) when doing non-simple duplicate filtering (issue #1514). this is the issue the v560a hotfix was made for
* fixed another stupid content update typo that was causing 'already in db' results to not get metadata updates
* as a hardcoded shortcut, Ctrl+C or Ctrl+Insert now copies the currently selected tags in any taglist. it'll output the full tag/predicate text, with namespace, no counts
* I've shortened some thumbnail/media-viewer menu labels, made the 'delete' line into a submenu, and ensured the top info line is always a short variant, with detailed info bumped off to the submenu off the top line. I hate how these menus are often super-wide and thus a pain to navigate to the submenus, so let me know what situations still make them wide
* the file log arrow button menu now has entries for 'delete already in db' and 'delete everything'
* the 'add these tags to the favourites list?' yes/no now only fires if you try to add more than five tags ot once
* the various dialogs in the client that auto-yes or auto-no now show a live countdown in their title string
* the window position saving system is now stricter about what it records. maximised and fullscreen state is only saved if 'remember size' is false, and the last size/position is not saved at all if 'remember size/position' is false (previously, it would save these values but not restore them, but let's try being more precise here)
* fixed a 'omg what happened, closing the window now' error in the duplicate filter if you try to 'go back' while it is loading a new set of pairs to show
* fixed the 'vacuum db' command to correctly save 'last vacuumed time' for all files vacuumed in a job, not just the last!
* whenever a `copy2` file copy (which includes copying file times and permission bits) fails for permission reasons, hydrus now falls back to a normal `copy` and logs the failure, including the modified time that failed to copy (which is the bit we actually care about here)

### db update stuff

* if there is a known bitrot issue on update, you now get a nicer error message. rather than the actual error, you are now told which version is safe to update to. to christen this system, I've added a check for the recent millisecond timestamp conversion, which caused some issues for users updating older clients. **if your client is v551 or older and you try to update to v561 or later, you will be told to update to v558 first.** sorry for the inconvenience here, and thank you for the reports (issue #1512)
* if you try to boot a database more than 50 versions earlier than the code, the client-based version popups now happen in the correct order, with the >50 exception firing before the >15 warning
* when an update asks a not-super-important yes/no question, I will now make it auto-yes or auto-no after ten minutes with the recommended value. this will ensure that automatic updaters will still progress (previously, they were hanging forever!)

### some downloader stuff

* thanks to a user, the derpibooru now fetches the post description as a note and the source as an associable URL. I tweaked the submitted stuff a bit, simplifying the parsing and discluding 'No description provided.' notes
* thanks to a user, the e621 parser can now grab files from posts where the (spicy, I think) content is normally not shown due to a guest login. the posts still won't show up in guest-login gallery searches, so this won't alter your normal results, but if you run into a post like this in your browser and drag-and-drop it onto the client, it now works
* I tried to improve the parsing system's de-newlining. this thing is a long-time hack--I've never liked it and I want to replace it with proper multi-line support--but for now I've made sure the de-newliner strips each line of leading/trailing whitespace and discards empty lines. the mode that _doesn't_ collapse newlines (note parsing, for the most part) now _does_ strip leading/trailing newlines along with other whitespace, meaning you no longer have to try and strip extra `<p>` and `<br>` tags and stuff yourself when grabbing notes. also, the formula UI where it says 'Newlines are collapsed before...' now says when it won't be collapsing newlines due to it being a note parser
* the String Match processing step now explicitly removes newlines before it runs, meaning it can still catch multi-line notes properly. you can now run a proper regex on a multi-line note

### boring cleanup

* optimised some thumbnail handling code, stuff like fetching the current list of sorted selected media
* large collections will be a little faster to select and otherwise do operations on
* sketched out a new `ClientGlobals` and client controller interface and started refactoring various HG.client_controller to the new CG. this makes no important running changes, but it cleans the messy HG file and will help future coding and type checking in the IDE as it is fleshed out
* added some help text to the edit file maintenance panel and fixed some gonk layout in the 'add new work' panel
* fixed some instances of the 'unknown' import status showing as a blank string
* fixed an error message in the export folder export job that fired when a file to be exported is missing--it was just giving blank instead of the file hash, and its direction to file maintenance was old and unclear

## [Version 560](https://github.com/hydrusnetwork/hydrus/releases/tag/v560)

### editing times for multiple files

* the 'edit times' dialog is now available when you select multiple files. it will show and apply time data for all of those files at once. when the files have different times, the various widgets and panels will show ranges and a count of how many files do and don't have that particular time type
* when you open the edit times dialog on more than one file, every time control now has a 'cascade step' section, where you can set a time delta, e.g. 100 milliseconds, and then, on dialog ok, each file in the selection that launched the dialog will be set that much successively later than the previous, obviously in the order they are currently in. this is a way of forcing/normalising file sorts based on time. negative values are allowed!
* when the edit times dialog is set to change more than 100 total times, it now verifies with the user that this is correct on dialog ok
* when the edit times dialog sets a lot of modified dates to files (i.e. actually writing them to your file system), this now happens in a non-gui thread and now makes a cancellable progress popup after a few seconds

### misc

* fixed the 'imported to' timestamp for files migrated to other local file domains, which were one of the ones incorrectly set, as expected, to 54 years ago. in the database update, I also fix all the wrongly saved ones from v559
* mr bones and the file history chart are now under the 'database' menu
* fixed an issue with the file history chart not maintaining the `show_deleted = False` state through search refreshes
* there's a new checkbox under `files and trash`, `Remove files from view when they are moved to another local file domain`. this re-introduces the unintended behaviour that I fixed recently when 'remove when trashed' was set, but now targeted specifically for that situation. if you use multiple local file domans a bunch and want files to disappear when you shoot them to a place you aren't looking at, give it a go and let me know how it works for you
* fixed a regression from my 'remove when trashed' fix where deleting collections with this option on would leave crazy ghost thumbnails behind. collections that are completely emptied should now properly remove themselves in all content update situations
* the gallery downloader page 'cog' icon now has a 'do not allow new duplicates' option, which will discard any (query_text,source) pairs you try to enter if they already exist in the list. this option is remembered through restarts
* added 'sort by pixel hash' to the file sort menu. it isn't super helpful, but it'll show pairs of exact-matching files next to each other amongst a sea of noise. I may expose perceptual hashes in a similar way in future, which would be more useful, but thumbnails don't have their phashes quickly available atm, so maybe only when there are other reasons to add that overhead
* fixed the `setup_venv.sh` and `setup_venv.command` files' custom qtpy and PySide6 (Qt stuff) version installer! there was a dumb typo, sorry for the trouble
* thanks to a user, the derpibooru parser now grabs `fanfic`, `spoiler`, and `error` tags

### boring cleanup

* neatened up how non-thumbnail-generatable files (e.g. rtf) present their default thumbs and refactored the code a little
* when a file's thumbnail is unavailable but the filetype is known (e.g. you are looking at records of deleted files that have no blurhash), hydrus should now deliver that file's default thumb instead
* unified this thumbnail-defaulting code a little more, fixing fetching for some weirder files and deduplicating some messy areas. the client thumbnail cache should be better about delivering the right unusual thumbnail now and as future filetypes are added
* added an 'image.png' to serve as a nicer fallback for various thumbnail-undeliverable but known-image files
* fixed rtf files not providing their rtf thumbnail in the Client API
* fixed up some ancient local booru thumbnail fetching code
* cleaned up some messy dialog launches that were having to navigate single/collected media in an awkward way
* removed the TestFunctions unit test stub, which was of diminishing use

### boring cleanup, time code

* updated the DateTime control and button to handle multiple times at once, and updated the edit timestamps dialog itself similarly throughout (this took a day and a half lol)
* rejiggered the DateTime widgets to handle a nice new object to hold the multiple times' range, since it was all getting messy
* rejiggered the time content update pipeline from top to bottom to take multiple hashes per content update, so applying the same timestamp to a thousand files should still be pretty quick
* fixed up various timestamp_ms->QtDateTime conversions so they all include local timezone info. also fixed the datetime widget so it returns properly local-timezone'd datetimes. I can no longer easily reproduce a particular time that jumps an hour every time you open it (due to retroactive summer-time fun)
* harmonised some older datestring conversions to come out 2023-06-30 instead of 2023/06/30
* fixed some time string calculations to handle our new sub-second times better
* updated the time delta widget to handle negative numbers

### boring cleanup, content updates

* moved all `ContentUpdate` gubbins out of the hydrus module scope; it is now client only
* made a new `ClientContentUpdates.py` to collect all content update code and refactored stuff there
* wrote a new `ContentUpdatePackage` to replace the ancient `service_keys_to_content_updates` structure. various hacky or ad-hoc processing and presentation is now gathered under this new object, and I refactor-spammed it across the program, with too many individual changes to talk about in detail

### client api

* the new `set_time` call has some additional safety rails. you can add (or delete) 'web domain' timestamps any time, but you now cannot add or delete any of the others, only edit when they already exist
* updated the client api unit tests and help to account for this
* the client api is now version 60

## [Version 559](https://github.com/hydrusnetwork/hydrus/releases/tag/v559)

### millisecond timestamps

* since the program started, the database and code has generally handled timestamps as an integer (i.e. whole number, no fractions) count of the number of seconds since 1970. this is a very common system, but one drawback is it cannot track any amount of time less than a second. when a very fast import in hydrus imports two files in the same second, they then get the exact same import time and thus when you sort by import time, the two files don't know which should be truly first and they may sort either way. this week I have moved the database to store all file timestamps (archived time, imported time, etc...) with millisecond resolution. you do not have to do anything, and very little actually changes frontend, but your update may take a minute or two
* whenever you sort by 'import time' now, we shouldn't get anymore switcheroos
* the 'manage times' dialog now has millisecond display and edit widgets to reflect this, but in most places across the client, you'll see the same time labels as before
* I changed a **ton** of code this week. all simple changes, but I'm sure a typo has slipped through somewhere. if you see a file with a 'last viewed time' of '54 years ago', let me know!

### time details

* this section is just a big list so I have somewhat of a record of what I did. you can broadly ignore it
* updated `vacuum_timestamps` to `timestamp_ms` and adjusted read/write and the dialog handling to ms
* updated `analyse_timestamps` to `timestamp_ms` and adjusted read/write to ms
* updated `json_dumps_named` to `timestamp_ms` and adjusted read/write and some UI-level gubbins around session loading and saving to ms
* updated `recent_tags` to `timestamp_ms` and adjusted the whole system to ms
* updated `file_viewing_stats` to `last_viewed_timestamp_ms` and adjusted read/write to ms
* updated `file_modified_timestamps` to `file_modified_timestamp_ms` and adjusted read/write to ms, including to and from the disk
* updated `file_domain_modified_timestamps` to `file_modified_timestamp_ms` and adjusted read/write to ms
* updated `archive_timestamps` to `archived_timestamp_ms` and adjusted read/write to ms
* updated all the current- and deleted-file tables for all file services to use ms (`timestamp_ms`, `timestamp_ms`, and `original_timestamp_ms`) and adjusted _all_ database file storage, search, and update to work in ms
* updated the `ClientDBFilesTimestamps` db module to use ms timestamps throughout
* updated the `ClientDBFilesViewingStats` db module to use ms timestamps throughout
* updated the `ClientDBFilesStorage` db module to use ms timestamps throughout
* updated the controller timestamp tracker and all callers to use ms timestamps throughout
* renamed `TimestampsManager` to `TimesManager` and `times_manager` across the program
* updated the `TimesManager` and all of its calls and callers in general to work in ms. too much stuff to list here
* the `TimestampData` object is now converted to ms, and since it does other jobs than a raw number, the various calls it is involved in are generally renamed from 'timestamp' to 'time'
* the file viewing stats manager now tracks 'last viewed time' as ms, and the update pipeline is also updated
* the locations manager now handles all file times in ms, and all the archive/add/delete pipelines are also updated
* wrote some MS-based variants of the core time functions for spamming around here, including for both Qt `QDateTime` and python `datetime`
* updated the main datetime edit panel, button, and widget to handle millisecond display and editing
* fleshed out a ton of ambiguous variable names to the new strict time/timestamp/timestamp_ms system
* wrote a clean transition method between ms<->s that accounts for various None situations and spammed it everywhere
* fixed up some ill-advised timestamp data juggling in the time edit UI

### what still has second-resolution

* the parsing system (and hence downloaded files' source times)
* the sidecar system's time stuff, both import and export
* the server and the hydrus network protocol in general
* Mr. Bones and the File History chart
* almost all the actual UI labels. I'm not going to spam milliseconds at you outside of the time edit UI
* almost all the general maintenance timers, sleepers, and grunt-work code across the program

### client api

* the `file_metadata` call has a new parameter, `include_milliseconds`, which turns the integer `1704419632` timestamps into floats with three sig figs `1704419632.154`, representing all the changes this week
* a new permission, `edit file times` is added, with value `11`
* a new command, `/edit_times/set_time` now lets you set any of the file times you see in the _manage times_ dialog. you can send it second- or millisecond-based timestamps
* the client api help is updated for all this, particularly the new section here https://hydrusnetwork.github.io/hydrus/developer_api.html#edit_times_set_time  
* added unit tests for this
* the client api version is now 59

### misc

* the sankaku parsers, GUGs, and custom header/bandwidth rules are removed from the defaults, so new users will not see them. none of this stuff works well/at all any more, especially in recent weeks. for sites that are so difficult to download from, if there isn't a nice solution on the shared downloader repo, https://github.com/CuddleBear92/Hydrus-Presets-and-Scripts, I recommend going with a more robust solution like gallery-dl or just finding the content elsewhere
* when there are multiple 'system:known url' predicates in a search, I now ensure the faster types run first, reducing the search domain for the slower, later ones. if you have a 'regex' 'known url' predicate, try tossing in a matching 'domain' one--it should accelerate it hugely, every time
* fixed a bug in the autocomplete dropdown where it was not removing no-longer-valid file services from the location button after their deletion from _manage services_ until program restart (which was causing some harmless but unwelcome database errors). it should now remove them instantly, and may even end up on the rare 'nothing' domain
* the duplicate filter will no longer mention pixel-perfect pngs being a waste of space against static gifs--this isn't necessarily true
* the default height of the 'read' autocomplete result list is now 21 rows, so `system:time` and `system:urls` are no longer subtly obscured by default. for existing users, that's under _options->search_
* in the 'running from source' requirements.txts, I bumped the 'new' and 'test' versions for python-mpv to 1.0.4/1.0.5. the newest python-mpv does not need you to rename libmpv-2.dll to mpv-2.dll, which will be one less annoying thing to do in future. I've also been testing this extremely new dll this week and ran into no problems, if you are also a Windows source user and would like to try it too: https://sourceforge.net/projects/mpv-player-windows/files/libmpv/mpv-dev-x86_64-20231231-git-abc2a74.7z . I also tried out Qt 6.6.1, but I just discovered a column-sizing bug I want to sort out before I roll it out to the wider community
* updated the sqlite dll that gets bundled into the windows release to 3.44.2. the sqlite3.exe is updated too

## [Version 558](https://github.com/hydrusnetwork/hydrus/releases/tag/v558)

### user contributions

* thanks to a user, we now have rtf support! no word count yet, but it should be doable in future.
* thanks to a user, ctrl+p and ctrl+n now move the tag listbox selection up and down, in case the arrow keys aren't what you want. it also works on the tag autocomplete results from the text input
* added a link to 'Hydra Vista', https://github.com/konkrotte/hydravista, a macOS booru-like browser that talks to a hydrus client, to the main Client API help

### misc

* if you right-click on a selection of multiple tags, you can now hide them or their namespaces en masse
* if you right-click on a selection of multiple tags, you can now add or remove them from the favourites list en masse. if you select a mix of tags that are part-in, part-out of the list, you'll get both add and remove menu entries summarising what's going on. also, this command is now wrapped in a yes/no confirmation with full summary of what's being added/removed
* the 'favourites' "tag suggestions" section is renamed to 'most used'. this was often confused with the favourites that sit under a tag autocomplete, and these tags aren't really 'favourite' anyway, just most-used, so they are renamed
* if you have 'remove files from view when they are sent to the trash' set, then moving a file from one local file domain to another or removing one of multiple local file domains will no longer trigger a 'remove media'! sorry for the trouble, it was dumb logic on my part
* fixed the 'known urls' menu's url class section ('open all blahbooru urls' etc...) not appearing when right-clicking a single 'collection' thumbnail
* fixed the 'known urls' menu's open/copy specific urls not appearing when right-clicking any collection. it now shows the front 'display media's' urls
* if you change the darkmode in _options->colours_, the _help->darkmode_ menu item now updates correctly. just a side note: I hate much of this system and will eventually unify it with the style system
* fixed a bunch of 'number of x' tests at the database level when the operator is `≠`

### system:number of urls

* added `system:number of urls`! note this counts raw URLs at the moment--I just don't have fast database filtering of post urls vs file urls or url-classless urls or whatever. it does a raw count.
* `system:known urls` is now tucked with this new `system:number of urls` under a new stub predicate called `system:urls`
* a variety of 'system:number of words: has/no words' predicates now parse correctly when typed
* wrote some new system predicate parsing tests

### more cbz rules

* cbzs' non-image files must now have an appropriate extension like .txt, .nfo, or .xml
* the test regarding the count of non-image files (typically allowing up to 5 non-image files per directory) is more precise with regards to subdirectories, meaning a cbz with a single subdirectory and three non-image files now counts as a cbz
* every cbz must now have at least two image files that contain a number of some sort

### cleanup and boring stuff

* I split the github workflow build file into three, so the windows, linux, and macOS builds now all happen and upload in parallel. previously, the upload step was blocked on the slowest of the three, which was typically the macOS build by about ten minutes; now they all upload whenever they are ready. this will also help some future testing situations. the newly split scripts are a little unclean/inefficient, so there is also more work to do here
* I think I fixed the non-Windows executable permission bits for the various .sh and .command files in the base directory, which were lacking them, and I removed it from a couple dozen pngs across the docs and static directories, which somehow had them. let me know if I missed anything or messed anything up!
* if you click one of the static system predicate buttons that appear in the system pred edit UI, for instance 'system:has duration', this no longer gets promoted to the 'recent' predicates list the next time you open the panel
* some sytem predicate edit panels should stretch vertically a bit better
* some 'number of tags' queries should be a little faster
* the 'tag suggestions' options page has a bit of brushed up UI and some new explanation labels
* unified the various thumbnail generation error reporting for all the different filetypes. it should also print the file's hash, too, since most of these error contexts only have a temporary path to talk about at this stage, which isn't useful after the fact

## [Version 557](https://github.com/hydrusnetwork/hydrus/releases/tag/v557)

### misc

* optimised large tag filter edit UI. you can now paste 5,000 items into an empty tag filter blacklist in less than a second, and if you have a big tag filter, removing or adding one thing is now instant (previously, this stuff would lag 4 seconds or more, sometimes multiple minutes!!)
* the ugoira 'num frames' counting method now discludes files ending in .js/.json, to catch future bundling of frame timings
* the cbz scanning tech should now recognise cbzs with four or fewer pages
* a legacy 'is this image all good?' check that happens on PIL-loading is now gone. this improves rendering for a variety of truncated files and clarifies some error messages (previously, this thing was just failing silently)
* fixed the delete file pre-flight logic so users on the non-advanced delete dialog can now delete repository updates. previously, they saw the menu entry, but hitting it was a no-op

### better hash predicate parsing

* `system:hash` labels are a little different now. they'll say `system:hash (md5) is abcd...`, with the algorithm after the "hash". hash is omitted for sha256 (the hydrus default). this eases parsing
* `system:similar to data` labels are a little different. they'll say 'distance' instead of 'max hamming', and the number and type of hashes they hold, and if they hold only pixel hashes, the distance is not stated
* `system:hash` predicate parsing is now more flexible. you can put the hash type pretty much anywhere now.
* `system:similar to` and `system:similar to data` predicate parsing is now more flexible. more combinations are allowed, and you can not include distance and it'll be fine
* these three hash predicates now copy to clipboard with all their hashes explicitly enumerated, making strings that are fully parsable! this is a big step forward in a completely sealed import-export predicate parsing loop; now I have the tech set up to export a different phrase to clipboard than what you see in the label, I just need the examples of where it goes wrong. if there is a system predicate that copies to clipboard in a way that won't parse back, let me know and I'll see if I can fix it.
* added more unit tests for this parsing

### documentation and cleanup

* wrote a guide on how to install 'Git for Windows' for the 'running from source' help. although most of the settings in its marathon 12-page install wizard can be left as default, the technical questions can be intimidating, so I've written them all out for a nice simple install. also brushed up some of the surrounding help here
* added a warning to the regular 'installing and updating' help regarding the danger of test-running extract releases before updating (you can overwrite your database by accident)
* thanks to a user, the filetypes help document is updated with Ugoira and CBZ info
* all the 'HydrusFiletypeHandling' files are refactored to a new 'files' module. there's a bunch of them these days!
* the hydrus.core.images module is moved beneath this 'files' module too
* the file log list panel right-click menu now says 'open URLs'/'open files' locations' depending on whether you are looking at a URL import log or local HDD import log

### client api

* the `file_metadata` call now returns `filetype_forced` and, if so, also `original_mime` to talk about the new forced filetype system
* the client api help and unit tests are updated to test this is working ok
* fixed a typo that was causing too much work in the updated file info manager call (and was often returning 'null' results for half-cached `file_metadata` requests with `only_return_basic_information=true`)
* thanks to a user, the `/add_urls/get_url_info` Client API call now has a cache timeout of ten minutes, and the `/add_urls/get_url_files` call now has a timeout of 30 seconds if all the files are 'already in db'. this should automatically reduce some overhead for several programs that talk to the Client API a lot about URLs
* the client api version is now 58

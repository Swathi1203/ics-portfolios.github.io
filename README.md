# ICS Portfolios

A simple web application to display professional portfolios for students, faculty, and staff.

## Installation

### 1. Install jekyll

ICS Portfolios is a static website generated by Jekyll.  So, the first thing you should do is [install Jekyll](https://jekyllrb.com/docs/installation/).

Before continuing, I suggest you verify your Jekyll installation by creating a simple Jekyll site and displaying it following the [Jekyll Quickstart Page](https://jekyllrb.com/docs/).

ICS Portfolios has been tested on Jekyll 4.2.0.

### 2. Install update_ics_portfolios script

`update_ics_portfolios` is a node script that is used to parse a data file containing information about portfolios and produce a new data file that can be used by the deployed site to display the "card" associated with each portfolio.

To install `update_ics_portfolios`, first [install node](https://nodejs.org/en/).

Second, make it possible to run `update_ics_portfolios` as a top-level command by doing the following:

```
 npm install
 sudo npm link
 ```

To test your installation, invoke `update_ics_portfolios`:

```
update_ics_portfolios
```

The output should look something like this:

```
$ update_ics_portfolios
Starting update_ics_portfolios
Writing jekyll info files.
All interests are mapped to a term
Writing _data/emails.txt
Writing to _includes/notableCards.html
```

If it differs, there is probably a problem with profile-entries.json. Don't worry, I'll provide some information on how to deal with that below.

### 3. Install Jekyll third party libraries

To install the libraries for ICS Portfolios, invoke:

```
bundle install
```

The output will look something like this:

```
$ bundle install
Using public_suffix 4.0.6
Using addressable 2.8.0
  :     :
Using webrick 1.7.0
Bundle complete! 7 Gemfile dependencies, 32 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
```

## Build and serve ICS Portfolios locally

To see the site locally, invoke:

```
bundle exec jekyll serve
```

The output from this command should look like:

```
$ bundle exec jekyll serve
Configuration file: /Users/philipjohnson/github/ics-portfolios/ics-portfolios.github.io/_config.yml
            Source: /Users/philipjohnson/github/ics-portfolios/ics-portfolios.github.io
       Destination: /Users/philipjohnson/github/ics-portfolios/ics-portfolios.github.io/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
                    done in 0.792 seconds.
 Auto-regeneration: enabled for '/Users/philipjohnson/github/ics-portfolios/ics-portfolios.github.io'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

You should be able to see the site locally by viewing http://127.0.0.1:4000/ in a browser.

## How to maintain the site

Maintaining this site involves:

  * Adding new portfolios each semester as new students create them.
  * Changing the status of portfolios from "undergrad" (or "grad") to "alumni" as students graduate.
  * Removing portfolios on student request or when the retrieval process for them is broken (i.e. the image is not retrieved, or there is some other issue with the portfolio.)

The basic workflow for site maintenance is:
   * Update profile-entries.json,
   * Invoke `update_ics_portfolios` script to process the profile-entries.json file.
   * Fix any errors noted by the script
   * Run jekyll to view the site in a browser, review pages to find any remaining problems.

### Update profile-entries.json

First, edit the file profile-entries.json.  This contains an array of objects, each object containing basic information about a single portfolio:
  * the "last" name of the person, which is used to sort the entries and display in alphabetical order.
  * The "techfolio", which is the domain name for the portfolio without protocol or slashes. For example, "philip.github.io" is valid, while "https://philip.github.io/" is invalid.
  * The "level" of the person: either "undergrad", "grad", "faculty", or "alumni".

### Run update_ics_portfolios and fix errors

Second, once you have updated profile-entries.json, you should run the `update_ics_portfolios` script.

In a nutshell, `update_ics_portfolios` reads the contents of profile-entries.json, and for each entry, uses the "techfolio" field to generate an HTTP request to get the contents of the bio.json file stored at that site in the `_data` directory.  Since almost all of the portfolios are built using [TechFolios](https://techfolios.github.io/), you may want to read more about them. That said, as long as a file called bio.json can be retrieved from an `_data` directory at the specified site, it doesn't actually have to be a TechFolio site.  Note that bio.json should be formatted to conform to [JSON Resume](https://jsonresume.org/).


If the script can successfully retrieve and process the contents of the bio.json file for every entry in profile-entries, then the output will look like this:

```
$ update_ics_portfolios
Starting update_ics_portfolios
Writing jekyll info files.
All interests are mapped to a term
Writing _data/emails.txt
Writing to _includes/notableCards.html
```

More often than not, though, there will be one of two problems: (1) For one or more entries, some problem occurred trying to retrieve or process the bio.json, or (2) The "interests" field of bio.json contains text that does not match any of the interest terms defined in _data/synonyms.json.

In either case, the script will output more-or-less helpful information that you can use to fix the problems that come up.

For example, if there is a portfolio containing a term that the system doesn't know about, it will listed in `_data/unclassifiedInterests.json`. You can copy those terms into `_data/synonyms.json` so they are known to the system the next time you run `update_ics_portfolios`.

If there's a problem processing the bio.json file, then you can either contact the student to fix the problem, or else remove the portfolio entry.

### Review site locally

Once update_ics_portfolios runs without errors, you are ready to preview the site locally.  As noted above, invoke:

```
bundle exec jekyll serve
```

The output from this command should look like:

```
$ bundle exec jekyll serve
Configuration file: /Users/philipjohnson/github/ics-portfolios/ics-portfolios.github.io/_config.yml
            Source: /Users/philipjohnson/github/ics-portfolios/ics-portfolios.github.io
       Destination: /Users/philipjohnson/github/ics-portfolios/ics-portfolios.github.io/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
                    done in 0.792 seconds.
 Auto-regeneration: enabled for '/Users/philipjohnson/github/ics-portfolios/ics-portfolios.github.io'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

You should be able to see the site locally by viewing http://127.0.0.1:4000/ in a browser.

There are two things you want to look for at this point:

1.  Portfolios that appear in the "wrong place" in the site.  The most common situation is a student that appears in the "undergrad" or "grad" areas but appears to have already graduated based upon their summary description.
2. Portfolio pictures that don't display, or text that is garbled or inappropriate.

In either case, edit profile-entries.json to fix (or remove) the entry, then run `update_ics_portfolios` to regenerate the data file for the site, then `bundle exec jekyll serve` to redisplay the updated site.

### Publish the updated site

Once the site displays locally the way you want it to, then simply commit your changes to the master branch. GitHub will automatically build and publish the site.









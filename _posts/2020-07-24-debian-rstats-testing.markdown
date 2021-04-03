---
layout: post
title:  "Automatic testing of R binary packages in Debian"
date:   2020-07-24
categories: debian rstats
---

# Why testing R packages in Debian?

Somewhere [after the release](https://trends.debian.net/#autopkgtest-test-suite) of [Wheezy](https://wiki.debian.org/DebianWheezy) (~ 2014), Debian developers have started to add tests into their packages (see [DEP-8](https://dep-team.pages.debian.net/deps/dep8/)) in order to improve the quality of their packages.

![](https://trends.debian.net/autopkgtest_testing-stacked.png "Trend of Autopkgtest test suite")

These tests allow to check the right behaviour of an installed package and to avoid to let in the Debian archive dysfunctional software.
Packages with an error in their tests are not keeped in Debian testing.
The migration from unstable to testing is blocked or the package is removed from testing if an error appears later.

R developers have a good tradition to commonly write units testing for their packages.
The unit tests are used during the developement of a package to ensure there is no regression or problem.
These tests are also checked during the submission to CRAN or Bioconductor repositories by `R CMD check` to avoid to upload a package with problems.
The creation of unit tests for R packages is facilitated by several packages: [RUnit](https://cran.r-project.org/package=RUnit) (the oldest), [testthat](https://cran.r-project.org/package=testthat) (the most popular) and [tinytest](https://cran.r-project.org/package=tinytest) (the newest).
I would suggest to use the last one, which comes with no dependency at all.

In Debian, we use them to perform continuous testing.
Each time there is a change in the dependency chain, all the unit tests are performed again to ensure there is no regression introduced, otherwise the migration to *testing* of the bugged package is blocked until the regression is fixed.
Moreover, the tests performed on Debian (and by implication on Ubuntu) are not restricted to the unique architecture x86-64 and use a newer OS and by consequence newer libraries than what is done by CRAN/Bioconductor.

# Testing environments

## Operating Systems

- [CRAN](https://cran.r-project.org/web/checks/check_flavors.html): tests packages on *Debian testing* `\o/`, *Fedora 32*, *Windows Server 2008*, *Solaris 10* and *OS X 10.11.6*.
- [Bioconductor](https://www.bioconductor.org/checkResults/): *Ubuntu 18.04.4 LTS*, *Windows Server 2012 R2 Standard* and *macOS 10.14.6 Mojave*.

Packages available on CRAN are already tested in a quite fresh and updated environement (aka *Debian testing*), so why should we care about testing packages in *Debian unstable*?
It is not rare than a new version of a random library in dependencies introduce a regression in R packages and usin *Debian unstable* instead *Debian testing* lets us detect the breakage earlier than what is done by CRAN/Bioconductor.

## Architectures

- [CRAN](https://cran.r-project.org/web/checks/check_flavors.html): only tests packages on a unique architecture that is x86_64 (also called *amd64*).
- [Bioconductor](https://www.bioconductor.org/checkResults/): idem.
- [Debian](https://ci.debian.net/): packages are tested on [amd64](https://www.debian.org/ports/amd64/), [arm64](https://www.debian.org/ports/arm/) and [ppc64el](https://www.debian.org/ports/powerpc/)
- [Ubuntu](https://autopkgtest.ubuntu.com/): packages are also tested on amd64, arm64, ppc64el and some less common architectures inherited from Debian: [armhf](https://www.debian.org/ports/arm/), [i386](https://www.debian.org/ports/i386/), and [s390x](https://www.debian.org/ports/s390x/)

This part is more interesting because R packages are only tested by upstream on *x86_64* architecture.
Okay, it is not easy neither cheap to maintain systems on differents architectures to perform testing.
I guess it is why CRAN/Bioconductor use only the most common architecture on *personal computer* for their tests.
This last sentence is less and less true, at least the end of the sentence seems to be less important.
At least, the ARM architecture seems to be more and more widely used, we can see it in mobile phones, single board computers (like *Raspberry Pi*) or even new laptops (*Pinebook*).
It is why, it would be not bad to test the R universe on different architectures.
This is confirmed by an increasing number of bug reports against R/deb packages about tests failure on non-x86_64 architectures, which is not surprising!

# How R packages are tested on the Debian infrastructure?

So, for downstream Debian developers, we just have to write a script in `debian/tests/` to execute these upstream unit tests.
The scripts in `debian/tests/` are quite basic and similar between all R packages, excepted for rare exceptions.
The `debian/tests/control` part is a bit more boring as all test-dependencies are defined here.
Once a new upstream version of a package is released, it is quite frequent to have to update the list of test-dependency packages into this file.
This step requires a lot of manuel maintenance to keep the list up-to-date and produce a lot of "false positive" errors in the DEP-8 status due to mistake into the test-dependencies.
Given the Debian R Packages team maintains something like [~ 850 packages](https://qa.debian.org/developer.php?email=r-pkg-team%40alioth-lists.debian.net), we have more useful works to do than maintaining this list manually.
This is why, the list of test-dependencies is now automagically generated when using the field `Testsuite: autopkgtest-pkg-r`.

Unfortunately, CRAN and Debian policies differ on the point how the test-dependencies are defined.
In the CRAN policy, tests-dependencies are allowed as "Suggested" dependency and all suggested dependencies from the chain are loaded at the test time.
Whereas in Debian, dependencies required for tests are commonly in "Depends".
This is not a problem, we can just load packages in the "Suggests" field.
However, it is only possible to load packages from the "Suggests" field for the first level, not for all levels of dependencies.
But, do we really want to load everything?
NO, so, all packages used for the unit tests of a package should be into its "Suggests" field and not into the list of one of its dependencies (A).
Otherwise, upstream developers can break unit tests of downsteam packages when they changed their "Suggests" field.

Another *issue* is that all R packages are not R/deb packages and unfortunately R developers don't have the good habits to test the presence of a package before to use it for a test (B).
Even if it is really easy to fix (see bellow), this issue leads again to a lot of "false positive" errors in the DEP-8 status.

## Environment preparation

So, now what happens when we add `Testsuite: autopkgtest-pkg-r` into `debian/control`

The following steps are based on code from  the [autodep8](https://tracker.debian.org/pkg/autodep8) package.
First, `/usr/share/autodep8/support/r/detect` will detect if a Debian package is an R package base on the presence of a `DESCRIPTION` file and on the name of the Debian source package (any R package should have a name starting by `r-(cran|bioc|other)-*`, see the outdated [Debian R Policy](https://lists.debian.org/debian-devel/2003/12/msg02332.html)).

If an R package is detected, `/usr/share/autodep8/support/r/generate` will generate a `debian/tests/control` file. At this step, the list of test-dependecies is generated using the `parse_descr_r` shell function (from `/usr/share/autodep8/support/common`). Currently, the `parse_descr_r` function is affected by [#961138](https://bugs.debian.org/961138) which could lead to the generation of wrong list of test-dependecies when the APT lists of the system running autodep8 is not sync with the test runner.

## Go for tests

Now, the code used to test R/deb packages is located in `/usr/share/dh-r/pkg-r-autopkgtest` ([dh-r](https://tracker.debian.org/pkg/dh-r) source package).  

First, it try to load the package into R.  
```
R> library(foo)
```
Nothing really exciting, but at least we can check if the package is correctly loaded.

Then, it will try to excecute all .R files from the `tests` folder:
```
cd tests
for testfile in *.R; do
  echo "Start: ${testfile}"
  R --no-save < ${testfile}
done
```
This feature is only activated for Bioconductor packages (not for CRAN packages neither for packages from GitHub) in order to test only on a subset of R/deb packages to avoid to break the migration of too many packages if the implementation is incorrect. I still plan to enable it for all others packages `r-other-*`, then `r-cran-*`.

# How to fix upstream unit tests

Of course, we can just patch the source code to remove the failing test, but there are more elegant ways to fix these tests. By the way, there is no reason to not forward the patch to upstream devs.

## Missing test-dependency packages in "Suggests" field of DESCRIPTION (A)

Quite simple, patch the [DESCRIPTION](https://cran.r-project.org/doc/manuals/r-release/R-exts.html#The-DESCRIPTION-file) file to add the missing package into the "Suggests" field.

## Missing verification before to use a package for test (B)

### Runit

If test is failing due a missing package in Debian:
```
if (!requireNamespace("foo", quietly=TRUE)){
  DEACTIVATED("'foo' package is not available in Debian")
}
```

If test is failing for whatever reason in Debian:  
```
DEACTIVATED("Skip this test, does not work on Debian infrastructure")
```

See documentation: <https://cran.r-project.org/package=RUnit>

### testthat

If test is failing due a missing package in Debian:  
```
skip_if_not_installed("package_not_available_in_debian")
```

If test is failing for whatever reason in Debian:  
```
skip("Skip this test, does not work on Debian infrastructure")
```

There are several others `skip_if` or `skip_on` available in testthat.

See documentation: <https://cran.r-project.org/package=testthat>


### tinytest

tinytest is too recent and has no function to easily skip a test.

See documentation: <https://cran.r-project.org/package=tinytest>

# References
- <https://wiki.debian.org/Teams/r-pkg-team>: Debian R Packages Team
- <https://ci.debian.net/>: Debian Continuous Integration infrastructure
- <https://autopkgtest.ubuntu.com/>: Ubuntu Continuous Integration infrastructure
- <https://dep-team.pages.debian.net/deps/dep8/>: Specificication of Debian package testing (DEP-8)
- [CRAN Policy](https://cran.r-project.org/web/packages/policies.html)
- [Bioconductor Policy](https://bioconductor.org/developers/)
- [Debian R Policy](https://lists.debian.org/debian-devel/2003/12/msg02332.html): outdated

--
Dylan

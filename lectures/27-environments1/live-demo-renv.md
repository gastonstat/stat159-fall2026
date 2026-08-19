---
title: Live-coding demo for renv. 
---

------------------------------

# Live Demo Script: The Time-Travel Paradox

## Setup (Do this before class)

1. Open a fresh RStudio instance.
2. Create a clean folder on your desktop called `storms_1975_2022`.
3. Create a script inside it named `storms-last-year.R` with this exact code:

```r
# Our critical production script
library(dplyr)

# last entry for 'name' and 'year'
storms |> 
  select(name, year) |> 
  tail(n = 1)

# Answer should be: Nicole 2022 (dplyr version <= 1.1.4)
# Answer should be: Sara 2024 (dplyr version > 1.1.4)
```

4. Important: Ensure your current global R environment has an older version of dplyr (1.1.4)
(or a similar package where syntax changed) installed so it runs perfectly during Step 1. 

------------------------------

## Minute 1: The Illusion of Safety

What to say:

"Class, let's look at a simple yet real example that I experienced last Spring (2026) in my STAT 133 class"

What to do:

* Highlight all lines in storms-last-year.R.
* Click Run.
* Show the successful output in the console.

What to say:

"I had an 'old' version od dplyr, probably version 1.1.4. The last year in storms data was 2022"
"One of my GSIs had a more recent version; the last year in storms data was 2024"
"I had written some code for a lab, using the 2022-year data version"
"The GSI updated the code for the same lab, using the 2024-year data version"
"I pulled the changes, try to preview the website, but it all crashed"


------------------------------

## Minute 2: The Sabotage

What to say:

"It's next year. I am working on a brand-new project that requires the absolute latest features of dplyr. So, I do what every R user does without thinking..."

What to do:

* Type this directly into the console and press Enter:

```r
install.packages("dplyr") 
```

* Let the text scroll by as it updates your global library.

What to say:

"I just upgraded my global user library. My new project works beautifully. But what happened to my old dinosaur project?"

------------------------------

## Minute 3: The Crash

What to say:

"A colleague emails me saying my old script doesn't match their results. I open it back up, confident as ever, and I hit run..."

What to do:

* Run storms-last-year.R again.
* Result: The last year has now changed to 2024.

What to say:

"Boom. My historical project is affected because my global library changed. I am trapped in dependency hell. Let's fix this forever using renv."

------------------------------

## Minute 4: Rewriting History with renv

What to say:

"I'm going to downgrade my dplyr back to the working version one last time, and then I am going to lock this project in a capsule using three commands."

What to do:

* Command 1: Initialize
Type in console:

```r
renv::init()
```

Point out to students: Look at the Files pane in RStudio. Point out the new renv/ folder, the .Rprofile, and the renv.lock file that just appeared out of thin air.

* Command 2: Verify Isolation
Type in console:

```r
.libPaths()
```

Point out to students: "Look! The path changed. R is no longer looking at my computer's global user folder. It is trapped inside storms_1975_2022/renv/library."

------------------------------

## Minute 5: Install dplyr ver 1.1.4

What to say:

"I'm going to install the old version of dplyr"

```r
renv::install("dplyr@1.1.4")
```

And then I'll run my script again

* Run storms-last-year.R again.
* Result: The last year has now changed to Nicole 2022.


------------------------------

## Minute 6: Lock in my package environment

We Take a Snapshot. In other words, to lock in your current package environment, run:

```r
renv::snapshot()
```

This will update the renv.lock file

* Press y when prompted to confirm the changes.
* Open the renv.lock file in your editor. 
* Notice how it is just a simple text list tracking exactly that stringr is locked to version 1.1.4.


------------------------------

## Minute 7: The Colleague's Perspective (Restore)

What to say:

"Now, watch this. If I go back to my global library and upgrade dplyr again, or if a collaborator opens this project on a totally different computer, all they have to do is run one magic command."

What to do:

* Command 3: Restore
Type in console:

```r
renv::restore()
```

* Run storms-last-year.R one final time to show the clean, working output.

What to say:

"Because the lockfile remembers exactly what version worked, renv::restore() rolls back the local environment to the exact day this script was successful. The global machine is ignored. The code is reproducible."


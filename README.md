# QmdReport.

[A demo a Quarto report live view](https://rverse-tutorials.github.io/QmdReport/)

## April 26, 2023 update

* Everything broken in the Aug 13, 2022 update is still broken.
* flextable cross-refs and captions are still not working in Word output
* The flextable 0.9.1 CRAN release broke PDF output in Quarto books. Known problem and fixed in the development version on GitHub (0.9.2) `devtools::install_github("davidgohel/flextable")`
* gt (v0.9.0) cross-refs still broken in Word

## Aug 13, 2022 update

I have mostly figured out workarounds for the table package issues.

The remaining big issues are
* For HTML, if you have two chapters (h1 level) in a single qmd it totally messes up the TOC. Word and PDF converters seem to figure it out ok and number things correctly.
    - I posted an issue https://github.com/quarto-dev/quarto-cli/issues/1712
    - This is important because we often don't know how many appendix chapters we will need. These are dynamically created based on the data. There is no good way to know this ahead of time. These report templates are used across very different regions and each region has a different number of appendices based on the number of, say, species or cities or whatever, in the region.
* When I have appendices so `appendices: ` in my YAML, the HTML TOC is messed up.
    - see https://rverse-tutorials.github.io/QmdReport/ and look at TOC
    - why doesn't the HTML figure out that the appendix A title is CRchum.csv?? Word and PDF figure that out.
* When I have `appendices: `, the PDF TOC and levels look right, but if I click on "Appendices" in the TOC, I go to References.
    - Look here to see what I mean [PDF version]( https://rverse-tutorials.github.io/QmdReport/A-Quarto-Template-Repo-to-Create-Big-Reports-and-Very-Long-Title-Because-Long-Titles-are-Common.pdf)
    - I would like to be able to have a `appendices.qmd` that I would go to. Maybe I can. I haven't tried.
* No merge and knit option. :( so no memory across chapters. That is a "feature" in many cases but some report writers use memory across chapters heavily.



## Aug 11 22 update

* flextable cross-refs don't work in pdf but kableExtra tables work and look better in some ways. So I use kableExtra for pdf's.
* flextable is best for Word but cross-refs are broken. The Quarto dev team knows about the problem.
* The Quarto {{include filename}} option for including files seems very limited or I don't know how to use it (most definitely true). The file has to be in same folder as the qmd that calls it.
* The Quarto {{include filename}} breaks if the thing you call is calling `knit_expand()`. Why? So weird. I might be doing something wrong.
* No merge and knit option. :( no sharing of variables across chapters
* Can't have two chapters (h1 level) in a single qmd so can't dynamically create chapters (one qmd with many h1 levels where that qmd is created dynamically)
* {gt} package and PDF table numbering don't mix! Breaks all numbering after a single `gt()` call. *Update. Fixed in the new gt update*

## Aug 11 22 update

I presented a more minimal version to a NOAA Fisheries group: https://rverse-tutorials.github.io/QuartoReport_SE and https://github.com/RVerse-Tutorials/QuartoReport_SE

## Aug 6 22 update

I presented QmdReport at the last RWorkflow workshop. I'd made a lot of progress but many things were not working still. I made a bunch of updates and cleaned things up.

* After fighting to try to get tables to look ok using one table engine, I have up and wrote some helper functions in `tables/_common.R` to select the best engine for LaTeX versus Word/HTML.
* flextable cross-refs don't work in pdf but kableExtra tables work and look better in some ways. So I use kableExtra for pdf's.
* flextable is by far best for Word but cross-refs are broken. The Quarto dev team knows about the problem.
* I worked out how to use child Rmds.
* I put tables separate from figures.
* I tried having appendices as a "part" using `appendices: ` in the `_quarto.yml` but when I added a cross-ref to a chapter it wouldn't build to Word. So had to get rid of that.
* Only one chapter per qmd :( I posted as an issue https://github.com/quarto-dev/quarto-cli/issues/1712 This is a big problem. Often you don't know ahead of time how many chapters you need.

## Aug 5 22 update

The title page work is living here at the moment: https://github.com/nmfs-opensci/quarto_titlepages

## Aug 2 22 update

I see a path forward (for HTML at least)! I can pass in `page-layout: custom` and use `::: ` to add the content in the right places. https://github.com/quarto-dev/quarto-web/blob/main/index.qmd
But I still don't know how to get any control over the page.

## July 30 22 update

I hacked the index.qmd yaml to make what looks like a title page using the `subtitle:`. But now PDF and Word won't build. Word says it can't find the image file even though it finds it fine when the image is in the main text.

I spend the day also playing with [quarto](https://github.com/quarto-journals) for making templates. I don't think that's the way forward. I struggled to make sense of the structure. Given my experience with LaTeX books, R Markdown Hugo blogs, html, css, etc, I can usually makes sense of templates with a bit of poking around the repo and start creating my own. 

The way they approached the title page design struck me as completely foreign coming from a LaTeX background and would require me to duplicate decades of LaTeX document class work (in the LaTeX community). There is a standard way to specify title pages (along with copyright pages etc) and to specify the parameters that go in: a document class definition and a redefinition of the `\maketitle` command along with parameters (that would presumably come in from the `_quarto.yml`). All I need is ablitity to freely define parameters in the yaml (e.g. `publishers`) AND the ability to control the LaTeX that is coming before `\mainmatter` in the document.  That's it. Then all the existing LaTeX document classes would work seamlessly.

Re Word, I didn't dig too deep. I need to find out how they are creating Word docs. Is it strictly pandoc or is {officerdown} helping out? If the latter, then I can hack (ug so tired of hacks) a title page.

Other things that are hard

* pandoc is not respecting `{.nolisting}` on the `index.qmd` for HTML output. So `# titlepage {.nonumbering .nolisting}` should work to for a title page without the first chapter starting on the html title page without `titlepage` appearing in the TOC. But `{.nolisting}` is only working on pages after `index.qmd`.

* My hack to get control of the front page for HTML output by hacking the `subtitle:` block breaks both Word and PDF output. :(

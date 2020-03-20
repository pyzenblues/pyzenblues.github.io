# My Github Pages

ruby version: 2.6.5

## Setup

```bash
gem install bundler jekyll
bundle exec jekyll serve
# now browse to http://localhost:4000

# check where minima is installed
bundle show minima

# show all gems in your Gemfile.
bundle list
```

### Warning

kramdown have different ouput for `$...$` and `$$...$$`

In LaTex, we generally use `$...$` as inline math formula. If we want to print curly brackets, we need escape them. For example, `$\{H,T\}$`. Unfortunately, kramdown (markdown processor) parse the document before MathJax parse it. It is very annoying you have to use `$\\{H,T\\}$`. You can use `$$\{H,T\}$$` to get the same result, but `$$...$$` should be used in block math formula.

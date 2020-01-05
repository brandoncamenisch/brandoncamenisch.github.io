---
layout: post
title:  "Reach for the GitHub stars"
date:   2019-10-10 00:00:00 -0800
categories: GitHub
---
As consumers of open source it's our obligation of sorts to contribute back upstream to the projects we love to use. For a very long time I've been starring projects on GitHub and that was the end of it. I would star, use, and/or forget about the cool thing I found. But as a user it's our job to also understand what we're consuming and why.

With that in mind I thought it clever to start contributing by first downloading all starred repositories on GitHub that I use or intend to at least. But I couldn't find a decent script which would do the thing. Below is a snippet for `zsh` which works beautifully for doing just that. This will clone repos into a subdir of their `GitHubName/Reponame`

{% highlight zsh %}
#$1 username
download_all_starred_repos() {
  user=$1
  pages=$(curl -I https://api.github.com/users/$user/starred | sed -nr 's/^Link:.*page=([0-9]+).*/\1/p')

  for page in $(seq 0 $pages); do
      curl "https://api.github.com/users/$user/starred?page=$page&per_page=100" | jq -r '.[].html_url' |
      while read rp; do
        owner=$(echo $rp | sed 's/.*\.com\///')
        git clone $rp $owner
      done
  done
}
{% endhighlight %}

Word of caution! You might make sure there's nothing nasty in there like a [git bomb](https://github.com/Katee/git-bomb) or you'll be having a bad day with it.

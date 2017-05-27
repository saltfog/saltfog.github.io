---
layout: post
title: Creating my GitHub repo listing page using GitHub API
description: Creating dedicated sites to list my GitHub repos and gists for better clarity especially when the numbers of my repo and gist started increasing.
keywords: listing site, github repo gist listing, grid layout site, github api v3, muuri jquery plugin
tags: [JavaScript, AJAX, GitHub API]
comments: true
---

Sometimes, I found that the best way to display all of my GitHub repositories for a better clarity is to create its own dedicated site for that, especially when the number of my repo started increasing. It will be quite helpful to look all those repos in one glance. Like [Twitter](https://twitter.github.io/), [IBM](https://ibm.github.io/) or [Uber](https://uber.github.io/) did. All of these are possible by using [GitHub API](https://developer.github.com/v3/).

Previously, my repo listing page was using the same layout as I did for [my gists](http://heiswayi.github.io/my-gists/). Below is the screenshot for my gist listing page, and for now I don't have a plan to change the page layout.

> GitHub Gist is a great service provided by GitHub to easily share your code snippets or save your notes online. [More about gist here...](https://help.github.com/articles/about-gists/)

![my-gists](http://i.imgur.com/5OpkLPM.png)

**URL:** [heiswayi.github.io/my-gists](http://heiswayi.github.io/my-gists/)

### My new GitHub repo listing page

Now it's 2017 and I thought I should change my repo listing page layout to a new layout, and here it is!

![my-repos](http://i.imgur.com/KYNh11h.png)

**URL:** [heiswayi.github.io/my-repos](http://heiswayi.github.io/my-repos/)

This new page layout provides more clarity and also supports pagination if my repos count reaches 100+ repos as [GitHub Search API only provides items in sets of 100](https://developer.github.com/guides/traversing-with-pagination/). It's built from scratch and passion using [jQuery](https://jquery.com/) (for AJAX request), [Bulma - A modern CSS framework based on Flexbox](http://bulma.io/), [Font Awesome](http://fontawesome.io/), [Muuri plugin](https://haltu.github.io/muuri/) and [GitHub API v3](https://developer.github.com/v3/).

### Example of code snippets

I use jQuery `$.ajax` to query GitHub API and then process the returned response data of JSON.

```js
var github_username = 'heiswayi';
var per_page = 100;

$.ajax({
    type: 'GET',
    url: 'https://api.github.com/users/' + github_username,
    dataType: 'json',
    success: function(data) {
        // do something JSON response data
        // for example, get total number of my repos:
        var total_repos = data.public_repos;

        // now, get all repos (max 100 repos):
        get_repos('https://api.github.com/users/' + github_username + '/repos?&per_page=' + per_page);
    }
});

function get_repos(api) {
    $.ajax({
        type: 'GET',
        url: api,
        dataType: 'jsonp',
        success: function(data) {
            // console.log(data);
            init(data); // initialize Muuri plugin
        }
    });
}
```

Initialization of [Muuri plugin](https://haltu.github.io/muuri/) and item iterations:

```js
function init(data) {
    if (!data || !data.data || !data.data.length) return;
    if (!grid) {
        grid = new Muuri({
            container: $grid.get(0),
            items: generateElements(data),
            dragEnabled: false,
            dragReleaseEasing: 'ease-in',
            dragContainer: document.body,
            layout: ["firstFit", {
                fillGaps: true
            }]
        });
    }
}

// here is the example of how I generate each repo info
function generateElements(data) {

    var repositories = data.data;

    // sort last modified repo to the most recents
    repositories.sort(function(a, b) {
        var aFork = a.fork,
            bFork = b.fork;
        if (aFork && !bFork) return 1;
        if (!aFork && bFork) return -1;
        return new Date(b.pushed_at) - new Date(a.pushed_at);
    });

    var ret = [];

    for (var i = 0; i < repositories.length; i++) {

        var r = repositories[i];

        var id = ++uuid;
        var desc = r.description == null ? '' : r.description;
        var lang = r.language == null ? 'unknown' : r.language;
        var size = r.forks >= 100 ? 'is-6' : 'is-3';
        var isFork = r.fork == true ? '<i class="fa fa-code-fork" title="Forked repository"></i> ' : '<i class="fa fa-github-alt" aria-hidden="true"></i> ';
        var isRepo = r.fork == false ? 'repo' : 'forked';

        var item = $('<a href="' + r.html_url + '" class="item column ' + size + '" title="Click to go to repository on GitHub...">' +
            '<div class="item-content notification ' + isRepo + '">' +
            '<span class="name">' + isFork + r.name + '</span>' +
            '<span class="desc">' + desc + '</span>' +
            '<span class="star" title="Number of watchers"><i class="fa fa-star" aria-hidden="true"></i> ' + r.watchers + '</span><span class="spacing-20"></span><span class="fork" title="Number of forks"><i class="fa fa-code-fork" aria-hidden="true"></i> ' + r.forks + '</span><span class="spacing-20"></span><span class="language" title="Major programming language"><i class="fa fa-code" aria-hidden="true"></i> ' + lang + '</span>' +
            '</div>' +
            '</a>').get(0);

        ret.push(item);
    }
    return ret;
}
```

### Free and open source

If you like my GitHub repo listing page, the source code is available on [GitHub](https://github.com/heiswayi/my-repos). Feel free to fork it. Similar to my gist listing page, the source code also is available on [GitHub](http://heiswayi.github.io/my-gists). All source code are licensed under [MIT License](http://heiswayi.github.io/mit-license).

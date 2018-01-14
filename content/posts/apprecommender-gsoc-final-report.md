---
title: "AppRecommender Gsoc Final Report"
date:   2016-08-01
---

With Google Summer of Code 2016 coming to an end, it is time to write my final
report about it. This was an amazing experience to have. Not only on the
technical side, but it has allowed me to meet and interact with awesome people,
which helped to improve a lot not only my coding skills, but the way I was
interacting with other people inside the community.

Debian
---------

Working with Debian was also another high point of the my Google Summer of Code
experience. Before may, I have only worked for Debian during this year ruby
sprint. Where I learned how to package applications and solve bugs. However, by
working in Google Summer of Code, I was not only able to understand more
advanced features about packaging but to better understand how the community
works and why Debian is such an incredible project.

The community was always really helpful to new members, but during DebConf, I
was able to see how they interact as well. I talked to a lot of Debian
contributors there, and everyone I talked to was nice and even when I was
talking with someone looking for help, they had the patience to explain to me
what should I do or why my approach was wrong.

I can honestly say that even after Google Summer of Code I want to keep helping
Debian on both the project that I worked during the summer and the packaging
part.


What I have done
--------

My project was intended to integrate AppRecommender with AppStream, in a way to
allow some graphical package managers to use the recommendations provided by
AppRecommender.

I was also responsible for packaging AppRecommender for Debian, since the
project was not yet packaged.

In between both of these tasks, I also tried to improve AppRecommender the best
I could, by making it's code easier to read and even adding some new features.

Therefore, I have work on three distinct repositories. On AppStream, this is the
work I have done:

Pull requests

* [AppStream Pull Requests](https://github.com/ximion/appstream/pull/45)

Commits:

* [AppStream commits](https://github.com/ximion/appstream/commits?author=lucasmoura)

On AppStream, with the help on Matthias Klumpp, I have created the feature to
allow recommendation metadata be added to an AppStream component.

For AppRecommender, I have added some features, bug fixes and some refactoring
as well:

* [AppRecommender commits](https://github.com/tassia/AppRecommender/commits?author=lucasmoura)

There was also a repository created only for the package of AppRecommender, that
I have worked on as well:

* [AppRecommender package](https://gitlab.com/AppRecommender/AppRecommender-package)

And this is can show the number of commits I have done on this repository as
well:

* [AppRecommender package commits graph](https://gitlab.com/AppRecommender/AppRecommender-package/graphs/master)


What's left to be done
------------------

There are two main things that still needs to be done. One of them is now to
allow AppRecommender to create file that can be parsed by AppStream, allowing
the recommendations to be available for graphical package managers.

Also, there is still one last thing to improve on the package, that is when an
user runs AppRecommender as a superuser, AppRecommender should re-execute and
runs the command with the "apprec" user, which is the user that has permissions
to write and read AppRecommender files.

Thi is because any user can read AppRecommender files, but only the "apprec"
user can modify these files.


Conclusions
--------

Working on Google Summer of Code was an amazing experience. I will really
consider to participate next year as well. Also, working for Debian was really
cool as well. I need to thank both Antonio Terceiro and Tassia Camões for
mentoring me on this project. Thanks so much for the help and patience on
teaching me from Debian packaging to some python coding as well. I also need to
thank the Debian community for being so great for newcomers. I hope that with
the experience that I have achieved with Google Summer of Code, I will be able
to help Debian with other projects as well.






<!-- Reference draft for Yingshu Wang to review, personalize, and rewrite before submission. -->

I contributed a dependency-tracing feature to Wheelgap, an open-source tool for checking Python package compatibility. It helps developers see which dependency introduced a package with a compatibility problem, giving them a clearer starting point for investigating it.

As part of my CodePath AI301 coursework, I implemented a shortest-path search and added explanations to both the readable report and its structured output. I also accounted for dependency paths that change across platforms. The maintainer merged my contribution after I added 12 automated tests and completed the project's checks.

One meaningful lesson came from feedback about the output format. I had initially built a single list, then revised it to preserve a separate path for each platform. That experience taught me to clarify design questions early and carry the answer through the code, tests, and documentation.

I used Claude Code for assistance, reviewed the changes, and ran the checks myself. I'm grateful for the maintainer's guidance on my first merged contribution to Wheelgap.

https://github.com/wheelgap/wheelgap/pull/45

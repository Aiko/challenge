# Technical Assessment

_If you have questions, please contact your recruiting contact via AngelList/email._

## Introduction

At Aiko Mail, we work with a variety of web technologies, integrating multi-faceted email data to create stable, high-value applications. As our core offering is a **desktop application** built in Electron, our engineers excel at building interop into our frontend.

This assessment is meant to test for that same skill. (Don't worry, you don't have to build an Electron app.)

While building Aiko Mail, we found that it was best for stability to do heavy data processing in the Electron process, parsing out data streams and piping them to the frontend. While we've developed some frameworks around how to accomplish this, email data is often oddly shaped and so our pipelines are far from standardized. In this assessment, we'd like you to work with similarly oddly shaped data and pipe it into a frontend.

## The Task

A popular platform for blogging is [Medium](https://medium.com). People of varying background write articles on Medium, and often times it acts as a source of high value information, especially concerning cutting-edge CS research. However, it's a big awkward to redirect users from your site to Medium when showing them your blog.

Fortunately, Medium provides an RSS feed, allowing us to duplicate our Medium blog onto our website. This feed can be accessed at `https://medium.com/feed/<username>`.

For example, Aiko Mail's RSS feed lives at: https://medium.com/feed/aiko-ai. Opening this in Microsoft Edge or Google Chrome will show you a nicely formatted XML document.

We'd like you to take an XML feed from Medium of your choice (it can be ours above), and showcase the articles in a frontend UI.

## Requirements

Just 3 requirements:

1. Parse an RSS feed from Medium (there are many ways to do this, e.g. DOMParser in browser JS)

2. Build a homepage that shows a list of articles from that feed, with their title, categories, date, and author

3. Clicking on an article should show the article content (it's sanitized HTML, so you can just inject it into a div)

**It is highly suggested to include either comments or a README writeup detailing any design choices you made.**

## Limitations

- You may **only** use JavaScript or TypeScript (no Rust/WASM)
- You can build your solution directly in the frontend or as a combination of frontend and backend, but if you choose the latter your backend must be using Node.js with JavaScript or TypeScript
- You may use any existing framework or library as long as it's open source
- Your solution can be a single page (SPA), or multiple pages
- Your solution must update over time
  - if you build it purely in the frontend, it should fetch and parse the RSS feed on page load
  - if you build a backend, it should fetch and parse the RSS feed periodically by itself
  - you may NOT parse the feed with another language (e.g. Python); the fetching and parsing must be done in JavaScript

## Directions

1. Create a **private** GitHub repository. It must be on GitHub, and it must be private (this is free)
2. Build a solution (this is designed to take less than 2 hours, please see the assessment section below).
3. **Optional but HIGHLY SUGGESTED:** write up any design choices you made.
4. Add 2 collaborators: `rubentouitou` and `cuuupid` ([How to Add Collaborators to a GitHub repo](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-user-account/managing-access-to-your-personal-repositories/inviting-collaborators-to-a-personal-repository))
5. Email us with a link to your repo so we know you're submitting your solution :)

## Assessment

We have a very transparent method by which we assess solutions, which you can find below. Solutions are marked out of 100.

**Important: the design of your solution (not UI, but system design, choices made etc) is the most important aspect beyond completion. This assessment is designed to take NO LONGER THAN 2 HOURS. If you need to make compromises, reduce functionality, etc please note that in either a comment or a README. We award very generous amounts of extra credit if you fall short in any aspect below but give an explanation as to why.**

- **Completion (40 points)**:
  - Building a solution (10 points)
  - Meeting the requirements (30 points, 10 for each requirement)
  - Not breaking any limitations (penalty, -8 for each limitation broken)
- **System Design (30 points)**:
  - This is the **most** important aspect beyond completion.
  - Again, it is **highly suggested to include either comments or a README writeup detailing design choices.** This will help us award credit for design decisions.
  - **Remember: it is NOT about making the right choice or building the perfect app. We want to see what your thought process was and WHY you built your solution this way.** Were you optimizing for speed? Was this decision made as a compromise to reduce difficulty (this is fine--good developers know how to balance their time)? Did you choose this structure to improve SEO? etc.
  - Extensibility (10 points), can this solution deal with more articles or article data in the future? Does it create technical debt?
  - Tooling (10 points), did you decide on a pure frontend or frontend-backend pair? Did you build a SPA or use different pages? What frameworks or tools did you use?
  - Code Style (5 points), this isn't about tabs/spaces or following whatever style guide some company or university put out, this concerns using modern syntax, good design patterns in your code, efficient and safe choices, etc. -- think `const` and `let` instead of `var`, or async methods, or where computations live
- **Stability (15 points)**
  - We can run the solution (5 points), if it requires a special command please make it clear in the README
  - Solution keeps working over time as we add articles to the publication, click things, etc. (5 points)
  - Design (5 points), whether there have been any design choices or considerations to support stability/security
- **User Experience (10 points)**
  - Intuitive interface (5 points), a member of our UX team will do a blind review
  - Styling (5 points), if there are no CSS artifacts and the page looks somewhat modernly styled you'll get full marks
- **Speed (5 points)**, measured in time taken until page is interactive
  - This is only 5 points, please keep this in mind and do not over-optimize
  - This is measured in buckets, with 2.5 seconds == 5 points, 4 seconds == 4 points, 5 seconds == 3 points, 7 seconds == 2 points, 10 seconds == 1 point
  - If you have a server-ed backend, we will not count startup time
  - If you have a serverless backend, we *will* count runtime, but we will measure this from a "hot" state
  - Network latency isn't included. This is because the largest load times/latencies here will definitely be on Medium's side
 
**Solutions utilizing Vue.js 2 or 3 will receive an extra 2 points. You will not net more points for using Vue 3, so if Vue 2 better suits your needs please use that (we use Vue 2 internally).**

**Solutions utilizing TypeScript will receive an extra 3 points. You do not need to write the whole thing in TypeScript, one component will suffice.**

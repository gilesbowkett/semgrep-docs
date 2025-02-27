# Frequently Asked Questions

[TOC]

# General

#### How are Semgrep and its rules licensed?

[Semgrep CLI](https://github.com/returntocorp/semgrep) is open-source, licensed under the [LGPL 2.1](<https://tldrlegal.com/license/gnu-lesser-general-public-license-v2.1-(lgpl-2.1)>) license — you can use it at work, on private and proprietary code, no problem!

The [Semgrep Registry](https://semgrep.dev/explore) contains rules from many different contributors, often under differing licenses. If you use Semgrep Registry through Semgrep.dev, r2c is vetting the rules to make sure that the licenses are compatible with using them at your company.

The source for many of r2c’s rules is available at [returntocorp/semgrep-rules](https://github.com/returntocorp/semgrep-rules/). These are licensed as LGPL 2.1 under the [Commons Clause](https://commonsclause.com/). This means the source for the rules is available, but they can’t be resold without r2c’s permission. Although the Commons Clause and related licenses like the BSL/SSPL are embraced by many great open-source projects ([Sentry](https://blog.sentry.io/2019/11/06/relicensing-sentry/), [Cockroach](https://www.cockroachlabs.com/blog/oss-relicensing-cockroachdb/), [Mongo](https://www.mongodb.com/licensing/server-side-public-license/faq)), they are not technically open-source under the OSI definition. Why do we have this restriction if we love open source software so much? Since r2c offers a paid, hosted version at Semgrep.dev, it’s important for us to have this restriction so other companies like major cloud providers can’t just resell our rules as a competing service.

If the LGPL + Common Clause license is an issue for you as a contributor, please give us feedback! r2c may be able to offer you semgrep-rules under a separate license on a case-by-case basis.

#### Is it ok to run Semgrep or the r2c rules on my work projects?

Yes! Semgrep is safe to run on your private code. The Common Clause restriction only comes into effect if you are _selling_ the rules provided in the semgrep-rules repository. If that’s the case, you’ll need to talk with r2c first to get permission.

#### I’m a security professional and want to use the semgrep-rules project with my clients as part of my paid services. Is that ok?

Probably! If you have a typical consulting service and running semgrep-rules is just part of your assessments, that’s great—and of course feel free to refer your clients to the hosted [Semgrep.dev](https://semgrep.dev/). But if your entire service is about scanning code and you want to charge for running the semgrep-rules repo that r2c did the work to create and maintain, that’s something you’d need to reach out to us about.

#### What is your support policy?

Help is available for all users, free or otherwise, through the [r2c Community Slack](https://r2c.dev/slack). Semgrep Team tier customers receive 8\*5 email/phone/Slack support with committed SLAs. See [Support](support.md) for more details.

# Billing / Pricing

#### What features differ between Semgrep App’s Community (free) and Team (paid) versions?

See the Semgrep feature and pricing comparison at [r2c.dev/pricing](https://r2c.dev/pricing)

#### Who is a user and how do you count them?

For the Semgrep Team tier, a user is any project contributor whose pull request or code was scanned in the billing cycle. For example, if a project has 4 unique contributors who pushed code during the period, and Semgrep runs on all their pull requests, there are 4 users. If these unique contributors push to many projects, they will only be counted once; please feel free to scan often and widely. Semgrep does not charge on a line-of-code or per-project basis.

# Comparisons

### How is Semgrep different than $OTHER*TOOL or $GENERIC*[SAST](https://en.wikipedia.org/wiki/Static_application_security_testing)?

Semgrep is an open-source tool with a simple syntax for writing rules: if you can write code, you can write a Semgrep rule — no program analysis PhD required!

To our knowledge, the only other tool with the explicit goal of allowing custom rules is GitHub’s proprietary tool, CodeQL. CodeQL has a domain-specific language which is extremely powerful but is designed for those with significant program analysis expertise, whereas Semgrep is designed for the security engineer or developer who wants to automate code review. Our goal is to make writing a Semgrep rule as easy as copying the code you want to find—and letting the Semgrep engine to make the rule and autofix high-quality enough to run in CI or the editor.

Our hosted offering, [Semgrep App](http://semgrep.dev/), has a generous free tier (even for private repos!). It offers a hosted CI integration with one-click setup so you can start running Semgrep right away. Its diff-awareness lets you scan new code and doesn’t force you to fix all the existing issues when you first start out. For users running inside orgs with lots of repos, the hosted offering also offers a policy and notification system that makes it easy to tune Semgrep so that it only reports issues or suggest fixes that actually get applied. Our goal is a 99% fix rate for what Semgrep reports.

### Besides open-source and ease of writing new rules, what else is different about Semgrep?

**1. Speedy & offline: Semgrep runs offline on every keystroke**

If you are shipping code daily a code analysis tool that takes a week to run is not helpful. We think modern static analysis tools should run on every keystroke in the editor, without needing network access. Semgrep runs at approximately 20K-100K loc/sec per rule but our goal is to be even faster.

**2. Semantic: Semgrep is smart**

Semgrep automatically handles the nuance of “there’s more than one way to do it”: you write your query and all equivalent variations of that code should be automatically matched.

As Semgrep evolves, a query like `foo("password")` becomes smarter. In the original version of Semgrep, this query would only match the code `foo("password")`. But a few months after release it would match `const x = "password"; foo(x).` Today Semgrep can [do even more with intraprocedural dataflow](https://semgrep.dev/s/ievans:c-dataflow) analysis, and we’re working on adding more of these semantic features with every release.

**3. Integrated: Semgrep understands git and other version-control systems**

It’s easy to write a new Semgrep rule and have it only apply _going forward_. You can [mute findings](ignoring-findings.md) of course, but we have [built-in support for this with Semgrep CI](semgrep-ci.md) and GitHub/GitLab/etc. integrations.

**4. Portable: If you write a Semgrep rule, it runs anywhere**

Many other tools require a buildable environment or can only be run in a VM. Semgrep runs “on the metal” and has minimal dependencies around a statically linked core; our parsers are declaratively-generated C libraries (we contribute to and use [tree-sitter](https://tree-sitter.github.io/)).

### How is Semgrep different than linters?

Linters use static analysis but typically have a narrower scope for analysis (most rules typically operate on a single line). Some linters also cover stylistic decisions — tabs vs. spaces, for instance — but Semgrep doesn’t care about whitespace or formatting.

Semgrep’s [registry](https://semgrep.dev/explore) has rulesets inspired by the rules of many popular linters and checkers, including ESLint, RuboCop, Bandit, and FindSecBugs. But Semgrep also allows you to enable multiple rulesets at the same time without adding linter-specific artifacts or installation to your code repository.

Some popular linter tools may use tools like Semgrep as an internal engine, and we encourage this! For instance, the popular scanner _NodeJSScan_ was re-written to use Semgrep as the core.

Lastly, while many linters are extensible, you need to learn specific abstract syntax tree (AST) based patterns for writing custom rules. Semgrep works across languages and you learn its syntax once; you don't have to mess with MemberExpressions, node visitors, and all that. Before Semgrep, many of us on the maintainer team were writing AST-based rules as well: [one of us wrote an article comparing writing linter rules to Semgrep expressions](https://r2c.dev/blog/2020/why-i-moved-to-semgrep-for-all-my-code-analysis/).

### How is Semgrep different from CodeQL?

Both Semgrep and CodeQL use static analysis to find bugs, but there are a few differences:

- Semgrep operates directly on source code, whereas CodeQL requires a buildable environment
- Semgrep is LGPL-2.1 and free to run anywhere; CodeQL is not open source and you must pay to run it on any non-open-source code

- Semgrep supports autofixes; CodeQL does not.
- Semgrep focuses on speed and ease of use. Because it doesn’t require a buildable environment, it doesn’t have some of the analysis features like interprocedural dataflow analysis that CodeQL does. (Semgrep does have [limited intraproceedural dataflow and a taint-tracking mode](experiments/overview.md))
- Both have publicly available rules
- Semgrep rules look like the source code you’re writing; CodeQL has a separate domain-specific-language for writing queries.
- Semgrep has an online, hosted free plan; both have a hosted paid plan

### How is Semgrep different than SonarQube?

Both Semgrep and SonarQube use static analysis to find bugs, but there are a few differences:

- Extending Semgrep with custom rules is simple, since Semgrep rules look like the source code you’re writing. Writing custom rules with SonarQube is [restricted to a handful of languages](https://docs.sonarqube.org/latest/extend/adding-coding-rules/) and requires familiarity with Java and abstract syntax trees (ASTs).
- Semgrep is LGPL-2.1, SonarQube offers an open-source version but it is missing features. For example, 12 of the supported languages are not available in the open-source offering, and more powerful dataflow features are only available in the paid versions.
- Semgrep supports autofixes; SonarQube does not.
- Semgrep focuses on speed and ease-of-use, making analysis possible at up to 20K-100K loc/sec per rule. SonarQube authors [report approximately 0.4K loc/sec for rulesets in production](https://web.archive.org/web/20210127020636/https://community.sonarsource.com/t/performance-guide-for-large-project-analysis/148/2).
- Semgrep CI supports scanning only changed files (differential analysis), SonarQube does not
- Both have publicly available rules
- Semgrep has an online, hosted free plan; both have a hosted paid plan

# Privacy and Security

#### Where do you store data?

r2c uses Amazon Web Services (US region) for storing customer data.

#### How is data secured, including data-at-rest and data-in-transit?

All customer data is located in AWS (US region). Amazon RDS encrypted DB instances use industry standard AES-256 encryption and TLS 1.2 or higher is used for all data-in-transit.

#### Is private source code shared with r2c?

No. Semgrep CI runs fully in your CI pipeline and your source-code never leaves your environment. Only meta-data related to Semgrep runs (see below) are sent to Semgrep's service.

#### What data do you store?

Semgrep CI collects usage data to provide useful results and to help improve the product. Two types of data are sent to r2c servers: scan data and findings data. Scan data includes project id, CI environment and scan meta-data. Findings data are used to provide human readable content for notifications and integrations, as well tracking results as new, fixed, or duplicate. For more information and detailed description for each data field, refer to Semgrep CI [PRIVACY.md](https://github.com/returntocorp/semgrep-action/blob/develop/PRIVACY.md). Note that [Semgrep CLI](https://github.com/returntocorp/semgrep) does not collect or send any scan or findings data.

#### What network requests are made?

Semgrep CI makes network requests in accordance with the data storage mentioned above.

[Semgrep CLI](https://github.com/returntocorp/semgrep) makes the following network requests:

- When running without `--disable-version-check`, Semgrep makes a network request to check for updates.
- When providing a URL to `--output`, Semgrep performs an HTTP `POST` of the results to the specified URL.
- When providing a registry ID like `p/ci` to `--config`, Semgrep requests the configuration from the [Registry](https://semgrep.dev/explore).

# Configuration

#### How do I configure Semgrep for different projects?

Semgrep App provides centralized policy management. See [Managing CI policy](managing-policy.md) for more details.

#### What is a policy?

A policy is a simple collection of rules and a definition of what to do with rule results: fail the Semgrep CI run and/or send non-blocking notifications to third-party services like Slack. Please see [Managing CI policy](managing-policy.md) for more details.

# Monitoring

#### Do you have a visualization UI?

Dashboarding is available for Semgrep Team users. Semgrep also supports posting results via web hooks to any JSON endpoint, so you can easily integrate it with your favorite visualization tool.

# Glossary

**Lesson 1** The terminal
* System under test (SUT) — the specific application, at a specific version, that your testing is about.
* Build / version — an identifiable snapshot of the software; here, 18.0.0. Without it, a bug report is not checkable.
* Docker image — a frozen, complete copy of an application and everything it needs to run.
* Container — one running instance of an image; restarting it returns the application to its initial state.
* Port — a numbered entry point on a machine; -p 3000:3000 connects your machine's port 3000 to the container's.
* HTTP status code — the server's verdict on a request. 200 succeeded, 404 not found, 500 the server crashed handling it.
* Defect (bug) — a flaw in the software that causes it to behave differently from what a reasonable expectation demands. Formal distinctions between error, defect and failure come in Lesson 20.
* Oracle — the basis on which you decide that observed behaviour is wrong. Not a feeling; a source you can name. Eleven of them in Lesson 5.
* Reproducibility — how reliably a defect reappears when the steps are repeated, expressed as a count: "4 of 4 attempts."
* Evidence — an artifact that shows the failure independently of your description: a screenshot, a response body, a log excerpt.
* Severity — how badly the defect damages the product. Priority — how urgently the business wants it fixed. They are different axes and Lesson 21 is entirely about not conflating them

**Lesson 2** What actually happens when you open a website

Terminal
* Terminal — the application window you type commands into.
* Shell — the program inside it that reads and executes what you type (zsh).
* Command line / CLI — the line you type on; the general name for working this way.
* Prompt — the text the shell prints when ready for input, ending in %.
* Directory — a folder.
* Working directory — the folder you're currently standing in.
* Path — a location written out, e.g. /Users/carina/qa-portfolio/docs.
* ~ — shorthand for your home folder.
* . / .. — this folder / the folder above.
* Flag (option) — a modifier starting with a dash: the -l in ls -l.
* Argument — the thing a command acts on: the Documents in cd Documents.
* Hidden file — name starts with a dot; invisible in Finder, shown by ls -a.
* man page — built-in manual for a command; quit with q.

Web
* Client — the machine making requests; for you, Chrome.
* Server — the machine receiving requests and sending responses.
* Protocol — an agreed format and sequence for exchanging messages.
* HTTP / HTTPS — the web's protocol; HTTPS is the same, encrypted in transit.
* Request / Response — the two halves of one exchange.
* Round trip — one request plus its response.
* Method — the verb. GET = give me this. POST = here's data, do something.
* Status code — the server's three-digit verdict.
* Redirect — a 3xx telling the browser to fetch a different address; it obeys automatically, so you only see it in the Network tab.
* URL — full address: scheme, host, port, path, query string, fragment.
* Port — a numbered door on a machine, after a colon: localhost:3000.
* localhost — host name meaning "this very computer".
* Payload — the data in a request body; also the DevTools tab showing it.
* Preserve log — the Network setting that stops Chrome clearing on navigation.
* Hash routing — using the fragment to track which screen a single-page app is on.
* Front end / back end / database — browser code, server code, permanent storage.
* User enumeration — a flaw revealing whether a given account exists.
* Post/Redirect/Get — the pattern that stops a page refresh resubmitting a form.

**Lesson 3**
* Package manager — a program whose job is installing and updating other programs. Homebrew does this for macOS; npm does it for JavaScript libraries.
* Formula — a Homebrew package that installs a command-line tool (no icon), into /opt/homebrew. Example: node.
* Cask — a Homebrew package that installs a normal Mac application (with an icon), into /Applications. Example: Firefox. A cask usually gives you no terminal command, and that's expected.
* PATH — the ordered list of folders the shell searches when you type a command; the first match wins.
* command -v X — asks the shell where it would find X, and prints that location (or nothing if it can't).
* command not found — the shell searched its PATH and found nothing by that name. This is not the same as "the program isn't installed."
* Symlink — a file that acts as a signpost to another file or folder elsewhere. Shown by ls -l as an arrow. (You've already written this one well.)
* Daemon (service) — a program running in the background with no window. Docker's daemon is what actually runs containers.
* CLI (command-line tool) — a program you drive by typing; here specifically the docker command, which sends instructions to the daemon.
* Node.js — runs JavaScript outside a browser. npm — installs JavaScript libraries. npx — runs a package once without installing it permanently.
* Environment — everything about your machine and its configuration that could change how software behaves: OS and version, browser and version, tool versions, locale, screen size, logged-in state.
* Present-but-unreachable — a failure category where the thing genuinely exists but can't be reached from where you're standing. (Your command not found case; also Lesson 2's script-that-404'd.)

**Lesson 4**
* Git — a program that watches a folder and saves snapshots of it.
* Repository (repo) — a folder Git is watching; the history lives in its hidden .git folder.
* Commit — one snapshot, with a message and a unique ID like 8682ba2.
* Working directory / staging area / repository — the three places a change passes through, in that order.
* git status — which files changed. git diff — what changed inside them. git log — the list of commits.
* git add . — stage everything from this folder downward. git commit -m — snapshot the staged changes. git restore <file> — put a file back as it was at the last commit.
* .gitignore — the list of things Git should never track. .gitkeep — an empty placeholder so an empty folder exists to Git.
* HEAD — the commit you're currently standing on.
* Branch — a named line of history. Yours is main, and it's the only one you'll use for now.
* GitHub — a website that hosts copies of repos. Git is the tool; GitHub is a place.
* Remote / origin — a nickname for a copy of the repo elsewhere; origin is the conventional name for the GitHub copy.
* git push — upload your commits to the remote.

**Lesson 5**
* Image — a frozen, complete copy of an application; bkimminich/juice-shop is one.
* Container — one running instance of an image; restarting it resets the app to a clean state.
* docker run -d -p 3000:3000 --name X image — create and start a container: detached, port-mapped, named.
* docker restart / docker rm -f — reset a container to clean state / force-remove it.
* Oracle — the basis on which you judge behaviour wrong. Consistency oracle (the app contradicts itself) is the strongest; external-standard oracle (weaker than common practice) is weaker and more arguable; personal preference is not an oracle.
* Severity — how badly the defect hurts the user. Minor = nothing breaks, task still completes.
* Evidence — an artifact (screenshot, response) that proves the failure independent of your words.
* Markdown image — ![description](path), path relative to the file.

**Lesson 6**  ISTQB seven principles:

1. Testing shows the presence of defects, not their absence.
You can find a bug and prove it's there. You can never test enough to prove there are none left — finding nothing means you didn't find anything, not that nothing is there.
The rule it gives you: never say "it's bug-free" or "it works." Say "I found no defects in the areas I tested" — which is a true statement about your activity, not a false guarantee about the software.
Juice Shop: you tested the registration password field and found BUG-001. You did not test what happens when registration is submitted with the Enter key, or on a slow connection, or twice quickly. Your report is honest about the first and silent about the rest — as it should be.

2. Exhaustive testing is impossible.
Testing every possible input and combination is, for almost any real feature, a number so large it exceeds available time by orders of magnitude.
The rule it gives you: you must choose what to test, deliberately, because you cannot test everything. Testing is a sampling problem, and your skill is picking the sample that finds the most defects.
Juice Shop: the password field accepts 5 to 40 characters. That's not 36 tests — each length can contain letters, digits, symbols, spaces, emoji, in any arrangement. You could test for a century. Instead you'll test the boundaries — 4, 5, 40, 41 — and one typical value, and move on. That specific choice is Lesson 12; principle 2 is why it exists.

3. Early testing saves time and money. (Shift-left.)
A defect caught in a planning conversation costs a sentence to fix. The same defect caught after release costs a hotfix, a support ticket, and possibly a lost customer. The cost of a defect rises the later it's found — steeply.
The rule it gives you: get involved as early as you're allowed to. Reading a requirement and asking "what does this do if the basket is empty?" is testing, and it's the cheapest testing there is.
Juice Shop: if someone had asked "should the password hint and the counter show the same maximum?" during design, BUG-001 would never have shipped. Finding it now is good; preventing it then would have been better and cheaper.

4. Defects cluster together. (The Pareto principle: roughly 80% of defects live in roughly 20% of the code.)
Bugs are not spread evenly. A few modules — usually the newest, most complex, or most rushed — hold most of the problems.
The rule it gives you: when you find one defect in an area, look harder there, not elsewhere. Density predicts density.
Juice Shop: you found a defect on the registration form. Principle 4 says the registration form is now your best bet for finding more — so test its email validation, its security-question dropdown, its error handling next, before wandering off to the product list. Follow the smoke.

5. Tests wear out. (The pesticide paradox.)
Run the exact same set of tests over and over and they stop finding new defects — they've found everything they're capable of finding. Like a pesticide that stops working as pests grow resistant.
The rule it gives you: your test set must evolve. Add new cases, vary your data, explore new paths — or your testing slowly becomes theatre that passes every time and catches nothing.
Juice Shop: if you run "register with test@test.com / Password1" every sprint forever, it'll pass forever and tell you nothing new. This principle is also the honest argument for automation later: the boring, unchanging checks should be run by a machine, freeing you to do the varied, exploratory testing that actually finds fresh bugs.

6. Testing is context-dependent.
You test a banking app differently from a game differently from a training app like Juice Shop. The risks, the standards, and therefore the testing all change with the context.
The rule it gives you: there is no universal checklist. What "enough testing" means is set by what the software does and what it costs when it fails.
Juice Shop: a 5-character-minimum password (your Exercise 2) is a shrug on a throwaway demo and a serious defect on a real bank. Same observation, different severity, entirely because of context. This is why your severity judgement (Lesson 25) always depends on what the app is for.

7. Absence-of-errors is a fallacy.
Software can be flawless against its spec and still be a failure — because it's the wrong product, or unusable, or nobody wanted it. "Zero bugs" does not mean "good."
The rule it gives you: test against real user needs, not only against the written requirements. "It matches the spec" is not the last word if the spec is wrong.
Juice Shop: imagine the checkout works perfectly, no defects — but there's no way to see your order history afterward. Every test passes; the product still fails the user. Your job includes noticing that gap, even though nothing is technically "broken." This is the principle that makes you a tester rather than a spec-checker, and it's why your 3/3 on the judgement questions matters more than any command you've learned.

Sprint — a fixed period (usually two weeks) in which a team commits to and delivers a set of work.
Shift-left — involving testing as early as possible in development, when defects are cheapest to fix.
Three amigos — a focused conversation between business, development, and testing about one feature's detail.
Test analysis / test design — working out what to test, and writing the tests, often before the code is ready.
Re-test — running a test again after a fix, to confirm the specific defect is gone.
Regression testing — re-checking that a change didn't break something that used to work.
The seven principles — presence-not-absence; exhaustive testing is impossible; early testing; defect clustering (Pareto); pesticide paradox; context-dependence; absence-of-errors fallacy.
Pareto principle — roughly 80% of defects cluster in roughly 20% of the software.
Pesticide paradox — repeated identical tests stop finding new defects.

Before writing an "expected result," ask: where does this expectation come from? Name the oracle — the app's own stated rule, a universal fact, an external standard, a comparable product, or a real user's need. If you can't name one, you're guessing, and a guessed expected leads to a false bug.

**Lesson 7**

Test process — the seven activities: planning, monitoring & control, analysis, design, implementation, execution, completion.
Test planning — defining scope, risks, and exit criteria before starting.
Test monitoring and control — tracking progress against the plan and steering; runs continuously.
Exit criteria — the conditions that define "done" for a test effort.
Test analysis — deciding what to test; produces test conditions.
Test basis — everything you test against: requirements, forms, rules, hints.
Test condition — an aspect worth testing, named but not yet turned into steps.
Test design — deciding how to test each condition; produces test cases.
Test case — a specific input + expected result (+ its oracle) that checks a condition.
Test implementation — ordering cases, preparing test data and preconditions to make them runnable.
Test data — the specific values a test uses.
Precondition — what must be true before a test can run.
Test execution — running cases and recording results: pass / fail / blocked / skipped.
Blocked — a test that couldn't be run (e.g. a broken dependency), distinct from failed.
Test completion — wrapping up: summary report, archived artifacts, lessons learned.
Work product — anything an activity produces (plan, conditions, cases, results, report).

**Lesson 8**

TEST BASIS         what you derive tests FROM. 
    (ISTQB): the body of knowledge used as the basis for test analysis and design.
    │  (test analysis: "what could I test?")
    ▼
TEST CONDITIONS    testable aspects of the basis 
    (ISTQB): a testable aspect of a component or system identified as a basis for testing.
    │  (test design + a technique: "what specific things must I check?")
    ▼
COVERAGE ITEMS     the specific, countable things a test must exercise
    (ISTQB): an attribute or combination of attributes derived from one or more test conditions by using a test technique.
    │  (test design: "what inputs and expected result?")
    ▼
TEST CASES         preconditions + inputs + expected result + postconditions
    (ISTQB): a set of preconditions, inputs, actions (where applicable), expected results and postconditions, developed based on test conditions.

COVERAGE = (coverage items exercised ÷ total coverage items) × 100%
    the degree to which specified coverage items have been exercised, expressed as a percentage.



**Lesson 10**
What an oracle is
    Definition (ISTQB): a test oracle is a source used to determine expected results. Plainly: the thing you compare the software's behaviour against to decide right from wrong.

The eleven oracles (FEW HICCUPPS)
F — Familiar. Consistent with the pattern of familiar problems — does this look like a class of bug you've seen before? Juice Shop: the password field showing two different maximums (BUG-001) matches the familiar pattern of "validation message and validation logic drifted apart," which any experienced tester has seen a hundred times. Recognising the pattern is itself an oracle.

E — Explainability. Consistent with your ability to explain it. If you can't construct a sensible reason the software would do this, that inability is a signal. Juice Shop: an empty search returning all 46 products — can you explain why it would? "Empty means show everything" is one explanation, so it passes this oracle weakly. But if a behaviour leaves you saying "there's no sane reason it would do that," suspect a bug.

W — World. Consistent with known facts about the world. Juice Shop: prices display as 1.99¤ and 5000¤. The ¤ is the generic "unknown currency" placeholder — but in the real world, prices are in euros, lei, dollars. A shop that can't name its currency is inconsistent with how commerce works in the world. 

H — History. Consistent with the product's own past behaviour. If a feature worked in the last release and doesn't now, that's a regression — history is the oracle. Juice Shop: the apostrophe search used to crash with a 500 in older versions and now returns a clean empty result — history tells us the behaviour changed, and (here) improved. Every regression bug you ever file uses this oracle.

I — Image. Consistent with the image or reputation the organisation wants to project. Juice Shop: a raw database error or stack trace shown to a user is inconsistent with the image any real company wants — it looks unprofessional and leaks internals. "This makes the company look bad" is a legitimate, nameable oracle, not just a vibe.

C — Comparable products. Consistent with comparable products. For Juice Shop, "every other e-commerce search trims whitespace / shows a currency / lets you remove a basket item" all draw on comparable products. 

C — Claims. Consistent with what people claim about it — the spec, the marketing, the documentation, the on-screen hint text. Juice Shop: the field claims "Password must be 5-40 characters long." 

U — User expectations. Consistent with what a reasonable user would expect and want. Juice Shop: a user expects that if they type a product name that exists, they'll find it. Note this differs from Claims — the app might never claim anything about this, but the user reasonably expects it anyway. (The real-user-need oracle.)

P — Product. Consistent within itself — internal consistency. Does the feature behave like the rest of the same product? Does one part contradict another? Juice Shop: BUG-001 again — the hint says 40, the counter says 20, the field accepts 30; three parts of one feature disagreeing. (The internal-consistency oracle — the one you nailed on the basket's − button.)

P — Purpose. Consistent with its intended purpose. What is this thing for, and does it serve that? Juice Shop: a login form's purpose is to admit valid users and reject invalid ones; if it let a wrong password through, it fails its purpose regardless of what any spec says.

S — Standards and statutes. Consistent with external standards and laws. Juice Shop, and this one matters enormously for your target market: a 5-character-minimum password is below security standards (NIST). Accessibility must meet WCAG (Lesson 52). And GDPR is EU law — how the app handles personal data isn't a matter of taste, it's a legal standard, and "this violates GDPR" is one of the most powerful oracle statements you can make in a Romanian or EU shop. (The external-standard oracle.)

**Lesson 11**

Executable (low-level / concrete) test case — a case with all values filled in, runnable step by step with no judgement required.
High-level (logical) test case — describes what to test without concrete data.
ID — unique, stable identifier for a case.
Precondition — what must be true before step 1; makes the case reproducible.
Test data — the specific values a case uses, often listed separately from steps.
Step — one numbered action; each observable step has its own expected result.
Expected result — what should happen, anchored to an oracle.
Postcondition — what must be true after the case finishes.
Atomicity — one test case verifies one thing, so a failure points at one feature.
Positive test case — verifies correct behaviour with valid input.
Negative test case — verifies correct rejection/handling of invalid input.
Status — the execution result: Pass / Fail / Blocked / Skipped (filled in when run, not when written).
Traceability link — which requirement or condition the case covers.
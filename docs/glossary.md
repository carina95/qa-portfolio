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

1. Terminal
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

2. Web
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

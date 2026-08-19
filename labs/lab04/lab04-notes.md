# Lab 4: Remote Repositories


# GitHub

## About GitHub

- Web-based platform that hosts [Git]{.mdlit} repositories in the cloud.

- Used by developers store, share, and collaborate on software code.

- Acquired by Microsoft in 2018.

- Provides: 

    + a user-friendly graphical interface, 
    + project management features, 
    + automation tools on top of the standard command-line Git environment

- [Github]{.hilit} $\neq$ [Git]{.mdlit}

    + [Git]{.mdlit} is the local version control engine,
    + [Github]{.hilit} is the global distribution hub.



## Github registration

To use github you have to open an account

- Github offers freemium account

- Choose a professional username

- We recommend lowercase, hyphenated name (like `john-doe`) suitable for resumes 
and academic papers.

- GitHub enforces mandatory 2FA. You must set up an authenticator app
(e.g. Google Authenticator, Bitwarden, Authy)


## Authentication: The Password Era is Over

- Personal Access Tokens (PATs - HTTPS): A long, randomly generated string 
created in the GitHub settings panel. It acts as a temporary password 
specifically for the command line. This is generally the easiest method for 
beginners.

- SSH Keys: A pair of cryptographic files generated on the student's local machine. 
The "public" key is pasted into GitHub, allowing seamless, passwordless login. 
This is the preferred choice for students working on remote university servers or 
Linux environments.


::: {.notes}
You also need to set-up an authentication mechanism that allows
your computer (local git config) to talk to github

This could be either a seamless process, akthough sometimes it can 
be a pain the butt
:::


# Creating Remotes

## Create a remote Repository

\

:::: {.columns}
::: {.column .fragment}
[New Repository]{.hilit}

You can create a brand new remote repository
:::

::: {.column .fragment}
[Existing Repository]{.mdlit}

You can **fork** (copy) an existing remote repository
:::
::::


## Creating a remote repo (in github)

- go to github
- locate ➕ button to create repo (and click it)
- select "New repository"
- follow steps


## Create a new remote repository

\

:::: {.columns}
::: {.column width=42% .fragment}
[Empty Repository]{.mdlit}

Initialize an empty repo (with nothing in it, no files).

Usually, you use this option to add an existing local repository.
:::

::: {.column width=6%}
:::

::: {.column width=42% .fragment}
[Minimal Repository]{.hilit}

Initialize a minimal repository

::: {.nonincremental}
- with a `README` file
- maybe a `.gitignore`
- maybe a `LICENSE`
:::

:::
::::



## GitHub: Authentication Setup

Setting up GitHub authentication feels like an overengineered chore because GitHub 
completely changed the rules, shifting from a user-friendly system to a hyper-secure 
enterprise model.

We are about to do the most annoying part of the entire course. It is frustrating, 
the errors are annoying, and everyone struggles with it. Once we get past this 
authentication setup, you will never have to think about passwords again.

For a student, standard passwords make intuitive sense. Cryptographic 
public/private key pairs do not. Explaining that a file named `id_ed25519.pub` 
needs to be copied into a web browser, while a invisible file named `id_ed25519` 
must stay hidden in a secret directory (~/.ssh), requires a massive jump in 
abstract thinking.


## Understanding SSH: The "Lock and Key" Analogy

Before writing code, we'll give you a clear mental model of how Secure Shell (SSH) 
authentication works:

- The Analogy: Imagine a physical padlock and a key.

- The Public Key (The Lock): You upload this to GitHub. Anyone can see it, 
but it cannot open anything by itself. It is explicitly designed to lock a 
connection so only one specific key can open it.

- The Private Key (The Key): This stays hidden on your local computer. 
You must never share it, email it, or move it.

- The Handshake: When you push code, GitHub presents your public "lock." 
Your local computer uses your private "key" to unlock it. If they match, 
GitHub verifies your identity instantly without you typing a password.

An SSH key allows automated scripts to pull or push data without a human 
present to type a token. Once set up, you never have to manage expiring 
30-day personal tokens, keeping your focus entirely on the reproducibility 
of your data science projects.


## Live Demo Steps: Generating and Adding SSH Keys

Walk students through these three precise steps on their terminal.

### Step 1: Check for Existing Keys

Before creating a new key, ensure the student doesn't 
already have one.

```bash
ls -al ~/.ssh
```

What to look for: If files named `id_ed25519` or `id_rsa` already exist, 
they can skip to Step 3.


### Step 2: Generate a New SSH Key

We use `ed25519`, which is the modern, highly secure cryptographic standard 
recommended by GitHub.

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

- Prompt 1: "Enter file in which to save the key..." 
Press Enter to accept the default location.

- Prompt 2: "Enter passphrase..."
Type a memorable passphrase for extra security, or Press Enter twice for a 
passwordless key (highly convenient for lab setups, though slightly less 
secure if the physical laptop is stolen).


### Step 3: Copy the Public Key

Students must copy the exact contents of the public key (`.pub`).

- Mac: `pbcopy < ~/.ssh/id_ed25519.pub`
- Windows (Git Bash): `clip < ~/.ssh/id_ed25519.pub`
- Linux: `cat ~/.ssh/id_ed25519.pub` (then manually highlight and copy).


### Step 4: Add the Key to GitHub

1) Log into GitHub.
2) Click your profile picture in the top right, then Settings.
3) In the left sidebar, click SSH and GPG keys.
4) Click the green New SSH key button.
5) In the Title field, add a descriptive label for the machine 
(e.g., "Personal MacBook Pro" or "Lab Desktop").
6) In the Key field, paste the copied text.
7) Click Add SSH key.


### Step 5: Testing the Connection

Have every student run this exact command to verify their setup works before 
moving on to Git operations:

```bash
ssh -T git@github.com
```

Expected Success Message:

```
"Hi username! You've successfully authenticated, but GitHub does not provide shell access."
```

Common Student Pitfalls to Address Live:

- The Fingerprint Warning: The terminal will say, 
`"The authenticity of host 'github.com' can't be established... Are you sure you want to continue connecting?"` 

Tell students to type yes and press Enter. Explain that this is a normal one-time security warning.

- Permission Denied (publickey): This means they copied the wrong file 
(the private key instead of the public one) or pasted it incorrectly. 
Emphasize that the key text must start with `ssh-ed25519` and end with their 
email address.



# Linking a local repo to GitHub

## 1) Create the Blank Target on GitHub

You create an empty container on GitHub to receive their local commits.

- Navigation: Click the `+` icon in the top right of GitHub, then Select **New repository**.

- Repository name: Use the exact folder name of the local project for consistency.

- Visibility: Select Public or Private based on project goals.

- The Golden Rule: Leave `"Add a README file"`, `"Add .gitignore"`, and 
`"Choose a license"` [UNCHECKED]{.hilit}.

::: {.notes}
Checking these creates an initial commit on GitHub. Because that commit 
does not exist in the local project's timeline, it creates divergent histories. 
This causes a confusing fatal: refusing to merge unrelated histories error on 
the first push.
:::


## 2) Copy the SSH URL

Once the repository is created, GitHub displays a quick setup page.

- Click the SSH button (not HTTPS).

- It will look exactly like this:

. . .

```
git@github.com:username/repository-name.git
```


## 3) Bind the Remote URL Locally

Open the terminal, navigate to the local project root folder, and run:

```bash
git remote add origin git@github.com:username/repository-name.git
```

\

- `git remote add`: Tells Git to map a new remote server.
- `origin`: The conventional nickname for the primary remote.
- `git@github.com:...`: The specific cryptographic target address. 


## Why the name `origin`?

`origin` is just a default nickname, not a requirement.

When you connect a local repository to GitHub, Git needs a way to reference 
that long, complex SSH URL (`git@github.com:user/repo.git`). Instead of forcing 
you to type that entire URL every time you want to push or pull, Git maps it 
to a short, convenient nickname.


## 4) Verify the Remote Binding

Confirm the link was written correctly:

```bash
git remote -v
```

\

. . .

Expected output:

```
origin  git@github.com:username/repository-name.git (fetch)
origin  git@github.com:username/repository-name.git (push)
```


## 5) Execute the first push (set upstream)

Transmit the history and link the local branch to the remote branch permanently.

```bash
git push -u origin main
```

\

- `-u` (or `--set-upstream`): Tells Git to remember the connection between 
the local `main` branch and the `main` branch on `origin`.

. . .

For every subsequent commit, you need to type a short command:

```bash
git push
```





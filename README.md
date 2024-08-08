# pct

## Description

Many of the [devx tools](https://puppetlabs.github.io/content-and-tooling-team/tools/) are ruby repositories.  When developing or testing against these, I want to be able to "sandbox" the code in its directory locally installing gem dependencies and configuring a ruby version.  In other words, I want to be able to use the code without interfering with the system ruby environment.  This repository gathers together scaffolding to achieve all of the above with a single command `pct new devx/ruby-config` This makes it easy to either sandbox an existing repository or begin new ruby development altogether.

For example, manual ruby configuration as follows will setup such a sandbox and assumes the presense of `rbenv` and `direnv`:

* setup bundler for local development only

```bash
# setup local bundle: this creates the `.bundle/config` file
bundle config set --local path ".direnv/ruby/gems"
bundle config set --local gemfile "Gemfile"
bundle config set --local bin ".direnv/bin"

# verify config
bundle config
```

* configure a particular version of ruby, e.g., 2.7.5

```bash
# initialize rbenv with the following if this isn't done automatically via your .zshrc)
# eval "$(rbenv init - zsh)" 

# set the local ruby environment: this creates a `.ruby-version` file
rbenv local 2.7.5

# verify rbenv version
rbenv version
```

* create a `.envrc` direnv file so that ruby BUNDLER and GEM environment variables are setup properly

```bash
# .envrc

# initialize rbenv shims, e.g., eval "$(rbenv init - zsh)" 
use rbenv

# setup all bundle/gem environment variables
layout ruby

```

On the terminal execute `direnv allow` to enable the direnv configuration above.

## Pre-requisites

* [direnv](https://direnv.net)
* OPTIONAL: [rbenv](https://github.com/rbenv/rbenv)
* PCT (Puppet Content Templates).  Instructions for this follow.

## Setup

Do the following to install PCT and configure the devx templates (See Appendix for [sample output](#sample-setup-output)):

```bash
# install via curl no longer works
# curl -L https://pup.pt/pdkgo/install.sh | sh
# ...instead install 
git clone https://github.com/puppetlabs-toy-chest/pct.git
cd pct/scripts
./install.sh

# Add puppet content templates (pct) to $PATH
export PATH=${PATH}:~/.puppetlabs/pct

# clone this repository somewhere on your local machine, e.g.,
mkdir -p ~/.config/devx
cd ~/.config/devx
git clone https://github.com/gavindidrichsen-puppetlabs/devx_team.git

# add the `pct_devx` templates to the pct installation directory.  For example:
cd ~/.puppetlabs/pct/templates                      # <== cd to the pct template installation directory
ln -s ~/.config/devx/pct_devx/templates/devx devx   # <== add a 'devx' namespace, i.e., a symlink to this repo
ls -la                                              # <== verify the symlink

# verify new `devx` templates including 'ruby-config' are present alongside the existing `puppetlabs` ones
pct new --list
```

## Usage

### Add bundler configuration to a directory

```bash
# setup bundler for a ruby directory via PCT
cd <ruby project directory>
pct new devx/ruby-config

# ensure direnv intitializes rbenv, etc
direnv allow

# select a ruby version for the directory
rbenv versions
rbenv local <choose_a_ruby_version>

# assuming a Gemfile already exists in this directory, then...
bundle install
```

## Appendix

### Sample setup output

* Install PCT and add it to the `$PATH`.  See <https://puppetlabs.github.io/devx/pct/install/>

```bash
# install
➜  pct git:(development) curl -L https://pup.pt/pdkgo/install.sh | sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:--  0:00:01 --:--:--     0
100  2765  100  2765    0     0   1370      0  0:00:02  0:00:02 --:--:--  1370
Downloading and extracting pct to /Users/gavin.didrichsen/.puppetlabs/pct
Remember to add the pct app to your path:
export PATH=$PATH:/Users/gavin.didrichsen/.puppetlabs/pct
➜  pct git:(development)

# ensure on $PATH, e.g.,
➜  pct git:(development) ✗ cat ~/.zshrc | grep -A 2 "pct"
# Add puppet content templates (pct) to $PATH
export PATH=${PATH}:~/.puppetlabs/pct
```

* Clone this repository somewhere on your local machine, e.g.,

```bash
mkdir -p ~/.config/devx
cd ~/.config/devx
git clone https://github.com/gavindidrichsen-puppetlabs/devx_team.git
```

* Add the `pct_devx` templates to the pct installation directory.  For example:

```bash
# cd to the user's templates directory (will normally contain a single 'puppetlabs' namespace directory)
cd ~/.puppetlabs/pct/templates
# add a new 'devx' namespace directory, i.e. symlink to the current repo templates
ln -s ~/.config/devx/pct_devx/templates/devx devx
# verify the symlink: it should be present and point to the expected directory
ls -la
```

Verify that the new `devx` templates as well as the `puppetlabs` tempates are listed

```bash
# verify the devx templates: it should have the new 'Ruby Bundle Config' template
➜  pct_devx git:(development) ✗ pct new --list
           DISPLAYNAME           |   AUTHOR   |                   NAME                   |  TYPE    
---------------------------------+------------+------------------------------------------+----------
  Ruby Bundle Config             | devx       | ruby-config                              | item     
  Bolt Plan                      | puppetlabs | bolt-plan                                | item     
  Bolt Project                   | puppetlabs | bolt-project                             | project  
  Bolt PowerShell Task           | puppetlabs | bolt-pwsh-task                           | item     
  ...
  ...
  Puppet Module Base             | puppetlabs | puppet-module-base                       | project  
  Puppet Resource API Provider   | puppetlabs | rsapi-provider                           | item     
➜  pct_devx git:(development) ✗ 
```

### Other Stuff

```bash
# create a bolt project
➜  pct git:(development) ✗ pct new puppetlabs/bolt-project --name my_template_bolt_project
12:41PM INF Deployed: /Users/gavin.didrichsen/@REFERENCES/github/app/development/tools/puppet/@products/pct/my_template_bolt_project
12:41PM INF Deployed: /Users/gavin.didrichsen/@REFERENCES/github/app/development/tools/puppet/@products/pct/my_template_bolt_project/bolt-project.yaml
➜  pct git:(development) ✗ 
➜  pct git:(development) ✗ tree my_template_bolt_project 
my_template_bolt_project
└── bolt-project.yaml

1 directory, 1 file
➜  pct git:(development) ✗ 

# see all the current templates "raw":
➜  pct git:(development) ✗ ls -1 ~/.puppetlabs/pct/templates/puppetlabs
bolt-plan
bolt-project
bolt-pwsh-task
bolt-yaml-plan
editorconfig
ghactions-spec-workflow-supported-puppet
git-attributes
git-ignore
puppet-class
puppet-content-template
puppet-control-repo
puppet-defined-type
puppet-fact
puppet-module-base
rsapi-provider
➜  pct git:(development) ✗ 
```

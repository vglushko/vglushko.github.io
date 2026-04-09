# Local Development Notes

## Environment preparation
1. Install prerequisites on Ubuntu:
   ```bash
   sudo apt update
   sudo apt install -y git curl build-essential libssl-dev libreadline-dev zlib1g-dev libyaml-dev libffi-dev libgdbm-dev
   ```

2. Install rbenv and ruby-build:
   ```bash
   git clone https://github.com/rbenv/rbenv.git ~/.rbenv
   git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
   ```

3. Enable rbenv in your shell:
   ```bash
   echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
   echo 'eval "$(rbenv init -)"' >> ~/.bashrc
   source ~/.bashrc
   ```

## Quick start
1. Install Ruby and Bundler via rbenv:
   ```bash
   rbenv install 3.1.4
   rbenv install 3.2.0
   rbenv local 3.1.4
   gem install bundler
   ```

2. Configure Bundler to install gems locally:
   ```bash
   bundle config set --local path vendor/bundle
   bundle install
   ```

3. Run Jekyll in serve mode with drafts:
   ```bash
   bundle exec jekyll serve --drafts
   ```

## Notes
- `vendor/` is ignored by Git and stores project-local gems.
- Use `rbenv local 3.1.4` in this project directory so the site runs with a compatible Ruby version.
- If you need another Ruby version, install it with `rbenv install` and switch using `rbenv local` or `rbenv global`.
`
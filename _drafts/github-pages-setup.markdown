Blog on GitHub

1. Create pages repository on GitHub
2. Clone repository to the local file system
3. Install ruby
	https://www.ruby-lang.org/en/documentation/installation/#apt
	From terminal use apt package manager:
	$ sudo apt-get install ruby-full
	It installs Ruby 2.3.1, which is an old stable release on Ubuntu.
4. Install jekyll (https://jekyllrb.com/)
	sudo gem install jekyll bundler //(https://jekyllrb.com/docs/quickstart/)
	sudo jekyll new blogs
	cd blogs
	~/blogs $ bundle exec jekyll serve
	Now browse to http://localhost:4000
5. Rebuild site for changes (https://jekyllrb.com/docs/usage/)
	jekyll build
	jekyll build --watch
	Changes to _config.yml are not included during automatic
6. Jekyll configuration (https://jekyllrb.com/docs/configuration/)
7. Directory structure (https://jekyllrb.com/docs/structure/)
8. 


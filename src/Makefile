# Folders containing generated index.html files that are ready to be copied
# as-is into /docs
INDEX_DIRS = \
  . \
  all \
  series/* \
  art/*

DOCROOT = ../docs
IMG = img
CSS = css

.PHONY: build
build:
	mypy --strict build
	./build

.PHONY: rebuild
rebuild:
	mypy --strict build
	./build --force

.PHONY: all
all:
	$(MAKE) build
	$(MAKE) local
	git status

.PHONY: live
live:
	xdg-open http://0.0.0.0:8000/
	python -m http.server --directory ../docs 8000

# Copy all website files to /docs
.PHONY: local
local:
	mkdir -p $(DOCROOT)
	for dir in $(INDEX_DIRS); do \
	  mkdir -p $(DOCROOT)/"$$dir"; \
	  cp -u "$$dir"/index.html $(DOCROOT)/"$$dir"/; \
	done
	for x in $(IMG); do \
	  mkdir -p $(DOCROOT)/$$x; \
	  cp -r -u $$x/* $(DOCROOT)/$$x/; \
	done
	for x in $(CSS); do \
	  mkdir -p $(DOCROOT)/$$x; \
	  cp -r -u $$x/* $(DOCROOT)/$$x/; \
	done

# Publish all changes
.PHONY: push
push:
	git add .
	git commit -a -m 'Publish site'
	git push origin main

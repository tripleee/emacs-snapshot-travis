GIT_USER=tripleee
GIT_EMAIL=emacs-snapshot-travis@eeera.imap.cc

E=cd emacs-snapshot &&
G=$E git

.PHONY: all merge bootstrap
all: merge
merge: .bootstrapped $(wildcard *.in) $(wildcard dot.*)
	$G checkout master
	$G branch -D travis-snapshot

	$G fetch upstream
	$G merge upstream/master

	$G checkout -b travis-snapshot
	#find emacs-snapshot -type f -exec sed -i 's/setuid/**bleeeep**/g' {} +
	for f in $(filter-out $<,$^); do \
		case $$f in *.in) d=$${f%.in};; dot.*) d=$${f#dot};; esac \
		; sed 1d "$$f" >emacs-snapshot/"$$d" \
	; done
	$G add $(patsubst dot.%,.%,$(patsubst %.in,%, $(filter-out $<,$^)))
	printf '%s\n' "Prepare for Travis $$(date +%c) $$(git describe)" \
		"$$($G describe)" \
	| ( $G commit -a -F - )

	$G push -f origin travis-snapshot

bootstrap: .bootstrapped
.bootstrapped:
	git clone git@github.com:tripleee/emacs-snapshot.git
	$G remote add upstream git@github.com:emacs-mirror/emacs.git
	$G checkout master

	$G checkout -b travis-snapshot
	$G config user.name "$(GIT_USER)"
	$G config user.email "$(GIT_EMAIL)"

	touch $@

.PHONY: clean realclean distclean
clean:
	-$G checkout travis-snapshot
	$(RM) *~
realclean: clean
	:
distclean: realclean
	$(RM) -r emacs-snapshot .bootstrapped

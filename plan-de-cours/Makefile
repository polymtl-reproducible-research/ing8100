TEX := ING8100_plan_de_cours.tex
PDF := $(TEX:.tex=.pdf)
LOG := $(TEX:.tex=.log)

# latexmk handles the rerun logic for us, but it is not part of every TeX
# installation (BasicTeX and some MacTeX setups omit it, and a stale symlink
# can be present without a working binary behind it). Only use it if it
# actually runs; otherwise fall back to two plain pdflatex passes, which is
# what this document needs anyway.
LATEXMK := $(shell latexmk -v >/dev/null 2>&1 && echo yes)

.PHONY: all clean watch

all: $(PDF)

ifeq ($(LATEXMK),yes)

$(PDF): $(TEX)
	latexmk -pdf -interaction=nonstopmode -halt-on-error $(TEX)

watch:
	latexmk -pdf -pvc -interaction=nonstopmode $(TEX)

clean:
	latexmk -C
	rm -f *.aux *.log *.out *.fls *.fdb_latexmk *.synctex.gz

else

# First pass may legitimately report unresolved references; only the second
# pass has to succeed.
$(PDF): $(TEX)
	-pdflatex -interaction=nonstopmode $(TEX)
	pdflatex -interaction=nonstopmode $(TEX)
	@test -f $(PDF) || { echo ""; echo "Build failed - see $(LOG)"; exit 1; }

watch:
	@echo "'make watch' needs latexmk:  tlmgr install latexmk"
	@exit 1

clean:
	rm -f *.aux *.log *.out *.fls *.fdb_latexmk *.synctex.gz $(PDF)

endif

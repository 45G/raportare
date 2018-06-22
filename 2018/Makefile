.SILENT:

# Processors
LATEX = latex 
BIBTEX = bibtex
L2H = latex2html
PDFLATEX = pdflatex 
PS2PDF = ps2pdf
DVIPS = dvips
FIG2DEV = fig2dev
FIND = find
XARGS = xargs
GZIP = gzip -f
COPY = if test -r $*.toc; then cp $*.toc $*.toc.bak; fi
RM = rm -rf

# Viewers
PSVIEWER = gv
MACVIEWER = open

RERUN = (There were undefined references|Rerun to get (cross-references|the bars) right)
RERUNBIB = No file.*\.bbl|There were undefined citations

# files
TARGET = main
ROOTTEXFILE = $(TARGET).tex
PDFFILES = $(TARGET).pdf
PSFILES = $(TARGET).ps
DVIFILES = $(TARGET).dvi
GOALS = $(PDFFILES)
GOALS_PS = $(DVIFILES) $(TARGET).ps 

PROJECTDIRECTORY = change-template
ALLFILES = main.tex ./content.tex ./introduction.tex ./authors.tex ./executive-summary.tex ./frontpages.tex ./glossary.tex ./macros.tex ./changeGlobalDefs.tex chapters/intro.tex chapters/oro.tex chapters/upb1.tex chapters/upb2.tex
TEXFILES = main.tex ./content.tex ./introduction.tex ./authors.tex ./executive-summary.tex ./frontpages.tex ./glossary.tex ./macros.tex ./changeGlobalDefs.tex
BIBFILES = ./references
IMAGEFILES_EEPIC = 
CEXCLUDE = --exclude=CVS --exclude=.cvs*  --exclude=.svn* --exclude=*.zip --exclude=*.dvi 

%.eepic:    %.fig
	fig2dev -L eepic $< $*.eepic

%.dvi:  %.tex $(ALLFILES)
	$(COPY);$(LATEX) $<
	egrep -c "$(RERUNBIB)" $*.log && ($(BIBTEX) $*;$(COPY);$(LATEX) $<) ; true
	egrep "$(RERUN)|$(RERUNBIB)" $*.log && ($(COPY);$(LATEX) $<) ; true
	egrep "$(RERUN)|$(RERUNBIB)" $*.log && ($(COPY);$(LATEX) $<) ; true
	if cmp -s $*.toc $*.toc.bak; then echo "OK" ;else $(LATEX) $< ; fi
	$(RM) $*.toc.bak

%.ps:       %.dvi
	dvips $< -o $@
	cp $@ $@.copy
	$(GZIP) $@
	mv $@.copy $@

%.pdf:      %.tex $(ALLFILES)
	$(COPY);$(PDFLATEX) $<
	egrep -c "$(RERUNBIB)" $*.log && ($(BIBTEX) $*;$(COPY);$(PDFLATEX) $<) ; true
	egrep "$(RERUN)|$(RERUNBIB)" $*.log && ($(COPY);$(PDFLATEX) $<) ; true
	if cmp -s $*.toc $*.toc.bak; then echo "OK" ;else $(PDFLATEX) $< ; fi
	$(RM) $*.toc.bak 
	pdftk A=main.pdf B="Studiu conectare MPTCP în reţeaua ORO.pdf" cat A1-5 B A12-end output final.pdf


.PHONY: default
default: all pdfshow

.PHONY: all
all:     $(IMAGEFILES_EEPIC) $(GOALS) clean_tex_stdout_files

# make a ps ouput
.PHONY: psmake
psmake: $(IMAGEFILES_EEPIC) $(GOALS_PS) clean_tex_stdout_files

# make and display in pdf format
.PHONY: psshow
psshow: $(TARGET).ps
	@echo Making $(TARGET).ps and launching it in postscript reader 
	$(MAKE) psmake
	$(PSVIEWER) $(PSFILES)

# make a pdf ouput
.PHONY: pdfshow
pdfshow: $(TARGET).pdf
	@echo Making $(TARGET).pdf and launching in default pdf reader
	$(MAKE) all

# make a pdf ouput and open for mac
.PHONY: macshow
macshow: $(TARGET).pdf
	@echo Making $(TARGET).pdf and launching it in kpdf reader
	$(MAKE) all
	$(MACVIEWER) $(PDFFILES)

# use latex2html to generate html 
.PHONY: html
html:
	@echo Making html documents 
	$(LATEX2HTML) $(ROOTTEXFILE)

# Build a distribution.
.PHONY: distribution
distribution: distributionclean
	@echo Making $(PROJECTDIRECTORY).tar.gz
	tar  $(CEXCLUDE) -czf /tmp/$(PROJECTDIRECTORY).tar.gz  ../$(PROJECTDIRECTORY) 
	mv -f /tmp/$(PROJECTDIRECTORY).tar.gz ./
	chmod 644 $(PROJECTDIRECTORY).tar.gz
	make

# Full clean, the removing all generated outputs including the Makefile
.PHONY: reset
reset: clean
	@echo Resetting project 
	$(RM) Makefile

# Clean tex stdout files
clean_tex_stdout_files:
	@echo Cleaning tex stdout files
	($(FIND) ./  . . \
		-maxdepth 1 \
		-name "*.aux" -or \
		-name "*.acn" -or \
		-name "*.glo" -or \
		-name "*.ist" -or \
		-name "*.lot" -or \
		-name "*.sbl" -or \
		-name "*.log" -or \
		-name "*.bbl" -or \
		-name "*.lof" -or \
		-name "*.blg" -or \
		-name "*.brf" -or \
		-name "*.cb" -or \
		-name "*.ind" -or \
		-name "*.idx" -or \
		-name "*.ilg" -or \
		-name "*.inx" -or \
		-name "*.thm" -or \
		-name "*.toc" -or \
		-name "*.bak" -or \
		-name "*.out" -or \
		-name "*.nav" -or \
		-name "*.snm" -or \
		-name "*.eepic" \
	) | $(XARGS) $(RM)

# Clean emacs junk 
clean_emacs_junk:
	@echo Cleanining emacs junk
	($(FIND) ./  .  \
		-maxdepth 1 \
	   -name "_region_*" -or \
		-name "*semantic.cache*" \
	) | $(XARGS) $(RM)


# Clean the workspace 
.PHONY: clean
clean:
	@echo Cleaning project
	${MAKE} clean_tex_stdout_files
	${MAKE} clean_emacs_junk
	@echo Cleaning generated files
	($(FIND) ./  .  \
		-maxdepth 1 \
		-name "*.ps" -or \
		-name "*.dvi" -or \
		-name "*.pdf" -or \
		-name "*.ps.gz"  \
	) | $(XARGS) $(RM)

# Clean the workspace in prepration for producing a ditribution 
distributionclean: clean
	@echo Cleanining for distribution 
	 ($(FIND) ./  .  \
	 	-maxdepth 1 \
	 	-name "*.tar.gz" -or \
	 	-name "*~*"  \
	) | $(XARGS) $(RM)

# Print an approximate word count 
wordcount:
	@echo Approximate word count: `grep -v '^\\\\' $(TEXFILES)|grep -v '^%'|wc -w`

info:
		echo "The following make options are available to you:"
		echo " all          => "
		echo " main         => "
		echo " ps           => "
		echo " psshow       => "
		echo " pdfshow      => "
		echo " kpdfshow     => "
		echo " macshow     => "
		echo " acroshow     => "
		echo " html         => "     
		echo " distribution => "
		echo " clean        => "
		echo " reset        => "
		echo " wordcount    => "

######################################################################
### Makefile.mk ends here



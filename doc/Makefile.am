## This file is part of dvisvgm
## Copyright (C) 2005-2017 Martin Gieseking <martin.gieseking@uos.de>
##
## Process this file with automake.

man_MANS = dvisvgm.1
EXTRA_DIST = $(man_MANS) conf-dblatex-man.xsl conf-dblatex-pdf.xsl tweak-dblatex-pdf.xsl

CONF_DBLATEX_MAN  = $(top_srcdir)/doc/conf-dblatex-man.xsl
CONF_DBLATEX_PDF  = $(top_srcdir)/doc/conf-dblatex-pdf.xsl
TWEAK_DBLATEX_PDF = $(top_srcdir)/doc/tweak-dblatex-pdf.xsl
TWEAK_DB_ARTICLE  = $(top_srcdir)/doc/tweak-db-article.xsl

DB_VARS = man.endnotes.list.enabled=0 man.endnotes.are.numbered=0 man.authors.section.enabled=0

dvisvgm.1: dvisvgm-man.xml $(CONF_DBLATEX_MAN)
	if [ `type -p xmlto` ]; then \
		xmlto -m $(CONF_DBLATEX_MAN) $(addprefix --stringparam , $(DB_VARS)) man $<; \
	fi

dvisvgm.pdf: dvisvgm-man.xml $(CONF_DBLATEX_PDF) $(TWEAK_DBLATEX_PDF)
	mv $< $<.tmp
	xsltproc -o $< $(TWEAK_DBLATEX_PDF) $<.tmp
	dblatex -bxetex --texstyle=./dvisvgm.sty -p $(CONF_DBLATEX_PDF) $<
	mv $<.tmp $<
	mv $(basename $<).pdf $@

dvi-pdf: dvisvgm.dvi
	dvipdfm $<
	rm -f $<

dvisvgm.dvi: dvisvgm.1
	groff -Tdvi -mandoc ./$< >$@

dvisvgm.html: dvisvgm-man.xml
	xsltproc -o $@ db2html.xsl $<

dvisvgm.epub: dvisvgm-article.xml
	a2x -darticle -fepub -L --icons --icons-dir=. $<
	mv $(basename $<).epub $@

dvisvgm-man.xml: dvisvgm.txt
	if [ `type -p asciidoc` ]; then \
		asciidoc -a icons -a 'iconsdir=.' -a badges -a 'revnumber=@VERSION@' --unsafe -bdocbook -dmanpage -o $@ $<; \
		sed -i "s#\(</refmeta>\)#<refmiscinfo class='date'>$(shell stat -c %y $<)</refmiscinfo>\n\1#" $@; \
	fi

dvisvgm-article.xml: dvisvgm.txt
	if [ `type -p asciidoc` ]; then \
		asciidoc -a icons -a 'iconsdir=.' -a badges -a 'revnumber=@VERSION@' --unsafe -bdocbook -darticle -o $@.tmp $<; \
		xsltproc -o $@ $(TWEAK_DB_ARTICLE) $@.tmp; \
		rm $@.tmp; \
		sed -i "s/{VERSION}/@VERSION@/" $@; \
	fi

epub: dvisvgm.epub
html: dvisvgm.html
man: dvisvgm.1
pdf: dvisvgm.pdf

clean:
	rm -f dvisvgm.pdf dvisvgm.html dvisvgm-man.xml dvisvgm-article.xml dvisvgm.txt dvisvgm.dvi dvisvgm.epub

distclean-local:
	rm -f dvisvgm.pdf dvisvgm.html dvisvgm-man.xml dvisvgm-article.xml dvisvgm.txt dvisvgm.dvi dvisvgm.epub dvisvgm.1

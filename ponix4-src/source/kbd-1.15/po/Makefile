PACKAGE_NAME  = kbd

srcdir = .

prefix        = /8
datarootdir   = ${prefix}/share
datadir       = ${datarootdir}
localedir     = $(DESTDIR)$(datadir)/locale
gnulocaledir  = $(DESTDIR)$(datadir)/locale
gettextsrcdir = $(DESTDIR)$(datadir)/gettext/po

FOREIGN       = 
HAVE_XGETTEXT = no

SHELL         = /bin/sh
INSTALL       = /bin/install -c
INSTALL_DATA  = ${INSTALL} -m 644

# po2tbl.sed is not taken from /usr/share/gettext/intl
# Often it is not present.
# A RedHat 5.2 installation only has po2tbl.sed.in.
#
PO2TBL = ./po2tbl.sed
INTL = /usr/share/gettext/intl

# Not giving an explicit path improves the chances of finding these
GENCAT = gencat
MSGFMT = msgfmt
XGETTEXT = xgettext
MSGMERGE = msgmerge

# ..      for <config.h> (if HAVE_CONFIG_H is set)
# $(INTL) for "libgettext.h"
INCLUDES = -I.. -I$(INTL)

COMPILE = $(CC) -c $(DEFS) $(INCLUDES) $(CPPFLAGS) $(CFLAGS) $(XCFLAGS)

# Enter here all .po files
POFILES =  cs.po da.po de.po el.po es.po fr.po gr.po nl.po pl.po \
 ro.po ru.po sv.po tr.po
# the same but with .gmo
GMOFILES = cs.gmo da.gmo de.gmo el.gmo es.gmo fr.gmo gr.gmo nl.gmo \
 pl.gmo ro.gmo ru.gmo sv.gmo tr.gmo

CATALOGS = $(GMOFILES)
CATOBJEXT = .gmo
INSTOBJEXT = .mo

.SUFFIXES:
.SUFFIXES: .c .o .po .gmo .mo .msg .cat

.c.o:
	$(COMPILE) $<

.po.mo:
	$(MSGFMT) -o $@ $<

.po.gmo:
	$(MSGFMT) -o $@ $<

.po.cat:
	sed -f $(PO2TBL) < $< > $*.msg \
	  && rm -f $@ && $(GENCAT) $@ $*.msg

all: all-$(HAVE_XGETTEXT)

all-yes: cat-id-tbl.c $(CATALOGS)
all-no:

$(PACKAGE_NAME).pot:
	$(XGETTEXT) --default-domain=$(PACKAGE_NAME) --directory=.. \
	  --add-comments --keyword=_ --keyword=N_ \
	  --files-from=POTFILES.in $(FOREIGN)
	mv $(PACKAGE_NAME).po $(PACKAGE_NAME).pot

cat-id-tbl.c: stamp-cat-id
stamp-cat-id: $(PACKAGE_NAME).pot
	rm -f cat-id-tbl.tmp
	sed -f $(PO2TBL) $(PACKAGE_NAME).pot \
		| sed -e "s/@PACKAGE NAME@/$(PACKAGE_NAME)/" > cat-id-tbl.tmp
	if cmp -s cat-id-tbl.tmp cat-id-tbl.c; then \
	  rm cat-id-tbl.tmp; \
	else \
	  echo cat-id-tbl.c changed; \
	  rm -f cat-id-tbl.c; \
	  mv cat-id-tbl.tmp cat-id-tbl.c; \
	fi
	rm -f stamp-cat-id && echo timestamp > stamp-cat-id

install: install-data-$(HAVE_XGETTEXT)
install-data-no: all
install-data-yes: all
	../mkinstalldirs $(datadir);
	@catalogs='$(CATALOGS)'; \
	for cat in $$catalogs; do \
	  case "$$cat" in \
	    *.gmo) destdir=$(gnulocaledir);; \
	    *)     destdir=$(localedir);; \
	  esac; \
	  destdir=$$destdir; \
	  lang=`echo $$cat | sed 's/$(CATOBJEXT)$$//'`; \
	  dir=$$destdir/$$lang/LC_MESSAGES; \
	  ../mkinstalldirs $$dir; \
	  $(INSTALL_DATA) $$cat $$dir/$(PACKAGE_NAME)$(INSTOBJEXT); \
	  echo "installing $$cat as $$dir/$(PACKAGE_NAME)$(INSTOBJEXT)"; \
	  if test -r $$cat.m; then \
	    $(INSTALL_DATA) $$cat.m $$dir/$(PACKAGE_NAME)$(INSTOBJEXT).m; \
	    echo "installing $$cat.m as $$dir/$(PACKAGE_NAME)$(INSTOBJEXT).m"; \
	  fi; \
	done

uninstall:
	catalogs='$(CATALOGS)'; \
	for cat in $$catalogs; do \
	  lang=`echo $$cat | sed 's/$(CATOBJEXT)$$//'`; \
	  rm -f $(DESTDIR)$(localedir)/$$lang/LC_MESSAGES/$(PACKAGE_NAME)$(INSTOBJEXT); \
	  rm -f $(DESTDIR)$(localedir)/$$lang/LC_MESSAGES/$(PACKAGE_NAME)$(INSTOBJEXT).m; \
	  rm -f $(DESTDIR)$(gnulocaledir)/$$lang/LC_MESSAGES/$(PACKAGE_NAME)$(INSTOBJEXT); \
	  rm -f $(DESTDIR)$(gnulocaledir)/$$lang/LC_MESSAGES/$(PACKAGE_NAME)$(INSTOBJEXT).m; \
	done
	rm -f $(DESTDIR)$(gettextsrcdir)/po-Makefile.in.in

cat-id-tbl.o: $(INTL)/libgettext.h

clean:
	rm -f core core.* *~ *.o $(PACKAGE_NAME).pot cat-id-tbl.tmp

distclean: clean
	rm -f POTFILES *.gmo *.mo *.msg *.cat *.cat.m
	rm -f Makefile

update-po: 
	$(MAKE) $(PACKAGE_NAME).pot
	catalogs='$(CATALOGS)'; \
	for cat in $$catalogs; do \
	  lang=`echo $$cat | sed 's/$(CATOBJEXT)$$//'`; \
	  mv $$lang.po $$lang.old.po; \
	  echo "$$lang:"; \
	  if $(MSGMERGE) $$lang.old.po $(PACKAGE_NAME).pot -o $$lang.po; then \
	    rm -f $$lang.old.po; \
	  else \
	    echo "msgmerge for $$cat failed!"; \
	    rm -f $$lang.po; \
	    mv $$lang.old.po $$lang.po; \
	  fi; \
	done

POTFILES: 
	./update-potfiles

distdir:
	cd $(srcdir) && cp *.po cat-id-tbl.c ChangeLog GNU.ABOUT-NLS po2tbl.sed POTFILES.in stamp-cat-id update-potfiles $(distdir)/

.PHONY: check distdir dvi installcheck

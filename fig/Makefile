
.PHONY: all

%.eps: %.svg
	inkscape -S $*.svg --export-type=eps --export-filename=$*.eps


all: jetbrainsResearch.eps polytech.eps kexOverview.eps kfgExample.eps

clean:
	rm *.eps

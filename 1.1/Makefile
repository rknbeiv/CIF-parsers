INP_DIR = tests
OUT_DIR = outputs

TESTS = $(shell find ${INP_DIR}/ -name .svn -prune \
            -o -name \*.result.canon -prune \
            -o -name runtests -prune \
            -o -name \*.tsv -prune \
            -o -name \*.md -prune \
            -o -type f -print)
OUTPUTS = $(sort ${TESTS:${INP_DIR}/%=${OUT_DIR}/%.out})

TABLE_TEX  = ${OUT_DIR}/table.tex
TABLE_HTML = ${OUT_DIR}/table.html

BENCHMARK_OUT = ${OUT_DIR}/benchmark.out

all table: ${TABLE_TEX} ${TABLE_HTML}

benchmark: ${BENCHMARK_OUT}

${OUT_DIR}/%.out: ${INP_DIR}/%
	for PARSER in $$(ls -1 drivers/* | sort); \
    do \
        echo $$(basename $${PARSER}) $$($${PARSER} $<) >> $@; \
    done

${BENCHMARK_OUT}:
	../scripts/benchmark > $@

cleanAll distclean:
	rm -f ${OUTPUTS} ${TABLE_TEX} ${TABLE_HTML}

${TABLE_TEX}: ${OUTPUTS}
	for OUTPUT in $+; \
	do \
		( \
			echo $${OUTPUT} | sed 's/outputs\///'; \
			perl -lpe 's/WARNING/\//g; s/ERROR/\$$\\times\$$/g;' $${OUTPUT} | \
			perl -ne 'my @a = split /\s/; print " & ", $$a[1]'; \
			echo \\\\\\\\ \
		) \
	done > $@

${TABLE_HTML}: ${OUTPUTS}
	echo '<html><head></head><body><table border="1"><tr><td></td><td>' > $@
	perl -e '$$, = "</td><td>"; print @ARGV' $$(ls -1 drivers/* | xargs -n1 basename | sort) >> $@
	echo '</td></tr>' >> $@
	for OUTPUT in $+; \
	do \
		( \
			echo '<tr><td>' $${OUTPUT}; \
			perl -lpe 's/([WE])\w+/$$1/g; ' $${OUTPUT} | \
			perl -ne 'my @a = split /\s/; print "</td><td>", $$a[1]'; \
			echo '</td></tr>' \
		) \
	done >> $@
	echo '</table></html>' >> $@

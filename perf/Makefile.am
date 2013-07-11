JAVACFLAGS=-classpath $(top_builddir)/src

jarfile = zmq-perf.jar

JZMQ_PERF_JAVA_FILES = \
	local_lat.java \
	local_thr.java \
	remote_lat.java \
	remote_thr.java

JZMQ_PERF_CLASS_FILES = \
	local_lat.class \
	local_thr.class \
	remote_lat.class \
	remote_thr.class

$(JZMQ_PERF_CLASS_FILES): dist_noinst_classes.stamp

dist_noinst_classes.stamp: $(JZMQ_PERF_JAVA_FILES)
	@echo $(JAVAC) $(JAVACFLAGS) $(JAVAC_EXTRA_FLAGS) $(JZMQ_PERF_JAVA_FILES)
	@NEWEST=`ls -t $? | head -1`; $(JAVAC) $(JAVACFLAGS) $(JAVAC_EXTRA_FLAGS) $? && touch -r $$NEWEST $@ && echo "  Touched $@ to match timestamp of $$NEWEST"

$(jarfile): dist_noinst_classes.stamp
	$(JAR) cf $(JARFLAGS) $@ $(JZMQ_PERF_CLASS_FILES)

dist_noinst_DATA = $(jarfile)

dist_noinst_JAVA = $(JZMQ_PERF_JAVA_FILES)

BUILT_SOURCES = \
	$(JZMQ_PERF_JAVA_FILES)

CLEANFILES = \
	$(JZMQ_PERF_CLASS_FILES) \
	dist_noinst_classes.stamp \
	$(jarfile)

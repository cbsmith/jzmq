jarfile = zmq.jar
jardir = $(datadir)/java

JZMQ_JAVA_FILES = \
    org/zeromq/App.java \
    org/zeromq/EmbeddedLibraryTools.java \
	org/zeromq/ZMQ.java \
	org/zeromq/ZMQException.java \
	org/zeromq/ZMQQueue.java \
	org/zeromq/ZMQForwarder.java \
	org/zeromq/ZMQStreamer.java

JZMQ_CPP_FILES = \
	ZMQ.cpp \
	Context.cpp \
	Socket.cpp \
	Poller.cpp \
	util.cpp

JZMQ_H_FILES = \
	org_zeromq_ZMQ.h \
	org_zeromq_ZMQ_Error.h \
	org_zeromq_ZMQ_Context.h \
	org_zeromq_ZMQ_Socket.h \
	org_zeromq_ZMQ_Poller.h

JZMQ_HPP_FILES = \
	util.hpp

JZMQ_CLASS_FILES = \
    org/zeromq/App.class \
    org/zeromq/EmbeddedLibraryTools.class \
	org/zeromq/ZMQ.class \
	org/zeromq/ZMQ$$Context.class \
	org/zeromq/ZMQ$$Socket.class \
	org/zeromq/ZMQ$$Poller.class \
	org/zeromq/ZMQ$$Error.class \
	org/zeromq/ZMQException.class \
	org/zeromq/ZMQQueue.class \
	org/zeromq/ZMQForwarder.class \
	org/zeromq/ZMQStreamer.class

TMP_MANIFEST_PARTIAL = .partial_manifest

NATIVE_LIB_FILE_PATH=libjzmq.$(LIB_EXTENSION)

#Rather tedious process to get the right dlname across platforms.
#We computed the what the name should be during autoconf, but this covers libtool variations
$(NATIVE_LIB_FILE_PATH): libjzmq.la
	ln -sf .libs/`fgrep dlname libjzmq.la | cut -d = -f 2 | tr -d \'` $@

$(TMP_MANIFEST_PARTIAL): Makefile
	@echo "Building JAR manifest..."
	@echo "Name: org/zeromq/" > $@
	@echo "Specification-Title: jZMQ" >> $@
	@echo "Specification-Version: $(SPEC_VERSION)" >> $@
	@echo "Implementation-Title: org/zeromq/" >> $@
	@echo "Implementation-Version: $(BUILD_VERSION) $(BUILD_TIME)" >> $@
	@echo "Main-Class: $(JZMQ_APP_CLASS)" >> $@
	@echo "" >> $@

$(jarfile): $(NATIVE_LIB_FILE_PATH) $(JZMQ_CLASS_FILES) $(TMP_MANIFEST_PARTIAL)
	@echo Adding Native Code Library
	@rm -rf NATIVE && mkdir -v -p "NATIVE/`$(JAVA) org.zeromq.App | grep '^current platform:' | cut -f 2`" && cp $< "NATIVE/`$(JAVA) org.zeromq.App | grep '^current platform:' | cut -f 2`"
	@echo Building JAR $@...
	$(JAR) cfm $(JARFLAGS) $@ $(TMP_MANIFEST_PARTIAL) @ESCAPED_JZMQ_CLASS_FILES@ NATIVE && rm -rf NATIVE

jar_DATA = $(jarfile)

lib_LTLIBRARIES = libjzmq.la
libjzmq_la_SOURCES = $(JZMQ_CPP_FILES)

nodist_libjzmq_la_SOURCES = \
	$(JZMQ_H_FILES) \
	$(JZMQ_HPP_FILES)

libjzmq_la_CXXFLAGS = -Wall
libjzmq_la_LDFLAGS = -version-info @JLTVER@

BUILT_SOURCES = \
	$(JZMQ_H_FILES) \
	$(JZMQ_CLASS_FILES)

#Horrible hack to get around automake not liking when we escape $ using $(subst $$,\$$,foo)
clean-local:
	@echo rm -f @ESCAPED_JZMQ_CLASS_FILES@
	@-echo '@ESCAPED_JZMQ_CLASS_FILES@' | xargs rm -f

CLEANFILES = \
	$(JZMQ_H_FILES) \
	$(JZMQ_CLASS_FILES) \
	$(jarfile) \
	$(NATIVE_LIB_FILE_PATH) \
	$(TMP_MANIFEST_PARTIAL) \
	jni_stubs_noinst.stamp \
	noinst_class_files.stamp \
	classnoinst.stamp \
	NATIVE

#These .stamp files are used to ensure that even in parallel mode with multiple targets needing a rebuild, the command runs but once
jni_stubs_noinst.stamp: org/zeromq/ZMQ.class
	$(CLASSPATH_ENV) $(JAVAH) -force -jni -classpath . org.zeromq.ZMQ && touch -r $< $@

noinst_class_files.stamp: $(JZMQ_JAVA_FILES)
	@echo $(JAVAC) $(JAVACFLAGS) $(JAVAC_EXTRA_FLAGS) $?
	@NEWEST=`ls -t $? | head -1`; $(JAVAC) $(JAVACFLAGS) $(JAVAC_EXTRA_FLAGS) $? && touch -r $$NEWEST $@ && echo "  Touched $@ to match timestamp of $$NEWEST"

$(JZMQ_H_FILES): jni_stubs_noinst.stamp

$(JZMQ_CLASS_FILES): noinst_class_files.stamp

$(srcdir)/ZMQ.cpp: $(JZMQ_H_FILES) $(JZMQ_HPP_FILES)

$(srcdir)/Context.cpp: \
	org_zeromq_ZMQ_Context.h \
	$(JZMQ_HPP_FILES)

$(srcdir)/Socket.cpp: \
	org_zeromq_ZMQ_Socket.h \
	$(JZMQ_HPP_FILES)

$(srcdir)/Poller.cpp: \
	org_zeromq_ZMQ_Poller.h \
	$(JZMQ_HPP_FILES)

$(srcdir)/util.cpp: \
	$(JZMQ_HPP_FILES)

dist-hook:
	-rm $(distdir)/*.h

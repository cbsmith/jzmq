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

JZMQ_APP_CLASS = org.zeromq.App

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
	$(JAR) cfm $(JARFLAGS) $@ $(TMP_MANIFEST_PARTIAL) $(subst $$,\$$,$(JZMQ_CLASS_FILES)) NATIVE && rm -rf NATIVE

jar_DATA = $(jarfile)

dist_noinst_JAVA = $(JZMQ_JAVA_FILES)

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

CLEANFILES = \
	$(JZMQ_H_FILES) \
	$(JZMQ_CLASS_FILES) \
	$(jarfile)

$(JZMQ_H_FILES): org/zeromq/ZMQ.class
	$(CLASSPATH_ENV) $(JAVAH) -force -jni -classpath . org.zeromq.ZMQ

$(JZMQ_CLASS_FILES): $(JZMQ_JAVA_FILES)
	$(JAVAC) $(JAVACFLAGS) $(JAVAC_EXTRA_FLAGS) $^

$(srcdir)/ZMQ.cpp: \
	$(JZMQ_H_FILES) \
	$(JZMQ_HPP_FILES)

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

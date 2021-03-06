
export PROJECT_NAME     = tango
export AUTHOR           = Tango contributors
export DESCRIPTION      =
export MAJOR_VERSION    = 2
export MINOR_VERSION    = 0
export PATCH_VERSION    = 0
export PROJECT_VERSION  = $(MAJOR_VERSION).$(MINOR_VERSION).$(PATCH_VERSION)
export LICENSE          = ACL v3
SOURCES                 = $(filter-out $(Filter), $(getSource))

DDOCFILES               =

# include some command
include build/command.make

OBJECTS             = $(patsubst %.d,$(BUILD_PATH)$(PATH_SEP)%.o,        $(SOURCES))
PICOBJECTS          = $(patsubst %.d,$(BUILD_PATH)$(PATH_SEP)%.pic.o,    $(SOURCES))
HEADERS             = $(patsubst %.d,$(IMPORT_PATH)$(PATH_SEP)%.di,      $(SOURCES))
PLAIN_DOC_FILES     = $(patsubst %.d,$(PLAIN_DOC_PATH)$(PATH_SEP)%.html, $(SOURCES))
DOC_FILES           = $(patsubst %.d,$(DOC_PATH)$(PATH_SEP)%.html,       $(SOURCES))
DDOC_FLAGS          = $(foreach macro,$(DDOCFILES), $(DDOC_MACRO)$(macro))

space :=
space +=

stripBugfix = $(subst $(space),.,$(strip $(wordlist 1, 2, $(subst ., ,$(1)))))

define make-lib
	$(AR) rcs $@ $^
	$(RANLIB) $@
endef

############# BUILD #############
all: static-lib header plain-doc pkgfile-static
	@echo ------------------ Building $^ done
all-shared: shared-lib header plain-doc pkgfile-shared
	@echo ------------------ Building $^ done

.PHONY : pkgfile
.PHONY : plain-doc
.PHONY : doc
.PHONY : clean
.PHONY : .FORCE

static-lib: $(STATIC_LIBNAME)

shared-lib: $(SHARED_LIBNAME)

header: $(HEADERS)
	@echo ------------------ Building headers done

plain-doc: $(PLAIN_DOC_FILES)
	@echo ------------------ Building plain documentation done

doc: $(DOC_FILES)
	$(DC) $(DDOC_FLAGS) $(DVERSION)=TangoDoc index.d $(DF)$(DOC_PATH)$(PATH_SEP)index.html
	@echo ------------------ Building documentation done

geany-tag:
	@echo ------------------ Building geany tag
	$(MKDIR) geany_config
	geany -c geany_config -g $(PROJECT_NAME).d.tags $(SOURCES)

pkgfile-shared:
	@echo ------------------ Building pkg-config file
	@echo "# Package Information for pkg-config"                        >  $(PKG_CONFIG_FILE)
	@echo "# Author: $(AUTHOR)"                                         >> $(PKG_CONFIG_FILE)
	@echo "# Created: `date`"                                           >> $(PKG_CONFIG_FILE)
	@echo "# Licence: $(LICENSE)"                                       >> $(PKG_CONFIG_FILE)
	@echo                                                               >> $(PKG_CONFIG_FILE)
	@echo prefix=$(PREFIX)                                              >> $(PKG_CONFIG_FILE)
	@echo exec_prefix=$(PREFIX)                                         >> $(PKG_CONFIG_FILE)
	@echo libdir=$(LIB_DIR)                                             >> $(PKG_CONFIG_FILE)
	@echo includedir=$(INCLUDE_DIR)$(PATH_SEP)tango                     >> $(PKG_CONFIG_FILE)
	@echo                                                               >> $(PKG_CONFIG_FILE)
	@echo Name: "$(PROJECT_NAME)"                                       >> $(PKG_CONFIG_FILE)
	@echo Description: "$(DESCRIPTION)"                                 >> $(PKG_CONFIG_FILE)
	@echo Version: "$(PROJECT_VERSION)"                                 >> $(PKG_CONFIG_FILE)
	@echo Libs: $(LINKERFLAG)-l$(LIB_NAME)                              >> $(PKG_CONFIG_FILE)
	@echo Cflags: -I$(INCLUDE_DIR)$(PATH_SEP)$(PROJECT_NAME) $(LDCFLAGS)>> $(PKG_CONFIG_FILE)
	@echo                                                               >> $(PKG_CONFIG_FILE)

pkgfile-static:
	@echo ------------------ Building pkg-config file
	@echo "# Package Information for pkg-config"                        >  $(PKG_CONFIG_FILE)
	@echo "# Author: $(AUTHOR)"                                         >> $(PKG_CONFIG_FILE)
	@echo "# Created: `date`"                                           >> $(PKG_CONFIG_FILE)
	@echo "# Licence: $(LICENSE)"                                       >> $(PKG_CONFIG_FILE)
	@echo                                                               >> $(PKG_CONFIG_FILE)
	@echo prefix=$(PREFIX)                                              >> $(PKG_CONFIG_FILE)
	@echo exec_prefix=$(PREFIX)                                         >> $(PKG_CONFIG_FILE)
	@echo libdir=$(LIB_DIR)                                             >> $(PKG_CONFIG_FILE)
	@echo includedir=$(INCLUDE_DIR)$(PATH_SEP)tango                     >> $(PKG_CONFIG_FILE)
	@echo                                                               >> $(PKG_CONFIG_FILE)
	@echo Name: "$(PROJECT_NAME)"                                       >> $(PKG_CONFIG_FILE)
	@echo Description: "$(DESCRIPTION)"                                 >> $(PKG_CONFIG_FILE)
	@echo Version: "$(PROJECT_VERSION)"                                 >> $(PKG_CONFIG_FILE)
	@echo Libs: $(LIB_DIR)$(PATH_SEP)$(STATIC_LIBNAME)                  >> $(PKG_CONFIG_FILE)
	@echo Cflags: -I$(INCLUDE_DIR)$(PATH_SEP)$(PROJECT_NAME) $(LDCFLAGS)>> $(PKG_CONFIG_FILE)
	@echo

############# MKDIR #############
$(BUILD_PATH):
	test -d $(BUILD_PATH) || $(MKDIR) $(BUILD_PATH)
$(IMPORT_PATH):
	test -d $(IMPORT_PATH) || $(MKDIR) $(IMPORT_PATH)
$(DLIB_PATH):
	test -d $(DLIB_PATH) || $(MKDIR) $(DLIB_PATH)
$(PLAIN_DOC_PATH):
	test -d $(PLAIN_DOC_PATH) || $(MKDIR) $(PLAIN_DOC_PATH)
#~ $(DOC_PATH):
	#~ test -d $(DOC_PATH) || $(MKDIR) $(DOC_PATH)


############# BUILD #############


# For build lib need create object files and after run make-lib
$(STATIC_LIBNAME): $(OBJECTS)
	@echo ------------------ Building static library
	$(make-lib)

# For build shared lib need create shared object files
$(SHARED_LIBNAME): $(PICOBJECTS)
	@echo ------------------ Building shared library
	$(DC) -shared $(SONAME_FLAG) $@.$(MAJOR_VERSION) $(OUTPUT)$@.$(MAJOR_VERSION) $^

# create object files
$(BUILD_PATH)$(PATH_SEP)%.o : %.d .FORCE
	$(DC) $(DCFLAGS) $(DCFLAGS_LINK) $(DCFLAGS_IMPORT) -c $< $(OUTPUT)$@

# create shared object files
$(BUILD_PATH)$(PATH_SEP)%.pic.o : %.d .FORCE
	$(DC) $(DCFLAGS) $(DCFLAGS_LINK) $(FPIC) $(DCFLAGS_IMPORT) -c $< $(OUTPUT)$@

# Generate Header files
$(IMPORT_PATH)$(PATH_SEP)%.di : %.d
	$(DC) $(DCFLAGS) $(DCFLAGS_LINK) $(DCFLAGS_IMPORT) -c $(NO_OBJ) $< $(HF)$@

# Generate Documentation
$(PLAIN_DOC_PATH)$(PATH_SEP)%.html : %.d
	$(DC) $(DCFLAGS) $(DCFLAGS_LINK) $(DCFLAGS_IMPORT) $(DVERSION)=TangoDoc -c $(NO_OBJ)  $< $(DF)$@

# Generate doc Documentation
$(DOC_PATH)$(PATH_SEP)%.html : %.d
	$(DC) $(DCFLAGS) $(DCFLAGS_LINK) $(DCFLAGS_IMPORT) $(DVERSION)=TangoDoc -c $(NO_OBJ) $(DDOC_FLAGS) $< $(DF)$@

############# CLEAN #############
clean: clean-objects clean-static-lib clean-plain-doc clean-header clean-pkgfile
	@echo ------------------ Cleaning $^ done

clean-shared: clean-shared-objects clean-shared-lib
	@echo ------------------ Cleaning $^ done

clean-objects:
	$(RM) $(OBJECTS)
	$(RMDIR) $(BUILD_PATH)
	@echo ------------------ Cleaning objects done

clean-shared-objects:
	$(RM) $(PICOBJECTS)
	$(RMDIR) $(BUILD_PATH)
	@echo ------------------ Cleaning shared-object done

clean-static-lib:
	$(RM) $(STATIC_LIBNAME)
	@echo ------------------ Cleaning static-lib done

clean-shared-lib:
	$(RM)  $(SHARED_LIBNAME).$(VERSION)
	@echo ------------------ Cleaning shared-lib done

clean-header:
	$(RM) $(HEADERS)
	$(RMDIR) $(IMPORT_PATH)
	@echo ------------------ Cleaning header done

clean-plain-doc:
	$(RM) $(PLAIN_DOC_FILES)
	$(RMDIR) $(PLAIN_DOC_PATH)
	@echo ------------------ Cleaning plain-doc done

clean-doc:
	$(RM) $(DOC_PATH)$(PATH_SEP)index.html
	$(RMDIR) $(DOC_PATH)
	@echo ------------------ Cleaning doc done

clean-geany-tag:
	$(RM) geany_config $(PROJECT_NAME).d.tags
	@echo ------------------ Cleaning geany tag done

clean-pkgfile:
	$(RM) $(PKG_CONFIG_FILE)
	@echo ------------------ Cleaning pkgfile done

############# INSTALL #############

install: install-static-lib install-plain-doc install-header install-pkgfile
	@echo ------------------ Installing $^ done

install-shared: install-shared-lib install-plain-doc install-header install-pkgfile
	@echo ------------------ Installing $^ done

install-static-lib:
	$(MKDIR) $(DESTDIR)$(LIB_DIR)
	$(CP) $(STATIC_LIBNAME) $(DESTDIR)$(LIB_DIR)
	@echo ------------------ Installing static-lib done

install-shared-lib:
	$(MKDIR) $(DESTDIR)$(LIB_DIR)
	$(CP) $(SHARED_LIBNAME).$(MAJOR_VERSION) $(DESTDIR)$(LIB_DIR)
	cd $(DESTDIR)$(LIB_DIR)$(PATH_SEP) & $(LN) $(SHARED_LIBNAME).$(MAJOR_VERSION) $(SHARED_LIBNAME).$(VERSION_PROJECT)
	cd $(DESTDIR)$(LIB_DIR)$(PATH_SEP) & $(LN) $(SHARED_LIBNAME).$(VERSION_PROJECT) $(SHARED_LIBNAME)
	@echo ------------------ Installing shared-lib done

install-header:
	$(MKDIR) $(DESTDIR)$(INCLUDE_DIR)
	$(CP) $(IMPORT_PATH)$(PATH_SEP)$(PROJECT_NAME) $(DESTDIR)$(INCLUDE_DIR)
	@echo ------------------ Installing header done

install-modules:
	$(MKDIR) $(DESTDIR)$(INCLUDE_DIR)
	$(CP) tango $(DESTDIR)$(INCLUDE_DIR)
	@echo ------------------ Installing header done

install-plain-doc:
	$(MKDIR) $(DESTDIR)$(DATA_DIR)$(PATH_SEP)doc$(PATH_SEP)$(PROJECT_NAME)$(PATH_SEP)plain_doc$(PATH_SEP)
	$(CP) $(PLAIN_DOC_PATH)$(PATH_SEP)* $(DESTDIR)$(DATA_DIR)$(PATH_SEP)doc$(PATH_SEP)$(PROJECT_NAME)$(PATH_SEP)plain_doc$(PATH_SEP)
	@echo ------------------ Installing doc done

install-doc:
	$(MKDIR) $(DESTDIR)$(DATA_DIR)$(PATH_SEP)doc$(PATH_SEP)$(PROJECT_NAME)$(PATH_SEP)doc$(PATH_SEP)
	$(CP) $(DOC_PATH)$(PATH_SEP)* $(DESTDIR)$(DATA_DIR)$(PATH_SEP)doc$(PATH_SEP)$(PROJECT_NAME)$(PATH_SEP)doc$(PATH_SEP)
	@echo ------------------ Installing doc done

install-geany-tag:
	$(MKDIR) $(DESTDIR)$(DATA_DIR)$(PATH_SEP)geany$(PATH_SEP)tags$(PATH_SEP)
	$(CP) $(PROJECT_NAME).d.tags $(DESTDIR)$(DATA_DIR)$(PATH_SEP)geany$(PATH_SEP)tags$(PATH_SEP)
	@echo ------------------ Installing geany tag done

install-pkgfile:
	$(MKDIR) $(DESTDIR)$(PKGCONFIG_DIR)
	$(CP) $(PKG_CONFIG_FILE) $(DESTDIR)$(PKGCONFIG_DIR)$(PATH_SEP)$(PROJECT_NAME).pc
	@echo ------------------ Installing pkgfile done

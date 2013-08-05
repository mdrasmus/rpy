# Using Rpy with R 2.15.3 (2013-03-01)

Several edits were needed to adapt `rpy-1.0.3` for R version
`2.15.3 (2013-03-01)`.

- The R version regex needs to handle double digit versions
- The dependency on `Rdevices.h` is no longer needed.
  - http://sourceforge.net/p/rpy/bugs/127/
- Link against `lapack` instead of `Rlapack`.
  - http://web.archiveorange.com/archive/v/4Vcxhy67R3zuMKnCkvU3
  - `R CMD config LAPACK_LIBS`

This summarizes the changes:
```diff
diff --git a/rpy_tools.py b/rpy_tools.py
index f312201..534643b 100644
--- a/rpy_tools.py
+++ b/rpy_tools.py
@@ -98,7 +98,7 @@ def get_R_VERSION(RHOME=None, force_exec=False):
       raise RuntimeError("Couldn't execute the R interpreter" +
                          " `%s'.\n" % rexec )
     # edd 05 Apr 2006  version = re.search("R +([0-9]\.[0-9]\.[0-9])", output)
-    version = re.search(" +([0-9]\.[0-9]\.[0-9])", output)
+    version = re.search(" +([0-9]+\.[0-9]+\.[0-9]+)", output)
     if not version:
       raise RuntimeError("Couldn't obtain version number from output\n"
                                "of `R --version'.\n")
diff --git a/setup.py b/setup.py
index cf0fa2e..4a2beb1 100644
--- a/setup.py
+++ b/setup.py
@@ -152,7 +152,7 @@ for RHOME in RHOMES:
     elif sys.platform=='osf1V5':
         include_dirs = [ os.path.join(RHOME.strip(), 'include'), 
                          'src' ]
-        libraries=['R','Rlapack']
+        libraries=['R','lapack']
         library_dirs = r_libs
         runtime_libs = r_libs
         extra_compile_args=["-shared"]
@@ -160,7 +160,7 @@ for RHOME in RHOMES:
     else: # unix-like systems, this is known to work for Linux and Solaris
         include_dirs = [ os.path.join(RHOME.strip(), 'include'), 
                          'src', '/usr/share/R/include' ]
-        libraries=['R','Rlapack']
+        libraries=['R','lapack']
         library_dirs = r_libs
         runtime_libs = r_libs
         extra_compile_args=["-shared"]
diff --git a/src/RPy.h b/src/RPy.h
index 94cf4af..7347437 100644
--- a/src/RPy.h
+++ b/src/RPy.h
@@ -74,7 +74,7 @@
 #  endif
 #endif  /* _WIN32 */
 
-#include <Rdevices.h> /* must follow Graphics.h */
+//#include <Rdevices.h> /* must follow Graphics.h */
 
 
 /* Missing definitions from Rinterface.h or RStartup.h */
```


To install with `pip` the following command can be used to install in a
user defined location:
```
pip install --install-option=--prefix=$HOME/sw .
```

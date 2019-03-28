This repository provides several ways to save the audio of recognitions from
Dragon, which can be used to train new speech models. There are two mechanisms,
you can use either one:

1. Add _natlink_save_audio.py to your MacroSystem directory. Make sure to
   set SAVE_DIR appropriately and manually create that directory. You can
   toggle saving with the included commands; saving is enabled by default.

2. Old method: patch natlink to automatically save all recognized audio.
   This is less flexible but will work with any front end including dragonfly
   and aenea. Look for a file called engine/natlink.py or engine/engine_natlink.py,
   depending on your version of natlink. For me the file is
   C:\Python27\Lib\site-packages\dragonfly\engine\engine_natlink.py. Backup the
   file and apply the following patch inline:
   (IMPORTANT NOTE: you must manually create the directory \recognition!)


diff --git a/engine_natlink--original.py b/engine_natlink.py
index 0d0f623..785f1c6 100644
--- a/engine_natlink--original.py
+++ b/engine_natlink.py
@@ -231,6 +231,18 @@ class GrammarWrapper(object):
         NatlinkEngine._log.debug("Grammar %s: received recognition %r."
                                  % (self.grammar._name, words))
 
+        wav = results.getWave()
+        if(len(wav) > 0 and words != "reject"):
+            import time
+            name = "rec-%.03f" % time.time()
+            print 'Recognition', name, ', wav len =', len(wav), 'words =', words
+            f = open("\\recognition\\" + name + ".wav", "wb")
+            f.write(wav)
+            f.close()
+            f = open("\\recognition\\" + name + ".txt", "wb")
+            f.write(str(words))
+            f.close()
+
         if hasattr(self.grammar, "process_results"):
             if not self.grammar.process_results(words, results):
                 return



Either method saves each phrase in a .wav file named according to the current
time, and also saves the recognition results (list of words) to a corresponding
.txt file. The first method can also optionally save rejected audio sequences
that did not match the grammar.

If you would like to contribute your saved audio or train a custom voice system
using it, please contact me at dwk at voxhub dot io.

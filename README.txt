This is a patch to natlink to save audio/text of recognitions. It will work
with any front end including dragonfly and aenea. It's also possible to write
this code in the process_results function of a specific grammar, but this is
short and simple. It saves each phrase in a .wav file named according to the
current time, and also save the recognition results (list of words) to a
corresponding .txt file.

Look for a file called engine/natlink.py or engine/engine_natlink.py, depending
on your version of natlink. For me the file is
C:\Python27\Lib\site-packages\dragonfly\engine\engine_natlink.py. Backup the
file and then just edit it inline.

IMPORTANT NOTE: you will have to manually create the directory \recognition!


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


# iHarder-FileDrop
It is corrected version of iHarder/FileDrop.

## fixed bug
 * The window(directory in OS) which has dropped files is stucked when filedrop listener is processing.
  > It can be the problem when you need to do some UI task inside listener.fileDropped(File).


 * When you do FileDrop again inside the listener, the program terminated because of the native code error(Minidump error)


## How did I fix it.
 * call listener.fileDropped() in the new Thread.
  > By doing this, you can solve the both of the problem above.

 * Also, it can be guaranteed to finish listener process by `synchronize flag` or `joinListener()`
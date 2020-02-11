# iHarder-FileDrop
It is corrected version of iHarder/FileDrop.

## fixed bug
 * The window(directory in OS) which has dropped files is stucked when filedrop listener is processing.
  > It can be the problem when you need to do some UI task inside listener.fileDropped(File).


 * When you do FileDrop again inside the listener, the program terminated because of the native code error(Minidump error)


## How did I fix it
 * call listener.fileDropped() in the new Thread.
  > By doing this, you can solve the both of the problem above.

 * Also, it can be guaranteed to finish listener process by `synchronize` flag or `joinListenerThread()`


## What is added?

 * Instance Variable
```
    // Thread for the listener processing asynchronously.
    private Thread listenerThread  = null;
```


 * Constuctor with `synchronize` flag
 ```
	/**
	 * Constructor with a default border, synchronize flag and the option to
	 * recursively set drop targets. 
	 * If your synchronize flag is true, it will work as the same with the version 1.1.
	 * It can cause some problem when your listener-work is UI task or time-consuming. 
	 * Set synchronize flag as false.
	 * When : 
	 * * You don't want the window(OS directory) which has dropped files stopped. 
	 * * FileDrop again inside listener function. (it means UI-task)
	 *
	 * @param c           Component on which files will be dropped.
	 * @param recursive   Recursively set children as drop targets.
	 * @param synchronize set whether it works synchronously or not.
	 * @param listener    Listens for <tt>filesDropped</tt>.
	 * @since 1.0
	 */
	public FileDrop(final java.awt.Component c, final boolean recursive, final boolean synchronize, final Listener listener) {
		this(null, // Logging stream
				c, // Drop target
				javax.swing.BorderFactory.createMatteBorder(2, 2, 2, 2, defaultBorderColor), // Drag border
				recursive, // Recursive
				synchronize, // Synchronize
				listener);
	} // end constructor
 ```


* Modified `public void drop( java.awt.dnd.DropTargetDropEvent evt )`
```
// Alert listener to drop.
if (listener != null) 
{
  if(synchronize)
  {
    listener.filesDropped(files);
  }
  else 
  {
    listenerThread = new Thread() 
    {
      public void run() 
      {
        listener.filesDropped(files);
      }
    };
    listenerThread.start();
  }
}
```


* Add `joinListenerThread()`
```
    public void joinListenerThread() throws InterruptedException {
    	if(listenerThread != null) {
    		System.out.println("join");
    		listenerThread.join();
    		listenerThread = null;
    	}
    }
```


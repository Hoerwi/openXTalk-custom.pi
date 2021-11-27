# openXTalk-custom.pi
Make your own Property-Inspector-Function for your Widget

I have been working on my new version of my customRect widget the days.
My goal was to set the label text of the widget on the canvas like in a text field right or left aligned. Also, i wanted to have the ability to set the label to top or bottom as well. 

Preferably via the property inspector, since I don't have to script the position of the label every time.
Unfortunately the default editor com.livecode.pi.textalign which is used for this only allows center, right and left justified.  So every customRect label can only be adjusted via script? 

Nope, is out of question for me lazy dog. I rather build my own .pi -function so that I don't have to set the label by script in the future.
So let's take a closer look at the whole construct.
I have created a small example widget which shows me a label and I can test the function of the alignment.
How the widget is built doesn't matter. It is mainly about how the function of the label-align is built up.

But let's have a look how it looks like in the standard.
With the metadata textalign.editor the entry in the Property-Inspector is displayed with com.livecode.pi.textalign. As you can see only Left - Center - Right is possible. Offsetting to top and bottom is not possible.
If we search now in the program directory of Livecode for "com.livecode.pi" we find some entries under   \Toolset\palettes\inspector\editors   
There you can also find the files for the com.livecode.pi.textalign

This is a script and a Stack file.
If we now look at the stack file we see that this is just the segmented widget in a group "template" that is in the stack. Nothing more. No script, only the appearance defined by the Property-Inspectors. But the function of the Align tool is described in the appropriate script.

What annoys me personally about it is that it, like almost all .pi-functions is hardly or not at all documented or commented.
If you look at it this way, the structure is so simple that you can quickly assemble a new .pi- function yourself.
We use the standard Align tool as a template. But we do not change it, because other functions of the IDE depend on this pi.

Let's first build the appropriate stack.
So open a default stack and drag in the segmented widget.
We make the settings as follow in the propertys of this widget:

Under Basic:

     Number of Segments: 5
     Segment names and labels on left / center / right / top / bottom
     minimum segment widths = 30
     Clicking toggels highlight = true
     Show border = true
     Display style = Icons
     Corner radius = 5

Under Icons we set the Segment Icons and Hilited Segment icons.
Here I chose the Chevron Signs for the direction of the alignment, for Center just the black Circle.

Under Position we set the Width to 150 and Height to 21

At this point, the developers got a little mean and put the Segmentcontrol widget group in a Group "template". Of course we do this as well.
If this is not done, nothing will be displayed in the Property Inspector!

Now we can save the stack with the name custom.pi.btnalign to \Toolset\palettes\inspector\editors
With this we already have the representation of our Property-Inspector entry

Now we only need the appropriate script for it.

We have to write this as a separate script. Writing the whole thing directly into the custom.pi.btnalign stack unfortunately doesn't work.  
But here we get really lazy and copy the content of the file com.livecode.pi.textalign.behavior.livecodescript into a new file and save it under the name custom.pi.btnalign.behavior.livecodescript.
I recommend to do this in a separate editor like Notepad++ or Atom

Now we need to make a few small adjustments in the new script at the following places:

     Line 1: script "custom.pi.btnalign.behavior".
     line 3: set the editorMinWidth from me to 150
     line 4: set the editorMaxWidth from me to 150
     line 28: set the width of widget 1 of me to 150

and save the whole thing to \Toolset\palettes\inspector\editors
Once everything is saved, we restart Livecode or openXTalk.

Now we can prepare and test this in the example widget.
I will now only go into the required entries in the LCB script.

First we have to define a variable for the btnAlign:

     private variable mBtnAlign as String

next we need the appropriate property to display the whole thing in the property inspector:
with the metadata btnalign.editor we call our new .pi.- element we just created 


     property "btnalign" get mAlign set setAlign
          metadata btnalign.editor is "custom.pi.btnalign“
          metadata btnalign.section is "Basic"


What must not be missing, of course, is the handler:


     public handler setBtnAlign(in pBtnAlign as String) returns nothing
          put pBtnAlign into mBtnAlign
          redraw all
     end handler	


But to make it work properly and put the label in the right place of the widget we have to add the following in the handler:


     Public handler onPaint()
     …
          if mBtnAlign is "right" then
               fill text mLabel at right of tRectangle on this canvas
          else if mBtnAlign is "left" then
               fill text mLabel at left of tRectangle on this canvas
          else if mBtnAlign is "top" then
               fill text mLabel at top of tRectangle on this canvas
          else if mBtnAlign is "bottom" then
               fill text mLabel at bottom of tRectangle on this canvas
          else
               fill text mLabel at center of tRectangle on this canvas
          end if
     …
     end handler






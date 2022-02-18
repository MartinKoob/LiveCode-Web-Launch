# LiveCode-Web-Launch
A sample app that will allow a LiveCode Standalone app to be launched using a custom protocol

## Setting up a custom protocol to open an cloud based document in a local application from a browser using a URL

My goal is to have a URL to a document that a user can click and then a local standalone app will open and open the document that the URL points to.  I also want to allow the user to click a document on the local machine and also have it launch the standalone app open the document.


I have only tried this on Mac OS X so far.  I would also want to do this on Windows.

### macOS
On macOS this is done by setting a ‘URL Types’ property in the macOS standalone app’s .plist file.

### How does Zoom do it?
To figure out how this would be done I used Zoom for an example. I know that a url that opens the Zoom app  has **zoommtg** as the custom protocol.

So in the browser there has to be the Protocol: zoommtg://
as well as a path:  my/path/to/data


To test this out for a LiveCode app initially it tried different URIs I might use by adding them to the zoommtg:// protocol.  So I tried some fake URIs to test.

I tried a path that ended with a file name:

zoommtg://my/path/mtg.lwl

And a URI with a Path and a parameter

zoommtg://subdomain.example.com/e5eda8db-6116-4a46-9c5b-28e61429fd54?pwd=wo894ur9jowijhf9iw3h4r9ihfw

I found both of these would launch the Zoom app.

## Creating my own test LC App
Then I made an example app called LC Web Launch and I would use a protocol lcWebLaunch and set a file extension of .lwl in the standalone settings.

I wanted the URI to look something like this.

lcWebLaunch://subdomain.example.com/e5eda8db-6116-4a46-9c5b-28e61429fd54/mydocument.lwl?pwd=woen


So to have that URI launch the **LC Web Launch** standalone app I needed to edit the **info.plist** file of the Contents folder of its **lcWebLaunch.app** file

Standalone/MacOSX/lcWebLaunch.app/Contents/info.plist


### How does Zoom do it?
To to see how I should set it up I looked in the Zoom info.plist for the **zoommtg** string which is the protocol the browser uses to open the Zoom App.

I found it in the key URLTypes
	
Information Property list
	URL Types
		Item 0
			Document Role [Editor]
			URL identifier [com.zoom.video]
			URL schemes
				item 0 [zoommtg]
				item 1 [callto]
				item 2 [tel]

## Adding the protocol to my own test LC App
I then opened the info.plist file of the standalone I created an added the following child to the key **Information Property list**.   

Information Property list
	URL Types
		Item 0
			URL identifier [LCWebLaunch]*
			URL schemes
				item 0 [LCWebLaunch]

I did not have a web url URL yet to put here so I just put **LCWebLaunch** for now.  I just wanted to see if entering the protocol would launch my standalone.

After making the changes to my application I restarted the Mac.   I had read somewhere that the Mac needs to register the App with its protocol.  *
(*I cant find my note with the URL - will find one and add it.)

### Launching the app success!
Once I had the updated .plist file in the app pasting a URI in the browser that started the with the LCWebLaunch:// protocol would launch the LC Web Launch App

## Getting the parameters and URL Path
*THIS IS A WORK IN PROGRESS






### Opening the document on the local machine
*THIS IS A WORK IN PROGRESS

To get the path of a file that was clicked to open an app use appleEvent

[LiveCode Forums :opening app by double-clicking Finder documents](https://forums.livecode.com/viewtopic.php?t=559)

In the above forum post I found the following code to get the file path of the document that is opening in the application by way of double clicking on it.
```
on appleEvent pClass, pID
  if pClass is "aevt" then  -- the 'core' class of apple events`
    if pID is "odoc" then`
      -- the user opened a file from the finder
      -- find out which file, and open it
      request appleEvent data
      put it into tFilePath
      MyOpenFile tFilePath
    else if pID is "pdoc" then
      -- the user printed a file from the finder
      -- find out which file, and print it
      request appleEvent data
      put it into tFilePath
      MyPrintFile tFilePath
    else
      pass appleEvent
    end if
 else
    pass appleEvent
  end if
end appleEvent
```

# reach

**reach** is a Twine story format for authoring immersive XR narratives, environments and prototypes.

**reach** works as a custom story format for [Twine](https://twinery.org), a popular tool for creating interactive stories. The reach story format adds custom codes to Twine that you can use to specify content that will appear in an interactive virtual world. reach also includes a runtime that renders your story as a virtual 3D environment in your browser. The reach runtime is built on top of the [a-frame](https://aframe.io) 3D browser framework. 

## Getting Started

To use reach as the story format for your Twine story you need to import the custom reach format file into Twine. 

1. Open the Twine story selection window.

2. On the right-hand panel click **Formats**

3. Click the **Add a New Format** tab.

4. Enter the following URL to get the latest version of reach, and click **Add**.

   https://cdn.jsdelivr.net/gh/UofTMADLab/reach-dist/reach.js

5. When you are editing a particular story in Twine, tell Twine to run it as a reach 3D story: Click the story name on the lower left of the Twine editor. On the menu that pops up, select **Change Story Format**, and select reach.

#### Advanced: Locking to a particular version of reach

Note: Since reach is still under early, unstable development, changes may be introduced in the future that could change how your story behaves or how your code is parsed. You can lock down to a particular story format version in Twine with the following URL format - notice that it refers to a particular version number, in this case v1.0.61: 

https://cdn.jsdelivr.net/gh/UofTMADLab/reach-dist@1.0.61/reach.js

Valid version numbers are indiciated in this repository as tags, and you can also see the version number if you look at the contents of the *reach.js* file.

Note that when you publish a story from Twine, the reach runtime will be 'baked' into the published HTML file and will not rely on this repository.

### Including images and media in your reach story

If your passages reference media, like images, sound or video, likely you will host those on an external server. The server needs to serve files with [the 'CORS' header set](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS). I have found that using the `assets` container in a basic https://glitch.com project works. And so does a website hosted by [github pages](https://guides.github.com/features/pages/).

### A very, very brief formatting guide for reach.

##### Link Panel

A link panel is a floating flat surface which is visible in the scene. When a user clicks the panel, the default action is to unload the current scene, and then load the scene defined in the Twine passage named by the link. 

So if the link is entered as: `[[GameOver]]` then when the user clicks this link in the scene it will open the Twine passage named "GameOver".

By default the name of the passage that will open is displayed as text overlaid on the floating panel, but you can customize that text to be displayed if you want to (it doesn't have to be the same as the name of the passage target by the link).

* Basic Syntax: `[[Passage Name]]`
* Separating the passage name and the text shown on the screen: `[[Visible Text|PassageName]]`

##### Text Panels

These work like links, except they are not clickable when the story is running in VR. The syntax is similar to the **basic** example above, except you surround the name of the passage with single quotes ( `'` ) .

For example: 

```javascript
[['HereIsMyStory']]
```

 When you enter this in the twine editor, it will create another linked passage. Any text you enter in that passage in the editor will be printed in a floating text panel in the scene passage where the link appears.

##### HTML panels

HTML panels are like Text Panels, in that they create a floating panel with non-interactive text. But with HTML panels, you can use formatting tags, like `<i>`, `<b>` , `<p>` in your text passage.

To create an HTML passage, surround the title of the new passage with `<` and `>` like this:

```javascript
[[<HereIsMyFormattedStory>]]
```

If you enter this in your scene passage, twine will create a new passage named `<HereIsMyFormattedStory>` Any text you enter in this passage will be interpreted as html code and will appear in a floating text panel in your scene.

*Note*: Only "simple" HTML formatting is supported at this time. Links (`<a>` elements) will not be clickable when they are rendered by *reach*.

*Tip*: Text and HTML panels can be referenced by multiple scene passages. So you could create a text panel as a template and then include it in as many scene passages as you like. You can use javascript templating to customize the text of the panel depending on the context where it is used.

##### Default Position of link panels, text panels, and html panels in the scene

Unless you override this with explicit options (as described below), the vr links and vr text panels will be positioned in an analogous way to the relative positions of the passage boxes in the twine editor. You can move text panels left, right, up ,down in the twine editor to move the virtual panels and links left, right, forward, behind the viewer in the resulting vr scene.

##### Image Panels

This type of panel displays a non-interactive 2d image. You can specify the position of this type of panel as follows:

```javascript
[img[http://url_for_image_file]]
```

Note that twine does not create a new passage in the editor when you enter an image panel code like this. All the information necessary to render your image (i.e. the url for the image) is contained within the image panel code itself.

Image panels can also function like link panels, becoming links to other passages. Use the following syntax:

```javascript
[img[http://url_for_image_file]NameOfPassage]
```

In this case if the user clicks the image, the current scene will be unloaded, and the scene passage named 'NameOfPassage' will be loaded. Note that the twine editor will not show a 'connection' line between passages for links like this. Make sure there is a link in your passage with the same name as specified in the image code.

##### 360 background images

Add this to VR scene passage:

* Basic Syntax: `((http://url_for_image_file))`

##### Explicit positioning for link panels, text panels, html panels, image panels and 360 backgrounds

Use the 'direction' option to explicitly declare the position of the panel or background in the scene.

*For example:* To put a link panel to the right in the vr space, regardless of the position of the passage boxes in the twine editor:

```javascript
{"direction": 3}[[NameOfPassage]]
```

This works for other types of passages as well:

```javascript
<% // position a text panel to the left %>
{"direction": 9}[['HereIsMyStory']]  

<% // position an HTML panel behind the user %>
 {"direction": 6}[[<HereIsMyFormattedStory>]]
          
<% // position an Image panel at 11 o'clock %>
{"direction": 11}[img[http://url_for_image_file]]
                      
<% // rotate a background to a particular orientation %>
{"direction": 2}((http://url_for_image_file))
```



*Direction numbers*: Based on 'clock' directions, with 0 (or 12) being forward, 3 is directly to the right, 9 is to the left, 6 behind, and any floating point value in between is valid too.

Multiple options can be combined like a regular json dictionary.

e.g. Set the direction and distance (in meters) of a panel:

```
{"direction": 3, "distance": 6}[[NameOfPassage]]
```

With background color and opacity:

```javas
{"direction": 3, "distance": 6, "backgroundColor":"#00AA00", "backgroundOpacity":0.7}[[NameOfPassage]]
```

Use 'inclination' to raise or lower a panel (or background) so that it is placed above or below the horizon. `"inclination": 0` is straight ahead (level with the horizon). `"inclination": 1` is slightly above the horizon. `"inclination": -1`  is slightly below the horizon. `"inclination": 3` is directly above the user's head. And `"inclination": -3 ` is directly below their feet.

E.g. A text panel that will be slightly to the right and slightly above the user: 

```javascript
{"direction": 2, "inclination": 1}[['LookUpHere']]
```



Note you cannot place a linebreak within an {options}[[link]] statement. It must be all on one line (soft-wrapping in the twine editor is ok).

##### Audio file

Wrap the url in double-tildes. This will start an audio file playing when the user opens the passage:

```javascript
~~https://link_to_mp3_file~~
```

To play a sound from a particular position (e.g. from the left) in vr space:

```javascript
{"direction": 9}~~https://link_to_mp3_file~~
```

Sounds will automatically play after being loaded and will loop by default. If you don't want this, you can override it with the autoplay and loop options:

```javascript
{"autoplay": false, "loop": false}~~https://link_to_mp3_file~~
```

You can use JavaScript to play a sound when a certain event occurs.  (See following sections in this document for more on scripting.)

```javascript
<% // load the sound %>
{"autoplay": false, "loop": false}~~https://link_to_mp3_file~~
<% // load the image; the image will emit the 'PlaySound0' event when it is clicked %> 
{"onClick": "PlaySound0"}[img[http://link_to_image_file]]
                             
<%
// Respond to the PlaySound0 event channel:
p.on("PlaySound0", function(source) {
  // This function is called whenever an object (source) emits a PlaySound event.
  
  p.sounds(0).playSound(); // play the first sound in the scene
});
%>
```



##### Scripting and templating

In scene, text, and HTML passages you can now add javascript. The javascript will run when the passage is opened. It operates the same as the scripting in the *Snowman* story format. Many of the same window.story and window.passage variables as you would find in Snowman are available.

Wrap your sections of script in:

`<% my script code %>`

Use `print()` to print text back into the passage at runtime.

(Or use `<%= ... %>` to output the result of a javascript expression to the passage.)

For example, splitting up an `if` statement to decide which background to use:

```javascript
<% if (window.story.state.lightsAreOn === true) { %>

		((https://example.com/backgroundWithLightsOn.jpg))

<% } else { %>

		((https://example.com/backgroundWithLightsOff.jpg))

<% } %>
```

In a text panel passage, using `%=`:

```php+HTML
Your score is <%= s.currentScore %>. Congratulations! 
```

In a text panel passage, using `print()`:

```php+HTML
Your score is now <% s.currentScore = s.currentScore + 1; print(s.currentScore); %>. Good luck!
```



##### Script Passages

Script templating works well for short snippets of scripting. But often you will want to separate your script from your scene passage, and create re-usable chunks of code. You can create a special type of passage called a 'script passage'. In the twine editor this will look like a regular passage, except the title must be surrounded by `%` symbols. For example: `%MyScriptCode%`. Any text in this passage will be interpreted directly as JavaScript (no need to have the  `<%` and `%>` delimeters surrounding your code). To load the script  into a scene passage and run it, use the following syntax:

```javascript
[[%MyScript%]]
```

You can load the same script passage into multiple different scene passages (and you can load the same script passage multiple times in the same scene!). Your script passages execute after all the static items (e.g. backgrounds, text panels, images etc) in your scene have been loaded. 

Any options you include in the options object just before the script tag are passed on to the JavaScript in the script passage in a local `params` variable.

For example, in a scene passage, to load a script and send it a message value:

```javascript
{"MessageForMyScript": "Hello world!"}[[%MyScript%]]                                      
```

In the script, reference that value this way:

```javascript
// prints "Hello world!" on the browser javascript console
console.log(params.MessageForMyScript);
```

Besides `params`, the script also has access to the variable `p` which is a reference to the current scene passage object, and the variable `s` which you can use to read and write global variables for your story.

The `p` object is especially useful for scripting: it has many functions that you can call to instantiate and reference objects in the scene

```javascript
// create a text panel
p.textPanel("message to show in the text panel", options);

// You would replace 'options' above with a JSON object containing options similar to the ones used above (or omit it if you don't want to specity any options).

// e.g. Display "Hello world!" in a VR text panel behind the user, rather than the Javascript console:
p.textPanel(params.MessageForMyScript, {"direction": 6});

// create an HTML panel
p.htmlPanel("<p>html text</p>", options);

// create a link panel:
p.linkPanel("Text to display", "NameOfPassageToLinkTo", options);

// create an imagePanel:
p.imagePanel("http://url_to_image_file", "NameOfPassageToLinkTo", options);
// if you don't want your image panel to link to anything, use null for the 2nd parameter:
p.imagePanel("http://url_to_image_file", null, options);

// create a 360 background:
p.sky("http://url_to_image_file", options);

// create a 360 video sphere:
p.video("http://link_to_360_video_file.mp4", options);

// create a sound:
p.sound("http://link_to_mp3_file", options);


```



*Manipulating existing objects in the scene with javascript*

You can tag elements in your scene with an 'id' tag. The tag should be letters and numbers only (no spaces), and is case-sensitive.

```javascript
{"id": "MyTextBox1"}[['TextPassageName']]
```

The Javascript in a script passage can find an object by its id by using one of the 'getX' methods on the `p` object.

```javascript
// get a reference to the text passage
var textPanel = p.getTextPanel("MyTextBox1");
// hide the text panel
p.getTextPanel("MyTextBox1").hide();
// show the text panel
p.getTextPanel("MyTextBox1").show();
// get the text in the text panel
p.getTextPanel("MyTextBox1").getOption("text");
// get the direction of the text panel
p.getTextPanel("MyTextBox1").getOptions("direction");
// set the text in the text panel
p.getTextPanel("MyTextBox1").setOption("text", "This is the new text to display.");
// set the direction of the text panel in the scene
p.getTextPanel("MyTextBox1").setOption("direction", 6);

// get an array of all text panels in the scene
p.textPanels(); // returns an array
p.textPanels(2); // returns the second textpanel in the scene

/* there are equivalent functions for other types of objects:
getSky(id)/skies(), getImagePanel(id)/imagePanels(), getSound(id)/sounds(), getHTMLPanel(id)/htmlPanels(), getLinkPanel(id)/linkPanels(), getVideo()/videos()
*/
```

##### Javascript and scene loading order (advanced)

There are several stages during the loading and initialization of scenes. The Javascript in your scene may run at different stages.

1. Executing the inital scene template
   * Any Javascript in `<% ... %>` template tags will load and run before the tags in the passage are processed (e.g. before any text panels, html panels, backgrounds etc are created). This means that functions like `p.getTextPanel(id)` or `p.textPanels()` will not find any text panels in the scene since they have not been created yet.
2. Loading scene objects like text panels and html panels
   * Text panels and HTML panels can also contain template code. This code will execute as these panels are created and loaded into the scene. 
3. Once the scene objects have been loaded, the code in any linked %CodePassages% will execute in the order in which they were defined.
   * You can now use `p.getTextPanel(id)` etc. to find objects you defined in the scene.
4. Content from passages that were 'hoisted' into the current one using the `&[[PassageName]]` syntax will be loaded and mixed into the current scene. 
   * Template and passage scripts in the hoisted (sub) passages will execute in the same order (starting at step 1 for each hoisted passage).

##### Hoisting (mixing) other passages into the current one (advanced)

If you put an `&` before a link code in your scene, it will load all the resources, scripts, and further items in the linked scene into the current one and mix them together, thus allowing sharing and reuse of content between passages:

```javascript
&[[PassageToMixIn]]
```

**Note** that `PassageToMixIn` must be a regular scene passage, not  `'textPanel'`, `<htmlPanel>` , or `%codePanel%` passages. 

You can pass options ('params') to the Javascript template code in the mixed-in passage using an options object:

```javascript
{"ShouldShowBackground": true}&[[PassageToMixIn]]
```

Then `PassageToMixIn` could contain the following code:

```javascript
<% if (params.ShouldShowBackground === true) { %>
  
  ((http://url_for_background_image))
  
<% } %>
```

You can accomplish the same thing using the Javascript passage `load` function:

```javascript
p.load("PassageToMixIn", {"ShouldShowBackground": true});
```

**Note** The difference with the Javascript `load` function is that you also allowed to import `'textPanel'`, `<htmlPanel>` , or `%codePanel%` passages.

##### Linking to a new passage with Javascript

You can use the `window.story.show()` function to unload the current passage and load a new one into the browser view (as if a user clicked a link to a new passage):

```
window.story.show("NameOfPassageToLoad");
```

##### Hiding links in Twine

If you start re-using lots of code passages and hoisted passages in many different places in your story, you'll find that Twine starts to display a nest of link arrows everywhere. You can hide them with  the `;` character:

```javascript
[[LinkToPassage]] <% // shows a link arrow in Twine %>
[;[LinkToPassage]] <% // same, but hides the link arrow in twine %>
  
<% // works for other passages type %>
[;['TextPassage']]
[;[<HTMLPassage>]]
[;[%codePassage%]]
```

This doesn't affect the default relative positioning for things like text panels and links.

##### TODO: documentation for Javascript Events
# How to (ab)use the APL 1.3 Time functionality for non time related purposes


With the introduction of [APL 1.3](https://developer.amazon.com/en-US/docs/alexa/alexa-presentation-language/apl-latest-version.html) we also received a tiny little new feature: a top level so called ["Time" property](https://developer.amazon.com/en-US/docs/alexa/alexa-presentation-language/apl-data-binding-syntax.html#time-functions) now gives us direct access to time and date right within the APL template. 

The obvious use case for this functionality is to make it easy to implement clock functionality into Alexa skills that work right from within the APL. No lambda required. There are quite some examples for clock skills around, with ["Starfield Clock"](https://applicate.de/alexa-skill-starfield-clock/index.html) I built one myself (sorry for self promotion).

## Update dynamic properties at runtime with Time-API

Now it turns out there are some other use cases for this Time feature which are not that obvious: The idea is to **(mis/ab)-use** the dynamic nature of the timer API for simple auto updating animations and content updates that do not require APL command execution. Such animations even proceed to run while Alexa is waiting for user feedback or while there is an ongoing utterance.  

In my own skills I use this technique for quite some time ("[Commander Speedo]"(https://applicate.de/alexa-skill-commander-speedo/index.html)). As I did not find this approach documented anywhere I want to share my experiences with the community. But why does this work? After some evaluation it turns out that actually every APL property which is marked as "dynamic" can be updated via runtime using Time-API. Unfortunately this technique will not work with so called ["styled properties"](https://developer.amazon.com/en-US/docs/alexa/alexa-presentation-language/apl-styled-properties.html) which would be an even greater thing to have.


## Some use cases

This concept gives us a number of options:

- Text can be updated in a loop. I found this useful to **auto update hint texts** every some seconds even if the user does not interact with the skill. This might help him to get an idea how to proceed. 
- Properties like opacity and color can be updated automatically to build **smooth effects**.
- Prepared values from an array can be accessed using Time-results as index. I found this flipbook like approach extremly powerful to **run simple effects**. 
- Using chains of Emojis you can build some nice **self running animations**.
- What is great: the APL "transform" property will evaluate and update the Time values within its settings. This allows **rotation** and **rescaling** running as endless loop in the background. The complete code example shows a "rocket" and a "heartbeat" animation demonstrating what is possible.

But first let us look at a basic example.

## Code example

When working with arrays you might have to do some adjustments. If you want to iterate within a loop over 8 elements while Time.seconds() runs from 0 to 59 or Time.milliseconds() runs from 0 to 9999 there is some work to do not to run out of bounds. 

For example have a look at the following piece of code:

          {
            "type": "Text",
            "id": "moonRotationAnimation",
            "height": "10%",
            "bind": [
              {
                "name": "moonAnimation",
                "value": [
                  "🌑",
                  "🌒",
                  "🌓",
                  "🌔",
                  "🌕",
                  "🌖",
                  "🌗",
                  "🌘"
                ]
              }
            ],
            "fontSize": "50dp",
            "text": "Phases of the moon with Emojis: ${moonAnimation[(Time.milliseconds(localTime/300)) % 8]}"
          },
 
You can see a binding for an array of emoji showing different moon phases characters to the the name "moonAnimation. Within the line "text" there is an expression "${moonAnimation[(Time.milliseconds(localTime/300)) % 8]}" which does all the work. The expression "%8" makes sure that the index does not run out of bounds. The part "localTime/300" triggers the speed of the animation. Careful readers will notice that the animation might not run fluently when the size of the array does not match the numbers of milliseconds in a second. In practice I found this effect barely noticable.


## Where can I see this running?

I put a number of ideas together into one APL to show what I am talking about. You will find a lot of snippets in the code which you might want to use for your own purposes. Feel free to do so! 

The code will execute right within the APL Authoring tool (https://developer.amazon.com/alexa/console/ask/displays#/resource/):

After having called aboce link:
- Use the "Start from Scratch" option
- Select the "{} APL" Buttom from the left hand side
- Copy and Paste the code from github into the editor to see the result


## Finally

I hope you find this useful, have fun with it and make use of the concept within your next skill!


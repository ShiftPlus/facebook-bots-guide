# Facebook Bots and Where to Find Them - A Node.js Guide
Due to everyone asking if there is any guide on how to start making bots, I took it into my own hands to write a (hopefully) concise and easily understood guide.
I am going to try to make this a beginner-friendly guide, so, if you're more advanced, hang with me for the first part of it.

## Setting up
We're going to need to download and install [Node.js][nodejs], this is what will allow us to run JavaScript outside of the browser and give us access to platform specific features (accessing the filesystem, connected devices, etc)
Once **Node.js** is installed we need to get ourselves a text editor (no, Notepad won't cut it).
I strongly suggest [Visual Studio Code][vscode] for all its *features, extensions and themes*, although [Sublime Text][sublime] works very well too.

## Getting started
1) Create a new folder - this is where our bot will live
2) Start **Visual Studio Code** and open our folder (``File > Open Folder``)
3) In that folder we're going to create a new javascript file called `index.js` - this is our bot!
(you can do this by going to ``File > New File`` or by pressing ``CTRL+N`` and then saving it as ``index.js``)

Now that we are set up and ready, here comes the fun part: coming up with the idea for your bot!
Bots come in various forms - `text bots`, `image bots` and `video bots` - for the sake of keeping this guide simple*ish* we are going to create a text bot.
Let's say we want our bot to generate sentences in the format ``<randomword> rhymes with <rhyme>`` and post them to Facebook every hour.

# Installing dependencies

Let's open a terminal inside Visual Studio Code by pressing ``CTRL`` + ``~`` , or, if you're not using VSCode, opening a terminal/cmd, navigating to our folder and typing
```sh
$ npm init -y
```
This command will generate a ``package.json`` file in the folder, this file holds all the information about our program 
*(name, version, description, repository, author, etc)*
If you want, you can go ahead and open this file in VSCode and complete all those fields with whatever is right for your bot. (not required for this example but it's good practice)

Now, for **RhymeBot** (name subject to change), we're going to need to install 3 npm packages that will make our life a lot easier - [bot-util][bot-util-npm] (which will be used for posting to Facebook), [random-words][random-words-npm] which is pretty self explanatory, and [datamuse][datamuse-npm] which we will use for getting rhymes.

So, in order to install these for our bot, we need to go back to that terminal (make sure you navigated to the bot folder) and type

```sh
$ npm install --save bot-util datamuse random-words
```

Now that we have everything ready we can go back to our ``index.js`` and start coding

# Actually coding

Firstly, we need to ``require`` the packages we just installed so that we can use them in our code (think of this as importing them), so, at the start of our script we're going to add

```js
    const bot_util = require('bot-util');
    const datamuse = require('datamuse');
    const random_words = require('random-words');
```

We're now going to declare a function that will generate our sentence.

```js
    function GenerateSentence() {
        //get a random first word by calling the random_words module
        let baseWord = random_words();
        console.log(baseWord);
    }
```
This function will be called and executed everytime the bot needs to post.
Now, if we write
```js
GenerateSentence();
```
**outside of it**, and run our program by going back to the terminal and typing
```sh
$ node index.js
```
we should see a random word logged in the console everytime we run it - but this is not what we want, is it? we need to also find a rhyme for it. Let's fix it!
Go back to our function and add
```js
    function GenerateSentence() {
        //get a random first word by calling the random_words module
        let baseWord = random_words();
        console.log(baseWord);
        //find a rhyme for the word
        //(datamuse has A LOT of other features, check out their site for more info, we're only
        //going to use the rhyme search)
        datamuse.words({
            rel_rhy: baseWord //this is the word that we need rhymes for
        }).then(words => {
            //`words` is an array of words that rhyme with our base word
            console.log(words)
        });
    }
    
    GenerateSentence();
```

now, if we run it again, we should see something like this

![Screenshot](https://i.imgur.com/Jy8bExY.png)

This is still not quite what we're looking for though. We want to only pick *one* of those words and form a sentence.

```js
    function GenerateSentence() {
        //get a random first word by calling the random_words module
        let baseWord = random_words();
        let sentence = baseWord; //initialize our sentence as the base word
        //find a rhyme for the word
        datamuse.words({
            rel_rhy: baseWord
        }).then(words => {
            //pick a random item from the array
            let rhyme = words[Math.floor(Math.random() * words.length)];
            sentence += ` rhymes with ${rhyme.word}`;
            console.log(sentence);
        });
    }
    
    GenerateSentence();
```

if we run it again...

![](https://i.imgur.com/J1n5DAu.png)

Voila!
Now, all that's left is actually posting to Facebook


[datamuse-npm]:<https://www.npmjs.com/package/datamuse>
[random-words-npm]: <https://www.npmjs.com/package/random-words>
[bot-util-npm]: <https://www.npmjs.com/package/bot-util>
[nodejs]: <https://nodejs.org/>
[vscode]: <https://code.visualstudio.com/>
[sublime]: <https://www.sublimetext.com/>

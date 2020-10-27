# Building QR Games

Whenever you want to test or publish your game, you want to build it into a QR code. Building a QR game is super easy!

## Getting the build tools

First step to build your games is obtaining all the necessary build tools. Right now, the only necessary build tool is the [game builder](https://github.com/QRGameStudio/util-games-builder), available from our GitHub.

```bash
# Clone the builder
git clone https://github.com/QRGameStudio/util-games-builder.git

# Install npm dependecies
cd util-games-builder
npm install
```

And you are good to go!

## Building

### From template repository

If you have used one of our templates to create your game, all you need to do is check that your game directory is next to the games builder, so that the directory structure looks like this:

```
.
├── my-game-folder
└── util-games-builder
```

Then, just enter your game folder and run make:

```bash
cd my-game-folder
make
```

### Own template

Building game from your custom template is also easy, but you have to make sure that your Typescript has been compiled into JavaScript beforehand. Then, head to your folder and manually run the build. If your directory structure looks like this

```
.
├── my-game-folder
│   └── game.html
└── util-games-builder
```

Then you have to do following:

```bash
cd my-game-folder
node ../util-games-builder/build-game.js game.html
```

## Checking out the result

### Console result

If your build was successful, the script should print into console something like

```
Url for debugging:
 http://qrpr.eu/html.html#base64

Production url:
 https://qgo.eu/Game/base32
...
Used 45 % of reccomended programm size and 31 % of maximal programm size
```

The printed URLs are functional instances of your game:

- debug URL is shorter and opening it replaces the whole page with your game
- production URL is optimized for scanning QR code and your game is executed sandboxed inside an iframe element

### Dist directory

And  a new directory called `dist` should be created in your game folder:

```
.
├── my-game-folder
│   ├── dist
│   │   ├── aux
│   │   │   ├── game.html.bin  # LZMA 9 compressed HTML
│   │   │   ├── game.html.b32a.txt  # base32 encoded compressed game with adaptive compression enabled
│   │   │   ├── game.html.b32.txt  # base32 encoded compressed game
│   │   │   ├── game.html.b32.url.txt   # production URL
│   │   │   ├── game.html.b64.png  # base64 encoded scannable URL QR code
│   │   │   ├── game.html.b64.svg   # base64 encoded scannable URL QR code
│   │   │   ├── game.html.b64.txt # base64 ecnoded compressed game
│   │   │   ├── game.html.repl.txt  # HTML code processed with the replacement map
│   │   │   └── game.html.url.txt  # debugging URL
│   │   ├── game.html  # Compressed HTLM code of your game
│   │   ├── game.html.manifest.json  # JSON containing information about your game
│   │   ├── game.html.png  # Scannable QR code of your game for production
│   │   └── game.html.svg  # Scannable QR code of your game for production
│   └── game.html
└── util-games-builder
```


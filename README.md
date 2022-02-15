# m3u8-streaming-template
cartelle assenti e generate da cordova:
/plugins/
/platforms/

Installare pacchetti node sia della root, sia nella cartella src-cordova

$ npm install -g cordova # If cordova is not already installed 

$ vue add cordova

$ npm run cordova-prepare # prepare for build (you can run this command, when you checkouted your project from GIT, it's like npm install)

$ npm run cordova-serve-android # Development Android
$ npm run cordova-build-android # Build Android
$ npm run cordova-build-only-www-android # Build only files to src-cordova

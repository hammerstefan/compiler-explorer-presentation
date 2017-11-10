# compiler-explorer-presentation

## PDF
See [compiler-explorer.pdf](compiler-explorer.pdf) for a PDF of the presentation.

## Slides via Reveal.js
Slides are intended to be viewed via reveal.js.  I have packaged reveal.js in a docker container for this use, [docker-reveal.js](https://github.com/hammerstefan/docker-reveal.js).

The easy way to run the server yourself and view these slides:
```
git clone --depth=1 https://github.com/hammerstefan/compiler-explorer-presentation.git
git clone --depth=1 https://github.com/hammerstefan/docker-reveal.js.git
cd docker-reveal.js
HTTP_ROOT=../compiler-explorer-presentation docker-compose up
```
Then view the slides at http://localhost:8001

# image-painter

This package is a Vue component that allow to load an image and edit simple strokes on it

To import the component use:
```
import ImageEditor from 'dom-image-painter/dist/image-painter.common.js'
import 'dom-image-painter/dist/image-painter.css' // even css
```

the component receive one props: image-data-url that is the data-url of the image (for instance what you get with getAsDataUrl() method), and emit on event: new-image that emits a object like {image: 'data-url image edited'}.


## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production (the component only)
```
npm run build:module
```

### Lints and fixes files
```
npm run lint
```

### Run your unit tests
```
npm run test:unit
```

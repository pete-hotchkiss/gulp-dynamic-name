# gulp-dynamic-name

gulp-dynamic-name is a [gulp](https://github.com/wearefractal/gulp) plugin to rename files easily.

<!-- [![NPM](https://nodei.co/npm/gulp-rename.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/gulp-rename/)

[![build status](https://secure.travis-ci.org/hparra/gulp-rename.svg)](http://travis-ci.org/hparra/gulp-rename)
[![devDependency Status](https://david-dm.org/hparra/gulp-rename/dev-status.svg)](https://david-dm.org/hparra/gulp-rename#info=devDependencies) -->

## Usage

gulp-dynamic-name is heavily based on the [gulp-rename](https://github.com/hparra/gulp-rename) package, extending it to allow for dynamic naming of files within a stream using a structured object on the first line of the file. Useful for naming and building files in a build process when combined with the likes of Jade.   

It provides all the features and methods of _gulp-rename_ plus an extended feature set
## Example
Assuming your using Jade to build out a series of templates

```html
//- { "dest_path": "./dist", "dest_name" : "lazy-dog" }
doctype html
  html
    body
      P The Quick Brown Fox Jumped Over The Lazy Dog
```

```javascript
var rename = require("gulp-dynamic-name");
var linereader = require("line-reader");
var intercept = require('gulp-intercept');

// dynamically naming and positioning a jade file
return gulp.src('path/to/jade/files/*.jade')
  .pipe( intercept( function(file){
      //  Read the first line of the Jade file for any local, file level settings
      linereader.eachLine(file.path, function(line, last, cb)
      {
        var ob;
        try {
            ob = JSON.parse( line.substring(4) );
            file.data = {
              'targetpath' : ob.dest_path,
              'filename' : ob.dest_name };
            ps.resume();
        } catch( err ) {
            file.data = {'targetpath' : cpath };
            ps.resume();
        }
      })
      return file;
  }))
  .pipe(jade({
    locals: MERGED,
    pretty: true
  })
  .pipe(rename({
    extname: '.foo',
    useconfig: true
  }))
  .pipe( gulp.dest( function(file, t) {
      return String( file.data.targetpath );
  })) // Builds the Jade files to /dist/lazy-dog.foo
```

## Notes

* `extname` is the file extension including the '.' like path.extname(filename).
* The file bring renamed must contain a structured JSON object on the first line of the file
  * `dest_path` is the path to where the compiled file should be sent
  * `dest_name` the base name the file should be called

## License

[MIT License](http://en.wikipedia.org/wiki/MIT_License)

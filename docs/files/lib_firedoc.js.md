
# firedoc 1.8.7

Fireball is the game engine for the future.


### File: ``

```js

/**
 * This is the __module__ description for the `YUIDoc` module.
 * ```
 * var options = {
 *   paths: [ './lib' ],
 *   outdir: './out'
 * };
 * ```
 *
 * @module firedoc
 * @submodule helpers
 * @submodule utils
 * @main firedoc
 */

const _ = require('underscore');
const path = require('path');
const fs = require('graceful-fs');
const readdirp = require('readdirp');
const debug = require('debug')('firedoc');

/**
 * Firedoc main class
 *
 * @class Firedoc
 * @constructor
 * @param config The config object
 */
function Firedoc (config) {
  if (!(this instanceof Firedoc)) {
    return new Firedoc(config);
  }

  /**
   * Holds the number of files that we are processing.
   * @property filecount
   * @type Boolean
   * @private
   */
  this.filecount = 0;
  /**
   * Holder for the list of files we are processing.
   * @property filemap
   * @type Object
   * @private
   */
  this.filemap = {};
  /**
   * Holder for the list of directories we are processing.
   * @property dirmap
   * @type Object
   * @private
   */
  this.dirmap = {};

  /**
   * Internal holder for configuration options.
   * @property options
   * @type Object
   * @private
   */
  this.options = {
    writeJSON: true,
    extensions: '.js',
    excludes: [
      '.git',
      '.svn',
      'CVS',
      'build_rollup_tmp',
      'build_tmp',
      'node_modules'
    ],
    norecurse: false,
    paths: ['./'],
    cwd: process.cwd(),
    http: false,
    dest: path.join(process.cwd(), 'out'),
    theme: path.join(__dirname, '../themes/default'),
    syntaxtype: 'js'
  };

  // setup options
  var cwd = config.cwd || this.options.cwd;
  var pkgpath = false;
  if (fs.existsSync(cwd + '/package.json')) {
    pkgpath = cwd + '/package.json';
  } else if (fs.existsSync(cwd + '/yuidoc.json')) {
    pkgpath = cwd + '/yuidoc.json';
  }
  if (pkgpath) {
    var pkg = require(pkgpath);

    // load firedoc options
    var firedocOption = pkg.firedoc;
    delete pkg.firedoc;

    //compatible with firedoc.options
    if (pkg.yuidoc && pkg.yuidoc.options) {
      firedocOption = pkg.yuidoc.options;
      delete pkg.yuidoc;
    }
    this.options.project = pkg;
    this.options = _.extend(this.options, firedocOption);

    // compatible with the old options
    this.options.dest = this.options.outdir || this.options.dest;

    // extend by NODE_ENV
    var envOption = pkg['firedoc.' + process.env.NODE_ENV];
    if (envOption) {
      this.options = _.extend(this.options, envOption);
    }
  } else {
    this.options.paths = config.paths || this.options.paths;
  }

  // setup options from config
  for (var key in config) {
    if (!_.isUndefined(config[key])) this.options[key] = config[key];
  }

  // if `markdown` enabled, set theme
  if (this.options.markdown === true) {
    this.options.theme = path.join(__dirname, '../themes/markdown');
  }
  if (!path.isAbsolute(this.options.dest)) {
    this.options.dest = path.join(process.cwd(), this.options.dest);
  }
};
exports.Firedoc = Firedoc;

Firedoc.prototype = {

  /**
   * Walks the paths and parses the directory contents
   *
   * @method walk
   * @param {Function} next
   */
  walk: function (next) {
    var self = this;
    var paths = _.clone(self.options.paths);
    var curr = paths.shift() || './';
    self.walkOne(curr, onfinish);

    function onfinish (err) {
      if (err) throw err;
      curr = paths.shift();
      if (_.isString(curr))
        return self.walkOne(curr, onfinish);
      else
        return next();
    }
  },

  /**
   * Walk one path
   *
   * @method walkOne
   * @param {String} path - The path to walk
   * @param {Function} next
   */
  walkOne: function (root, next) {
    if (!path.isAbsolute(root)) {
      root = path.join(process.cwd(), root);
    }

    var self = this;
    readdirp(
      {
        root: root,
        fileFilter: '*.@(js|rs|ts|coffee)',
        directoryFilter: self.options.excludes.map(
          function (ex) {
            return '!' + ex;
          }
        )
      }
    )
    .on('data', function (entry) {
      var text = fs.readFileSync(entry.fullPath, 'utf8');
      self.filecount += 1;
      self.filemap[entry.fullPath] = text.replace(/\r?\n|\r/g, '\n');
      self.dirmap[entry.fullPath] = entry.fullParentDir;
    })
    .on('end', next)
    .on('error', next);
  },

  lint: function (warnings) {
    var code = 0, count = 0;
    if (warnings && warnings.length) {
      code = 1;
      console.log('FireDoc found', warnings.length, 'lint errors in your docs');
      warnings.forEach(function (item) {
        count++;
        console.log('#' + count, item.message, item.line + '\n');
      });
    }
  },

  /**
   * Process the config, walk the file tree and write out the JSON data.
   * @method build
   * @param {Function} callback
   */
  build: function (callback) {
    debug('Starting from: ' + this.options.path);
    var self = this;
    this.walk(function () {
      var parser = require('./docparser');
      var builder = require('./builder');
      var ast = parser.parse(
        self.options.syntaxtype,
        self.filemap,
        self.dirmap);

      debug('Parsing completed');
      if (self.options.lint) {
        debug('lint the warnings from ast');
        self.lint(ast.warnings);
        if (_.isFunction(callback)) return callback(ast.warnings);
      }
      if (self.options.parseOnly) {
        debug('skip the build because parse only');
        if (_.isFunction(callback)) callback(null, ast, self.options);
        return;
      }
      builder.compile(ast, self.options, callback);
    });
  }
};

```
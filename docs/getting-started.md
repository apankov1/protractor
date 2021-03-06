Getting Started
===============

This guide describes how to get started writing Protractor tests.

WebDriverJS
-----------

When writing tests, it's important to remember that Protractor is a wrapper
around [WebDriverJS](https://code.google.com/p/selenium/wiki/WebDriverJs). 

-  The test code and the browser code are separated, and only communicate
   through the [WebDriver wire protocol](https://code.google.com/p/selenium/wiki/JsonWireProtocol). 
-  WebDriver commands are scheduled on a control flow and return promises. See
   the [control flow doc](docs/control-flow.md) for more info.
-  To run tests, WebDriverJS needs to talk to a selenium standalone server
   running as a separate process.

Setup and Run
-------------

Install Protractor with

    npm install -g protractor

(or omit the -g if you'd prefer not to install globally). 

The example test expects a selenium standalone server to be running at 
localhost:8888. Protractor comes with a script to help download and install
the standalone server. Run

    node_modules/protractor/bin/install_selenium_standalone

This installs selenium standalone server and chromedriver to `./selenium`. Start
the server with

    java -jar selenium/selenium-server-standalone-2.35.0.jar -Dwebdriver.chrome.driver=./selenium/chromedriver

Protractor is now available as a command line program which takes one argument,
a configuration file. 

    protractor example/protractorConf.js

The configuration file tells Protractor what tests to run, how to connect to a
webdriver server, and various other options for reporting. See
[referenceConf.js](https://github.com/angular/protractor/blob/master/referenceConf.js)
for an example and explanation of the options. This simple configuration is

```javascript
// An example configuration file.
exports.config = {
  // The address of a running selenium server.
  seleniumAddress: 'http://localhost:4444/wd/hub',

  // Capabilities to be passed to the webdriver instance.
  capabilities: {
    'browserName': 'chrome'
  },

  // Spec patterns are relative to the current working directly when
  // protractor is called.
  specs: ['example/onProtractorRunner.js'],

  // Options to be passed to Jasmine-node.
  jasmineNodeOpts: {
    showColors: true, // Use colors in the command line report.
  }
};
```

Writing tests
-------------

By default, Protractor uses [Jasmine](http://pivotal.github.io/jasmine/) as its
test framework. The `protractor` variable is exposed globally an can be used
to grab an instance of Protractor (which is called `ptor` in the docs). 

Take this example:

```javascript
describe('angularjs homepage', function() {
  var ptor;

  it('should greet using binding', function() {
    ptor = protractor.getInstance();
    ptor.get('http://www.angularjs.org');

    ptor.findElement(protractor.By.input("yourName")).sendKeys("Julie");

    var greeting = ptor.findElement(protractor.By.binding("yourName!"));

    expect(greeting.getText()).toEqual('Hello Julie!');
  });
```

The instance `ptor` is set up with `protractor.getInstance()`.

The `get` method loads a page. Protractor expects Angular to be present on a
page, so it will throw an error if the page it is attempting to load does
not containt he Angular library. (If you need to interact with a non-Angular
page, you may access the wrapped webdriver instance directly with
`ptor.driver`).

The `findElement` method searches for an element on the page. It requires one
parameter, a strategy for locating the element. Protractor offers Angular
specific strategies:

-  `protractor.By.binding` searches for elements by matching binding names,
   either from `ng-bind` or `{{}}` notation in the template.
-  `protractor.By.input` searches for elements by input `ng-model`.
-  `protractor.By.repeater` seraches for `ng-repeat` elements. For example,
   `protractor.By.repeater('phone in phones').row(12).column('price')` returns
   the element in the 12th row of the `ng-repeat = "phone in phones"` repeater
   with the binding matching `{{phone.price}}`.

You may also use plain old WebDriver strategies such as `protractor.By.id` and
`protractor.By.css`.

Once you have an element, you can interact with it with methods such as
`sendKeys`, `getText`, and `click`. I recommend referencing the
[WebDriverJS code](https://code.google.com/p/selenium/source/browse/javascript/webdriver/webdriver.js)
to find the latest and best documentation on these methods.

See Protractor's [findelements test suite](https://github.com/angular/protractor/blob/master/spec/findelements_spec.js)
for more examples.

Further Reading
---------------

- [WebDriverJS User's Guide](https://code.google.com/p/selenium/wiki/WebDriverJs)
- [WebDriver FAQ](https://code.google.com/p/selenium/wiki/FrequentlyAskedQuestions)
- [w3 WebDriver Working Draft](http://www.w3.org/TR/webdriver/)

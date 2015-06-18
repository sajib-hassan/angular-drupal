# angular-drupal

An Angular module for Drupal Services.

# Intro

This Angular module makes it easy to `read/write` entity data `to/from` Drupal,
as well as handling user authentication and registration.

Here's a very *simple* Angular app that loads node # 123 from Drupal and then
displays the node's title (via an `alert`):

```
// My simple app.
angular.module('myApp', ['angular-drupal']).run(['drupal', function(drupal) {

  drupal.node_load(123).then(function(node) {
    alert(node.title);
  });

}]);

// The angular-drupal configuration settings for my simple app.
angular.module('angular-drupal').config(function($provide) {

  $provide.value('drupalSettings', {
    sitePath: 'http://my-drupal-site.com',
    endpoint: 'my-services-endpoint'
  });

});
```

# Setup and Configuration

As usual, be sure to include the `angular-drupal.js` file in your app. This
typically is included via the `index.html` file somewhere after you include the
`angular.js` file:

```
<script src="angular-drupal.js"></script>
```

The `angular-drupal` module comes with a Service called `drupal`. You can
include this service throughout your app using Angular's dependency injection
mechanism.

The simple app, listed above, injects the `drupal` service into the app's `run`
function. Then when the app runs it loads `node # 123` from Drupal and then
alerts the node's title.

Notice how we used a `config` function on the `angular-drupal` module in the
simple app to provide the URL to our Drupal site, as well as the machine name of
the Services endpoint. Without this, the module won't know how to connect to
our Drupal site, so this must be added to our app as in the example above.

# Usage

## CONNECT
```
drupal.connect().then(function(data) {
  if (data.user.uid) { alert('Hello ' + data.user.name + '!'); }
  else { alert('Please login.');  }
});
```

## USER LOGIN
```
drupal.user_login('bob', 'secret').then(function(data) {
  if (data.user.uid) {
    alert('Hello ' + data.user.name + '!');
  }
});
```

## USER LOGOUT
```
drupal.user_logout().then(function(data) {
  if (!data.user.uid) {
    alert('Logged out!');
  }
});
```

## COMMENT LOAD
```
drupal.comment_load(123).then(function(comment) {        
    alert('Loaded comment: ' + comment.subject);  
});
```

## COMMENT SAVE - NEW
```
var comment = {
  nid: 123,
  subject: 'Hello world',
  comment_body: { und: [ { value: 'How are you?' } ] }
};
drupal.comment_save(comment).then(function(data) {
    alert('Created comment: ' + data.cid);
});
```

## COMMENT SAVE - EXISTING
```
var comment = {
  cid: 456,
  subject: 'Goodbye world',
  comment_body: { und: [ { value: 'I am fine, thank you.' }] }
};
drupal.comment_save(comment).then(function(data) {
    alert('Updated comment: ' + data.cid);
});
```

## COMMENT DELETE
```
drupal.comment_delete(123).then(function(data) {
    if (data[0]) {
      alert('Deleted comment.');
    }
});
```

## COMMENT INDEX
```
var query = {
  parameters: {
    'nid': 123
  }
};
drupal.comment_index(query).then(function(comments) {
    var msg = '';
    for (var i = 0; i < comments.length; i++) {
      var comment = comments[i];
      msg += 'Loaded comment: ' + comment.subject + '\n';
    }
    alert(msg);
});
```

## FILE LOAD
To load a file the user must have the `Get any binary files ` permission in
Drupal.
```
drupal.file_load(123).then(function(file) {
    alert('Loaded file: ' + file.filename);
});
```

## NODE LOAD
```
drupal.node_load(123).then(function(node) {
    alert('Loaded node: ' + node.title);
});
```

## NODE SAVE - NEW
```
var node = {
  type: 'article',
  title: 'Hello world',
  language: 'und',
  body: { und: [ { value: 'How are you?' }] }
};
drupal.node_save(node).then(function(data) {
    alert('Created node: ' + data.nid);
});
```

## NODE SAVE - EXISTING
```
var node = {
  nid: 123,
  title: 'Goodbye world',
  language: 'und',
  body: {
    und: [ { value: 'I am fine, thank you.' }]
  }
};
drupal.node_save(node).then(function(data) {
    alert('Updated node: ' + data.nid);
});
```

## NODE DELETE
```
drupal.node_delete(123).then(function(data) {
    if (data[0]) {
      alert('Deleted node.');
    }
});
```

## NODE INDEX
```
var query = {
  parameters: {
    'type': 'article'
  }
};
drupal.node_index(query).then(function(nodes) {
    var msg = '';
    for (var i = 0; i < nodes.length; i++) {
      var node = nodes[i];
      msg += 'Loaded node: ' + node.title + '\n';
    }
    alert(msg);
});
```

## TAXONOMY TERM LOAD
```
drupal.taxonomy_term_load(123).then(function(term) {
    alert('Loaded term: ' + term.name);
});
```

## TAXONOMY TERM SAVE - NEW
```
https://api.drupal.org/api/drupal/includes!common.inc/constant/SAVED_NEW/7
var taxonomy_term = {
  vid: 1,
  name: 'Hello world'
};
drupal.taxonomy_term_save(taxonomy_term).then(function(data) {
    if (data[0] == 1) { // SAVED_NEW
      alert('Created taxonomy term.');
    }
});
```

## TAXONOMY TERM SAVE - EXISTING
```
// @see https://api.drupal.org/api/drupal/includes!common.inc/constant/SAVED_UPDATED/7
var taxonomy_term = {
  vid: 1,
  tid: 123,
  name: 'Goodbye world'
};
drupal.taxonomy_term_save(taxonomy_term).then(function(data) {
    if (data[0] == 2) { // SAVED_UPDATED
      alert('Updated taxonomy term.');
    }
});
```

## TAXONOMY TERM DELETE
```
// @see https://api.drupal.org/api/drupal/includes!common.inc/constant/SAVED_DELETED/7
drupal.taxonomy_term_delete(123).then(function(data) {
    if (data[0] == 3) { // SAVED_DELETED
      alert('Deleted taxonomy term.');
    }
});
```

## TAXONOMY TERM INDEX
```
var query = {
  parameters: {
    'vid': 1
  }
};
drupal.taxonomy_term_index(query).then(function(taxonomy_terms) {
    var msg = '';
    for (var i = 0; i < taxonomy_terms.length; i++) {
      var taxonomy_term = taxonomy_terms[i];
      msg += 'Loaded taxonomy term: ' + taxonomy_term.name + '\n';
    }
    alert(msg);
});
```

## TAXONOMY VOCABULARY LOAD
```
drupal.taxonomy_vocabulary_load(1).then(function(vocabulary) {
    alert('Loaded vocabulary: ' + vocabulary.name); 
});
```

## USER LOAD
```
drupal.user_load(1).then(function(account) {
    alert('Loaded user: ' + account.name);
});
```

## USER SAVE - NEW
To create a new user account, the user must have the `Administer users`
permission enabled in Drupal.
```
var account = {
  name: 'jane',
  mail: 'jane@example.com',
  pass: 'secret-sauce'
};
drupal.user_save(account).then(function(data) {
  alert('Created new user #' + data.uid);
});
```

## USER SAVE - EXISTING
To update an existing user account, the user must have the `Change own username`
or `Administer users` permission enabled in Drupal.
```
var account = {
  uid: 123,
  name: 'john'
};
drupal.user_save(account).then(function(data) {
  alert('Name changed to: ' + data.name);
});
```

## USER DELETE
The user must have the `Administer users` permission to delete a user account.
```
drupal.user_delete(123).then(function(data) {
    if (data[0]) {
      alert('Deleted user.');
    }
});
```

## USER INDEX
```
var query = {
  parameters: {
    'name': 'bob'
  }
};
drupal.user_index(query).then(function(users) {
    var msg = '';
    for (var i = 0; i < users.length; i++) {
      var user = users[i];
      msg += 'Loaded user: ' + user.name + '\n';
    }
    alert(msg);
});
```

## X-CSRF-Token
The `angular-drupal` module automatically takes care of the `X-CSRF-Token` when
it is needed. If you need to manually get the token it can easily be retrieved:
```
drupal.token().then(function(token) {
  console.log('Got the token: ' + token);
});
```

# DISCLAIMER
I, `Tyler Frankenstein`, admit I am very much a *n00b* when it comes to Angular
JS. The way this module is currently written is by no means the "Angular" way to
do it. Writing this module is my gateway into learning Angular. This module has
unit test coverage to maintain quality. I very much welcome comments, criticisms
and contributions for this project. Thank you!


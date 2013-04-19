knockoutFire
============

KnockoutFire map [Firebase](https://www.firebase.com) json structure into HTML structure using [KnockoutJs](http://knockoutjs.com).
[Live demo at jsFiddle](http://jsfiddle.net/4E8nh/).

Example
-------

### Firebase

    items: {
      -XXX: {
        content: "Hello"
      }
      -YYY: {
        content: "World."
      }
    }

### HTML

    <div id="viewModel">
      <ul data-bind="foreach: items">
        <li data-bind="text: content"></li>
      </ul>
    </div>

### Javascript

    var firebase = new Firebase("https://knockoutFire-README-example.firebaseio-demo.com");
    var viewModel = KnockoutFire.observable(firebase, {
      items: {
        "$item": {
          content: true,
        }
      }
    });
    ko.applyBindings(viewModel, document.getElementById("viewModel"));


## API Reference

### KnockoutFire.observable(firebaseRef, map)

#### The map option

The notation resembles to [Firebase Security Rule](https://www.firebase.com/docs/security/security-rules.html).

##### `Named propertiy`

You need to specify which properties are used as observable properties of view models. KnockoutFire will retrieve only what specified in the map.

- Each properties will be a `ko.observable()` and synchronized with the corresponding value in Firebase.

    person: {
      firstName: true,
      lastName: true
    }

##### `$Variables` and `.reverse`

If you use a property name start with `$`, the parent property will be `ko.observableArray()`.

    users: {
      "$user": {
        nickname: true,
      }
    }

For add/remove/move operations, you should use [Firebase API](https://www.firebase.com/docs/javascript/firebase/index.html) instead of manipulating observable array directly.

    users()[1]().firebase.remove();

If you need reverse order;

    comments: {
      ".reverse": true,
      "$comment": {
        content: true
      }
    }


##### `.indexOf`

To use [Denormalized data](https://www.firebase.com/blog/2013-04-12-denormalizing-is-normal.html) use `.indexOf`.

    members: {
      "$user": {
        ".indexOf": "/users/$user",
        "nickName": true
      }
    }

You can access nickName like this:

    members()[0]().nickName()

##### `.extend`

You can use [Knockout extender](http://knockoutjs.com/documentation/extenders.html).

    ko.extenders.person = function(self, option) {
      self.fullName = ko.computed(function() {
        return this.firstName() + " " + this.lastName();
      });
    };

Then specify the extender by name:

    person: {
      firstName: true,
      lastName: true,
      ".extend": {person: true}
    }

# Query Params and Nested Routes

Let's take an example:

```javascript
// router.js
Router.map(function(){
    this.route('posts', function(){
        this.route('latest'); 
    });
});
```

```javascript
// controllers/posts.js
import Ember from 'ember';
export default Ember.Controller.extend({
    queryParams: ['category']
});
```

The `category` query parameter is defined on the parent route. The most directly way to access the `category` query parameter is by injecting the parent controller onto the nested route's controller.

```
// controllers/posts/latest.js
import Ember from 'ember';
export default Ember.Controller.extend({
    posts: Ember.inject.controller('posts') 
});
```

This will allow you to access posts controller on `posts` property in your template.

```hbs
{{! templates/posts/latest.hbs }}
{{posts.category}}
```

## Route paramsFor method

An alternative way to access the parent's query parameter is via `this.paramsFor`.

```
// routes/posts/latest.js
import Ember from 'ember';
export default Ember.Route.extend({
    model() {
        let { category } = this.paramsFor('posts');
        // do something with category here
    }
});
```

One caveat being that if `category` changes on the controller, the model will not reload unless you specify `refreshModel: true` on parent's Route. 

## refreshModel: true

If the query parameter is defined with `refreshModel: true` flag in the route, the router will refetch the model hooks whenever `category` property is changed on the controller.

```javascript
// routes/posts.js
import Ember from 'ember';
export default Ember.Route.extend({
   queryParams: {
       category: {
           refreshModel: true
       }
   },
   model() {
       // this model hook is triggered everytime that category property changes
   }
});
``` 

```javascript
// routes/posts/latest.js
import Ember from 'ember';
export default Ember.Route.extend({
   model() {
       let { category } = this.paramsFor('posts');
       // this model hook will be reloaded everytime that category changes
   }
});
``` 


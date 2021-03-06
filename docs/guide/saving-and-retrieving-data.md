# Saving and retrieving data

First, let's create a model for our posts. Each post will have a title, body and timestamp. We'll use the timestamp property to display the most recent post first:

```
$ ember generate model post title:string body:string timestamp:number
```

```js
// app/models/post.js
export default DS.Model.extend({
  title: DS.attr('string'),
  body: DS.attr('string'),
  timestamp: DS.attr('number')
});
```

To add blog posts to our app, we'll create a template with a form for submitting blog posts:

```
$ ember generate template posts
```

```handlebars
<!-- app/templates/posts.hbs -->
<h2>New Post</h2>
<ul class="post-publish">
  <li>
    {{input value=title placeholder="Title"}}
  </li>
  <li>
    {{textarea value=body placeholder="Body"}}
  </li>
  <li>
    <button {{action "publishPost"}}>Publish</button>
  </li>
</ul>
```

We haven't written the publishPost action yet, so let's do that now in our PostsController:

```
$ ember generate controller posts
```

```js
// app/controllers/posts.js
export default Ember.ArrayController.extend({
  sortProperties: ['timestamp'],
  sortAscending: false, // sorts post by timestamp
  actions: {
    publishPost: function() {
      var newPost = this.store.createRecord('post', {
        title: this.get('title'),
        body: this.get('body'),
        timestamp: new Date().getTime()
      });
      newPost.save();
    }
  }
});
```

We used an ArrayController to sort our posts by timestamp. In our publishPost action, we create a new post in the data store with the title and body entered in our Handlebars template. Simply calling newPost.save() will save our post to the data store and automatically create a record in the database.

EmberFire uses Firebase's push() function under the hood, which creates a unique timestamp-based ID for each record that is added to the database. Our data now looks like this:

```json
{
  "posts": {
    "-JS4hh97qukW9_JWoPRu": {
      "body": "You can store and sync data in realtime without a backend.",
      "title": "EmberFire is flaming hot!",
      "timestamp": 1425940107418
    }
  }
}
```

Notice that our data is stored under a posts path. EmberFire will automatically try to determine the correct Firebase reference based on the model name. Since we have a Post model, all of our posts our automatically stored under a posts path in the database.

To retrieve the post data from the database, we just need to add a model hook to our posts route:

```
$ ember generate route posts
```

```js
// app/routes/posts.js
export default Ember.Route.extend({
  model: function() {
    return this.store.find('post');
  }
});
```

Now we have access to all of our posts, and can display them in our template:

```js
$ ember generate template posts
```

```handlebars
<!-- app/templates/posts.hbs -->
<section>
{{#each model as |post|}}
  <div>{{post.title}}</div>
  <div>{{post.body}}</div>
{{/each}}
</section>
```

Next: [Querying Data](querying-data.md)

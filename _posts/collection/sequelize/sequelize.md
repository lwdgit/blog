# 'Include' in Sequelize: The One Confusing Query That You Should Memorize
When querying your database in Sequelize, you'll often want data associated with a particular model which isn't in the model's table directly. This data is usually typically associated through join tables (e.g. a 'hasMany' or 'belongsToMany' association), or a foreign key (e.g. a 'hasOne' or 'belongsTo' association).

When you query, you'll receive just the rows you've looked for. With eager loading, you'll also get any associated data. For some reason, I can never remember the proper way to do eager loading when writing my Sequelize queries. I've seen others struggle with the same thing.

Eager loading is confusing because the 'include' that is uses has unfamiliar fields is set in an array rather than just an object.

So let's go through the *one query* that's worth memorizing to handle your eager loading.

#### The Basic Query
Here's how you would find all the rows of a particular model without eager loading.

```js
Albums.findAll()
.then(albums => console.log(albums))
.catch(console.error)
```

#### Vanilla `Include`
Here's how you would find all the Artists associated with your albums.

```js
Albums.findAll({
  include: [{// Notice `include` takes an ARRAY
    model: Artists
  }]
})
.then(albums => console.log(albums))
.catch(console.error)
```

This will take you a long way. Sequelize is smart enough to pull in any rows from your Artists model that are associated with your Albums.

Include takes an array of objects. These objects have properties like `model`, `as`, and `where` which tell Sequelize how to look for associated rows.

#### Customized Include with an Alias
Now, let's customize how we receive our eagerly-loaded rows.

```js
Albums.findAll({
  include: [{
    model: Artists,
    as: 'Singer' // specifies how we want to be able to access our joined rows on the returned data
  }]
})
.then(albums => console.log(albums))
.catch(console.error)
```

In this query, we have specified that the instances we receive back from Sequelize should have a property called 'Singer'. We'll be able to access any rows from our Artists table associated with our Albums through this .Singer property.

#### Customized Include with Alias and `Where`
Finally, let's layer a `where` onto our include, so we can narrow down the rows that we'll receive.

```js
Albums.findAll({
  include: [{
    model: Artists,
    as: 'Singer',
    where: { name: 'Al Green' } //
  }]
})
.then(albums => console.log(albums))
.catch(console.error)
```

Our `where` query should look familiar if you've used Sequelize before: it has the same format as any typical Sequelize query. Our query will now only return joined rows where 'Al Green' is the name of the associated artist. We can access Al Green's artist data for relevant Album instances on our aliased `.Singer` property.

To wrap up, `include` takes an array of objects. These objects are queries of their own, essentially just Sequelize queries within our main query. Inside each include query we specify the associated `model`, narrow our results with `where`, and alias our returned rows with `as`. Memorizing `model`, `where`, `as`, and that include takes an array will make your next experience with eager loading much more pleasant.
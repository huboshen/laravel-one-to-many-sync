# Laravel One To Many Sync
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](https://github.com/huboshen/laravel-one-to-many-sync/blob/master/LICENSE)

Allow sync method for Laravel HasMany and MorphMany Relationship.

## Prerequisite
Laravel 5 or above should be good.

## Installing

```bash
composer require huboshen/laravel-one-to-many-sync
```

After updating composer, add the service provider to the providers array in config/app.php

```php
Huboshen\OneToManySync\OneToManySyncServiceProvider::class,
```
Laravel 5.5 (or above) uses Package Auto-Discovery, so doesn't require you to manually add the ServiceProvider.

## Usage

### Synchronizing with a hasMany relationship in Laravel

When a post has many comments with a hasMany relationship

```php
class Post extends Model
{
    protected $fillable = ['title'];

    public $timestamps = true;

    /**
     * @return \Illuminate\Database\Eloquent\Relations\HasMany
     */
    public function comments()
    {
        return $this->hasMany(Comment::class);
    }
}
```

Synchronize the post with these comments with a "sync_one_to_many"  method

```php
$post->comments()->sync_one_to_many([
    ['id' => 1, 'body' => 'update the comment where id = 1'],
    ['body' => 'this is a new comment without a id yet'],
]);
```

As a result, no matter how much comments the post originally have, the post would have only two comments left:
1. comment with id is 1, with the "body" field updated as above.
2. a **new** comment with "body" field is "this is a new comment without a id".

**Don't want to delete things when synchronizing?**  
No problem. Just pass false as a second parameter to indicate whether enabling the deletion behavior (default is true).

```php
$post->comments()->sync_one_to_many([
    ['id' => 1, 'body' => 'update the comment where id = 1'],
    ['body' => 'this is a new comment without a id yet'],
], false);
```

**Don't want to update the timestamp of the parent model when synchronizing?**  
No problem. Just pass false as a third parameter to indicate whether enabling a parent touch (default is true).

```php
$post->comments()->sync_one_to_many([
    ['id' => 1, 'body' => 'update the comment where id = 1'],
    ['body' => 'this is a new comment without a id yet'],
], true, false);
```

### Synchronizing with a morphMany relationship in Laravel

When a post has many comments with a morphMany relationship

```php
class PostPoly extends Model
{
    protected $table = "posts_poly";
    
    protected $fillable = ['title'];

    /**
     * Get all of the post's comments.
     */
    public function comments()
    {
        return $this->morphMany('App\Models\CommentPoly', 'commentable');
    }
}
```

Synchronize the post with these comments with a "sync_one_to_many_morph" method

```php
$post->comments()->sync_one_to_many_morph(
[
    ['id' => 1, 'body' => 'update the comment where id = 1'],
    ['body' => 'this is a new comment without a id yet'],
]);
```

The result will be similar to the previous hasMany example.  
"sync_one_to_many_morph" method has the second parameter for controlling deletion and the third paramter for controlling parent touch as well.

## License

This project is licensed under the MIT License - see the [LICENSE.md](https://github.com/huboshen/laravel-one-to-many-sync/blob/master/LICENSE) file for details

## Acknowledgments

* This package derives from [alfa6661/laravel-hasmany-sync](https://github.com/alfa6661/laravel-hasmany-sync).

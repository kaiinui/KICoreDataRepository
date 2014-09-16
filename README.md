KICoreDataRepository
====================

Repository pattern for iOS

Background
---

Putting all methods in model class considered harmful.

API
---

```objc
@interface BookRespository : KICoreDataRespository

# pragma mark - KICoreDataRepository Overrides

+ (Class)modelClass {
    return [Book class]; // Assume that `Book` is `NSManagedObject` class.
}

+ (NSString *)primaryKeyKeyPath {
    return @"bookID";
}

# pragma mark - Scope

- (KIScope *)sosekiBooks {
    return [KIScope scopeWithPredicate:[NSPredicateWithFormat:@"title == %@", @"Soseki Natsume"]]
}

@end
```

### Persistence

```objc
Book *book = ...;
BookRepository *repository = [[KIRepositoryLocator locator] bookRepository];

BOOL success = [repository save:book]; // To insert & commit.

BOOL success = [repository insert:book]; // To only insert.
```

### Querying with QueryKit

```objc
QKQuerySet *query = [repository query];
NSArray /* <Book *> */ *books = [[query filter:[NSPredicate predicateWithFormat:@"title == %@", @"Harry Potter"]] array];
```

### Shorthand Queries

```objc
Book *book = [repository find:@"someBookID"]; // To find by bookID.

Book *book = [repository find:@"someTitle" by:@"title"];

NSArray /* <Book *> */ *books = [repository findAll:@"someTitle" by:@"title"];

[repository whereWithDictionary:@{
    @"title": @"Harry Potter",
    @"deleted": @NO,
    @"$ORDER": @{
        @"timestamp": @"ASC"
    }
}];

NSPredicate *predicate = [NSPredicate predicateWithFormat:@"title == %@ AND deleted == %hhd", @"Harry Potter", NO];
[repository whereWithPredicate:predicate];
```

### Methods

- `- insert:`
- `- insertAndCommit:` - insert & commit
- `- remove:`
- `- removeAndCommit:` - remove & commit
- `- commit`
- `- find:`
- `- find:by:`
- `- findAll:by:`
- `- whereWithDictionary:`
- `- whereWithPredicate`
- `- startTransaction`
- `- commitTransaction`

Concerns
---

- Compile time KeyPath checking?
- Scope support
- Default scope support
- Query building
- Paranoid support

Links
---

- QueryKit - https://github.com/QueryKit/QueryKit
- https://github.com/supermarin/ObjectiveRecord

# Relations 关系 {#content-title}

> There are three kinds of relationships. One-to-one relationships enable one object to be associated with another object. One-to-many relationships enable one object to have many related objects. Finally, many-to-many relationships enable complex relationships among many objects.

有三种关系。 一对一关系使一个对象能够与另一个对象相关联。 一对多关系使一个对象与许多对象相关联。 最后，多对多关系使许多对象之间的复杂关系成为可能。

> There are four ways to build relationships in Parse:

在Parse中有四种方式建立关系：

## One-to-Many一对多

> When you’re thinking about one-to-many relationships and whether to implement Pointers or Arrays, there are several factors to consider. First, how many objects are involved in this relationship? If the “many” side of the relationship could contain a very large number \(greater than 100 or so\) of objects, then you have to use Pointers. If the number of objects is small \(fewer than 100 or so\), then Arrays may be more convenient, especially if you typically need to get all of the related objects \(the “many” in the “one-to-many relationship”\) at the same time as the parent object.

当您考虑一对多的关系以及是否实现指针或数组时，有几个要考虑的因素。 首先，这个关系涉及到多少对象？ 如果关系有“很多”，一方可能包含非常大的数目（大于100个）的对象，则必须使用指针。 如果对象数量较少（少于100个），则数组可能更为方便，特别是如果您通常需要获取所有相关对象（“一对多关系”中的“许多”） 同时作为父对象。

##### USING POINTERS 使用指南

> Let’s say we have a game app. The game keeps track of the player’s score and achievements every time she chooses to play. In Parse, we can store this data in a singleGameobject. If the game becomes incredibly successful, each player will store thousands ofGameobjects in the system. For circumstances like this, where the number of relationships can be arbitrarily large, Pointers are the best option.

假设我们有一个游戏应用程序。 游戏记录玩家每次选择玩的成绩和得分。 在Parse中，我们可以将这些数据存储在一个Game对象中。 如果游戏变得非常成功，每个玩家将在系统中存储数千个Game对象。 对于这样的情况，如果关系的数量可以任意大，指针是最好的选择。

> Suppose in this game app, we want to make sure that everyGameobject is associated with a Parse User. We can implement this like so:

假设在这个游戏应用程序中，我们要确保每个Game对象与Parse User相关联。 我们可以这样实现：

> We can obtain all of theGameobjects created by a Parse User with a query:

我们可以获取由Parse User创建的所有Game对象的查询：

> And, if we want to find the Parse User who created a specificGame, that is a lookup on thecreatedBykey:

而且，如果我们想要找到创建特定游戏的Parse User，那就是在createdBy键上查找：

> For most scenarios, Pointers will be your best bet for implementing one-to-many relationships.

对于大多数情况，指针将是实现一对多关系的最佳选择。

##### USING ARRAYS 使用数组

> Arrays are ideal when we know that the number of objects involved in our one-to-many relationship are going to be small. Arrays will also provide some productivity benefit via theincludeKeyparameter. Supplying the parameter will enable you to obtain all of the “many” objects in the “one-to-many” relationship at the same time that you obtain the “one” object. However, the response time will be slower if the number of objects involved in the relationship turns out to be large.

当我们知道我们的一对多关系中涉及到的对象的数量将会很小时，数组是理想的。 数组还将通过includeKey参数提供一些生产力优势。 提供参数将使您能够在获得“一个”对象的同时获取“一对多”关系中的所有“许多”对象。 然而，如果关系中涉及到的对象数量变大，响应时间将会更慢。

> Suppose in our game, we enabled players to keep track of all the weapons their character has accumulated as they play, and there can only be a dozen or so weapons. In this example, we know that the number of weapons is not going to be very large. We also want to enable the player to specify the order in which the weapons will appear on screen. Arrays are ideal here because the size of the array is going to be small and because we also want to preserve the order the user has set each time they play the game:

假设在我们的游戏中，我们让玩家跟踪他们的角色在玩耍时积累的所有武器，而且只能有十几个武器。 在这个例子中，我们知道武器的数量不会很大。 我们也想让玩家指定武器在屏幕上出现的顺序。 数组是理想的，因为数组的大小会变小，因为我们也希望保留用户每次玩游戏时设置的顺序：

> Let’s start by creating a column on our Parse User object calledweaponsList.Now let’s store someWeaponobjects in theweaponsList:

我们首先在我们的Parse User对象上创建一个名为weaponsList的列。现在我们把武器对象存放在武器列表中：

> Later, if we want to retrieve theWeaponobjects, it’s just one line of code:

后来，如果我们要检索武器对象，那只是一行代码：

> Sometimes, we will want to fetch the “many” objects in our one-to-many relationship at the same time as we fetch the “one” object. One trick we could employ is to use theincludeKey\(orincludein Android\) parameter whenever we use a Parse Query to also fetch the array ofWeaponobjects \(stored in theweaponsListcolumn\) along with the Parse User object:

有时，我们将在我们获取“一个”对象的同时，以我们的一对多关系获取“许多”对象。 我们可以采用的一个技巧就是使用includeKey（或者incluede在Android中）参数，无论何时使用Parse Query，还可以读取“数据库”对象（存储在weaponList列中）以及Parse User对象：

> You can also get the “one” side of the one-to-many relationship from the “many” side. For example, if we want to find all Parse User objects who also have a givenWeapon, we can write a constraint for our query like this:

你也可以从“很多”一方获得一对多关系的“一个”一面。 例如，如果我们想要找到所有具有给定武器的解析用户对象，我们可以为我们的查询写一个约束：

```

```

## Many-to-Many多对多

> Now let’s tackle many-to-many relationships. Suppose we had a book reading app and we wanted to modelBookobjects andAuthorobjects. As we know, a given author can write many books, and a given book can have multiple authors. This is a many-to-many relationship scenario where you have to choose between Arrays, Parse Relations, or creating your own Join Table.

现在我们来处理多对多关系。 假设我们有一个书本阅读应用程序，我们想为Book对象和Author对象建模。 我们知道，一个给定的作者可以写很多书，一本书可以有多个作者。 这是一个多对多的关系场景，您必须在数组，解析关系或创建自己的连接表之间进行选择。

> The decision point here is whether you want to attach any metadata to the relationship between two entities. If you don’t, Parse Relation or using Arrays are going to be the easiest alternatives. In general, using arrays will lead to higher performance and require fewer queries. If either side of the many-to-many relationship could lead to an array with more than 100 or so objects, then, for the same reason Pointers were better for one-to-many relationships, Parse Relation or Join Tables will be better alternatives.

这里的决定点是您是否要将任何元数据附加到两个实体之间的关系中。 如果没有，解析关系或使用数组将是最简单的选择。 一般来说，使用阵列将导致更高的性能，并且需要较少的查询。 如果多对多关系的任何一方可能导致一个具有超过100个对象的数组，那么，由于同样的原因指针对于一对多关系更好，解析关系或连接表将是更好的替代。

> On the other hand, if you want to attach metadata to the relationship, then create a separate table \(the “Join Table”\) to house both ends of the relationship. Remember, this is information about the relationship, not about the objects on either side of the relationship. Some examples of metadata you may be interested in, which would necessitate a Join Table approach, include:

另一方面，如果要将元数据附加到关系中，则创建一个单独的表（“连接表”）以容纳关系的两端。 记住，这是关系的信息，而不是关系的任何一方的对象。 您可能感兴趣的元数据的一些示例，这将需要Join Table方法，包括：

##### USING PARSE RELATIONS 使用PARSE关系

> Using Parse Relations, we can create a relationship between aBookand a fewAuthorobjects. In the Data Browser, you can create a column on theBookobject of type relation and name itauthors.

使用解析关系，我们可以创建一本书和几个作者对象之间的关系。 在数据浏览器中，您可以在类型关系的Book对象上创建一个列，并将其命名为作者。

> After that, we can associate a few authors with this book:

之后，我们可以将一些作者与本书联系起来：

> To get the list of authors who wrote a book, create a query:

要获取撰写书籍的作者名单，请创建一个查询：

> Perhaps you even want to get a list of all the books to which an author contributed. You can create a slightly different kind of query to get the inverse of the relationship:

也许你甚至想得到作者贡献的所有书籍的清单。 您可以创建稍微不同的查询类型来获得关系的倒数：

##### USING JOIN TABLES 使用联结表

> There may be certain cases where we want to know more about a relationship. For example, suppose we were modeling a following/follower relationship between users: a given user can follow another user, much as they would in popular social networks. In our app, we not only want to know if User A is following User B, but we also want to know when User A started following User B. This information could not be contained in a Parse Relation. In order to keep track of this data, you must create a separate table in which the relationship is tracked. This table, which we will callFollow, would have afromcolumn and atocolumn, each with a pointer to a Parse User. Alongside the relationship, you can also add a column with aDateobject nameddate.

在某些情况下，我们想要更多地了解关系。 例如，假设我们正在建模用户之间的关注关系：给定的用户可以关注另一个用户，就像在流行的社交网络中一样。 在我们的应用程序中，我们不仅想知道用户A是否关注用户B，而且我们也想知道用户A何时开始关注用户B.此信息不能包含在解析关系中。 为了跟踪这些数据，您必须创建一个单独的表，其中跟踪关系。 这个表，我们将调用Follow，将有一个从一列到一列，每个都有一个指向分析用户的指针。 除了关系之外，您还可以添加一个名为date的Date对象的列。

> Now, when you want to save the following relationship between two users, create a row in theFollowtable, filling in thefrom,to, anddatekeys appropriately:

现在，如果要保存两个用户之间的以下关系，请在“跟踪”表中创建一行，从而适当填写from，to和date键：

> If we want to find all of the people we are following, we can execute a query on theFollowtable:

如果我们想找到我们正在关注的所有人，我们可以在Follow表上执行查询：

> It’s also pretty easy to find all the users that are following the current user by querying on thetokey:

通过查询关键字查找当前用户的所有用户也很容易找到：

##### USING AN ARRAY 使用数组

> Arrays are used in Many-to-Many relationships in much the same way that they are for One-to-Many relationships. All objects on one side of the relationship will have an Array column containing several objects on the other side of the relationship.

数组在多对多关系中使用的方式与用于一对多关系的方式大致相同。 关系一侧的所有对象都将包含一个Array列，该对象在关系的另一边包含多个对象。

> Suppose we have a book reading app withBookandAuthorobjects. TheBookobject will contain an Array ofAuthorobjects \(with a key namedauthors\). Arrays are a great fit for this scenario because it’s highly unlikely that a book will have more than 100 or so authors. We will put the Array in theBookobject for this reason. After all, an author could write more than 100 books.

假设我们有一本书阅读应用程序与Book和Author对象。 Book对象将包含一个作者对象数组（使用一个名为authors的键）。 数组是非常适合这种情况，因为一本书不可能有超过100个作者。 为此，我们将Array放在Book对象中。 毕竟，作者可以写超过100本书。

> Here is how we save a relationship between aBookand anAuthor.

这是我们如何保存书和作者之间的关系。

> Because the author list is an Array, you should use theincludeKey\(orincludeon Android\) parameter when fetching aBookso that Parse returns all the authors when it also returns the book:

因为作者列表是一个数组，所以在获取一本Book时应该使用includeKey（或者包含在Android中）参数，以便Parse在返回该书时返回所有作者：

> At that point, getting all theAuthorobjects in a givenBookis a pretty straightforward call:

在这一点上，获取给定书中的所有作者对象是一个非常简单的调用：

> Finally, suppose you have anAuthorand you want to find all theBookobjects in which she appears. This is also a pretty straightforward query with an associated constraint:

最后，假设你有一个作者，你想找到她出现的所有的Book对象。 这也是一个非常简单的查询与相关的约束：

## One-to-One 一对一

> In Parse, a one-to-one relationship is great for situations where you need to split one object into two objects. These situations should be rare, but two examples include:

在Parse中，一对一的关系对于需要将一个对象分割成两个对象的情况是非常好的。 这些情况应该很少，但有两个例子包括：

* Limiting visibility of some user data. In this scenario, you would split the object in two, where one portion of the object contains data that is visible to other users, while the related object contains data that is private to the original user \(and protected via ACLs\).
* 限制某些用户数据的可见性。 在这种情况下，您将拆分两个对象，其中对象的一部分包含对其他用户可见的数据，而相关对象包含对原始用户是私有的（并通过ACL保护）的数据。
* Splitting up an object for size. In this scenario, your original object is greater than the 128K maximum size permitted for an object, so you decide to create a secondary object to house extra data. It is usually better to design your data model to avoid objects this large, rather than splitting them up. If you can’t avoid doing so, you can also consider storing large data in a Parse File.
* 拆分大小的对象。 在这种情况下，您的原始对象大于对象允许的128K最大大小，因此您决定创建一个辅助对象来存放额外的数据。 通常最好设计你的数据模型，以避免这个庞大的对象，而不是分开它们。 如果您不能避免这样做，您还可以考虑将大数据存储在解析文件中。

  Thank you for reading this far. We apologize for the complexity. Modeling relationships in data is a hard subject, in general. But look on the bright side: it’s still easier than relationships with people.

  谢谢你阅读这么远。 我们为复杂性道歉。 一般来说，数据建模关系是一个难题。 但看看光明的一面：它比人们的关系还要容易些。




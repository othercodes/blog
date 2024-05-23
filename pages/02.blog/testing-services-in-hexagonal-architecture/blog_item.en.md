---
title: 'Testing Services in Hexagonal Architecture'
aura:
    pagetype: article
    description: 'Optimize your service testing in Hexagonal Architecture by leveraging interface mocking and contract validation. Learn how to streamline testing processes without intricate infrastructure setups.'
    metadata:
        keywords: 'testing services in hexagonal architecture, service testing, hexagonal architecture, interface contracts, interface mocking, software development, unit testing, repository mocking, behavioral simulation'
    image: featured.webp
metadata:
    description: 'Optimize your service testing in Hexagonal Architecture by leveraging interface mocking and contract validation. Learn how to streamline testing processes without intricate infrastructure setups.'
    'og:url': 'https://othercode.io/blog/testing-services-in-hexagonal-architecture'
    'og:type': article
    'og:title': 'Testing Services in Hexagonal Architecture | otherCode'
    'og:description': 'Optimize your service testing in Hexagonal Architecture by leveraging interface mocking and contract validation. Learn how to streamline testing processes without intricate infrastructure setups.'
    'og:image': 'https://othercode.io/user/pages/02.blog/testing-services-in-hexagonal-architecture/featured.webp'
    'og:image:type': image/webp
    'og:image:width': 900
    'og:image:height': 300
    'og:author': otherCode
    'twitter:card': summary_large_image
    'twitter:title': 'Testing Services in Hexagonal Architecture | otherCode'
    'twitter:description': 'Optimize your service testing in Hexagonal Architecture by leveraging interface mocking and contract validation. Learn how to streamline testing processes without intricate infrastructure setups.'
    'twitter:site': '@othercode'
    'twitter:creator': '@othercode'
    'twitter:image': 'https://othercode.io/user/pages/02.blog/testing-services-in-hexagonal-architecture/featured.webp'
    'article:published_time': '2024-05-23T10:55:00+10:00'
    'article:modified_time': '2024-05-23T11:00:50+10:00'
    'article:author': otherCode
    keywords: 'testing services in hexagonal architecture, service testing, hexagonal architecture, interface contracts, interface mocking, software development, unit testing, repository mocking, behavioral simulation'
published: false
date: '23-05-2024 10:55'
taxonomy:
    category:
        - blog
    tag:
        - 'software architecture'
        - php
        - 'hexagonal architecture'
        - 'unit test'
media_order: featured.webp
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
---

In software architecture, ensuring effective communication between different components of a system is vital. One effective technique for verifying these connections is through interface mocking. This involves creating mock implementations of interfaces to simulate the behavior of different components. By using these mocks, we can test the services independently, ensuring that the messages they send and receive adhere to a predefined standard, commonly referred to as a "contract". This approach allows us to validate the interactions and functionality of each service without needing the actual implementations or infrastructure.

===

In [Hexagonal Architecture](/blog/hexagonal-architecture), we adhere to a practice of creating a distinct interface or contract for each service that may have one or more implementations. These implementations are then allocated to different layers. For instance, consider a `UserRepository` contract which may have multiple implementations such as `SQLiteUserRepository`, `PostGreSQLUserRepository`, or `JSONFSUserRepository`, depending on the scalability needs of our system. The interface or contract belongs to the domain or application layer, while the actual implementations reside in the infrastructure layer.

By employing interface mocking, we streamline the process of testing the domain and application layers without the complexity of setting up a real infrastructure environment.

Let's say we have the following application service from the previous article [Hexagonal Architecture](/blog/hexagonal-architecture):

```php
final readonly class UserFinder  
{  
    public function __construct(  
        private UserRepository $repository  
    ) {  
    }  
    
    public function byId(int $id): User  
    {  
        if (!$user = $this->repository->find($id)) {  
            throw new UserNotFound("User $id not found");  
        }  
        return $user;  
    }
	
	public function all(): array
	{
		return $this->repository->all();
	} 
}
```

The service above depends on a `UserRepository` contract exposing the following methods:

```php
interface UserRepository  
{  
    public function find(int $id): ?User;

	public function all(): array

    public function save(User $user): User;
}
```

Initially, when testing the `UserFinder` service, one might assume that a functioning database is necessary. This would involve deploying the schema, seeding the database with test data, creating an instance of a `UserRepository` service implementation, and then injecting it into the new `UserFinder` instance for testing.

However, this process can be overly complex for testing just one application service. Fortunately, we can simplify it by conducting interface mocking on the `UserRepository` interface. This approach allows us to test the code in the domain and application layers without the need for any actual infrastructure services.

To ensure proper testing of the domain and application services, we must rigorously validate the API exposed by the contract we are testing. This means thoroughly testing all the inputs and outputs specified by the contract.

Let's explore a quick example using the Pest and Mockery frameworks. First, we need to install both frameworks via composer:

```bash
composer require pestphp/pest --dev --with-all-dependencies
composer require mockery/mockery --dev 
```

Next, we need to initialize Pest:

```bash
vendor/bin/pest --init
```

Now, let's create our first unit test in `tests/Unit/UserFinderTest.php`. The first use case we will test is finding a user by their unique ID, which involves testing the `byId` function. This function calls the `find` method of the `UserRepository` contract. According to the contract, this method can return either a valid instance of `User` (success) or null if a user with the specified ID is not found (fail). With this in mind, let's write the test.

```php
describe('UserFinder', function () {

	test('should find a `User` by give ID.', function () {  
	    $repository = Mockery::mock(UserRepository::class);  
	    $repository->expects('find')  
	        ->with(Mockery::type('int')) 
	        ->once()  
	        ->andReturnUsing(fn(int $id) => new User($id, 'Vincent Vega'));  
	  
	    $service = new UserFinder($repository);  
	  
	    expect($service->byId(42))->toBeInstanceOf(User::class);  
	});

});
```

Let's review what we have done:

1. We created a mock of the `UserRepository` contract.
2. We specified that the mock should expect a call to the `find` method with an `int` argument.
4. We defined that the `find` method must be called exactly once during this test case.
5. We provided a function that will execute when the `find` method is called. This function returns a new instance of `User`, which matches the expected output defined by the contract for a successful case.
6. We instantiated a new `UserFinder`, our subject under test.
7. We executed the `byId` method of `UserFinder` and asserted the response. Since we are testing the successful scenario, the returned value must be an instance of `User`.

Since our test subject is the `UserFinder`, we must assert what this service exposes. By mocking the `UserRepository`, we can control the behavior of `UserFinder` during the test. In this first test, we want the `UserRepository` to always return a `User` instance, simulating a scenario where a matching user record exists in a database. As we can see, we don't need a running database to execute the test; we only need to adhere to the defined contract properly.

We have already tested the success case, so now let's test the failure case. According to the `UserRepository` contract, if a user is not found by the given ID, the return value must be `null`.

```php
describe('UserFinder', function () {  

	//...

    test('should throw `UserNotFound` exception when a `User` is not found by the given ID.', function () {  
		$repository = Mockery::mock(UserRepository::class);  
		$repository->expects('find')  
		    ->with(Mockery::type('int'))  
		    ->once()  
		    ->andReturnUsing(fn(int $id) => null);  
		  
		$service = new UserFinder($repository);  
		$service->byId(42); 
    })->throws(UserNotFound::class, "User 42 not found");

});
```

This new test has some differences; let's review the changes:

1. The return value of the function is now `null`, simulating that the database does not have the requested user record.
2. Instead of directly asserting the return value of the service, we assert that an exception must be raised. Specifically, we expect a `UserNotFound` exception.

This test case verifies the failure scenario of the `UserFinder`. By changing the behavior of the `UserRepository` and adhering to the contract, we can test the entire functionality without any real implementation of a `UserRepository`.

Let's continue adding more tests to our `UserFinder`. Now it's time to cover the `all` method. This method calls the `all` method of the `UserRepository`. According to the contract, this method always returns an array.

```php
describe('UserFinder', function () {

	// ...
  
    test('should return all `User`s', function () {  
		$repository = Mockery::mock(UserRepository::class);  
		$repository->expects('all')  
		    ->withNoArgs()  
		    ->once()  
		    ->andReturnUsing(fn() => []);  
		  
		$service = new UserFinder($repository);  
		  
		expect($service->all())->toBeArray();
    });});
```

In this case, we instruct the mock to:

1. Not expect any arguments for the `all` method.
2. Return an empty array when the `all` method is called.
3. Finally, we assert that the value returned from the `UserFinder` is indeed an array.

At this point, we have thoroughly tested the `UserFinder` service. Next, let's move on to the `UserCreator` service (`tests/Unit/UserCreatorTest.php`), which also depends on the `UserRepository`. For this service, we need to test the `save` method. This method takes a `User` without an ID as an argument and returns a `User` with an ID.

```php
describe('UserCreator', function () {  

    test('should create a new `User` from the given data.', function () {  
        $repository = Mockery::mock(UserRepository::class);  
        $repository->expects('save')  
            ->with(Mockery::type(User::class))  
            ->once()  
            ->andReturnUsing(fn(User $user) => new User(1, $user->name));  
  
        $creator = new UserCreator($repository);  
        $user = $creator->create('Vincent Vega');  
  
        expect($user->name)->toBe('Vincent Vega')  
            ->and($user->id())->toBeGreaterThan(0);  
    });  

});
```

In the code above, we did the following:

1. Created a mock of the `UserRepository` contract.
2. Specified that the mock should expect a call to the `save` method with a `User` argument and that it should return a `User`.
3. Defined that the `save` method must be called exactly once during the test case.
4. Ensured the returned `User` has a valid `int` ID.
5. Instantiated the `UserCreator`.
6. Executed the `save` method and asserted the response. Since we are testing the successful scenario, the returned value must be an instance of `User` with a valid `int` ID.

## Conclusion

Interface mocking in Hexagonal Architecture helps ensure that different parts of your application work together correctly without needing a complex setup. By focusing on the contracts or interfaces, we can test the core logic without setting up real infrastructure like databases.

In this article, we showed how to use interface mocking to check the `UserFinder` and `UserCreator` services. By creating mocks of the `UserRepository`, we controlled how these services behaved during tests. This allowed us to verify that they respond correctly in different situations without needing a real database.


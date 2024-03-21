# valid-env
A decorator enhanced .env syntax for validating your environment variables all within your .env file.

```ruby
@startsWith("https://")
# Must be min 10 for name of domain and domain postfix Ex: (https://d.t)
@min(10)
ALLOWED_ORIGINS=["https://github.com", "https://aidanbleser.com"]
@min(1000)
@public
POLLING_INTERVAL=5000
@min(1024)
@max(49151)
@public
PORT=3000
@min(1)
API_KEY="g74Ed6Z6txrEiGX9rSybQxWfVCFDfvAvhuOBrZvsTjfuGNrNt1jyjHfhQPSdzNh5kf6juBsGfRhjFpyfJEl8L2pw39DCs2A2yJKLfWht6sY7HCalLNpDNWcHbWip8Jpc"
# IP Address regex thanks ChatGPT
@matches("^(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$")
ALLOWED_IPS=["172.16.100.10", "192.168.1.1"]
```

> Note: This is not stable for production use

## Variable Types
Currently valid-env supports 4 different types of environment variables.

- String
- Number
- String[]
- Number[]

For simplicity to the end user all numbers are 64 bit floating point integers.

## Decorator Enhanced
valid-env extends the .env syntax with decorators that allow you to validate and scope your environment variables.

### Decorators
- [@public](#public)
- [@private](#private)
- [@min](#min)
- [@max](#max)
- [@startsWith](#startsWith)
- [@endsWith](#endsWith)
- [@matches](#matches)
- [@doesNotMatch](#doesNotMatch)

### @public
Changes the scope of the environment variable to **public**;

Usage:
```ruby
@public
PORT=3000
```

#### Allowed Variable Types
- String
- Number
- String[]
- Number[]

### @private
This is not necessary as all variables by default are **private** but may be useful to annotate the importance for a variables privacy. 

Usage:
```ruby
@private
SECRET="this is a super private secret"
```

#### Allowed Variable Types
- String
- Number
- String[]
- Number[]

### @min
Allows you to validate the minimum length or size of a variable. For number types it will validate the size of the number. For string types it will validate the length.

Usage:
```ruby
@min(1000)
POLLING_INTERVAL=5000
@min(10)
DOMAIN="https://google.com"
@min(1024)
MICROSERVICE_PORTS=[8080, 8081, 8082]
@min(5)
ADMIN_EMAILS=["aidan@gmail.com", "john@yahoo.com"]
```

#### Allowed Variable Types
- String
- Number
- String[]
- Number[]

### @max
Allows you to validate the maximum length or size of a variable. For number types it will validate the size of the number. For string types it will validate the length.

Usage:
```ruby
@max(45000)
POLLING_INTERVAL=5000
@max(25)
APP_NAME="super-powered-app"
@max(49151)
MICROSERVICE_PORTS=[8080, 8081, 8082]
@max(254)
ADMIN_EMAILS=["aidan@gmail.com", "john@yahoo.com"]
```

#### Allowed Variable Types
- String
- Number
- String[]
- Number[]

### @startsWith
Allows you to validate the start of a string variable.

Usage:
```ruby
@startsWith("https://")
DOMAIN="https://google.com"
@startsWith("https://")
ALLOWED_ORIGINS=["https://google.com", "https://github.com"]
```

#### Allowed Variable Types
- String
- String[]

### @endsWith
Allows you to validate the end of a string variable.

Usage:
```ruby
@endsWith("@gmail.com")
EMAIL="john.doe@gmail.com"
@endsWith("@gmail.com")
ALLOWED_ORIGINS=["john.doe@gmail.com", "jane.doe@gmail.com"]
```

#### Allowed Variable Types
- String
- String[]

### @matches
Enables regex based pattern matching to validate string variables. Will return an error when the pattern doesn't match. 

Usage:
```ruby
# Digits only regex
@matches("^\d+$")
PHONE_NUMBER="4427211223"
@matches("^\d+$")
PHONE_NUMBERS=["4427211223", "4427511227", "4428211213"]
```

#### Allowed Variable Types
- String
- String[]

### @doesNotMatch
Enables regex based pattern matching to validate string variables. Will return an error when the pattern matches.

Usage:
```ruby
# Special characters regex
@doesNotMatch("[^\w.]")
SUPER_USER="admin"
@doesNotMatch("[^\w.]")
ADMIN_USERNAMES=["johnothy", "jimnothy"]
```

#### Allowed Variable Types
- String
- String[]

## Public and Private
Some environment variable handlers allow you to scope your variables to be public or private. (For example [SvelteKit](https://learn.svelte.dev/tutorial/env-static-private)). This allows you to separate privileges to use environment variables between server and client code. By default all variables are scoped as **private** but can be marked public using the `@public` decorator.

> Note: While the `@private` decorator is valid syntax and listed as a decorator it does not change the scope of the variable. However it can be useful for annotating something that should be treated as sensitive and should not be changed to public.
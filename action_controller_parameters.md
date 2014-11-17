# Action Controller Parameters


## First, some definitions
### Mass Assignment

```ruby
def create
    Person.create(params[:person])
end
```

### Not Mass Assignment

```ruby
def update
  person = current_account.people.find(params[:id])
  person.update!(person_params)
  redirect_to person
end

private
def person_params
  params.require(:person).permit(:name, :age)
end
```

## Issues
* Whitelists vs blacklists
* Rails does not make any distinction between query string parameters and POST parameters, and both are available in the params hash in your controller
* Prevent accidentally allowing data to be changed which shouldn’t be exposed (Github 2012)

## Solution -- require whitelisting
With strong parameters, Action Controller parameters are forbidden to be used in Active Model mass assignments until they have been whitelisted.

`id` is permitted by default

```ruby
params.permit(:id)
```


## Requiring certain param keys
Ensures that a parameter is present. If it's present, returns the parameter at the given key, otherwise raises an ActionController::ParameterMissing error.

```ruby
ActionController::Parameters.new(person: { name: 'Francesco' }).require(:person)
=> {"name"=>"Francesco"}
```

```ruby
ActionController::Parameters.new(person: nil).require(:person)
=> ActionController::ParameterMissing: param not found: person
```

```ruby
ActionController::Parameters.new(person: {}).require(:person)
=> ActionController::ParameterMissing: param not found: person
```

## Permitting certain param keys
Sets the permitted attribute to true. This can be used to pass mass assignment. Returns self.

```ruby
params = ActionController::Parameters.new(name: 'Francesco')

params.permitted?  # => false
```

```ruby
Person.new(params) # => ActiveModel::ForbiddenAttributesError
params.permit!

params.permitted?  # => true

Person.new(params) # => #<Person id: nil, name: "Francesco">
```

## Resources
* [Mass Assignment & You](http://code.tutsplus.com/tutorials/mass-assignment-rails-and-you--net-31695)
* [Multiple Models, One Form](http://railscasts.com/episodes/196-nested-model-form-part-1)
 

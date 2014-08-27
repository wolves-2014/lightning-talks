<h1>Action Controller Parameters</h1>

<h2>MASS ASSIGNMENT:</h2>

<p>
def create
    Person.create(params[:person])
end
</p>

<h2>NOT MASS ASSIGNMENT:</h2>

<p>
def update
    person = current_account.people.find(params[:id])
    person.update!(person_params)
    redirect_to person
  end
 
  private
    def person_params
      params.require(:person).permit(:name, :age)
    end
end
</p>

<h2>ISSUES:</h2>
<ul>
<li>Rails does not make any distinction between query string parameters and POST parameters, and both are available in the params hash in your controller</li>
<li>whitelists vs blacklists</li>
<li>Prevent accidentally allowing data to be changed which shouldn’t be exposed (Github 2012)</li>
</ul>

<h2>ASSUMED</h2>
<p>
params.permit(:id)
</p>

<h2>SOLUTION</h2>
<p>
With strong parameters, Action Controller parameters are forbidden to be used in Active Model mass assignments until they have been whitelisted.
</p>

<h2>REQUIRE</h2>
<p>
Ensures that a parameter is present. If it's present, returns the parameter at the given key, otherwise raises an ActionController::ParameterMissing error.
</p>

<p>
ActionController::Parameters.new(person: { name: 'Francesco' }).require(:person)
# => {"name"=>"Francesco"}

ActionController::Parameters.new(person: nil).require(:person)
# => ActionController::ParameterMissing: param not found: person

ActionController::Parameters.new(person: {}).require(:person)
# => ActionController::ParameterMissing: param not found: person
</p>

<h2>PERMIT</h2>
<p>
Sets the permitted attribute to true. This can be used to pass mass assignment. Returns self.

class Person < ActiveRecord::Base
end

params = ActionController::Parameters.new(name: 'Francesco')
params.permitted?  # => false
Person.new(params) # => ActiveModel::ForbiddenAttributesError
params.permit!
params.permitted?  # => true
Person.new(params) # => #<Person id: nil, name: "Francesco">
</p>

<h2>SOURCES</h2>
<p>
great introduction
http://code.tutsplus.com/tutorials/mass-assignment-rails-and-you--net-31695

multiple models one form
http://railscasts.com/episodes/196-nested-model-form-part-1
</p>
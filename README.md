# String Calculator

This is a Ruby on Rails application that implements a String Calculator using Test-Driven Development (TDD). The application uses PostgreSQL as the database and RSpec for testing.

## Requirements

- Ruby
- Rails
- PostgreSQL

## Setup

### Install Ruby and Rails

If you don't have Ruby and Rails installed, you can follow these steps:

1. Install Ruby using a version manager like [rbenv](https://github.com/rbenv/rbenv) or [rvm](https://rvm.io/).

   ```sh
   rbenv install 3.0.0
   rbenv global 3.0.0
   ```

2. Install Rails:

   ```sh
   gem install rails
   ```

### Create and Setup the Rails Application

1. Create a new Rails application with PostgreSQL as the database:

   ```sh
   rails new string_calculator --database=postgresql
   cd string_calculator
   ```

2. Configure the database settings in `config/database.yml`:

   ```yaml
   default: &default
     adapter: postgresql
     pool: 5
     timeout: 5000

   development:
     <<: *default
     database: string_calculator_development

   test:
     <<: *default
     database: string_calculator_test

   production:
     <<: *default
     database: string_calculator_production
   ```

3. Create and migrate the database:

   ```sh
   rails db:create
   rails db:migrate
   ```

### Implement the String Calculator

1. Generate a Calculator model:

   ```sh
   rails generate model Calculator
   ```

2. Add the `add` method to the Calculator model in `app/models/calculator.rb`:

   ```ruby
   class Calculator < ApplicationRecord
     def add(numbers)
       return 0 if numbers.empty?

       delimiter = ","
       if numbers.start_with?("//")
         parts = numbers.split("
", 2)
         delimiter = parts.first[2..-1]
         numbers = parts.last
       end

       numbers = numbers.gsub("\n", delimiter)
       nums = numbers.split(delimiter).map(&:to_i)

       negatives = nums.select { |n| n < 0 }
       raise "negative numbers not allowed: #{negatives.join(', ')}" if negatives.any?

       nums.sum
     end
   end
   ```

### Add Tests with RSpec

1. Add RSpec to your Gemfile:

   ```ruby
   group :development, :test do
     gem 'rspec-rails'
   end
   ```

2. Install RSpec and generate the necessary files:

   ```sh
   bundle install
   rails generate rspec:install
   ```

3. Create the test file for the Calculator model:

   ```sh
   mkdir -p spec/models
   touch spec/models/calculator_spec.rb
   ```

4. Add the following tests to `spec/models/calculator_spec.rb`:

   ```ruby
   require 'rails_helper'

   RSpec.describe Calculator, type: :model do
     let(:calculator) { Calculator.new }

     it "returns 0 for an empty string" do
       expect(calculator.add("")).to eq(0)
     end

     it "returns the number for a single number" do
       expect(calculator.add("1")).to eq(1)
     end

     it "returns the sum of two numbers" do
       expect(calculator.add("1,5")). to eq(6)
     end

     it "returns the sum of multiple numbers" do
       expect(calculator.add("1,2,3")). to eq(6)
     end

     it "handles new lines between numbers" do
       expect(calculator.add("1\n2,3")). to eq(6)
     end

     it "supports different delimiters" do
       expect(calculator.add("//;\n1;2")). to eq(3)
     end

     it "raises an exception for negative numbers" do
       expect { calculator.add("1,-2") }.to raise_error("negative numbers not allowed: -2")
     end

     it "raises an exception for multiple negative numbers" do
       expect { calculator.add("1,-2,-3") }.to raise_error("negative numbers not allowed: -2, -3")
     end
   end
   ```

### Run the Tests

Run the RSpec tests to ensure everything is working correctly:

```sh
bundle exec rspec
```

## Contributing

1. Fork the repository.
2. Create a new branch (`git checkout -b feature-branch`).
3. Commit your changes (`git commit -am 'Add new feature'`).
4. Push to the branch (`git push origin feature-branch`).
5. Create a new Pull Request.

## License

This project is licensed under the MIT License.

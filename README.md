# code_snippets
Code Snippets of different projects I helped develop

## ATM
Below is a snippet from the first week of my course where we created a virtual ATM run in IRB. The following is the ATM class which handles the extraction of money, denominations and different scenarios.  

```ruby
require 'date'
class Atm
  attr_accessor :funds

  def initialize
    @funds = 1000
  end

  def withdraw(amount, pin_code, account)
    case
      when insufficient_funds_in_account?(amount, account)
        { status: false, message: 'insufficient funds in account', date: Date.today }
      when insufficient_funds_in_atm?(amount)
        { status: false, message: 'insufficient funds in ATM', date: Date.today }
      when incorrect_pin?(pin_code, account.pin_code)
        { status: false, message: 'wrong pin', date: Date.today }
      when card_expired?(account.exp_date)
        { status: false, message: 'card expired', date: Date.today }
      else
        perform_transaction(amount, account)
    end
  end

  private

    def add_bills(amount)
      denominations = [20, 10, 5]
      bills = []
      denominations.each do |bill|
        while amount - bill >= 0
          amount -= bill
          bills << bill
        end
      end
      bills
    end

    def card_expired?(exp_date)
      Date.strptime(exp_date, '%m/%y') < Date.today
    end

    def incorrect_pin?(pin_code, actual_pin)
      pin_code != actual_pin
    end

    def insufficient_funds_in_account?(amount, account)
      amount > account.balance
    end

    def insufficient_funds_in_atm?(amount)
      @funds < amount
    end

    def perform_transaction(amount, account)
      @funds -= amount
      account.balance = account.balance - amount
      { status: true, message: 'success', date: Date.today, amount: amount, bills: add_bills(amount) }
    end

  end

```


## Cooper client
Below is a snippet where I developed the client application in Ionic.  This was developed on week 7 of the course.  

```typescript

import { Component } from '@angular/core';
import { NavController, ModalController} from 'ionic-angular';

import { PersonProvider } from '../../providers/person/person';
import { PerformanceDataProvider } from '../../providers/performance-data/performance-data';
import { ResultsPage } from '../results/results'


@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  user: any = {};

  constructor(public navCtrl: NavController,
              public person: PersonProvider,
              public modalCtrl: ModalController,
              public performanceData: PerformanceDataProvider) {
    this.user = {distance: 1000, age: 20, gender: 'female' };
  }

  calculate() {
    this.person.age = this.user.age;
    this.person.gender = this.user.gender;

    this.person.doAssessment(this.user.distance);
    console.log(this.person.assessmentMessage);
  }

  showResults() {
    this.modalCtrl.create(ResultsPage).present();
  }
  
  saveResults(){
    this.performanceData
      .saveData({ performance_data: { data: { message: this.person.assessmentMessage } } })
      .subscribe(data => console.log(data));
  }
}

```

## Newsroom App

Below is an acceptance test wrriten in gherkin and used with the cucumber gem from the current project we are working on.  

```gherkin

Feature: Logged in user can access article show view
  As a visitor
  In order to see full content of articles
  I should be able to create an account

Background:
  Given we have the following articles
    | headline                  | content     | approval |
    | The awesome article       | Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. | true |
    | Another awesome articles  | Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum. | true |

Scenario: A logged i user can access article show view
  Given user is signed in
  And I am on the landing page
  When I click "The awesome article"
  And I should see "The awesome article"
  And I should see "Lorem ipsum dolor sit amet, consectetur adipisicing elit"
  And I should not see "Another awesome articles"
  And I should not see "Excepteur sint occaecat cupidatat non proident"

Scenario: A user not logged in cannot access article show view
  Given I am on the landing page
  When I click "The awesome article"
  And I should not see "Lorem ipsum dolor sit amet, consectetur adipisicing elit"
  And I should be redirected to login page
  And I should see "You need to sign in or sign up before continuing"

```

The following code snippets are the step definitions that we used to innteract with the application.  We made the test pass using the devise gem.  

```ruby

Given("I am on the landing page") do
  visit root_path
end

Given("we have the following articles") do |table|
  table.hashes.each do |article|
    category = article["category"]
    article["category"] = Category.find_by(name: category) if category != nil
    create(:article, article)
  end
end

When("I click {string}") do |link|
  click_link_or_button link
end

When("I fill in {string} with {string}") do |field, text|
  fill_in field, with: text
end

Given("user is signed in") do
  user = create(:user)
  login_as user
end

Given("I am on the {string} page") do |article_title|
  article = Article.find_by(headline: article_title)
  visit article_path(article)
end

Given("the following users exist") do |table|
  table.hashes.each do |user|
    create(:user, user)
  end
end

Given("I am signed in as {string}") do |user_email|
  user = User.find_by(email: user_email)
  login_as user
end

```

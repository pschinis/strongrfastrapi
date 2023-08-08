---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - Need help? <a href="mailto:philip@strongrfastr.com">Contact us</a>

search: true

code_clipboard: true

meta:
  - name: Strongr Fastr for Professionals API
    content: Documentation for the Strongr Fastr for Professionals API
---


# Introduction

The Strongr Fastr for Professionals API allows enterprise users to programmatically create new clients in Strongr Fastr. Currently, client creation is the only publicly available endpoint. If your business needs something more please contact [philip@strongrfastr.com](mailto:philip@strongrfastr.com). The API is a work in progress and we're happy to help.

# Authentication

Authenticate each request by setting the Authorization header of the request to your API key as follows:

`Authorization: YOUR_API_KEY`

If you are the primary owner of the account and your account has been activated for API access your API Key should be visible within the app under Menu > Account. To get activated for API access contact [philip@strongrfastr.com](mailto:philip@strongrfastr.com).

# Content Type

Requests to the API should have a JSON body and content-type (‘application/json’). All responses from the API will also have a JSON body and content-type.

`Content-Type: application/json`

# Clients

## Create a Client

> Minimal create client request if delivering via PDF:

```json
{  
    "user": {  
        "first_name": "John",  
        "last_name": "Smith"
    }  
}
```

> Minimal create & invite a client if delivering via app/web:

```json
{  
    "type": "invite",
    "user": {  
        "first_name": "John",  
        "last_name": "Smith",
        "email": "johnsmith@example.com"
  }  
}
```

> Create & invite client and have them set up their own profile, meal plan, and workouts:

```json
{  
    "type": "invite",
    "user": {  
        "first_name": "John",  
        "last_name": "Smith",
        "email": "johnsmith@example.com",
        "setup_own_profile": true,
        "setup_own_mp_prefs": true,
        "pick_own_routine": true
  }  
}
```

> Create client with basic profile:

```json
{
   "type": "invite",
   "user": {
       "first_name": "Jane",
       "last_name": "Smith",
       "email": "johnsmith@example.com",
       "gender": 1,
       "age": 32,
       "foot_height": 5,
       "inch_height": 6,
       "current_weight": 149,
       "weight_goal": -1,
       "activity_level": 2
   }
}
```

> Create client with basic profile & meal plan profile & generate first meal mlan immediately:

```json
{
   "type": "create",
   "generate_meal_plan": true,
   "user": {
       "first_name": "Jane",
       "last_name": "Smith",
       "gender": 1,
       "age": 32,
       "foot_height": 5,
       "inch_height": 6,
       "current_weight": 149,
       "weight_goal": -1,
       "activity_level": 2,
       "meal_plan_weekday": 2,
       "diet_type": 1,
       "budget": 3,
       "weekly_variety": 3,
       "complexity_preference": 3,
       "selected_meal_types": ["breakfast","lunch","dinner"]
   }
}
```

> Create client with specific macro goals & food allergies:

```json
{
   "user": {
       "first_name": "Aaardvark",
       "last_name": "Tom",
       "gender": 1,
       "age": 32,
       "foot_height": 5,
       "inch_height": 6,
       "current_weight": 149,
       "weight_goal": -1,
       "activity_level": 2,
       "meal_plan_weekday": 2,
       "diet_type": 1,
       "budget": 3,
       "weekly_variety": 3,
       "complexity_preference": 3,
       "selected_meal_types": ["breakfast","lunch","dinner"],
       "excluded_keywords": "dairy1,soy1,gluten1",
       "extra_keywords": "brussel sprouts,lima beans",
       "override_calories": true,
       "calorie_override": 2200,
       "fiber_goal": 25,
       "macro_parameters": [
           { "macro": "protein", "amount": 180, "unit_type": 0, "compare_type": "at_least" },
           { "macro": "carbs", "amount": 20, "unit_type": 1, "compare_type": "at_most" }
       ]
   }
}
```

> This will create a lactose intolerant client (dairy1) who is also allergic to soy and gluten (soy1,gluten1) and does not want brussel sprouts or lima beans in their meal plan. They have a daily calorie goal of 2200 calories, a daily fiber goal of 25 grams, a daily protein goal of at least 180g, and a daily carbs goal of 20% or less.


This endpoint creates a new client.


### HTTP Request

`POST https://www.strongrfastr.com/en/api/v1/trainers/create_client`

### Request Body

-   **user** *object* (required) - the [user object](#user-object) with details (e.g. name, age, height, weight, etc) for the client being created
-   **trainer_identifier** *string* (optional) - the id or email address of the trainer/team member to whom this client should be assigned. If left blank, the client will be assigned to the account owner
-   **type** *string* (optional) - if specified must be one of:
	-   invite (if delivering plans via the app/website an invite will get sent to the client’s email address)
	-   create (default - creates the client without sending an invite)
-   **tags** *array of string* (optional) - an array of any tags (arbitrary strings for grouping clients) to be applied to the client. Can also be passed as a comma-delimited string.
-   **generate_meal_plan** *boolean* (optional) - if set to true, the client’s first meal plan will automatically be generated after the client is created. This will fail unless the meal_plan_weekday, diet_type, budget, weekly_variety, complexity_preference, and selected_meal_types fields are all set in the [user object](#user-object).
-   **suppress_notification_email** *boolean* (optional) - by default an email will be sent to the new client’s trainer after the client is created. Setting this flag to true prevents that email from being sent.
-   **delete_and_replace** *boolean* (optional) - by default if you already have a client with the same email address in our system, the call will error out and leave the existing client in place. If you set this flag to true, then the call will succeed and the pre-existing client will be deleted.

### Response

If the request succeeds the response will be an object with a single key/value pair:
- **client_id** *integer* - the id of the newly created client 

If the request fails, the response will be an object with an error message:
- **error** *string* - the error message


### <a id="user-object"></a>User Object

#### Basic Fields
When creating a client, if you provide a first_name, last_name, gender, age, height (foot_height/inch_height or metric_height), weight (current_weight or metric_weight), weight_goal, and activity_level, then the manual “Set Up Client” step can be skipped within the app.

-   **first_name** *string* (required) - the client’s first name
-   **last_name** *string* (required) - the client’s last name
-   **email** *string* (optional if you’re delivering plans via “pdf”, required otherwise) - client’s email address
-   **setup_own_profile** *boolean* (optional) - set to true to have the client fill in their own gender, weight goal, age, height, weight, and activity level when they accept their invite
-   **gender** *integer* (optional) - 0 for male, 1 for female
-   **age** *integer* (optional) - the client’s age as an integer, must be >= 18 and <= 99
-   **foot_height** *integer*, **inch_height** *number* (optional) - the client’s height in feet and inches. inch_height should be blank or a number >= 0 and < 12. foot_height must be an integer. Total height must be between 2’6” and 8’
-   **metric_height** *number* (optional) - the client’s height in centimeters. This field is ignored if foot_height or inch_height are present. Must be between 77 and 243 cm.
-   **current_weight** *number* (optional) - the client’s current weight in lbs. Must be >= 60 and <= 800
-   **metric_weight** *number* (optional) - the client’s weight in kgs. Must be between 28 and 363 kgs. This field is ignored if current_weight is present
-   **weight_goal** *integer* (optional) - the client’s weight goal. Must be either -1 (lose weight), 0 (maintain/tone), or 1 (gain weight).
-   **activity_level** *integer* (optional) - must be one of:
    -   0 (for sedentary)
    -   1 (for lightly active)
    -   2 (for moderately active)
    -   3 (for heavily active)
    -   4 (for extremely active)

#### Meal Plan Fields
<aside class="notice">
If any of these fields are provided then the basic fields are required.
</aside>
If you provide meal_plan_weekday, diet_type, budget, weekly_variety, complexity_preference, and selected_meal_types then the manual meal plan settings setup step for the client can be skipped within the app.

-   **setup_own_mp_prefs** *boolean* (optional) - set to true to have the client fill in their own meal plan profile after they accept their invite. Does nothing if you’re delivering plans via PDF.

-   **meal_plan_weekday** *integer* (optional) - indicates which weekday should be the first day of the client’s meal plan each week. <span style="color:red">**If this field is included then diet_type, budget, weekly_variety, complexity_preference, and selected_meal_types are all REQUIRED**</span>. Must be one of:
	-   0 (for Sunday)
	-   1 (for Monday)
	-   2 (for Tuesday)
	-   3 (for Wednesday)
	-   4 (for Thursday)
	-   5 (for Friday)
	-   6 (for Saturday)

-   **diet_type** *integer* (optional) - must be one of:
	-   0 (for no restriction)
	-   1 (for pescetarian)
	-   2 (for vegetarian)
	-   3 (for paleo)
	-   4 (for keto)
	-   5 (for vegan)

-   **budget** *integer* (optional) - an integer (0,1,2, or 3) indicating how high the client’s grocery budget is. 0 for the lowest possible budget and 3 for the highest. Generally this field should be maxed out to increase variety, but can also be a good way to modulate expenses for cost-sensitive clients

-   **weekly_variety** *integer* (optional) - an integer (0,1,2, or 3) indicating how much variety to include in the client’s meal plan (i.e. how often to switch up meals vs repeating them/eating leftovers meal prep style). 0 for the least amount of variety and the least amount of cooking, 3 for the most variety and most cooking.

-   **complexity_preference** *integer* (optional) - an integer (0,1,2, or 3) indicating the maximum level of recipe complexity to include in this client’s plan. Recipe complexity is a qualitative metric determined by ingredient counts and total time required by a recipe. 0 is the least complex and 3 is the most. Note that restricting complexity too much can reduce variety in the meal plan.

-   <a id="selected-meal-types"></a>**selected_meal_types** *array of strings* (optional) - an array indicating which meals to include in the client’s plan each day. At least two meal types must be specified. Possible values are:
	-   breakfast
	-   lunch
	-   dinner
	-   snack

-   **excluded_keywords** *string* (optional) - a comma-separated list of tags used for food allergy exclusions. Possible tags are:
	-   dairy1 (excludes high-lactose dairy from the meal plan)
	-   dairy2 (excludes all dairy from the meal plan)
	-   egg1 (excludes eggs from the meal plan)
	-   peanut1 (excludes peanuts from the meal plan)
	-   tree_nuts (excludes tree nuts from the meal plan)
	-   soy1 (excludes soy from the meal plan)
	-   gluten1 (excludes gluten from the meal plan)
	-   fish1 (excludes fish from the meal plan)
	-   shellfish1 (excludes shellfish from the meal plan)

-   **extra_keywords** *string* (optional) - a comma separated list of free-text keywords/phrases to exclude from the meal plan. For example, if this was set to “brussels sprouts,beef” the meal plan would never include any recipes whose name, description, or ingredients included the keyword “brussels sprouts” or “beef”. Important: these keywords should be as specific as possible. For example, instead of setting the field to “nightshade” you should set it to “tomato,pepper,potato,eggplant”.

-   **meal_type_daily_percentages** *object* (optional) - an object whose keys are the meal types included in [selected_meal_types](#selected-meal-types) and values are the percentage of the client’s daily calories that should be allocated to that meal type. For example, a value of { “breakfast”: 20, “lunch”: 30, “dinner”: 50 } would tell the meal planner to allocate 20%, 30%, and 50% of the client’s daily calories to breakfast, lunch, and dinner respectively.

-   **override_calories** *boolean* (optional) - if set to true, the meal planner will use the calorie goal specified in calorie_override for this client’s meal plan. Otherwise, calorie_override will be ignored and the client’s calorie goal will be calculated automatically based on their profile.

-   **calorie_override** *integer* (optional) - the client’s daily caloric needs. Must be between 600 and 5500 if specified. If this is not specified, or override_calories is not set to true, the client’s daily calories are calculated automatically based on their profile.
    
-   **fiber_goal** *integer* (optional) - how many grams of fiber the client should be eating per day. Must be between 0 and 40.
    
-   **macro_parameters** *array of objects* (optional) - an array representing the client’s daily macronutrient targets. Each object in the array represents a single macronutrient and its daily target. You can specify targets for up to two macronutrients. If an empty array is set, the client’s macro goals will be calculated automatically based on their profile. Each object should have the following fields:
	-   **macro** *string* (required) - the macro whose daily target is being specified. Must be one of:
		-   protein
		-   carbs
		-   fat
	-   **amount** *number* (required) - how much of the specified macro the client should be eating each day. This will either be interpreted as a percent of the client’s daily calories or a gram amount, depending on the value of unit_type.
	-   **unit_type** *integer* (required) - must be one of:
		-   0 - the amount field will be interpreted as grams
		-   1 - the amount field will be interpreted as a percentage of the client’s daily calories
	-   **compare_type** *string* (required) - must be one of:
		-   at_least - the client should be eating AT LEAST the specified amount of the macronutrient 
		-   at_most - the client should be eating AT MOST the specified amount of the macronutrient


#### Workout Plan Fields

-   **pick_own_routine** *boolean* (optional) - set to true to have client go through a guided workout selection flow and pick an appopriate routine from Strongr Fastr's database of routines.


## Design system for a Wending Machine


# Step1: Requirements Gathering
    Functional Requirements:
        - Ability to select a product
        - Ability to accept payment in cash/ if credit card then third party payment gateway is needed
        - Ability to return change
        - Ability to dispense
        - Update inventory
        - Notify the vendor when stock is low or change is not present.


# This is bit different from the usual system design problem, here we will just discuss process and structure

# Step 2: Discussing process flow

    User selects a product
        -if the product is not available send error to user
        -if available calculate amount
    -Accept Payment
        -If payment fails return
        -If payment is accepted then 
            -Check amount if less than product cost return error else
            -Calculate Change
                -if change is not available return error to user and send notification to vendor
                -else if change is returned then
    -Dispense the product
                
# Step 3: Designing system  

                                        user
                        
    Select Product                     Make Payment                   Dispense Product

                    Calculate Payment               Calculate Change
                    
            |               |--------  Return Change
            |               |          Payment Gateway(id credit card allowed)
            Notification Service( which will tell vendor if inventory is low on something or change is not present)
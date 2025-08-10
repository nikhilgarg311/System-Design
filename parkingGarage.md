## Design Parking Space reservation and payment system


# Step1 Requirements Gathering
    (1) Functional Requirements:
        -Reserve a spot (3 types based on size of vehicle)
        -Accept Payment( flat rate based on time, but different for 3 different types of vechicles)
        -Receipt generation
        -Cancel booking
    (2) Non Functional Requirements:
        - Consistency

# Step2 Api Design
Public Endpoints
    (1) /reserve (this will reserve the parking spot)
        - it will accept params as 
            garage_id, 
            vehicle_id, 
            entry_time, 
            vechicle_type
        - it will return 
            reservation_id,
            spot_id
    (2) /payments()
        -params
            reservation_id

        Let assume this will be handled by an third party API
        -returns
            payment_id

    (3)/cancel
        -params
            reservation_id

    // we can have signup login endpoints also

Internal Endpoints
    (4) /freespots
        -params
            garage_id
            vehicle_type
            fromTime

    (4) /calculate-payment
        -params
            garage_id
            vehicle_type
        
        
    (5) /allocate-spot
        -params
            garage_id
            spot_id
            vehicle_id


# Step 3 DataBase Design
    //always start with RDBMS, then go for SQL or NoSQL

    Resercation table
        reservation_id (primarykey,int)
        garage_id (foreignKey,int)
        spot_id (foreignKey,int)
        vehicle_type(enum)
        startTime (timestamp)
        endTime (timestamp)
        paid (boolean)


    Garage table
        garage_id(primaryKey,int)
        rate_regular
        rate_compact
        ratte_large
        
    Spot Table
        spot_id(primary,key)
        vechicle_type(enum)
        status(enum)

# Step4 Storage/Capacity Estimation


# Step5 HLD

    User------->LoadBalancer--------->Server---------->Services
                                                        |---------------Our Services--------------|Database/Cace (Have replica and use different replicas for read and write but they should have strong consistency (we can use read lock while writing))
                                                        |--------------ThirdPartPaymentService----|Database
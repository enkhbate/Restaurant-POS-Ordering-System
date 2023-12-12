# Restaurant-POS-Ordering-System
This is my first programming practise. 

import re

# Store user data in a dictionary
user_data = {}
CURRENT_YEAR = 2023 # current year


# Define a regular expression for a date string in the format "dd/mm/yyyy"
current_date = re.compile(r"^\d{2}/\d{2}/\d{4}$")

# regex to check if password ends with a digit
password_regex = r"\d$"

dine_in_orders = {} # Store dine in orders
pick_up_orders = {}  # Store pick up orders
delivery_data = {}  # Store delivery data
orders = {} # Store all orders

order_number = 1 # Initialize order number


#------------------------FOOD CLASS-------------------------------------------------
# Define a class called Food with two attributes: name and price
class Food:
    def __init__(self, name, price):
        self.name = name
        self.price = price
    # Define a method to return the name of the food item
    def get_name(self):
        return self.name

#------------------------------singup function------------------------------------------
def signup():
    # get user input
    name = input("Please enter your your name: ")
    mobile_number = input("Please enter your mobile number: ")
    password = input("Please enter your password: ")
    confirm_password = input("Please confirm your password: ")
    date_of_birth = input("Please enter your date of birth, The date of birth is in the format DD/MM/YYYY: ")
    address = input("Please enter your address: ")

    # check if mobile number already exists
    while mobile_number in user_data:
        print("mobile number already exists! Please use another mobile number.")
        mobile_number = input("Please enter your mobile number: ")

    # check if any of the input is empty
    if name == "" or mobile_number == "" or  password == "" or confirm_password == "" or date_of_birth == "":
        print("Signing up process failed, Please following Signing up instruction")
        return
    
    # mobile number validation
    is_valid, message = check_mobile_number(mobile_number)
    if not is_valid:
        print(message)
        return

    # password validation
    is_valid, message = check_password(password, confirm_password)
    if not is_valid:
        print(message)
        return
    
    # date of birth validation
    if not current_date.match(date_of_birth):
        print("You have entered the Date of birth in invalid format. Please start again:")
        return
    age = CURRENT_YEAR - int(date_of_birth[-4:])

    # check if age is less than 21
    if age < 21:
        print("Age requirement at least 21 years old.")
        return
    # password validation
    is_valid, message = check_password(password, confirm_password)
    if not is_valid:
        print(message)
        return
    print("You have Successfully Signed up.")
    # save user data
    user_data[mobile_number] = {            #One mobile number one customer
        "name": name,
        "mobile_number": mobile_number,
        "password": password,
        "date_of_birth": date_of_birth,
        "address": address                 #Optional       
    }    

#---------------------------login function--------------------------------------------------------------
def login():
    login_times = 0
    global order_number
    while login_times < 3: #3 times chance for login
        # get user input
        mobile_number = input("Please enter your Username (mobile number): ")
        password = input("Please enter your password: ")

        if mobile_number in user_data:
            if password == user_data[mobile_number]["password"]: #checking password
                print("You have successfully Signed in!")
                print("Welcome ", user_data[mobile_number]["name"]) #User name from database
                print("Please enter 2.1 to Start Ordering.")
                print("Please enter 2.2 to Print Statistics.")
                print("Please enter 2.3 for Logout.")
                while True: # Repeat until user provides valid input
                    action = input("Please select one of the those option 2.1, 2.2, 2.3: ").lower()
                    if action == "2.1":
                        start_ordering(mobile_number)   #Jump to another menu
                    elif action == "2.2":
                        print_statistics(mobile_number)  #Jump to another menu
                    elif action == "2.3":
                        print("You have successfully Signed out!")
                        login()
                    else: # If user enters invalid input
                        print("Invalid input. Please try again.")
                return
            else: # If user enters invalid input
                print("Password is incorrect!")
        else: # If user enters invalid input
            print("You have not signed up with this contact number. Please sign up first.")
        login_times += 1
        print("Please try again.")

    # if user has used the maximum attempts of login then reset password
    if login_times == 3:
        print("You have used the maximum attempts of login. Please reset your password.")
        reset_password()


#------------------------------------address_add------------------------------------------
def address_add():
    while True: # Repeat until user provides valid input
        action = input("You Have not mentioned your address, while signing up. \nPlease Enter Y if would like to enter your address. \nEnter N if you would like to select other mode of order.")
        if action == "Y":
            list(user_data.items())[0][1]["address"] = input("Please enter your address: ") #address added
            print("You address is", list(user_data.items())[0][1]["address"], "" )
            menu_home_delivery()
        elif action == "N":
            print("Enter N if you would like to select other mode of order HAHAHA")
        else: # If user enters invalid input
            print("Invalid input. Please try again.")
            
#----------------------------------reset_password----------------------------------
def reset_password():
    # get user input
    mobile_number = input("Please enter your Username (mobile number): ")
    dob = input("Please enter your date of birth, The DOB is in the format DD/MM/YYYY: ")

    if mobile_number in user_data:
        if dob == user_data[mobile_number]["dob"]:
            # get user input
            password = input("Please enter your new password: ")
            confirm_password = input("Please confirm your new password: ")

            # password validation
            is_valid, message = check_password(password, confirm_password)
            if not is_valid:
                print(message)
                return
            else: # If user enters invalid input
                user_data[mobile_number]["password"] = password
                print("Password has been reset successfully!")
        else: # If user enters invalid input
            print("Date of birth is incorrect!")

#----------------------------Start Ordering --------------------------------

def start_ordering(mobile_number):
    print("Please enter 1 for Dine in.")
    print("Please enter 2 for Order Online.")
    print("Please enter 3 to go to Login Page.")                
    while True: # Repeat 
        action = input("Please select one of the those option 1, 2, 3: ").lower()
        if action == "1":
            menu_dine_in(mobile_number)    #Jump to another menu                                               
        elif action == "2":
            order_online(mobile_number)     #Jump to another menu
        elif action == "3":
            login()
        else:
            print("Invalid input. Please try again.")

#---------------------------------------menu_dine_in-----------------------------------------------
def menu_dine_in(mobile_number):
    global order_number
    print("Enter 1 for Noodles    Price AUD 2")
    print("Enter 2 for Sandwich   Price AUD 4")
    print("Enter 3 for Dumpling   Price AUD 6")
    print("Enter 4 for Muffins    Price AUD 8")
    print("Enter 5 for Pasta      Price AUD 10")
    print("Enter 6 for Pizza      Price AUD 20")
    print("Enter 7 for Drinks Menu")
    print("Enter 0 for Back To Main Menu")
    while True: # Repeat 
        action = input("Please select one of the those option 1, 2, 3, 4, 5, 6, 7, 0: ").lower()
        if action == "1":
            food_item = Food("Noodles", 2)          #Add order
        elif action == "2":
            food_item = Food("Sandwich", 4)          #Add order
        elif action == "3":
            food_item = Food("Dumpling", 6)          #Add order
        elif action == "4":
            food_item = Food("Muffins", 8)          #Add order
        elif action == "5":
            food_item = Food("Pasta", 10)          #Add order
        elif action == "6":
            food_item = Food("Pizza", 20)          #Add order
        elif action == "7":
            menu_drinks_menu(mobile_number, dine_in_orders)
            continue
        elif action == "0":
            menu_dine_in(mobile_number)
            continue
        else: # If user enters invalid input
            print("Invalid input. Please try again.")
            continue

        if mobile_number in dine_in_orders:
            dine_in_orders[mobile_number]["food"].append(food_item)
        else:
            dine_in_orders[mobile_number] = {"food": [food_item]}





#---------------------Order number generation------------------------------------
def generate_order_id(order_number): #Order ID generation process
    return "S" + str(order_number).zfill(3)  #S letter + 0001 generation

#--------------------menu_drinks_menu--------------------------------------------------
def menu_drinks_menu(mobile_number, dine_in_orders):
    print("Enter 1 for Coffee      Price AUD 2")
    print("Enter 2 for Colddrink   Price AUD 4")
    print("Enter 3 for Shake       Price AUD 6")
    print("Enter 4 for Checkout")
    print("Enter 0 for Back To Main Menu")
    while True: # Repeat 
        action = input("Please select one of the those option 1, 2, 3, 4, 0: ").lower()
        if action == "1":                                                                                     
            food_item = Food("Coffee", 2)          #Add order
        elif action == "2":
            food_item = Food("Colddrink", 4)           #Add order         
        elif action == "3":
            food_item = Food("Shake", 6)          #Add order
        elif action == "4":
            check_out(mobile_number, dine_in_orders)                       
        elif action == "0":
            menu_dine_in(mobile_number)
        else:
            print("Invalid input. Please try again.")
            continue
        if mobile_number in dine_in_orders:
            dine_in_orders[mobile_number]["food"].append(food_item)
        else:
            dine_in_orders[mobile_number] = {"food": [food_item]}
            
#---------------------------------check_out------------------------------------------------
def check_out(mobile_number, dine_in_orders):
    global order_number
    total_price = 0
    for mobile_number, dine_in_orders in dine_in_orders.items():
        for food_item in dine_in_orders["food"]:
            total_price += food_item.price

    total_price_with_service = total_price*0.15+total_price
    #print customer total amount and extra service charge
    print("Your total payble amount is: ",total_price_with_service, " inclusing AUS", round(total_price*0.15, 2), "for Service Charges")
    dine_in_booking_persons = input("Please enter the number of Persons: ")
    dine_in_booking_date = input("Please enter the Date of Booking for Dine DD/MM/YYYY: ")
    dine_in_booking_time = input("Please enter the Time of booking for Dine HH:MM: ")

    while True: # Repeat 
        action = input("Please Enter Y to proceed to Check out or Enter N to cancel the order: ").upper()
        if action == "Y":
            
            order_id = generate_order_id(order_number) # add order id to dine_in_orders
            # order saving process
            order = {
                "order_id": order_id,
                "date": dine_in_booking_date+" "+dine_in_booking_time,
                "total_amount": total_price_with_service,
                "order_type": "Dine In"
            }
            order_number += 1
            orders[order_id] = order
            
            print("Date of Booking for Dine:", dine_in_booking_date)
            print("Time of Booking for Dine:", dine_in_booking_time)
            print("The number of Persons:", dine_in_booking_persons)
            print("Thank You for entering the details, Your Booking is confirmed.")
            login()         

        elif action == "N":
            print("Your order already canceled")
            del dine_in_orders[mobile_number]
            login()
        else: # If user enters invalid input
            print("Invalid input. Please try again.")  
            continue


#----------------------------Print Statistics--------------------------
def print_statistics(mobile_number):
    global orders
    print("1 - All Dine in Orders.")
    print("2 - All Pick up Orders.")
    print("3 - All Deliveries.")
    print("4 - All Orders (Ascending Order).")
    print("5 - Total Amount Spent on All Orders.")
    print("6 - To go to Previous Menu.")
    while True: # Repeat until user provides valid input
        action = input("Please Enter the Option to Print the Statistics.:").lower()
        if action == "1":
            print_all_dine_in_orders("Dine In")          #Add order
        elif action == "2":
            print_all_pick_up_orders("Pick Up")          #Add order
        elif action == "3":
            print_all_deliveries("Delivery")          #Add order
        elif action == "4":
            print_all_orders_ascending_order("All")
        elif action == "5":
            total_amount = 0
            for order_id, order in orders.items():
                total_amount += float(order["total_amount"])
            print("Total Amount Spent on All Orders: $", total_amount)
        elif action == "6":
            login()
        else: # If user enters invalid input
            print("Invalid input. Please try again.") 

#------------------All Dine in Orders-----------------------------------------
def print_all_dine_in_orders(order_type):
    global orders

    if len(orders) == 0:
        print("\n\n\nNo orders found.\n\n\n")
        return

    print("\n" * 3)
    table = "Order ID |       Date        | Total Amount   | Order Type \n"
    table += "---------------------------------------------------\n"

    # Add the data rows to the table
    for order_id, order in orders.items():
        if order_type == "All":
            table += f"{order['order_id']}     | {order['date']}  | ${order['total_amount']}          | {order['order_type']}    \n"
        elif order["order_type"] == order_type:
            table += f"{order['order_id']}     | {order['date']}  | ${order['total_amount']}          | {order['order_type']}    \n"

    table += "---------------------------------------------------\n"
    # Print the table
    print(table)
    print("\n" * 3)
    
#------------------All Pick up Orders-----------------------------------------
def print_all_pick_up_orders(order_type):
    global orders

    if len(orders) == 0:
        print("\n\n\nNo orders found.\n\n\n")
        return

    print("\n" * 3)
    table = "Order ID |       Date        | Total Amount   | Order Type \n"
    table += "---------------------------------------------------\n"

    # Add the data rows to the table
    for order_id, order in orders.items():
        if order_type == "All":
            table += f"{order['order_id']}     | {order['date']}  | ${order['total_amount']}          | {order['order_type']}    \n"
        elif order["order_type"] == order_type:
            table += f"{order['order_id']}     | {order['date']}  | ${order['total_amount']}          | {order['order_type']}    \n"

    table += "---------------------------------------------------\n"
    # Print the table
    print(table)
    print("\n" * 3)
    
#---------------------------------------------------------------------------------
#------------------All Deliveries-----------------------------------------
def print_all_deliveries(order_type):
    global orders

    if len(orders) == 0:
        print("\n\n\nNo orders found.\n\n\n")
        return

    print("\n" * 3)
    table = "Order ID |       Date        | Total Amount   | Order Type \n"
    table += "---------------------------------------------------\n"

    # Add the data rows to the table
    for order_id, order in orders.items():
        if order_type == "All":
            table += f"{order['order_id']}     | {order['date']}  | ${order['total_amount']}          | {order['order_type']}    \n"
        elif order["order_type"] == order_type:
            table += f"{order['order_id']}     | {order['date']}  | ${order['total_amount']}          | {order['order_type']}    \n"

    table += "---------------------------------------------------\n"
    # Print the table
    print(table)
    print("\n" * 3)
    


#---------------All orders ascending---------------------------
def print_all_orders_ascending_order(order_type):
    global orders
    if len(orders) == 0:
        print("\n\n\nNo orders found.\n\n\n")
        return

    print("\n" * 3)
    table = "Order ID |       Date        | Total Amount   | Order Type \n"
    table += "---------------------------------------------------\n"

    # Add the data rows to the table
    for order_id, order in orders.items():
        if order_type == "All":
            table += f"{order['order_id']}     | {order['date']}  | ${order['total_amount']}          | {order['order_type']}    \n"
        elif order["order_type"] == order_type:
            table += f"{order['order_id']}     | {order['date']}  | ${order['total_amount']}          | {order['order_type']}    \n"

    table += "---------------------------------------------------\n"
    # Print the table
    print(table)
    print("\n" * 3)
    
#-------------------------order_online--------------------------------

def order_online(mobile_number):
    print("Enter 1 for Self Pickup.")
    print("Enter 2 for Home Delivery.")
    print("Enter 3 to go to Previous Menu.")
    while True: # Repeat until user provides valid input
        action = input("Please select one of the those option 1, 2, 3: ").lower()
        if action == "1":
            menu_self_pickup(mobile_number)
        elif action == "2":
            if list(user_data.items())[0][1]["address"] == "":
                address_add(mobile_number)              
            else:
                menu_home_delivery(mobile_number) 
        elif action == "3":
            print("There is no previous menu right now")
        else: # If user enters invalid input
            print("Invalid input. Please try again.")

#---------------------------------menu_self_pickup------------------------
def menu_self_pickup(mobile_number):
    print("Enter 1 for Noodles    Price AUD 2")
    print("Enter 2 for Sandwich   Price AUD 4")
    print("Enter 3 for Dumpling   Price AUD 6")         
    print("Enter 4 for Muffins    Price AUD 8")
    print("Enter 5 for Pasta      Price AUD 10")
    print("Enter 6 for Pizza      Price AUD 20")
    print("Enter 7 for Checkout")
    print("Enter 0 for Back To Main Menu")
    while True: # Repeat until user provides valid input
        action = input("Please select your order for those option 1, 2, 3, 4, 5, 6, 7, 0: ").lower()
        if action == "1":
            food_item = Food("Noodles", 2)
        elif action == "2":
            food_item = Food("Sandwich", 4)
        elif action == "3":
            food_item = Food("Dumpling", 6)
        elif action == "4":
            food_item = Food("Muffins", 8)
        elif action == "5":
            food_item = Food("Pasta", 10)
        elif action == "6":
            food_item = Food("Pizza", 20)
        elif action == "7":
            check_out_menu_self_pickup(mobile_number, pick_up_orders)
            continue
        elif action == "0":
            start_ordering(mobile_number)
            continue
        else: # If user enters invalid input
            print("Invalid input. Please try again.")
            continue
        
        if mobile_number in pick_up_orders:
            pick_up_orders[mobile_number]["food"].append(food_item)
        else:
            pick_up_orders[mobile_number] = {"food": [food_item]}
        
#-----------------------check_out_menu_self_pickup-----------------------------------
def check_out_menu_self_pickup(mobile_number, pick_up_orders):
    global order_number 
    total_price = 0
    for mobile_number, pick_up_orders in pick_up_orders.items():
        for food_item in pick_up_orders["food"]:
            total_price += food_item.price
        self_pickup_date = input("Please enter the Date of Pick up DD/MM/YYYY: ")
        self_pickup_time = input("Please enter the Time of Pick up HH:MM: ")
        self_pickup_persons = input("Please enter the name of Persons: ")
        while True: # Repeat 
            action = input("Please Enter Y to proceed to Check out or Enter N to cancel the order: ").upper()
            if action == "Y":
                order_id = generate_order_id(order_number) # add order id to dine_in_orders
               # order saving process
                order = {
                    "order_id": order_id,
                    "date": self_pickup_date+" "+self_pickup_time,
                    "total_amount": total_price,
                    "order_type": "Pick Up"
                }
                order_number += 1
                orders[order_id] = order
            
                print("Date of Pick up:", self_pickup_date)
                print("Time of Pick up:", self_pickup_time)
                print("Name of the Persons:", self_pickup_persons)
                print("Thak You for entering the details, Your Booking is confirmed.")
                print("Your total amount AUD", total_price)
                login()
            elif action == "N":
                print("Your order already canceled")
                del pick_up_orders[mobile_number]
                login()
            else: # If user enters invalid input
                print("Invalid input. Please try again.")  
                continue


#----------------------------Update Address------------------------------------

def address_add(mobile_number):
    while True: # Repeat until user provides valid input
        action = input("You Have not mentioned your address, while signing up. \nPlease Enter Y if would like to enter your address. \nEnter N if you would like to select other mode of order:")
        if action == "Y":
            list(user_data.items())[0][1]["address"] = input("Please enter your address: ")
            print("You address is", list(user_data.items())[0][1]["address"], "" )
            menu_home_delivery(mobile_number)
        elif action == "N":
            order_online(mobile_number)
        else: # If user enters invalid input
            print("Invalid input. Please try again.")
                


#------------------------------menu_home_delivery----------------------------------
def menu_home_delivery(mobile_number):
    print("Enter 1 for Noodles    Price AUD 2")
    print("Enter 2 for Sandwich   Price AUD 4")
    print("Enter 3 for Dumpling   Price AUD 6")
    print("Enter 4 for Muffins    Price AUD 8")
    print("Enter 5 for Pasta      Price AUD 10")
    print("Enter 6 for Pizza      Price AUD 20")
    print("Enter 7 for Checkout")
    print("Enter 0 for Back To Main Menu")
    while True: # Repeat until user provides valid input
        action = input("Please select your order for those option 1, 2, 3, 4, 5, 6, 0: ").lower()
        if action == "1":
            food_item = Food("Noodles", 2)
        elif action == "2":
            food_item = Food("Sandwich", 4)
        elif action == "3":
            food_item = Food("Dumpling", 6)
        elif action == "4":
            food_item = Food("Muffins", 8)
        elif action == "5":
            food_item = Food("Pasta", 10)
        elif action == "6":
            food_item = Food("Pizza", 20)
        elif action == "7":
            check_out_menu_home_delivery(mobile_number, delivery_data)
            continue
        elif action == "0":
            start_ordering(mobile_number)
            continue
        else:
            print("Invalid input. Please try again.")
            continue
        if mobile_number in delivery_data:
            delivery_data[mobile_number]["food"].append(food_item)
        else:
            delivery_data[mobile_number] = {"food": [food_item]}       
 
 #-------------------check_out_menu_home_delivery------------------------           
def check_out_menu_home_delivery(mobile_number, delivery_data):
    global order_number
    total_price = 0
    for mobile_number, delivery_data in delivery_data.items():
        for food_item in delivery_data["food"]:
            total_price += food_item.price
        delivery_date = input("Please enter the Date of Pick up DD/MM/YYYY: ")
        delivery_time = input("Please enter the Time of Pick up HH:MM: ")
        print("Delivery mode: \nA fix charge for Delivery based on the distance i.e. \nMore than 0 to 5 Kms         AUD 5 \nMore than 5 to 10 Kms        AUD 10 \nMore than 10 to 15 Kms       AUD 18 \nMore than 15 Kms             No Delivery provided")
        distance_of_delivery = float(input("Please enter the Distance from the Restaurant (in Kms): "))
        if distance_of_delivery > 15:
            print("No Deliveri provided")
            menu_home_delivery(mobile_number)
        else:   
            while True: # Repeat 
                action = input("Please Enter Y to proceed to Check out or Enter N to cancel the order: ").upper()
                if action == "Y":
                    order_id = generate_order_id(order_number) # add order id to dine_in_orders
                       # order saving process
                    order = {
                        "order_id": order_id,
                        "date": delivery_date+" "+delivery_time,
                        "total_amount": total_price,
                        "order_type": "Delivery"
                    }
                    order_number += 1
                    orders[order_id] = order
                
                    print("Date of Delivery:", delivery_date)
                    print("Time of Delivery:", delivery_time)
                    print("Thak You for entering the details, Your Booking is confirmed.")
                    print("Your total amount AUD", total_price, "and there will be an additional charges for Delivery.")
                    login()
                elif action == "N":
                    print("Your order already canceled")
                    del delivery_data[mobile_number]
                    login()
                else: # If user enters invalid input
                    print("Invalid input. Please try again.")  
                    continue
                  
            start_ordering(mobile_number)

#--------------------------check mobile number function---------------------------------------
def check_mobile_number(mobile_number):
    if mobile_number[0] != "0": # Check if mobile number starts with "0"
        return False, "Mobile number should start with 0."
    if len(mobile_number) != 10: # Check if mobile number is exactly 10 digits long
        return False, "Mobile number should be 10 digits."
    if not mobile_number.isdigit(): # Check if mobile number contains only digits
        return False, "Mobile number should be digits."
    return True, "Mobile number is valid."

#----------------------------check password function-----------------------------------------
def check_password(password, confirm_password):
    # Check if password contains only alphanumeric characters
    if password.isalnum():
        return False, "Password should contain at least one special character."
    if not re.search(password_regex, password):
        return False, "Password should end with a digit."
    if password != confirm_password: #check if password match
        return False, "Passwords do not match." #If all checks pass, return True and a message indicating that the password is valid
    return True, "Password is valid."

#-------------------------main function-----------------------------------------------
def main():
    while True: # Repeat until user provides valid input
        print("Please enter 1 for signup.")
        print("Please enter 2 for sign in.")
        print("Please enter 3 for quit.")
        #print("Please select one of the those option 1, 2, 3")
        action = input("Please select one of the those option 1, 2, 3: ").lower()
        if action == "1":
            signup()
        elif action == "2":
            login()
        elif action == "3":
            print("Thank you for using the application!")
            main()
        else: # If user enters invalid input
            print("Invalid input. Please try again.")

if __name__ == "__main__":
    main()

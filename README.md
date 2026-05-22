 import requests
import csv
import json


# -------------------------------
# Function to Fetch Data from API
# -------------------------------
def fetch_users():
    url = "https://jsonplaceholder.typicode.com/users"

    try:
        response = requests.get(url, timeout=5)

        # Check API response status
        response.raise_for_status()

        data = response.json()

        # Check empty response
        if not data:
            print("Error: Empty response received from API.")
            return []

        return data

    except requests.exceptions.RequestException as e:
        print(f"API Error: {e}")
        return []


# --------------------------------
# Function to Process User Data
# --------------------------------
def process_users(users):
    processed_data = []

    for user in users:
        user_info = {
            "ID": user.get("id"),
            "Name": user.get("name"),
            "Username": user.get("username"),
            "Email": user.get("email"),
            "City": user.get("address", {}).get("city"),
            "Company": user.get("company", {}).get("name")
        }

        processed_data.append(user_info)

    return processed_data


# --------------------------------
# Function to Display Data
# --------------------------------TTTTT
def display_users(users):
    print("\n" + "=" * 90)
    print(
        f"{'ID':<5} {'Name':<25} {'Username':<15} {'Email':<30} {'City':<15}"
    )
    print("=" * 90)

    for user in users:
        print(
            f"{user['ID']:<5} "
            f"{user['Name']:<25} "
            f"{user['Username']:<15} "
            f"{user['Email']:<30} "
            f"{user['City']:<15}"
        )

    print("=" * 90)


# --------------------------------
# Function to Save CSV File
# --------------------------------
def save_to_csv(users, filename="users_data.csv"):
    try:
        with open(filename, mode="w", newline="", encoding="utf-8") as file:
            writer = csv.writer(file)

            # CSV Header
            writer.writerow(
                ["ID", "Name", "Username", "Email", "City", "Company"]
            )

            # CSV Data
            for user in users:
                writer.writerow([
                    user["ID"],
                    user["Name"],
                    user["Username"],
                    user["Email"],
                    user["City"],
                    user["Company"]
                ])

        print(f"\nCSV file '{filename}' created successfully.")

    except IOError as e:
        print(f"File Writing Error: {e}")


# --------------------------------
# Feature 1: Search User by Name
# --------------------------------
def search_user(users):
    search_name = input("\nEnter username or name to search: ").lower()

    found = False

    for user in users:
        if (search_name in user["Name"].lower() or
                search_name in user["Username"].lower()):

            print("\nUser Found:")
            print("-" * 40)
            for key, value in user.items():
                print(f"{key}: {value}")

            found = True

    if not found:
        print("No matching user found.")


# --------------------------------
# Feature 2: Export First N Users
# --------------------------------
def export_first_n_users(users):
    try:
        n = int(input("\nEnter number of users to export: "))

        if n <= 0:
            print("Please enter a positive number.")
            return

        limited_users = users[:n]

        save_to_csv(limited_users, "first_n_users.csv")

    except ValueError:
        print("Invalid number entered.")


# --------------------------------
# Feature 3: Save Data as JSON
# --------------------------------
def save_to_json(users, filename="users_data.json"):
    try:
        with open(filename, "w", encoding="utf-8") as file:
            json.dump(users, file, indent=4)

        print(f"JSON file '{filename}' created successfully.")

    except IOError as e:
        print(f"JSON File Error: {e}")


# --------------------------------
# Main Function
# --------------------------------
def main():
    users = fetch_users()

    if not users:
        return

    processed_users = process_users(users)

    print("\n========= USER DATA MENU =========")
    print("1. Display Data in Terminal")
    print("2. Save Data to CSV")
    print("3. Both")
    print("4. Search User by Name")
    print("5. Export First N Users")
    print("6. Save Data as JSON")

    choice = input("\nEnter your choice: ")

    if choice == "1":
        display_users(processed_users)

    elif choice == "2":
        save_to_csv(processed_users)

    elif choice == "3":
        display_users(processed_users)
        save_to_csv(processed_users)

    elif choice == "4":
        search_user(processed_users)

    elif choice == "5":
        export_first_n_users(processed_users)

    elif choice == "6":
        save_to_json(processed_users)

    else:
        print("Invalid menu choice. Please run the program again.")


# --------------------------------
# Program Entry Point
# --------------------------------
if __name__ == "__main__":
    main()

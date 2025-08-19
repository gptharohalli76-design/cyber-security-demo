
import datetime

class Product:
    def __init__(self, id, name, price, quantity):
        self.id = id
        self.name = name
        self.price = price
        self.quantity = quantity

    def update_stock(self, amount):
        if amount < 0 and abs(amount) > self.quantity:
            print(f"Error: Cannot remove {amount} from stock. Only {self.quantity} in inventory.")
            return False
        self.quantity += amount
        return True

    def get_total_value(self):
        return self.price * self.quantity

    def __str__(self):
        return f"Product[{self.id}] {self.name} - ${self.price:.2f} x {self.quantity}"

class Inventory:
    def __init__(self):
        self.products = {}

    def add_product(self, product):
        if product.id in self.products:
            print(f"Product {product.id} already exists. Updating quantity.")
            self.products[product.id].quantity += product.quantity
        else:
            self.products[product.id] = product

    def remove_product(self, product_id):
        if product_id in self.products:
            del self.products[product_id]
            print(f"Product {product_id} removed.")
        else:
            print("Product not found.")  # BUG: No ID mentioned in message

    def list_inventory(self):
        print("Current Inventory:")
        for product in self.products.values():
            print(product)

    def get_inventory_value(self):
        total = 0
        for p in self.products:
            total += self.products[p].get_total_value()
        return total

    def sell_product(self, product_id, amount):
        if product_id not in self.products:
            print("Product not found.")
            return False
        product = self.products[product_id]
        if product.quantity < amount:
            print("Not enough stock.")
            return False
        product.quantity -= amount
        revenue = product.price * amount
        print(f"Sold {amount} of {product.name} for ${revenue:.2f}")
        return True

    def restock_product(self, product_id, amount):
        if product_id not in self.products:
            print("Product does not exist.")
            return False
        self.products[product_id].update_stock(amount)
        print(f"Restocked {amount} units of {self.products[product_id].name}.")
        return True

    def search_product(self, name):
        found = []
        for product in self.products.values():
            if name in product.name:  # BUG: Case-sensitive search
                found.append(product)
        return found

    def generate_report(self):
        filename = f"report_{datetime.date.today()}.txt"
        with open(filename, "w") as file:
            file.write("Inventory Report\n")
            file.write("================\n")
            for product in self.products.values():
                file.write(str(product) + "\n")
            file.write(f"\nTotal Inventory Value: ${self.get_inventory_value():.2f}")
        print(f"Report written to {filename}")

def main():
    inventory = Inventory()

    # Prepopulate inventory
    inventory.add_product(Product(1, "Laptop", 1200.00, 10))
    inventory.add_product(Product(2, "Mouse", 25.50, 100))
    inventory.add_product(Product(3, "Keyboard", 45.00, 50))
    inventory.add_product(Product(4, "Monitor", 300.00, 20))
    inventory.add_product(Product(5, "USB Cable", 5.00, 200))

    while True:
        print("\n--- Inventory Management ---")
        print("1. List Inventory")
        print("2. Add Product")
        print("3. Remove Product")
        print("4. Sell Product")
        print("5. Restock Product")
        print("6. Search Product")
        print("7. Generate Report")
        print("8. Exit")
        choice = input("Enter your choice: ")

        if choice == 1:
            inventory.list_inventory()

        elif choice == 2:
            try:
                pid = int(input("Enter product ID: "))
                name = input("Enter name: ")
                price = float(input("Enter price: "))
                qty = int(input("Enter quantity: "))
                inventory.add_product(Product(pid, name, price, qty))
            except:
                print("Invalid input.")  # BUG: Bare except

        elif choice == 3:
            pid = int(input("Enter product ID to remove: "))
            inventory.remove_product(pid)

        elif choice == 4:
            pid = int(input("Enter product ID to sell: "))
            amt = int(input("Enter amount to sell: "))
            success = inventory.sell_product(pid, amt)
            if not success:
                print("Sale failed.")

        elif choice == 5:
            pid = int(input("Enter product ID to restock: "))
            amt = int(input("Enter amount to add: "))
            inventory.restock_product(pid, amt)

        elif choice == 6:
            name = input("Enter name to search: ")
            results = inventory.search_product(name)
            if results:
                for r in results:
                    print(r)
            else:
                print("No products found.")

        elif choice == 7:
            inventory.generate_report()

        elif choice == 8:
            print("Exiting system...")
            break

        else:
            print("Invalid choice")

# BUG: Missed main() ca

# Inventory-management-system-
A console app to manage warehouse/store inventory — add products, update stock, track low-stock alerts, and record price/quantity changes, with file persistence.
import java.util.List;
import java.util.Scanner;

public class InventoryManagementSystem {

    private static InventoryManager manager = new InventoryManager();

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        boolean running = true;

        System.out.println("===== Inventory Management System =====");

        while (running) {
            printMenu();
            String choice = scanner.nextLine().trim();

            switch (choice) {
                case "1": addProduct(scanner); break;
                case "2": viewAllProducts(); break;
                case "3": updateStock(scanner); break;
                case "4": deleteProduct(scanner); break;
                case "5": viewLowStock(); break;
                case "6": viewTotalValue(); break;
                case "7":
                    manager.saveProducts();
                    System.out.println("Data saved. Goodbye!");
                    running = false;
                    break;
                default:
                    System.out.println("Invalid choice.");
            }
        }
        scanner.close();
    }

    private static void printMenu() {
        System.out.println("\n1. Add Product");
        System.out.println("2. View All Products");
        System.out.println("3. Update Stock Quantity");
        System.out.println("4. Delete Product");
        System.out.println("5. View Low Stock Alerts");
        System.out.println("6. View Total Inventory Value");
        System.out.println("7. Save & Exit");
        System.out.print("Enter your choice: ");
    }

    private static void addProduct(Scanner scanner) {
        System.out.print("Enter product name: ");
        String name = scanner.nextLine().trim();
        int qty = readValidInt(scanner, "Enter quantity: ");
        double price = readValidAmount(scanner, "Enter price per unit: ");
        manager.addProduct(name, qty, price);
        System.out.println("Product added.");
    }

    private static void viewAllProducts() {
        List<Product> products = manager.getAllProducts();
        if (products.isEmpty()) {
            System.out.println("No products in inventory.");
            return;
        }
        for (Product p : products) System.out.println(p);
    }

    private static void updateStock(Scanner scanner) {
        int id = readValidInt(scanner, "Enter product ID: ");
        Product p = manager.findById(id);
        if (p == null) {
            System.out.println("Product not found.");
            return;
        }
        int newQty = readValidInt(scanner, "Enter new quantity: ");
        p.setQuantity(newQty);
        System.out.println("Stock updated.");
    }

    private static void deleteProduct(Scanner scanner) {
        int id = readValidInt(scanner, "Enter product ID to delete: ");
        if (manager.deleteProduct(id)) {
            System.out.println("Product deleted.");
        } else {
            System.out.println("Product not found.");
        }
    }

    private static void viewLowStock() {
        List<Product> lowStock = manager.getLowStockProducts();
        if (lowStock.isEmpty()) {
            System.out.println("No low-stock products.");
            return;
        }
        System.out.println("\n--- Low Stock Alerts (<= 5 units) ---");
        for (Product p : lowStock) System.out.println(p);
    }

    private static void viewTotalValue() {
        System.out.printf("Total Inventory Value: $%.2f%n", manager.getTotalInventoryValue());
    }

    private static int readValidInt(Scanner scanner, String prompt) {
        int value = -1;
        while (value < 0) {
            System.out.print(prompt);
            try {
                value = Integer.parseInt(scanner.nextLine().trim());
                if (value < 0) System.out.println("Value cannot be negative.");
            } catch (NumberFormatException e) {
                System.out.println("Invalid number. Try again.");
            }
        }
        return value;
    }

    private static double readValidAmount(Scanner scanner, String prompt) {
        double value = -1;
        while (value < 0) {
            System.out.print(prompt);
            try {
                value = Double.parseDouble(scanner.nextLine().trim());
                if (value < 0) System.out.println("Value cannot be negative.");
            } catch (NumberFormatException e) {
                System.out.println("Invalid number. Try again.");
            }
        }
        return value;
    }
}

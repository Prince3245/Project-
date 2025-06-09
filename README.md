import java.io.*;
import java.security.MessageDigest;
import java.util.*;

public class AuthSystem {
    static Scanner scanner = new Scanner(System.in);
    static String userFile = "users.txt";

    public static void main(String[] args) {
        while (true) {
            System.out.println("\n--- User Authentication System ---");
            System.out.println("1. Register");
            System.out.println("2. Login");
            System.out.println("3. Exit");
            System.out.print("Choice: ");
            String choice = scanner.nextLine();

            switch (choice) {
                case "1":
                    register();
                    break;
                case "2":
                    login();
                    break;
                case "3":
                    System.out.println("Goodbye!");
                    return;
                default:
                    System.out.println("Invalid choice.");
            }
        }
    }

    public static void register() {
        System.out.print("Enter username: ");
        String username = scanner.nextLine();
        System.out.print("Enter password: ");
        String password = scanner.nextLine();

        if (!Utils.isValidUsername(username) || !Utils.isValidPassword(password)) {
            System.out.println("Invalid username or weak password.");
            return;
        }

        if (userExists(username)) {
            System.out.println("User already exists.");
            return;
        }

        String hashed = Utils.hashPassword(password);
        try (FileWriter fw = new FileWriter(userFile, true)) {
            fw.write(username + "," + hashed + "\n");
            System.out.println("User registered successfully.");
        } catch (IOException e) {
            System.out.println("Error saving user.");
        }
    }

    public static void login() {
        System.out.print("Enter username: ");
        String username = scanner.nextLine();
        System.out.print("Enter password: ");
        String password = scanner.nextLine();
        String hashed = Utils.hashPassword(password);

        try (BufferedReader br = new BufferedReader(new FileReader(userFile))) {
            String line;
            while ((line = br.readLine()) != null) {
                String[] parts = line.split(",");
                if (parts.length == 2 && parts[0].equals(username) && parts[1].equals(hashed)) {
                    System.out.println("Login successful.");
                    return;
                }
            }
            System.out.println("Invalid credentials.");
        } catch (IOException e) {
            System.out.println("Error reading user file.");
        }
    }

    private static boolean userExists(String username) {
        try (BufferedReader br = new BufferedReader(new FileReader(userFile))) {
            String line;
            while ((line = br.readLine()) != null) {
                if (line.split(",")[0].equals(username)) return true;
            }
        } catch (IOException e) {
            return false;
        }
        return false;
    }
}

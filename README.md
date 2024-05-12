import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;
import java.io.FileWriter;
import java.io.IOException;

public class TravelReservationSystem {

    private static final DateTimeFormatter dateFormatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");

    private List<String> destinations;
    private List<Reservation> reservations;

    public TravelReservationSystem() {
        destinations = new ArrayList<>();
        destinations.add("China   22,100 php");
        destinations.add("USA     25,000 php");
        destinations.add("UK      27,000 php");
        destinations.add("Japan   30,100 php");
        destinations.add("Korea   28,500 php");
        reservations = new ArrayList<>();
    }

    public static void main(String[] args) {
        TravelReservationSystem reservationSystem = new TravelReservationSystem();
        reservationSystem.run();
    }

    private void run() {
        Scanner scanner = new Scanner(System.in);
        System.out.println("\t+-----------------------------------------------------+");
        System.out.println("\t+       Welcome to the Travel Reservation System!     +");
        System.out.println("\t+-----------------------------------------------------+");

        do {
            System.out.println("\nEnter your name: ");
            String customerName = scanner.nextLine();

            displayAvailableDestinations();

            String destination;
            do {
                System.out.println("\nEnter destination: ");
                destination = scanner.nextLine();
            } while (!isValidDestination(destination));

            System.out.println("\t+-----------------------------------------------------+");
            System.out.println("\t|    Enter preferred travel dates (DD-MM-YYYY):       |");
            System.out.println("\t+-----------------------------------------------------+");

            String travelDate;
            do {
                travelDate = scanner.nextLine();
            } while (!isValidDate(travelDate));

            String reservationTime;
            do {
                System.out.println("\t+------------------------+");
                System.out.println("\t|   Reservation Time     |");
                System.out.println("\t+------------------------+");
                System.out.println("\t| 1. DAWN                |");
                System.out.println("\t| 2. AM                  |");
                System.out.println("\t| 3. PM                  |");
                System.out.println("\t| 4. MIDNIGHT            |");
                System.out.println("\t+------------------------+");
                reservationTime = scanner.nextLine();
            } while (!isValidReservationTime(reservationTime));

            String flightType;
            do {
                System.out.println("\t+----------------------+" );
                System.out.println("\t|     Flight Type      |" );
                System.out.println("\t+----------------------+" );
                System.out.println("\t| 1. Economy           |" );
                System.out.println("\t| 2. Business          |" );
                System.out.println("\t| 3. First Class       |" );
                System.out.println("\t+----------------------+" );
                flightType = scanner.nextLine();
            } while (!isValidFlightType(flightType));

            String paymentMethod;
            do {
                System.out.println("\t+-------------------------+");
                System.out.println("\t|    Payment Method       |");
                System.out.println("\t+-------------------------+");
                System.out.println("\t| 1. Gcash                |");
                System.out.println("\t| 2. BPI                  |");
                System.out.println("\t| 3. Paymaya              |");
                System.out.println("\t+-------------------------+");
                paymentMethod = scanner.nextLine();
            } while (!isValidPaymentMethod(paymentMethod));

            System.out.println("----------------------------------------------");
            System.out.printf("Subtotal: P %.2f%n", totalCost);

            double payment = 0;
            boolean valid = false;
            do {
                try{
                    System.out.print("PAYMENT: P ");
                    payment = scanner.nextDouble();
                    if (payment < totalCost) {
                        System.out.println("Insufficient Amount!");
                    }
                    else{
                        valid = true;
                    }
                }catch (Exception e){
                    System.out.println("Invalid input. Numbers only!");
                    scanner.nextLine();
                }

            } while (!valid);

            double change = payment - totalCost;
            System.out.println("Payment Successfully Please see the receipt!");
            System.out.print("\nCHANGE: P " + change + "0");
            System.out.println("\nThank you for your trust, " + customerName + "! have a great day.\n");


            displayReservationDetails(customerName, destination, travelDate, flightType, paymentMethod, totalCost, paymentAmount, changeAmount, getReservationTime(reservationTime));

            Reservation reservation = new Reservation(customerName, destination, travelDate, flightType, paymentMethod, totalCost, paymentAmount, changeAmount, getReservationTime(reservationTime));
            saveReservation(reservation);

            System.out.println("\nDo you want to make another reservation? (yes/no): ");
            scanner.nextLine();
        } while (scanner.nextLine().equalsIgnoreCase("yes"));

        System.out.println("Goodbye! Thank you for using the Travel Reservation System.");
    }

    private void displayAvailableDestinations() {
        System.out.println("\t+--------------------------------+");
        System.out.println("\t|     Available Destinations     |");
        System.out.println("\t+--------------------------------+");
        for (String destination : destinations) {
            System.out.println("\t| " + destination);
        }
        System.out.println("\t+--------------------------------+");
    }

    private boolean isValidDestination(String destination) {
        for (String dest : destinations) {
            if (dest.contains(destination))
                return true;
        }
        return false;
    }


    private boolean isValidDate(String date) {
        try {
            LocalDate.parse(date, dateFormatter);
            return true;
        } catch (Exception e) {
            System.out.println("Invalid date format. Please use DD-MM-YYYY.");
            return false;
        }
    }

    private boolean isValidFlightType(String flightType) {
        return flightType.equals("1") || flightType.equals("2") || flightType.equals("3");
    }

    private boolean isValidPaymentMethod(String paymentMethod) {
        return paymentMethod.equals("1") || paymentMethod.equals("2") || paymentMethod.equals("3");
    }

    private double calculateTotalCost(String destination) {
        for (String dest : destinations) {
            if (dest.contains(destination)) {
                String[] parts = dest.split("\\s+");
                return Double.parseDouble(parts[1].replace(",", ""));
            }
        }
        return 0.0;
    }

    private double makePayment(double totalCost, String destination) {
        Scanner scanner = new Scanner(System.in);
        double paymentAmount;
        String destinationPrice = getDestinationPrice(destination);
        do {
            System.out.println("Enter payment amount for " + destination + " (" + destinationPrice + "): ");
            while (!scanner.hasNextDouble()) {
                System.out.println("Invalid input. Please enter a valid amount.");
                scanner.next();
            }
            paymentAmount = scanner.nextDouble();
        } while (paymentAmount < totalCost);

        return paymentAmount;
    }


    private String getDestinationPrice(String destination) {
        for (String dest : destinations) {
            if (dest.contains(destination)) {
                String[] parts = dest.split("\\s+");
                return parts[1];
            }
        }
        return "";
    }

    private double calculateChangeAmount(double totalCost, double paymentAmount) {
        return paymentAmount - totalCost;
    }

    private void displayReservationDetails(String customerName, String destination, String travelDate, String flightType, String paymentMethod, double totalCost, double paymentAmount, double changeAmount, String reservationTime) {
        String destinationPrice = getDestinationPrice(destination);
        System.out.println("|*************************************************************|");
        System.out.println("|                Reservation Details                          |");
        System.out.println("|*************************************************************|");
        System.out.println("| Customer Name:      " + customerName);
        System.out.println("| Destination:        " + destination + " (" + destinationPrice + " php)");
        System.out.println("| Travel Date:        " + travelDate);
        System.out.println("| Flight Type:        " + getFlightType(flightType));
        System.out.println("| Payment Method:     " + getPaymentMethod(paymentMethod));
        System.out.println("| Reservation Time:   " + reservationTime);
        System.out.println("| Total Cost:         " + totalCost + " php");
        System.out.println("| Payment Amount:     " + paymentAmount + " php");
        System.out.println("| Change Amount:      " + changeAmount + " php");
        System.out.println("|*************************************************************|");

        if (paymentAmount < totalCost) {
            System.out.println("Insufficient amount. Please pay accordingly.");
            return;
        }

        try {
            generateReservationReceipt(customerName, destination, travelDate, getFlightType(flightType), getPaymentMethod(paymentMethod), totalCost, paymentAmount, changeAmount, reservationTime);
        } catch (IOException e) {
            System.out.println("Failed to generate reservation receipt.");
        }
    }

    private String getFlightType(String flightType) {
        switch (flightType) {
            case "1":
                return "Economy";
            case "2":
                return "Business";
            case "3":
                return "First Class";
            default:
                return "";
        }
    }

    private String getPaymentMethod(String paymentMethod) {
        switch (paymentMethod) {
            case "1":
                return "Gcash";
            case "2":
                return "BPI";
            case "3":
                return "Paymaya";
            default:
                return "";
        }
    }

    public static void generateReservationReceipt(String customerName, String destination, String travelDate, String flightType, String paymentMethod, double totalCost, double paymentAmount, double changeAmount, String reservationTime) throws IOException {
        FileWriter fileWriter = new FileWriter("ReservationReceipt.txt");
        fileWriter.write("|*************************************************************|\n");
        fileWriter.write("|                     RESERVATION RECEIPT                     |\n");
        fileWriter.write("|*************************************************************|\n");
        fileWriter.write("Customer Name:      " + customerName + "\n");
        fileWriter.write("Destination:        " + destination + "\n");
        fileWriter.write("Travel Date:        " + travelDate + "\n");
        fileWriter.write("Flight Type:        " + flightType + "\n");
        fileWriter.write("Payment Method:     " + paymentMethod + "\n");
        fileWriter.write("Reservation Time:   " + reservationTime + "\n");
        fileWriter.write("Total Cost:         " + totalCost + " php\n");
        fileWriter.write("Payment Amount:     " + paymentAmount + " php\n");
        fileWriter.write("Change Amount:      " + changeAmount + " php\n");
        fileWriter.write("|*************************************************************|\n");
        fileWriter.close();
    }

    private void saveReservation(Reservation reservation) {
        reservations.add(reservation);
        if (reservations.size() > 1) {
            reservations.remove(0);
        }
    }

    private boolean isValidReservationTime(String reservationTime) {
        return reservationTime.equals("1") || reservationTime.equals("2") || reservationTime.equals("3") || reservationTime.equals("4");
    }

    private String getReservationTime(String reservationTime) {
        switch (reservationTime) {
            case "1":
                return "DAWN";
            case "2":
                return "AM";
            case "3":
                return "PM";
            case "4":
                return "MIDNIGHT";
            default:
                return "";
        }
    }

    private static class Reservation {
        private String customerName;
        private String destination;
        private String travelDate;
        private String flightType;
        private String paymentMethod;
        private double totalCost;
        private double paymentAmount;
        private double changeAmount;
        private String reservationTime;

        public Reservation(String customerName, String destination, String travelDate, String flightType, String paymentMethod, double totalCost, double paymentAmount, double changeAmount, String reservationTime) {
            this.customerName = customerName;
            this.destination = destination;
            this.travelDate = travelDate;
            this.flightType = flightType;
            this.paymentMethod = paymentMethod;
            this.totalCost = totalCost;
            this.paymentAmount = paymentAmount;
            this.changeAmount = changeAmount;
            this.reservationTime = reservationTime;
        }

        // Getters and setters
    }
} fix the error in 117 123 123

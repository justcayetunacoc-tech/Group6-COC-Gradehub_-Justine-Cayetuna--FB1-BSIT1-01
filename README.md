# Group6-COC-Gradehub_-Justine-Cayetuna--FB1-BSIT1-01
HCI research
import java.util.*;

public class GradeSystem {

    static Scanner sc = new Scanner(System.in);

    // Student class
    static class Student {
        String user, pass;
        double project = 0, quiz = 0, assignment = 0, behavior = 0, exam = 0;
        boolean requested = false;

        Student(String u, String p) { user = u; pass = p; }
    }

    // Teacher class
    static class Teacher {
        String user, pass;
        Teacher(String u, String p) { user = u; pass = p; }
    }

    static HashMap<String, Student> students = new HashMap<>();
    static HashMap<String, ArrayList<String>> comments = new HashMap<>();
    static Teacher teacher;
    static HashSet<String> gradeRequests = new HashSet<>();

    // Validate grade input
    static double getValidGrade(String name) {
        double grade = -1;
        while (true) {
            System.out.print(name + " (0-100): ");
            String input = sc.nextLine().trim();
            try {
                grade = Double.parseDouble(input);
                if (grade < 0 || grade > 100) {
                    System.out.println("Grade must be between 0 and 100. Try again.");
                    continue;
                }
                break;
            } catch (NumberFormatException e) {
                System.out.println("Invalid input! Enter a number between 0 and 100.");
            }
        }
        return grade;
    }

    // Validate menu choice
    static int getValidMenuChoice(int min, int max) {
        int choice = -1;
        while (true) {
            String input = sc.nextLine().trim();
            try {
                choice = Integer.parseInt(input);
                if (choice < min || choice > max) {
                    System.out.println("Invalid choice! Enter a number between " + min + " and " + max);
                    continue;
                }
                break;
            } catch (NumberFormatException e) {
                System.out.println("Invalid input! Enter a number.");
            }
        }
        return choice;
    }

    public static void main(String[] args) {
        // User setup
        students.put("Regodon", new Student("Regodon", "02-2526-021093"));
        students.put("Sumahon", new Student("Sumahon", "02-2526-029290"));
        teacher = new Teacher("Teacher", "1234");

        //Log in System
        while (true) {
            System.out.println("\n==== LOGIN ====");
            System.out.print("Username: ");
            String username = sc.nextLine();
            System.out.print("Password: ");
            String password = sc.nextLine();

            if (students.containsKey(username) && students.get(username).pass.equals(password)) {
                System.out.println("Welcome, Student " + username + "!");
                studentMenu(students.get(username));
            } 
            else if (username.equals(teacher.user) && password.equals(teacher.pass)) {
                System.out.println("Welcome, Teacher!");
                teacherMenu();
            } 
            else {
                System.out.println("Invalid credentials. Try again.\n");
            }
        }
    }

    // STUDENT MENU
    static void studentMenu(Student s) {
        while (true) {
            System.out.println("\n--- Student Menu ---");
            System.out.println("1. View Grades");
            System.out.println("2. Request Grade");
            System.out.println("3. Cancel Grade Request");
            System.out.println("4. Comments & Feedback");
            System.out.println("5. Logout");
            System.out.print("Choose an option: ");

            // Pang validate para dili molahos sa 1-5 nga options
            int choice = getValidMenuChoice(1, 5);

            //E display ang grade sa cs ug finalgrade
            if (choice == 1) {
                double cs = s.project*0.4 + s.quiz*0.3 + s.assignment*0.2 + s.behavior*0.1;
                double finalGrade = cs*0.5 + s.exam*0.5;

                System.out.println("Project: " + s.project);
                System.out.println("Quiz: " + s.quiz);
                System.out.println("Assignment: " + s.assignment);
                System.out.println("Behavior: " + s.behavior);
                System.out.println("Exam: " + s.exam);
                System.out.println("Class Standing: " + cs + " Status: " + (cs >= 50 ? "Passed" : "Not Yet Passed"));
                System.out.println("Final Grade: " + finalGrade + " Status: " + (finalGrade >= 50 ? "Passed" : "Not Yet Passed"));
            }

            // E request ang grade, pero kung mana dili na ma duplicate ang request
            else if (choice == 2) {
                if (s.requested) {
                    System.out.println("You have already requested your grade.");
                } else {
                    gradeRequests.add(s.user);
                    s.requested = true;
                    System.out.println("Grade request submitted.");
                }
            }

            // E cancel ang grade request, pero kung wala pa dili ma cancel para dili malibog ang student
            else if (choice == 3) {
                if (!s.requested) {
                    System.out.println("You have no active grade request.");
                } else {
                    gradeRequests.remove(s.user);
                    s.requested = false;
                    System.out.println("Grade request cancelled.");
                }
            }

            // E comment ang student, pero kung wala pa ang comment kay ipakita nga wala pa,
            // pero kung naa na ipakita ang comment, ug pwede pud mag add ug comment
            else if (choice == 4) {
                comments.putIfAbsent(s.user, new ArrayList<>());
                System.out.println("\n--- Comments & Feedback ---");

            // Ipakita ang existing comments sa student, pero kung wala pa ipakita nga wala pay comments
                ArrayList<String> stuComments = comments.get(s.user);
                if (stuComments.isEmpty()) {
                    System.out.println("No comments yet.");
                } else {
                    for (String c : stuComments) {
                        System.out.println("- " + c);
                    }
                }

                System.out.print("Enter a comment (or leave blank): ");
                String comment = sc.nextLine().trim();
                if (!comment.isEmpty()) {
                    stuComments.add(s.user + ": " + comment);
                    System.out.println("Comment submitted.");
                }
            }

            //Log out sa student
            else if (choice == 5) {
                System.out.println("Logging out...");
                break;
            }
        }
    }

    // TEACHER MENU
    static void teacherMenu() {
        while (true) {
            System.out.println("\n--- Teacher Menu ---");
            System.out.println("1. View All Grades");
            System.out.println("2. Edit Student Grades");
            System.out.println("3. View Grade Requests");
            System.out.println("4. Clear All Requests");
            System.out.println("5. Comments & Feedback");
            System.out.println("6. Logout");
            System.out.print("Choose an option: ");

            // Validation para dili molahos sa 1-6 nga options
            int choice = getValidMenuChoice(1, 6);

            // Ipakita ang tanang grades sa students
            if (choice == 1) {
                for (Student s : students.values()) {
                    double cs = s.project*0.4 + s.quiz*0.3 + s.assignment*0.2 + s.behavior*0.1;
                    double finalGrade = cs*0.5 + s.exam*0.5;

                    System.out.println("\nStudent: " + s.user);
                    System.out.println("Project: " + s.project);
                    System.out.println("Quiz: " + s.quiz); 
                    System.out.println("Assignment: " + s.assignment);
                    System.out.println("Behavior: " + s.behavior);
                    System.out.println("Exam: " + s.exam);
                    System.out.println("Class Standing: " + cs);
                    System.out.println("Final Grade: " + finalGrade);
                }
            }

            // I edit ang grade sa student, pero naay confirmation para dili ma edit by accident,
            // ug naay navigation para dili ma loop balik sa same student
            // ang navigation kay after ma edit ang grade, naay option kung gusto ba mag balik sa student list or mag balik sa teacher menu
            else if (choice == 2) {
                ArrayList<Student> studentList = new ArrayList<>(students.values());
                boolean stayInList = true;

                // mag loop sa student list hangtod mag choose ang teacher nga mag balik sa teacher menu, 
                // pero kung mag choose nga mag balik sa student list, 
                // mag balik siya sa student list para makapili ug lain student
                // Maoy ang condition stayInList
                while (stayInList) {
                    System.out.println("\n--- Select Student ---");
                    for (int i = 0; i < studentList.size(); i++) {
                        Student s = studentList.get(i);
                        System.out.println((i + 1) + ". " + s.user + (s.requested ? " (Requested)" : ""));
                    }
                    System.out.println((studentList.size() + 1) + ". Back to Teacher Menu");
                    System.out.print("Your choice: ");
                    int pick = getValidMenuChoice(1, studentList.size() + 1);
                    if (pick == studentList.size() + 1) {
                        System.out.println("Returning to Teacher Menu.");
                        break;
                    }

                    Student s = studentList.get(pick - 1);
                    System.out.println("\nSelected: " + s.user);
                    System.out.println("1. Proceed Editing");
                    System.out.println("2. Go back to Student List");
                    System.out.print("Your choice: ");
                    int confirm = getValidMenuChoice(1, 2);
                    if (confirm == 2) {
                        System.out.println("Returning to Student List.");
                        continue;
                    }

                    System.out.println("\nEditing: " + s.user);
                    s.project = getValidGrade("Project");
                    s.quiz = getValidGrade("Quiz");
                    s.assignment = getValidGrade("Assignment");
                    s.behavior = getValidGrade("Behavior");
                    s.exam = getValidGrade("Exam");
                    s.requested = false;
                    gradeRequests.remove(s.user);
                    System.out.println("Grades updated.");

                    System.out.println("\nChoose next action: ");
                    System.out.println("1. Back to Student List");
                    System.out.println("2. Back to Teacher Menu");
                    System.out.print("Your choice: ");
                    int nextAction = getValidMenuChoice(1, 2);
                    if (nextAction == 2) stayInList = false;
                    System.out.println();
                }
            }

            // VIEW GRADE REQUESTS
            else if (choice == 3) {
                if (gradeRequests.isEmpty()) {
                    System.out.println("");
                    System.out.println("No grade requests.");
                } else {
                    System.out.println("");
                    System.out.println("Grade Requests:");
                    for (String r : gradeRequests) {
                        System.out.println("- " + r);
                    }
                }
            }

            // Clear all grade requests, pero naay confirmation para dili ma clear by accident
            else if (choice == 4) {
                System.out.println("");
                System.out.print("Are you sure you want to clear all grade requests? (Yes/No): ");
                String confirm = sc.nextLine().trim().toLowerCase();
                if (confirm.equals("yes")) {
                    gradeRequests.clear();
                    for (Student s : students.values()) s.requested = false;
                    System.out.println("All grade requests cleared.");
                } else {
                    System.out.println("Action Cancelled.");
                }
            }

            // 
            else if (choice == 5) {
                ArrayList<Student> studentList = new ArrayList<>(students.values());
                boolean stayInComments = true;

                // Loop with Condition stayInComments para mag navigate sa student list ug teacher menu,
                while (stayInComments) {
                    System.out.println("\n--- Select Student ---");
                    for (int i = 0; i < studentList.size(); i++) {
                        Student s = studentList.get(i);
                        System.out.println((i + 1) + ". " + s.user);
                    }
                    System.out.println((studentList.size() + 1) + ". Back to Teacher Menu");
                    System.out.print("Your choice: ");
                    int pick = getValidMenuChoice(1, studentList.size() + 1);
                    if (pick == studentList.size() + 1) break;

                    Student s = studentList.get(pick - 1);
                    comments.putIfAbsent(s.user, new ArrayList<>());
                    System.out.println("\nExisting comments for " + s.user + ":");

                    ArrayList<String> stuComments = comments.get(s.user);
                    if (stuComments.isEmpty()) System.out.println("No comments yet.");
                    else for (String c : stuComments) System.out.println("- " + c);

                    System.out.print("Enter your comment (or leave blank to cancel): ");
                    String comment = sc.nextLine().trim();
                    if (!comment.isEmpty()) {
                        comments.get(s.user).add(teacher.user + ": " + comment);
                        System.out.println("Comment submitted for " + s.user);
                    }

                    // Navigation para mag balik sa student list or teacher menu
                    System.out.println("\nChoose next action:");
                    System.out.println("1. Back to Student List");
                    System.out.println("2. Back to Teacher Menu");
                    System.out.print("Your choice: ");
                    int nextAction = getValidMenuChoice(1, 2);
                    if (nextAction == 2) stayInComments = false;
                    System.out.println();
                }
            }

            //Log out sa teacher
            else if (choice == 6) {
                System.out.println("Logging out...");
                break;
            }
        }
    }
}

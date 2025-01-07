import csv

# File names
STUDENT_FILE = "students.csv"
COURSE_FILE = "courses.csv"
ENROLLMENT_FILE = "enrollment.csv"

# Initialize CSV files if they don't exist
def initialize_files():
    for file_name, headers in [
        (STUDENT_FILE, ["StudentID", "Name"]),
        (COURSE_FILE, ["CourseID", "CourseName", "CreditHours"]),
        (ENROLLMENT_FILE, ["StudentID", "CourseID", "Grade"]),
    ]:
        try:
            with open(file_name, "x", newline="") as file:
                writer = csv.writer(file)
                writer.writerow(headers)
        except FileExistsError:
            pass

# Add a student
def add_student():
    student_id = input("Enter Student ID: ")
    name = input("Enter Student Name: ")
    with open(STUDENT_FILE, "a", newline="") as file:
        writer = csv.writer(file)
        writer.writerow([student_id, name])
    print("Student added successfully!")

# Add a course
def add_course():
    course_id = input("Enter Course ID: ")
    course_name = input("Enter Course Name: ")
    credit_hours = int(input("Enter Credit Hours: "))
    with open(COURSE_FILE, "a", newline="") as file:
        writer = csv.writer(file)
        writer.writerow([course_id, course_name, credit_hours])
    print("Course added successfully!")

# Enroll a student in a course
def enroll_student():
    student_id = input("Enter Student ID: ")
    course_id = input("Enter Course ID: ")
    grade = input("Enter Grade (leave blank if not yet graded): ")
    with open(ENROLLMENT_FILE, "a", newline="") as file:
        writer = csv.writer(file)
        writer.writerow([student_id, course_id, grade])
    print("Enrollment recorded successfully!")

# Record grade for a student
def record_grade():
    student_id = input("Enter Student ID: ")
    course_id = input("Enter Course ID: ")
    grade = input("Enter Grade: ")
    
    rows = []
    updated = False
    with open(ENROLLMENT_FILE, "r") as file:
        reader = csv.reader(file)
        rows = list(reader)
    
    for row in rows:
        if row[0] == student_id and row[1] == course_id:
            row[2] = grade
            updated = True
            break
    
    if updated:
        with open(ENROLLMENT_FILE, "w", newline="") as file:
            writer = csv.writer(file)
            writer.writerows(rows)
        print("Grade recorded successfully!")
    else:
        print("Enrollment not found.")

# Calculate GPA
def calculate_gpa(student_id):
    with open(COURSE_FILE, "r") as file:
        courses = {row[0]: int(row[2]) for row in csv.reader(file)}
    
    total_points = 0
    total_credits = 0
    
    with open(ENROLLMENT_FILE, "r") as file:
        for row in csv.reader(file):
            if row[0] == student_id and row[2]:
                grade = row[2].upper()
                points = {
                    "A": 4.0,
                    "B": 3.0,
                    "C": 2.0,
                    "D": 1.0,
                    "F": 0.0,
                }.get(grade, 0.0)
                credit_hours = courses[row[1]]
                total_points += points * credit_hours
                total_credits += credit_hours
    
    if total_credits == 0:
        return 0.0
    return total_points / total_credits

# View student details
def view_student():
    student_id = input("Enter Student ID: ")
    
    with open(STUDENT_FILE, "r") as file:
        students = {row[0]: row[1] for row in csv.reader(file)}
    
    if student_id not in students:
        print("Student not found.")
        return
    
    print(f"Student Name: {students[student_id]}")
    print("Courses:")
    
    with open(COURSE_FILE, "r") as file:
        courses = {row[0]: row[1] for row in csv.reader(file)}
    
    with open(ENROLLMENT_FILE, "r") as file:
        for row in csv.reader(file):
            if row[0] == student_id:
                course_name = courses.get(row[1], "Unknown")
                grade = row[2] if row[2] else "Not graded"
                print(f"- {course_name}: {grade}")
    
    gpa = calculate_gpa(student_id)
    print(f"GPA: {gpa:.2f}")

# Main menu
def main():
    initialize_files()
    while True:
        print("\n=== Bong County Technical College ===")
        print("1. Add Student")
        print("2. Add Course")
        print("3. Enroll Student")
        print("4. Record Grade")
        print("5. View Student Details")
        print("6. Exit")
        choice = input("Choose an option: ")
        
        if choice == "1":
            add_student()
        elif choice == "2":
            add_course()
        elif choice == "3":
            enroll_student()
        elif choice == "4":
            record_grade()
        elif choice == "5":
            view_student()
        elif choice == "6":
            print("Exiting program.")
            break
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    main()

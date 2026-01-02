import sqlite3
from datetime import datetime

# ---------------- DATABASE SETUP ---------------- #

def connect_db():
    conn = sqlite3.connect("hospital.db")
    cur = conn.cursor()

    cur.execute("""
    CREATE TABLE IF NOT EXISTS patients (
        patient_id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT,
        age INTEGER,
        gender TEXT,
        contact TEXT
    )""")

    cur.execute("""
    CREATE TABLE IF NOT EXISTS doctors (
        doctor_id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT,
        specialization TEXT
    )""")

    cur.execute("""
    CREATE TABLE IF NOT EXISTS appointments (
        appointment_id INTEGER PRIMARY KEY AUTOINCREMENT,
        patient_id INTEGER,
        doctor_id INTEGER,
        date TEXT,
        notes TEXT
    )""")

    cur.execute("""
    CREATE TABLE IF NOT EXISTS prescriptions (
        prescription_id INTEGER PRIMARY KEY AUTOINCREMENT,
        appointment_id INTEGER,
        medicines TEXT,
        instructions TEXT
    )""")

    cur.execute("""
    CREATE TABLE IF NOT EXISTS billing (
        bill_id INTEGER PRIMARY KEY AUTOINCREMENT,
        patient_id INTEGER,
        amount REAL,
        status TEXT
    )""")

    conn.commit()
    return conn

# ---------------- PATIENT FUNCTIONS ---------------- #

def add_patient(conn):
    name = input("Patient Name: ")
    age = int(input("Age: "))
    gender = input("Gender: ")
    contact = input("Contact: ")

    conn.execute(
        "INSERT INTO patients (name, age, gender, contact) VALUES (?,?,?,?)",
        (name, age, gender, contact)
    )
    conn.commit()
    print("Patient added successfully")


def view_patients(conn):
    cur = conn.cursor()
    cur.execute("SELECT * FROM patients")
    for row in cur.fetchall():
        print(row)

# ---------------- DOCTOR FUNCTIONS ---------------- #

def add_doctor(conn):
    name = input("Doctor Name: ")
    specialization = input("Specialization: ")
    conn.execute(
        "INSERT INTO doctors (name, specialization) VALUES (?,?)",
        (name, specialization)
    )
    conn.commit()
    print("Doctor added successfully")


def view_doctors(conn):
    cur = conn.cursor()
    cur.execute("SELECT * FROM doctors")
    for row in cur.fetchall():
        print(row)

# ---------------- APPOINTMENT FUNCTIONS ---------------- #

def book_appointment(conn):
    patient_id = int(input("Patient ID: "))
    doctor_id = int(input("Doctor ID: "))
    date = input("Appointment Date (YYYY-MM-DD): ")
    notes = input("Notes: ")

    conn.execute(
        "INSERT INTO appointments (patient_id, doctor_id, date, notes) VALUES (?,?,?,?)",
        (patient_id, doctor_id, date, notes)
    )
    conn.commit()
    print("Appointment booked")


def view_appointments(conn):
    cur = conn.cursor()
    cur.execute("""
        SELECT a.appointment_id, p.name, d.name, a.date, a.notes
        FROM appointments a
        JOIN patients p ON a.patient_id = p.patient_id
        JOIN doctors d ON a.doctor_id = d.doctor_id
    """)
    for row in cur.fetchall():
        print(row)

# ---------------- PRESCRIPTION FUNCTIONS ---------------- #

def add_prescription(conn):
    appointment_id = int(input("Appointment ID: "))
    medicines = input("Medicines: ")
    instructions = input("Instructions: ")

    conn.execute(
        "INSERT INTO prescriptions (appointment_id, medicines, instructions) VALUES (?,?,?)",
        (appointment_id, medicines, instructions)
    )
    conn.commit()
    print("Prescription added")


def view_prescriptions(conn):
    cur = conn.cursor()
    cur.execute("SELECT * FROM prescriptions")
    for row in cur.fetchall():
        print(row)

# ---------------- BILLING FUNCTIONS ---------------- #

def generate_bill(conn):
    patient_id = int(input("Patient ID: "))
    amount = float(input("Amount: "))
    status = "Unpaid"

    conn.execute(
        "INSERT INTO billing (patient_id, amount, status) VALUES (?,?,?)",
        (patient_id, amount, status)
    )
    conn.commit()
    print("Bill generated")


def view_bills(conn):
    cur = conn.cursor()
    cur.execute("""
        SELECT b.bill_id, p.name, b.amount, b.status
        FROM billing b
        JOIN patients p ON b.patient_id = p.patient_id
    """)
    for row in cur.fetchall():
        print(row)

# ---------------- MAIN MENU ---------------- #

def main():
    conn = connect_db()

    while True:
        print("\n--- Hospital Management System ---")
        print("1. Add Patient")
        print("2. View Patients")
        print("3. Add Doctor")
        print("4. View Doctors")
        print("5. Book Appointment")
        print("6. View Appointments")
        print("7. Add Prescription")
        print("8. View Prescriptions")
        print("9. Generate Bill")
        print("10. View Bills")
        print("0. Exit")

        choice = input("Enter choice: ")

        if choice == "1": add_patient(conn)
        elif choice == "2": view_patients(conn)
        elif choice == "3": add_doctor(conn)
        elif choice == "4": view_doctors(conn)
        elif choice == "5": book_appointment(conn)
        elif choice == "6": view_appointments(conn)
        elif choice == "7": add_prescription(conn)
        elif choice == "8": view_prescriptions(conn)
        elif choice == "9": generate_bill(conn)
        elif choice == "10": view_bills(conn)
        elif choice == "0":
            print("Thank you")
            break
        else:
            print("Invalid choice")

    conn.close()


if __name__ == "__main__":
    main()

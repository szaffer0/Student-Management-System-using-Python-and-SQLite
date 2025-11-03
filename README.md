# Student-Management-System-using-Python-and-SQLite
📘 Project Features  ✅ Add New Student ✅ Update Student Details ✅ Delete Student Record ✅ Search Student by ID ✅ View All Students
# student_management_system.py
import tkinter as tk
from tkinter import ttk, messagebox
import sqlite3

# Database setup
def connect_db():
    con = sqlite3.connect("students.db")
    cur = con.cursor()
    cur.execute("""CREATE TABLE IF NOT EXISTS student (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT,
                roll TEXT,
                course TEXT,
                gender TEXT,
                contact TEXT
            )""")
    con.commit()
    con.close()

# Add new student
def add_student():
    if name_var.get() == "" or roll_var.get() == "" or course_var.get() == "":
        messagebox.showerror("Error", "All fields are required!")
        return

    con = sqlite3.connect("students.db")
    cur = con.cursor()
    cur.execute("INSERT INTO student (name, roll, course, gender, contact) VALUES (?, ?, ?, ?, ?)",
                (name_var.get(), roll_var.get(), course_var.get(), gender_var.get(), contact_var.get()))
    con.commit()
    con.close()
    fetch_students()
    clear_fields()
    messagebox.showinfo("Success", "Student added successfully!")

# Fetch all students
def fetch_students():
    con = sqlite3.connect("students.db")
    cur = con.cursor()
    cur.execute("SELECT * FROM student")
    rows = cur.fetchall()
    student_table.delete(*student_table.get_children())
    for row in rows:
        student_table.insert("", tk.END, values=row)
    con.close()

# Clear input fields
def clear_fields():
    name_var.set("")
    roll_var.set("")
    course_var.set("")
    gender_var.set("")
    contact_var.set("")

# Get selected student
def get_selected(event):
    selected = student_table.focus()
    values = student_table.item(selected, 'values')
    if values:
        id_var.set(values[0])
        name_var.set(values[1])
        roll_var.set(values[2])
        course_var.set(values[3])
        gender_var.set(values[4])
        contact_var.set(values[5])

# Update student record
def update_student():
    if id_var.get() == "":
        messagebox.showerror("Error", "Select a record to update!")
        return
    con = sqlite3.connect("students.db")
    cur = con.cursor()
    cur.execute("""UPDATE student SET name=?, roll=?, course=?, gender=?, contact=? WHERE id=?""",
                (name_var.get(), roll_var.get(), course_var.get(), gender_var.get(), contact_var.get(), id_var.get()))
    con.commit()
    con.close()
    fetch_students()
    clear_fields()
    messagebox.showinfo("Success", "Student updated successfully!")

# Delete student
def delete_student():
    if id_var.get() == "":
        messagebox.showerror("Error", "Select a record to delete!")
        return
    con = sqlite3.connect("students.db")
    cur = con.cursor()
    cur.execute("DELETE FROM student WHERE id=?", (id_var.get(),))
    con.commit()
    con.close()
    fetch_students()
    clear_fields()
    messagebox.showinfo("Success", "Student deleted successfully!")

# Search student by roll number
def search_student():
    con = sqlite3.connect("students.db")
    cur = con.cursor()
    cur.execute("SELECT * FROM student WHERE roll=?", (roll_var.get(),))
    rows = cur.fetchall()
    student_table.delete(*student_table.get_children())
    for row in rows:
        student_table.insert("", tk.END, values=row)
    con.close()

# GUI setup
root = tk.Tk()
root.title("🎓 Student Management System")
root.geometry("900x600")
root.config(bg="#f0f4f7")

# Variables
id_var = tk.StringVar()
name_var = tk.StringVar()
roll_var = tk.StringVar()
course_var = tk.StringVar()
gender_var = tk.StringVar()
contact_var = tk.StringVar()

# Title
title = tk.Label(root, text="Student Management System", font=("Arial", 20, "bold"), bg="#0047AB", fg="white", pady=10)
title.pack(fill=tk.X)

# Frame for form
form_frame = tk.Frame(root, bg="white", padx=20, pady=20)
form_frame.place(x=30, y=80, width=400, height=400)

tk.Label(form_frame, text="Name:", font=("Arial", 12, "bold"), bg="white").grid(row=0, column=0, sticky="w", pady=5)
tk.Entry(form_frame, textvariable=name_var, font=("Arial", 12), width=25).grid(row=0, column=1, pady=5)

tk.Label(form_frame, text="Roll No:", font=("Arial", 12, "bold"), bg="white").grid(row=1, column=0, sticky="w", pady=5)
tk.Entry(form_frame, textvariable=roll_var, font=("Arial", 12), width=25).grid(row=1, column=1, pady=5)

tk.Label(form_frame, text="Course:", font=("Arial", 12, "bold"), bg="white").grid(row=2, column=0, sticky="w", pady=5)
tk.Entry(form_frame, textvariable=course_var, font=("Arial", 12), width=25).grid(row=2, column=1, pady=5)

tk.Label(form_frame, text="Gender:", font=("Arial", 12, "bold"), bg="white").grid(row=3, column=0, sticky="w", pady=5)
ttk.Combobox(form_frame, textvariable=gender_var, values=["Male", "Female", "Other"], font=("Arial", 12), width=23).grid(row=3, column=1, pady=5)

tk.Label(form_frame, text="Contact:", font=("Arial", 12, "bold"), bg="white").grid(row=4, column=0, sticky="w", pady=5)
tk.Entry(form_frame, textvariable=contact_var, font=("Arial", 12), width=25).grid(row=4, column=1, pady=5)

# Buttons
btn_frame = tk.Frame(form_frame, bg="white")
btn_frame.grid(row=5, columnspan=2, pady=20)

tk.Button(btn_frame, text="Add", command=add_student, width=8, bg="#28a745", fg="white", font=("Arial", 11, "bold")).grid(row=0, column=0, padx=5)
tk.Button(btn_frame, text="Update", command=update_student, width=8, bg="#007bff", fg="white", font=("Arial", 11, "bold")).grid(row=0, column=1, padx=5)
tk.Button(btn_frame, text="Delete", command=delete_student, width=8, bg="#dc3545", fg="white", font=("Arial", 11, "bold")).grid(row=0, column=2, padx=5)
tk.Button(btn_frame, text="Clear", command=clear_fields, width=8, bg="#6c757d", fg="white", font=("Arial", 11, "bold")).grid(row=0, column=3, padx=5)
tk.Button(btn_frame, text="Search", command=search_student, width=8, bg="#ffc107", fg="black", font=("Arial", 11, "bold")).grid(row=0, column=4, padx=5)

# Frame for table
table_frame = tk.Frame(root, bg="white")
table_frame.place(x=460, y=80, width=420, height=400)

scroll_y = tk.Scrollbar(table_frame, orient=tk.VERTICAL)
student_table = ttk.Treeview(table_frame, columns=("id", "name", "roll", "course", "gender", "contact"), yscrollcommand=scroll_y.set)
scroll_y.pack(side=tk.RIGHT, fill=tk.Y)
scroll_y.config(command=student_table.yview)

student_table.heading("id", text="ID")
student_table.heading("name", text="Name")
student_table.heading("roll", text="Roll No")
student_table.heading("course", text="Course")
student_table.heading("gender", text="Gender")
student_table.heading("contact", text="Contact")

student_table["show"] = "headings"
student_table.column("id", width=40)
student_table.column("name", width=100)
student_table.column("roll", width=80)
student_table.column("course", width=80)
student_table.column("gender", width=70)
student_table.column("contact", width=100)

student_table.pack(fill=tk.BOTH, expand=1)
student_table.bind("<ButtonRelease-1>", get_selected)

connect_db()
fetch_students()

root.mainloop()


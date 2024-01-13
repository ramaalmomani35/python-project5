import tkinter as tk
from tkinter import filedialog, messagebox
class Employee:
    def __init__(self, emp_id, name, emp_type, hours ):
        self.emp_id = emp_id
        self.name = name
        self.type = emp_type
        self.hours = hours
        self.salary = self.calculate_salary()

    def calculate_salary(self):
        salary = 0
        commission = 0.05

        if self.type == 'full':
            if self.hours > 208:
                salary = self.hours * 2.4
            else:
                salary_full_commission = self.hours * 2.4 * commission
                salary = self.hours * 2.4 + salary_full_commission
        else:
            if self.hours > 130:
                salary = self.hours * 1.8
            else:
                salary_part_commission = self.hours * 1.8 * commission
                salary = self.hours * 1.8 + salary_part_commission

        return salary

def authenticate_login():
    entered_username = username_entry.get()
    entered_password = password_entry.get()

    file_path = "credentials.txt"
    with open(file_path, 'r') as file:
        username = file.readline().strip()
        password = file.readline().strip()

    if entered_username == username and entered_password == password:
        login_window.destroy()
        open_employee_calculator()
    else:
        login_window.withdraw()
        error_page()

def error_page():
    error_window = tk.Toplevel()
    error_window.title("Error Page")

    tk.Label(error_window, text="Invalid credentials. Please try again.").pack(pady=10)
    tk.Button(error_window, text="OK", command=close_error_page).pack(pady=10)

def close_error_page():
    login_window.deiconify()

def browse_file():
    file_path = filedialog.askopenfilename(filetypes=[("Text Files", "*.txt")])
    if file_path:
        read_file_content(file_path)

def read_file_content(file_path):
    try:
        with open(file_path, 'r') as file:
            content = file.readlines()
            employees = []
            for line in content:
                data = line.strip().split(',')
                emp_id, name, _, emp_type, hours = data
                hours = float(hours)
                employee = Employee(emp_id, name, emp_type, hours)
                employees.append(employee)

            display_employees(employees)
    except Exception as e:
        text.delete('1.0', tk.END)
        text.insert(tk.END, f"Error reading file: {str(e)}")
def display_employees(employees):
    employees_window = tk.Toplevel(root)
    employees_window.title("Employee Information")

    employees_text = tk.Text(employees_window, wrap=tk.WORD, width=50, height=15)
    employees_text.pack(padx=10, pady=10)

    employees_text.insert(tk.END, "Employee Information:\n\n")
    for employee in employees:
        employees_text.insert(tk.END, f"ID: {employee.emp_id}\n")
        employees_text.insert(tk.END, f"Name: {employee.name}\n")
        employees_text.insert(tk.END, f"Salary: ${employee.salary:.2f}\n\n")

def open_employee_calculator():
    global root
    root = tk.Tk()
    root.title("Employee Salary Calculator")

    browse_button = tk.Button(root, text="Browse", command=browse_file)
    browse_button.pack(pady=10)

    text = tk.Text(root, wrap=tk.WORD, width=50, height=15)
    text.pack(padx=10, pady=10)

    root.mainloop()

login_window = tk.Tk()
login_window.title("Login Page")

tk.Label(login_window, text="Username:").pack(pady=10)
username_entry = tk.Entry(login_window)
username_entry.pack(pady=10)

tk.Label(login_window, text="Password:").pack(pady=10)
password_entry = tk.Entry(login_window, show="*")
password_entry.pack(pady=10)

login_button = tk.Button(login_window, text="Login", command=authenticate_login)
login_button.pack(pady=10)
login_window.mainloop()

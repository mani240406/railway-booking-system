# railway-booking-system
A simple Railway Ticket Booking System built using Python (Tkinter GUI). This project demonstrates how Greedy Algorithms, Queue (FIFO), and Hashing can be applied in real-world problems like seat allocation in railways.
[Railway.py](https://github.com/user-attachments/files/22692247/Railway.py)
import tkinter as tk
from tkinter import messagebox
from collections import deque

class RailwayBooking:
    def __init__(self):
        self.seats = {"Lower": 2, "Middle": 2, "Upper": 1}
        self.booked = {}
        self.waiting = deque()

    def book_ticket(self, name, preference):
        if not name:
            return "⚠️ Name cannot be empty!"
        if self.seats.get(preference, 0) > 0:
            self.seats[preference] -= 1
            self.booked[name] = preference
            return f"✅ {name} got {preference} berth."
        elif any(v > 0 for v in self.seats.values()):
            for berth, count in self.seats.items():
                if count > 0:
                    self.seats[berth] -= 1
                    self.booked[name] = berth
                    return f"⚡ {name}'s preference not available. Allotted {berth} berth."
        else:
            self.waiting.append(name)
            return f"⏳ No seats left. {name} added to Waiting List."

    def cancel_ticket(self, name):
        if name in self.booked:
            freed_berth = self.booked.pop(name)
            msg = f"❌ {name}'s {freed_berth} berth cancelled."
            if self.waiting:
                next_passenger = self.waiting.popleft()
                self.booked[next_passenger] = freed_berth
                msg += f"\n✅ {next_passenger} from Waiting List got {freed_berth} berth."
            else:
                self.seats[freed_berth] += 1
            return msg
        else:
            return f"⚠️ No booking found for {name}."

    def status(self):
        return (f"📌 Current Status:\n\n"
                f"Booked: {self.booked}\n\n"
                f"Available Seats: {self.seats}\n\n"
                f"Waiting List: {list(self.waiting)}")


# -------- GUI Implementation --------
def book():
    name = name_entry.get()
    pref = pref_var.get()
    result = system.book_ticket(name, pref)
    messagebox.showinfo("Booking Result", result)
    update_status()

def cancel():
    name = name_entry.get()
    result = system.cancel_ticket(name)
    messagebox.showinfo("Cancellation", result)
    update_status()

def update_status():
    status_text.set(system.status())


# Initialize booking system
system = RailwayBooking()

# Create Tkinter window
root = tk.Tk()
root.title("🚆 Railway Ticket Booking System")
root.geometry("600x500")
root.config(bg="#f0f8ff")  # light background

# Title Label
title = tk.Label(root, text="🚆 Railway Ticket Booking System 🚆",
                 font=("Helvetica", 18, "bold"), bg="#4682b4", fg="white", pady=10)
title.pack(fill="x")

# Passenger Name
frame1 = tk.Frame(root, bg="#f0f8ff")
frame1.pack(pady=10)
tk.Label(frame1, text="Passenger Name:", font=("Arial", 12), bg="#f0f8ff").grid(row=0, column=0, padx=5)
name_entry = tk.Entry(frame1, font=("Arial", 12), width=20)
name_entry.grid(row=0, column=1)

# Berth Preference
frame2 = tk.Frame(root, bg="#f0f8ff")
frame2.pack(pady=10)
tk.Label(frame2, text="Berth Preference:", font=("Arial", 12), bg="#f0f8ff").grid(row=0, column=0, padx=5)
pref_var = tk.StringVar(value="Lower")
pref_menu = tk.OptionMenu(frame2, pref_var, "Lower", "Middle", "Upper")
pref_menu.config(font=("Arial", 12), width=15, bg="#e6e6fa")
pref_menu.grid(row=0, column=1)

# Buttons
button_frame = tk.Frame(root, bg="#f0f8ff")
button_frame.pack(pady=15)

tk.Button(button_frame, text="🎟️ Book Ticket", command=book, bg="#32cd32",
          fg="white", font=("Arial", 12, "bold"), width=15).grid(row=0, column=0, padx=10)

tk.Button(button_frame, text="❌ Cancel Ticket", command=cancel, bg="#ff6347",
          fg="white", font=("Arial", 12, "bold"), width=15).grid(row=0, column=1, padx=10)

# Status Display
status_text = tk.StringVar()
status_label = tk.Label(root, textvariable=status_text, justify="left",
                        font=("Courier New", 11), bg="#ffffe0", fg="black",
                        relief="solid", bd=1, padx=10, pady=10, width=65, height=12, anchor="nw")
status_label.pack(pady=20)

# Initialize status
update_status()

# Run GUI
root.mainloop()

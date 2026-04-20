# PDA-CEP-
Built an E-commerce dashboard using Tkinter and Matplotlib to manage orders, analyze customer spending, and segment users. Features include real-time updates, data visualization, and interactive UI for tracking sales, frequency, and customer categories efficiently.
import tkinter as tk
from tkinter import ttk, messagebox
import matplotlib.pyplot as plt

orders = [
    {"customerId": "C1", "amount": 1000, "date": "2024-04-01"},
    {"customerId": "C2", "amount": 500, "date": "2024-04-03"}
]

# --- Functions ---
def add_order():
    cid = cid_entry.get()
    amount = amount_entry.get()
    date = date_entry.get()

    if not cid or not amount or not date:
        messagebox.showerror("Error", "Enter all fields")
        return

    orders.append({
        "customerId": cid,
        "amount": float(amount),
        "date": date
    })

    cid_entry.delete(0, tk.END)
    amount_entry.delete(0, tk.END)
    date_entry.delete(0, tk.END)

    render()

def render():
    total = sum(o["amount"] for o in orders)
    total_label.config(text=f"Total: ₹{total}")
    orders_label.config(text=f"Orders: {len(orders)}")

    customers = {}
    for o in orders:
        cid = o["customerId"]
        if cid not in customers:
            customers[cid] = {"total": 0, "freq": 0}
        customers[cid]["total"] += o["amount"]
        customers[cid]["freq"] += 1

    for row in tree.get_children():
        tree.delete(row)

    labels = []
    values = []

    for cid, data in customers.items():
        total = data["total"]

        if total > 5000:
            segment = "High"
        elif total > 2000:
            segment = "Medium"
        else:
            segment = "Low"

        tree.insert("", "end", values=(cid, total, data["freq"], segment))

        labels.append(cid)
        values.append(total)

    global chart_data
    chart_data = (labels, values)

def show_chart():
    labels, values = chart_data
    plt.bar(labels, values)
    plt.title("Customer Spend")
    plt.xlabel("Customer ID")
    plt.ylabel("Total Spend")
    plt.show()

# --- UI ---
root = tk.Tk()
root.title("E-commerce Dashboard")
root.geometry("700x550")
root.configure(bg="#eef2f7")

# Header
header = tk.Label(root, text="E-commerce Dashboard", bg="#1e293b", fg="white",
                  font=("Arial", 18, "bold"), pady=10)
header.pack(fill="x")

# Card Frame function
def create_card(parent):
    frame = tk.Frame(parent, bg="white", bd=0)
    frame.pack(padx=15, pady=10, fill="x")
    return frame

# --- Add Order Card ---
card1 = create_card(root)

tk.Label(card1, text="Add Order", bg="white", font=("Arial", 14, "bold")).pack()

cid_entry = tk.Entry(card1, width=25)
cid_entry.pack(pady=5)

amount_entry = tk.Entry(card1, width=25)
amount_entry.pack(pady=5)

date_entry = tk.Entry(card1, width=25)
date_entry.pack(pady=5)

tk.Button(card1, text="Add Order", bg="#2563eb", fg="white",
          command=add_order).pack(pady=10)

# --- Stats Card ---
card2 = create_card(root)

tk.Label(card2, text="Sales", bg="white", font=("Arial", 14, "bold")).pack()

total_label = tk.Label(card2, text="Total: 0", bg="white")
total_label.pack()

orders_label = tk.Label(card2, text="Orders: 0", bg="white")
orders_label.pack()

# --- Table Card ---
card3 = create_card(root)

tk.Label(card3, text="Customer Segmentation", bg="white",
         font=("Arial", 14, "bold")).pack()

tree = ttk.Treeview(card3, columns=("ID", "Total", "Freq", "Segment"), show="headings")

tree.heading("ID", text="Customer")
tree.heading("Total", text="Total")
tree.heading("Freq", text="Frequency")
tree.heading("Segment", text="Segment")

tree.pack(fill="both", expand=True)

# --- Chart Button ---
tk.Button(root, text="Show Chart", bg="#2563eb", fg="white",
          command=show_chart).pack(pady=10)

chart_data = ([], [])

render()

root.mainloop()

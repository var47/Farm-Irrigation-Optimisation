# DAA Project: Irrigation system using MST
import time
import networkx as nx
import matplotlib.pyplot as plt
import tkinter as tk
from tkinter import messagebox

class UnionFind:
    def _init_(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x]) 
        return self.parent[x]
    
    def union(self, x, y):
        rootX = self.find(x)
        rootY = self.find(y)
        
        if rootX != rootY:
            if self.rank[rootX] > self.rank[rootY]:
                self.parent[rootY] = rootX
            elif self.rank[rootX] < self.rank[rootY]:
                self.parent[rootX] = rootY
            else:
                self.parent[rootY] = rootX
                self.rank[rootX] += 1
            return True
        return False

class IrrigationNetwork:
    def _init_(self, nodes, water_usage_per_meter, cost_per_meter):
        self.nodes = nodes  
        self.edges = []  
        self.water_usage_per_meter = water_usage_per_meter 
        self.cost_per_meter = cost_per_meter  
    
    def add_edge(self, u, v, cost):
        self.edges.append((cost, u, v))  
    
    def kruskal(self):
        self.edges.sort() 
        
        uf = UnionFind(len(self.nodes))
        mst = []  
        mst_cost = 0 
        
        for cost, u, v in self.edges:
            if uf.union(u, v):
                mst.append((self.nodes[u], self.nodes[v], cost))
                mst_cost += cost
        
        return mst, mst_cost

    def calculate_water_loss(self, loss_percentage):
        total_loss = 0
        for cost, _, _ in self.edges:
            total_loss += cost * loss_percentage / 100  
        return total_loss

    def calculate_water_loss_mst(self, mst, loss_percentage):
        total_loss = 0
        for _, _, cost in mst:
            total_loss += cost * loss_percentage / 100  
        return total_loss

    def calculate_total_water_usage(self):
        total_usage = 0
        for cost, _, _ in self.edges:
            total_usage += cost * self.water_usage_per_meter  
        return total_usage

    def calculate_total_cost(self):
        total_cost = 0
        for cost, _, _ in self.edges:
            total_cost += cost * self.cost_per_meter  
        return total_cost

def plot_network(nodes, edges, title="Irrigation Network"):
    """Function to plot the network as a graph using NetworkX."""
    G = nx.Graph()
    
    for node in nodes:
        G.add_node(node)
    
    for _, u, v in edges:
        G.add_edge(nodes[u], nodes[v])
    
    pos = nx.spring_layout(G)  
    nx.draw(G, pos, with_labels=True, node_color='lightblue', node_size=2000, font_size=10, font_weight='bold', edge_color='gray')
    labels = nx.get_edge_attributes(G, 'weight')
    nx.draw_networkx_edge_labels(G, pos, edge_labels=labels)
    plt.title(title)
    plt.show()

def plot_mst(nodes, mst, title="Optimized Irrigation Network (MST)"):
    """Function to plot the Minimum Spanning Tree (MST)."""
    G = nx.Graph()
    
    for node in nodes:
        G.add_node(node)
    
    for u, v, cost in mst:
        G.add_edge(u, v, weight=cost)
    
    pos = nx.spring_layout(G)  
    nx.draw(G, pos, with_labels=True, node_color='lightgreen', node_size=2000, font_size=10, font_weight='bold', edge_color='darkgreen')
    labels = nx.get_edge_attributes(G, 'weight')
    nx.draw_networkx_edge_labels(G, pos, edge_labels=labels)
    plt.title(title)
    plt.show()

def submit_input():
    """Function to handle form submission and execute the logic."""
    try:
        num_nodes = int(entry_num_nodes.get())
        nodes = [entry_nodes[i].get() for i in range(num_nodes)]
        
        water_usage_per_meter = float(entry_water_usage.get())
        cost_per_meter = float(entry_cost_per_meter.get())
        
        network = IrrigationNetwork(nodes, water_usage_per_meter, cost_per_meter)
        
        num_edges = int(entry_num_edges.get())
        
        for edge in edges:
            node1 = edge[0].get()
            node2 = edge[1].get()
            length = int(edge[2].get())
            if node1 in nodes and node2 in nodes:
                u = nodes.index(node1)
                v = nodes.index(node2)
                network.add_edge(u, v, length)
        
        mst, mst_cost = network.kruskal()
        loss_percentage = 1 
        total_water_loss = network.calculate_water_loss(loss_percentage)
        total_water_loss_mst = network.calculate_water_loss_mst(mst, loss_percentage)

        water_saved = total_water_loss - total_water_loss_mst
        total_water_usage = network.calculate_total_water_usage()
        total_cost = network.calculate_total_cost()
        money_saved = total_water_usage - total_cost

        result_text = f"Optimized Irrigation Network (Minimum Spanning Tree):\n"
        for u, v, cost in mst:
            result_text += f"{u} <-> {v} with pipeline length {cost} meters\n"

        result_text += f"\nTotal pipeline length for the irrigation network: {mst_cost} meters\n"
        result_text += f"Total water usage in the original network: {total_water_usage:.2f} liters\n"
        result_text += f"Total cost in the original network: {total_cost:.2f} currency units\n"
        result_text += f"Total water loss in the original network: {total_water_loss:.2f} liters\n"
        result_text += f"Total water loss in the optimized network (MST): {total_water_loss_mst:.2f} liters\n"
        result_text += f"Total water saved by optimization: {water_saved:.2f} liters\n"
        result_text += f"Total water usage cost in the optimized network (MST): {total_water_usage:.2f} liters\n"
        result_text += f"Total cost in the optimized network (MST): {total_cost:.2f} Rupees\n"
        result_text += f"Total money saved using the optimizes network (MST): {money_saved: .2f} Rupees\n"

        result_label.config(text=result_text)

        plot_network(nodes, network.edges, title="Original Irrigation Network")
        plot_mst(nodes, mst, title="Optimized Irrigation Network (MST)")

    except Exception as e:
        messagebox.showerror("Input Error", f"An error occurred: {e}")

def add_edge_fields():
    """Function to add fields dynamically for each edge."""
    row = len(edges)
    if row >= num_edges:
        messagebox.showwarning("Max Edges", f"You can only add {num_edges} edges.")
        return
    
    edge_entry_frame = tk.Frame(scrollable_frame)
    edge_entry_frame.pack(pady=5)

    node1_label = tk.Label(edge_entry_frame, text="Node 1:")
    node1_label.grid(row=0, column=0)
    node1_entry = tk.Entry(edge_entry_frame)
    node1_entry.grid(row=0, column=1)
    
    node2_label = tk.Label(edge_entry_frame, text="Node 2:")
    node2_label.grid(row=0, column=2)
    node2_entry = tk.Entry(edge_entry_frame)
    node2_entry.grid(row=0, column=3)

    length_label = tk.Label(edge_entry_frame, text="Length (meters):")
    length_label.grid(row=0, column=4)
    length_entry = tk.Entry(edge_entry_frame)
    length_entry.grid(row=0, column=5)
    
    edges.append([node1_entry, node2_entry, length_entry])

    # Disable the button if we reach the max number of edges
    if len(edges) >= num_edges:
        add_edge_button.config(state=tk.DISABLED)

def reset_page():
    """Reset the entire page back to the basics."""
    for widget in scrollable_frame.winfo_children():
        widget.destroy()

    # Recreate the initial components (form fields and buttons)
    setup_page()

def setup_page():
    global entry_num_nodes, entry_nodes, entry_water_usage, entry_cost_per_meter, entry_num_edges, edges, num_edges, add_edge_button, result_label, scrollable_frame

    # User input form
    tk.Label(scrollable_frame, text="Enter the number of nodes:").pack(pady=5)
    entry_num_nodes = tk.Entry(scrollable_frame)
    entry_num_nodes.pack(pady=5)

    tk.Label(scrollable_frame, text="Enter node names:").pack(pady=5)
    entry_nodes = []
    nodes_frame = tk.Frame(scrollable_frame)
    nodes_frame.pack(pady=5)
    
    def update_nodes():
        num_nodes = int(entry_num_nodes.get())
        for widget in nodes_frame.winfo_children():
            widget.destroy()

        for i in range(num_nodes):
            node_label = tk.Label(nodes_frame, text=f"Node {i+1}:")
            node_label.grid(row=i, column=0)
            node_entry = tk.Entry(nodes_frame)
            node_entry.grid(row=i, column=1)
            entry_nodes.append(node_entry)

    entry_num_nodes.bind("<FocusOut>", lambda e: update_nodes())

    tk.Label(scrollable_frame, text="Enter water usage per meter (in liters):").pack(pady=5)
    entry_water_usage = tk.Entry(scrollable_frame)
    entry_water_usage.pack(pady=5)

    tk.Label(scrollable_frame, text="Enter cost per meter of pipeline (in Rupees):").pack(pady=5)
    entry_cost_per_meter = tk.Entry(scrollable_frame)
    entry_cost_per_meter.pack(pady=5)

    tk.Label(scrollable_frame, text="Enter the number of edges:").pack(pady=5)
    entry_num_edges = tk.Entry(scrollable_frame)
    entry_num_edges.pack(pady=5)
    
    edges = []
    num_edges = 0
    
    def set_edge_count():
        global num_edges
        try:
            num_edges = int(entry_num_edges.get())
        except ValueError:
            messagebox.showerror("Invalid Input", "Please enter a valid number for edges.")
    
    tk.Button(scrollable_frame, text="Set Edge Count", command=set_edge_count).pack(pady=5)
    add_edge_button = tk.Button(scrollable_frame, text="Add Edge", command=add_edge_fields)
    add_edge_button.pack(pady=5)

    submit_button = tk.Button(scrollable_frame, text="Submit", command=submit_input)
    submit_button.pack(pady=20)

    reset_button = tk.Button(scrollable_frame, text="Reset", command=reset_page)
    reset_button.pack(pady=5)

    result_label = tk.Label(scrollable_frame, text="", justify=tk.LEFT)
    result_label.pack(pady=20)

# Main execution
root = tk.Tk()
root.title("Irrigation Network Optimization")

# Create the canvas and scrollbar
canvas = tk.Canvas(root)
canvas.pack(side="left", fill="both", expand=True)

scrollbar = tk.Scrollbar(root, orient="vertical", command=canvas.yview)
scrollbar.pack(side="right", fill="y")

canvas.configure(yscrollcommand=scrollbar.set)

# Create a frame inside the canvas for the content
scrollable_frame = tk.Frame(canvas)
scrollable_frame.pack(fill="both", expand=True)

canvas.create_window((0, 0), window=scrollable_frame, anchor="nw")

# Update scroll region
scrollable_frame.bind(
    "<Configure>", lambda e: canvas.configure(scrollregion=canvas.bbox("all"))
)

setup_page()

root.mainloop()

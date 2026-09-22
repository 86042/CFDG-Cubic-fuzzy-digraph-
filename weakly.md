import networkx as nx
import matplotlib.pyplot as plt

# Function to compute domination value
def vertex_cardinality(G, dom_set):
    return sum(((1 - G.nodes[n]['L']) + G.nodes[n]['U'] + G.nodes[n]['mu']) / 3 for n in dom_set)

# Build the Weakly Connected CFDG
G_wc = nx.DiGraph()
vertex_attrs = {
    'v1': (0.3, 0.7, 0.5),
    'v2': (0.2, 0.6, 0.4),
    'v3': (0.4, 0.8, 0.6),
    'v4': (0.3, 0.9, 0.7),
    'v5': (0.1, 0.5, 0.3)
}
for n, (L, U, mu) in vertex_attrs.items():
    G_wc.add_node(n, L=L, U=U, mu=mu)

edges = [
    ('v1', 'v2', (0.2, 0.6, 0.4)),
    ('v2', 'v3', (0.3, 0.7, 0.5)),
    ('v3', 'v1', (0.1, 0.5, 0.3)),
    ('v2', 'v4', (0.4, 0.8, 0.6)),
    ('v4', 'v5', (0.2, 0.6, 0.4))
]
for u, v, (L_e, U_e, mu_e) in edges:
    G_wc.add_edge(u, v, L=L_e, U=U_e, mu=mu_e)

# Verify weak connectivity
assert nx.is_weakly_connected(G_wc), "Graph is not weakly connected!"

# Use the specified dominating set
dom_set_wc = {'v5', 'v2', 'v1'}
dom_val_wc = vertex_cardinality(G_wc, dom_set_wc)

# Plot
pos = nx.spring_layout(G_wc, seed=42)
plt.figure(figsize=(7, 5))

# Highlight dominating nodes
colors = ['orange' if n in dom_set_wc else 'lightblue' for n in G_wc]
nx.draw(G_wc, pos, node_color=colors, node_size=700, arrows=True, with_labels=False)

# Bold node labels with fuzzy values
node_labels = {
    n: f"{n}\n($\\bf{{{G_wc.nodes[n]['L']}}}$, $\\bf{{{G_wc.nodes[n]['U']}}}$, $\\bf{{{G_wc.nodes[n]['mu']}}}$)"
    for n in G_wc
}
nx.draw_networkx_labels(G_wc, pos, labels=node_labels, font_size=8, font_weight='bold')

# Bold edge labels with fuzzy values
edge_labels = {
    (u, v): f"($\\bf{{{d['L']}}}$, $\\bf{{{d['U']}}}$, $\\bf{{{d['mu']}}}$)"
    for u, v, d in G_wc.edges(data=True)
}
nx.draw_networkx_edge_labels(G_wc, pos, edge_labels=edge_labels, font_size=7, font_weight='bold')

# Annotate domination value
plt.text(
    0.98, 0.98,
    fr"$\bf{{Domination = {dom_val_wc:.4f}}}$",
    transform=plt.gca().transAxes,
    ha='right', va='top',
    bbox=dict(boxstyle="round,pad=0.3", facecolor="white", alpha=0.8),
    fontsize=11
)

plt.title("Weakly Connected CFDG — Dominating Nodes Highlighted", fontsize=12, fontweight='bold')
plt.axis('off')
plt.tight_layout()
plt.show()

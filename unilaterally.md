import networkx as nx
import matplotlib.pyplot as plt

# Function to compute vertex cardinality (domination value)
def vertex_cardinality(G, dom_set):
    return sum(((1 - G.nodes[n]['L']) + G.nodes[n]['U'] + G.nodes[n]['mu']) / 3 for n in dom_set)

# Build unilaterally connected CFDG
G_uni = nx.DiGraph()
vertex_attrs = {
    'e1': (0.2, 0.7, 0.5),
    'e2': (0.2, 0.8, 0.4),
    'e3': (0.2, 0.7, 0.5),
    'e4': (0.3, 0.6, 0.4),
    'e5': (0.6, 0.4, 0.7),
    'e6': (0.2, 0.7, 0.5)
}

for n, (L, U, mu) in vertex_attrs.items():
    G_uni.add_node(n, L=L, U=U, mu=mu)

edges = [
    ('e1', 'e2', (0.2, 0.7, 0.4)),
    ('e2', 'e3', (0.2, 0.7, 0.4)),
    ('e3', 'e4', (0.2, 0.6, 0.4)),
    ('e4', 'e5', (0.3, 0.4, 0.4)),
    ('e5', 'e6', (0.2, 0.4, 0.5)),
    ('e6', 'e1', (0.2, 0.7, 0.5))
]

for u, v, (L_e, U_e, mu_e) in edges:
    G_uni.add_edge(u, v, L=L_e, U=U_e, mu=mu_e)

# Check unilateral connectivity
def is_unilaterally_connected(G):
    nodes = list(G)
    for i, u in enumerate(nodes):
        for v in nodes[i+1:]:
            if not (nx.has_path(G, u, v) or nx.has_path(G, v, u)):
                return False
    return True

assert is_unilaterally_connected(G_uni), "Graph is not unilaterally connected!"

# ✅ Manually verified dominating set for directed cycle
dom_set_uni = {'e1', 'e3', 'e5'}
dom_val_uni = vertex_cardinality(G_uni, dom_set_uni)

# Plotting
pos = nx.circular_layout(G_uni)
plt.figure(figsize=(7, 5))

# Highlight dominating nodes
node_colors = ['orange' if n in dom_set_uni else 'lightgreen' for n in G_uni]
nx.draw(G_uni, pos, with_labels=False, node_color=node_colors, node_size=700, arrows=True)

# Node labels in bold with fuzzy values
node_labels = {
    n: f"{n}\n($\\bf{{{G_uni.nodes[n]['L']}}}$, $\\bf{{{G_uni.nodes[n]['U']}}}$, $\\bf{{{G_uni.nodes[n]['mu']}}}$)"
    for n in G_uni
}
nx.draw_networkx_labels(G_uni, pos, labels=node_labels, font_size=8, font_weight='bold')

# Edge labels in bold with fuzzy values
edge_labels = {
    (u, v): f"($\\bf{{{d['L']}}}$, $\\bf{{{d['U']}}}$, $\\bf{{{d['mu']}}}$)"
    for u, v, d in G_uni.edges(data=True)
}
nx.draw_networkx_edge_labels(G_uni, pos, edge_labels=edge_labels, font_size=7, font_weight='bold')

# Annotate domination value
ax = plt.gca()
ax.text(
    0.98, 0.98,
    fr"$\bf{{Domination = {dom_val_uni:.3f}}}$",
    transform=ax.transAxes,
    horizontalalignment='right',
    verticalalignment='top',
    bbox=dict(boxstyle="round,pad=0.3", facecolor="white", alpha=0.8),
    fontsize=11
)

plt.title("Unilaterally Connected CFDG — Dominating Nodes Highlighted", fontsize=12, fontweight='bold')
plt.axis('off')
plt.tight_layout()
plt.show()

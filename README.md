# Network-analysis
Malign network analysis
import pandas as pd
import networkx as nx
import matplotlib.pyplot as plt

# Step 1: Sample Data Preparation (You can replace this with your collected data)
# Sample data representing user interactions on social media
data = {
    'user': ['User_A', 'User_B', 'User_C', 'User_D', 'User_E', 'User_F', 'User_G'],
    'influencer': ['User_A', 'User_A', 'User_B', 'User_C', 'User_A', 'User_D', 'User_B'],
    'interaction': ['retweet', 'mention', 'reply', 'retweet', 'retweet', 'mention', 'retweet'],
    'misinformation': [True, True, True, False, True, False, True]
}

df = pd.DataFrame(data)

# Step 2: Create the Network Graph
G = nx.DiGraph()  # Directed graph since interactions have direction

# Add edges between influencers and users based on misinformation interactions
for _, row in df.iterrows():
    if row['misinformation']:  # Focus only on misinformation interactions
        G.add_edge(row['influencer'], row['user'], interaction=row['interaction'])

# Step 3: Calculate Centrality Measures
degree_centrality = nx.degree_centrality(G)
betweenness_centrality = nx.betweenness_centrality(G)
pagerank = nx.pagerank(G)

# Assign centrality measures as node attributes
for node in G.nodes():
    G.nodes[node]['degree_centrality'] = degree_centrality.get(node, 0)
    G.nodes[node]['betweenness_centrality'] = betweenness_centrality.get(node, 0)
    G.nodes[node]['pagerank'] = pagerank.get(node, 0)

# Step 4: Visualization
plt.figure(figsize=(12, 8))

# Node size based on degree centrality
node_size = [1000 * G.nodes[node]['degree_centrality'] for node in G]

# Node color based on PageRank
node_color = [G.nodes[node]['pagerank'] for node in G]

pos = nx.spring_layout(G, k=0.5, seed=42)
nx.draw(G, pos, with_labels=True, node_size=node_size, node_color=node_color, cmap=plt.cm.viridis, 
        font_size=12, font_weight='bold', edge_color='grey', alpha=0.6, arrowsize=20)

# Adding color bar for PageRank
sm = plt.cm.ScalarMappable(cmap=plt.cm.viridis, 
                           norm=plt.Normalize(vmin=min(node_color), vmax=max(node_color)))
sm.set_array([])
plt.colorbar(sm, label='PageRank')

plt.title('Key Influencers in Misinformation Network')
plt.show()

# Global Air Transportation Network Analysis

This study applies graph-theoretic methods to analyse the global air transportation network comprising 3,214 airports and 66,771 scheduled flight records. The main aims are to:

- Characterise the network's topological structure
- Identify its most important airports
- Examine whether well-known theoretical properties of complex networks, such as heavy-tailed connectivity, small-world behaviour, and geographically structured community organisation, are present in this real-world system

## Data

The dataset represents a network of regularly scheduled flights among airports worldwide. The original data were obtained from [OpenFlights](https://openflights.org/) and subsequently processed into a format suitable for network analysis. The processed dataset is available through the [Netzschleuder network catalogue](https://networks.skewed.de/net/openflights).

The dataset consists of two tables:

- **`nodes.csv`:** 3,214 airports with identifying attributes (name, city, country, IATA/FAA and ICAO codes), geographic coordinates (latitude, longitude, altitude) and time zone information (UTC offset, daylight-saving-time rule)
- **`edges.csv`:** 66,771 flight records, one row per regularly occurring commercial flight by a particular airline from one airport to another, with source/target airport indices, great-circle distance, operating airline (name and code), a codeshare flag, aircraft equipment, and number of stops

Multiple edges can exist between the same pair of airports when several airlines operate the route, or when a single airline operates multiple flights on the same route each day.

## Approach

Three complementary graph representations were built from the raw data and analysed with standard network-science tools (python-igraph, NetworkX):

- **Directed multigraph:** one edge per flight record, preserving airline-level detail
- **Simplified directed graph:** parallel routes merged into unique routes
- **Undirected weighted graph:** for metrics defined on undirected networks

The analysis combines:

- Global and node-level descriptive statistics
- Centrality measures
- Maximum-likelihood distribution fitting
- Degree- and attribute-based assortativity analysis, supported by bootstrap confidence intervals and a degree-preserving rewiring null model
- Random-graph null-model comparisons
- Community detection with algorithm stability testing

## Key Findings

### 1. Network structure & connectivity
- The network is highly connected: **99.19%** of airports belong to a single giant component, with a short average path length (**~3.96 hops**) despite covering 3,214 airports worldwide.
- Very high reciprocity (**>0.97**) shows that flight routes are almost always bidirectional.
- Degree and strength distributions are heavy-tailed: most airports have few connections and low traffic volume, while a small number of major hubs have exceptionally high values.
- Maximum-likelihood analysis supports this heavy-tailed structure. However, likelihood-ratio tests show that the power law is significantly outperformed by the lognormal, truncated power-law, and stretched-exponential alternatives, so the network is **heavy-tailed but not best characterised as a pure power-law network**.

### 2. Hub dominance & airport importance
- Connectivity is heavily concentrated: the top **1%** of airports account for **28.5%** of all edges, rising to **84.5%** for the top 10%, which is clear quantitative evidence of hub-and-spoke organisation.
- Hub importance is multi-dimensional, not a single ranking. Different centrality measures identify different types of hubs:
  - **Strength / degree:** Atlanta and Amsterdam lead, respectively
  - **Eigenvector centrality:** Atlanta and Heathrow
  - **Harmonic closeness:** Frankfurt, Paris, and Amsterdam
  - **Betweenness:** Paris and Los Angeles emerge as key strategic bridges
- Being important under one metric does not guarantee importance under another:
  - **Anchorage:** despite modest traffic, it plays a disproportionately important bridging role (high betweenness)
  - **Atlanta:** despite ranking first in both strength and eigenvector centrality, it does not appear in the top ten by betweenness

### 3. Airline business models
- **U.S. major carriers (AA, UA, DL, US)** show hub-and-spoke signatures: large networks with low density, though part of this low density is a mechanical size effect rather than purely structural.
- **Ryanair, easyJet, and Southwest** show point-to-point signatures: smaller networks with both high density and high average degree. Ryanair operates the most routes of any carrier despite serving relatively fewer airports, so the point-to-point reading holds even after accounting for network size.

### 4. Small-world properties
- The network is a **genuine small-world network**: its clustering coefficient is roughly **67×** higher than that of an Erdős–Rényi random graph and about **3.2×** higher than that of a degree-matched configuration model, while the average path length increases only modestly by comparison.
- Even after controlling for the heterogeneous degree distribution, the small-world coefficient remains well above 1 (**σ ≈ 2.67**). Small-world structure is therefore an intrinsic property of the network, not merely an artefact of its skewed degree sequence.

### 5. Assortativity & geographic mixing
- The overall degree assortativity coefficient is close to zero, but the average-nearest-neighbour-degree function k_nn(k) shows that this does **not** reflect an absence of degree-based mixing. It conceals opposing tendencies at different degree scales: a dip at low degrees and a rise at mid degrees, which largely cancel out in the global average.
- Airports connect much more within the same country (assortativity **0.445**) and the same time zone (**0.906**) than random chance would predict.
- Geography and national boundaries are therefore the clearer, more straightforward organising factors of the network, while degree-based connectivity still plays a real but scale-dependent role.

### 6. Community structure
- **Louvain** was selected over Infomap, Walktrap, and Label Propagation because it provides the highest modularity while maintaining good stability and a relatively compact, interpretable community structure.
- It detected **17 communities** (Q ≈ 0.65). Although no geographic data was given to the algorithm, the communities map well onto real-world regions (North America, Europe, Asia-Pacific, South Asia/Middle East, East Asia, South America), strongly suggesting that geography drives the network's mesoscale organisation.
- Each community's top hub matches a real-world major airport (Atlanta, Paris CDG, Singapore, Dubai, Beijing, São Paulo), reinforcing the external validity of the result. This profile reflects a single run.

<H3> Name :Yuvabharathi.B</H3>
<H3>Register No.212222230181</H3>
<H3> Experiment 1</H3>
<H3>DATE:</H3>
<H1 ALIGN=CENTER> Implementation of Bayesian Networks</H1>

## Aim :
To create a bayesian Network for the given dataset in Python
## Algorithm:
Step 1:Import necessary libraries: pandas, networkx, matplotlib.pyplot, Bbn, Edge, EdgeType, BbnNode, Variable, EvidenceBuilder, InferenceController<br/>
Step 2:Set pandas options to display more columns<br/>
Step 3:Read in weather data from a CSV file using pandas<br/>
Step 4:Remove records where the target variable RainTomorrow has missing values<br/>
Step 5:Fill in missing values in other columns with the column mean<br/>
Step 6:Create bands for variables that will be used in the model (Humidity9amCat, Humidity3pmCat, and WindGustSpeedCat)<br/>
Step 7:Define a function to calculate probability distributions, which go into the Bayesian Belief Network (BBN)<br/>
Step 8:Create BbnNode objects for Humidity9amCat, Humidity3pmCat, WindGustSpeedCat, and RainTomorrow, using the probs() function to calculate their probabilities<br/>
Step 9:Create a Bbn object and add the BbnNode objects to it, along with edges between the nodes<br/>
Step 10:Convert the BBN to a join tree using the InferenceController<br/>
Step 11:Set node positions for the graph<br/>
Step 12:Set options for the graph appearance<br/>
Step 13:Generate the graph using networkx<br/>
Step 14:Update margins and display the graph using matplotlib.pyplot<br/>

## Program:
#### Import the needed Libaries:
```
import networkx as nx
import pandas as pd
import matplotlib.pyplot as plt
from pybbn.graph.dag import Bbn
from pybbn.graph.dag import Edge,EdgeType
from pybbn.graph.jointree import EvidenceBuilder
from pybbn.graph.node import BbnNode
from pybbn.graph.variable import Variable
from pybbn.pptc.inferencecontroller import InferenceController
pd.options.display.max_columns=50
```
#### Read the Dataset:
```
df=pd.read_csv('weatherAUS.csv',encoding='utf-8')
df=df[pd.isnull(df['RainTomorrow'])==False]
```
#### Filling in missing values:
```
df=df.fillna(df.mean())
```
#### Bands for variables:
```
df['WindGustSpeedCat']=df['WindGustSpeed'].apply(lambda x: '0.<=40'   if x<=40 else '1.40-50' if 40<x<=50 else '2.>50')
df['Pressure9amCat']=df['Pressure9am'].apply(lambda x: '1.>1000' if x>1000 else '0.<=1000')
df['Pressure3pmCat']=df['Pressure3pm'].apply(lambda x: '1.>1000' if x>1000 else '0.<=1000')
print(df)
```
#### Function helps to calculate probability distribution:
```
def probs(data, child, parent1=None, parent2=None):
    if parent1==None:
        # Calculate probabilities
        prob=pd.crosstab(data[child], 'Empty', margins=False, normalize='columns').sort_index().to_numpy().reshape(-1).tolist()
    elif parent1!=None:
            # Check if child node has 1 parent or 2 parents
            if parent2==None:
                # Caclucate probabilities
                prob=pd.crosstab(data[parent1],data[child], margins=False, normalize='index').sort_index().to_numpy().reshape(-1).tolist()
            else:
                # Caclucate probabilities
                prob=pd.crosstab([data[parent1],data[parent2]],data[child], margins=False, normalize='index').sort_index().to_numpy().reshape(-1).tolist()
    else: print("Error in Probability Frequency Calculations")
    return prob
```
#### Function to Automatically Calculate Probabilities:
```
P9am = BbnNode(Variable(0, 'P9am', ['<=1000', '>1000']), probs(df, child='Pressure9amCat'))
P3pm = BbnNode(Variable(1, 'P3pm', ['<=1000', '>1000']), probs(df, child='Pressure3pmCat', parent1='Pressure9amCat'))
W = BbnNode(Variable(2, 'W', ['<=40', '40-50', '>50']), probs(df, child='WindGustSpeedCat'))
RT = BbnNode(Variable(3, 'RT', ['No', 'Yes']), probs(df, child='RainTomorrow', parent1='Pressure3pmCat', parent2='WindGustSpeedCat'))
```
#### Network:
```
bbn = Bbn() \
    .add_node(P9am) \
    .add_node(P3pm) \
    .add_node(W) \
    .add_node(RT) \
    .add_edge(Edge(P9am, P3pm, EdgeType.DIRECTED)) \
    .add_edge(Edge(P3pm, RT, EdgeType.DIRECTED)) \
    .add_edge(Edge(W, RT, EdgeType.DIRECTED))
```
#### BBN to Join tree:
```
join_tree = InferenceController.apply(bbn)
```
#### Setting node Positions:
```
pos={0: (-1,2), 1: (-1,0.5), 2: (1,0.5), 3:(0,-1)}
```
#### Setting Options for Graph looks:
```
options = {
    "font_size": 16,
    "node_size": 5000,
    "node_color": "#82DEFF",
    "edgecolors": "#82A0FF",
    "edge_color": "black",
    "linewidths": 5,
    "width": 5,}

```
#### Graph Generation:
```
n, d = bbn.to_nx_graph()
nx.draw(n, with_labels=True, labels=d, pos=pos, **options)
```
#### Margin and printing Graph:
```
ax = plt.gca()
ax.margins(0.10)
plt.axis("off")
plt.show()
```
## Output:
#### Bands for variables:
![image](https://github.com/shalini-venkatesan/Ex1-AAI/assets/118720291/900e6160-3859-4dc8-8675-0a4b8faa80df)

![image](https://github.com/shalini-venkatesan/Ex1-AAI/assets/118720291/323ea7fc-bfa5-46b7-863e-0e822d8a6c00)

#### Graph:

![image](https://github.com/shalini-venkatesan/Ex1-AAI/assets/118720291/49c58799-e7e2-4987-bf0b-c390ee5249dd)

## Result:
Thus a Bayesian Network is generated using Python

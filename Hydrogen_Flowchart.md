# Hydrogen Simulation Flowchart
Below is a flowchart to illustrate how the script in [`Simulate_Hydrogen_Production.ipynb`](Scripts/Simulate_Hydrogen_Production.ipynb) simulates the production of hydrogen. 
```mermaid
flowchart TD
    A(["Start"]) --> B{"Electrolyser has been off for one hour or was on before"}
    B -- Yes --> C["Sufficient power for the electrolyser?"]
    C -- Yes --> n1["Power electrolyser"]
    n3["Possible to charge battery and remain within constraints?"] -- Yes --> n4["Charge battery"]
    n3 -- No --> n5["Waste energy"]
    B -- No --> n6["Battery?"]
    n6 -- Yes --> n3
    n1 --> n6
    C -- No --> n7["Battery?"]
    n7 -- Yes --> n8["Provides sufficient charge to power electrolyser?"]
    n7 -- No --> n3
    n8 -- No --> n3
    n6 -- No --> n5
    n8 -- Yes --> n9["Discharge battery"]
    n9 --> n1
    C@{ shape: diam}
    n1@{ shape: rounded}
    n3@{ shape: rect}
    n4@{ shape: rounded}
    n5@{ shape: rounded}
    n6@{ shape: diam}
    n7@{ shape: diam}
    n8@{ shape: diam}
    n9@{ shape: rounded}
```
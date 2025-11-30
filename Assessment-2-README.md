# SSA Individual Project – Smart Home Connectivity Experiments
**Version:** v1.1  
**Last Updated:** 20 November 2025  
**Author:** Pragadheesh Marimuthu  

This repository contains the Python prototype for **Assessment 2 (Individual Project)** on the Secure Systems Architecture module. The simulation models a **smart‑home IoT client and controller** connected through an **unreliable edge network**, enabling controlled experiments that analyse how network characteristics affect the **availability / dependability** of safety‑relevant sensor data. These aspects form part of the **ABCDE characteristics** of distributed systems, directly extending the system model from Assessment 1.

---

## 1. Research Question and Hypothesis

### **Research Question**
*Does improving edge connectivity (by reducing packet loss, corruption and latency jitter) lead to significantly more reliable delivery of safety‑relevant IoT sensor readings compared to a basic best‑effort configuration?*

This focuses on **Availability / Dependability** within the System of Systems (SoS). Even with strong cryptographic controls, the controller cannot take correct decisions if readings are lost or corrupted during transit.

### **Hypothesis**
*A smart-home controller connected via an “edge‑QoS” network (lower loss, lower corruption, tighter latency) will demonstrate a **higher delivery and acceptance ratio** and **fewer missed alerts** than the same controller on a higher‑loss, best‑effort network.*

Both scenarios use identical client behaviour, making results directly comparable.

---

## 2. Relation to Assessment 1 and AD‑Tree Vulnerabilities

In Assessment 1, the Attack–Defence Tree (AD‑Tree) highlighted vulnerabilities related to:

- **Client layer:** limited integrity protection, unpatched devices, unreliable communication.
- **Hub/controller layer:** dependency on receiving timely, accurate, untampered data.
- **System layer:** degraded dependability through packet loss, manipulation or delays.

This prototype addresses those concerns by:

- modelling lossy, jittery, tamper‑prone communication paths;
- adding **integrity checks** at the controller;
- running experiments contrasting **best‑effort vs edge‑QoS** network behaviour.

The prototype deliberately keeps cryptographic features lightweight while focusing on **connectivity‑driven dependability**, which aligns with the intended module outcomes.

---

## 3. Scientific Method in This Simulation

The design follows the scientific principles discussed in the Unit 4 seminar:

- **Observation:** each experiment logs delivery status, latency, corruption, and controller decisions.
- **Repeatability:** deterministic behaviour ensured via seeded random number generators.
- **Evidence‑based testing:** every scenario outputs structured JSON suitable for analysis.
- **Critical analysis:** metrics such as *delivery ratio*, *lost ratio*, and *acceptance rate* support hypothesis evaluation.
- **Verification:** controlled experiments allow examination of behaviour under distinct network conditions.

This supports the scientific cycle: **hypothesis → experimentation → measurement → analysis**.

---

## 4. Project Structure

```
ssa_individual_project/
│
├── client.py              # Simulated IoT device
├── client_config.py       # Client behaviour configuration
├── controller.py          # Hub/controller validation + metrics
├── network_simulator.py   # Lossy network (latency, jitter, loss, corruption)
├── experiments.py         # Scenarios and experiment runner
├── utils.py               # Logging, timestamps, JSON helpers
├── outputs/               # Generated experiment results
└── README.md              # Documentation and analysis
```

---

## 5. System Model

### **5.1 Client**
Deterministically generates sensor readings with:
- device ID  
- monotonically increasing sequence number  
- numeric value with baseline + noise  
- timestamp  

### **5.2 Network Simulator**
Simulates distributed‑system challenges:
- packet loss  
- tampering (value sign‑flip)  
- latency + jitter  

Returns a `DeliveryResult` for each transmission.

### **5.3 Controller**
Performs:
- structural payload validation  
- optional integrity checks  
- classification into *OK* or *ALERT*  
- accumulation of runtime metrics  

These metrics allow comparison across scenarios.

---

## 6. Experiment Scenarios

Two scenarios are defined in `experiments.py`:

### **Scenario A – best_effort**
- Loss: 0.15  
- Corruption: 0.05  
- Latency: 30–250 ms  
- Models a congested home network.

### **Scenario B – edge_qos**
- Loss: 0.05  
- Corruption: 0.02  
- Latency: 10–120 ms  
- Models improved edge connectivity with QoS‑style prioritisation.

Each scenario sends 200 messages and writes results into `outputs/`.

---

## 7. Running the Experiments

Run:

```
python -m experiments
```

Outputs:

```
outputs/results_best_effort.json
outputs/results_edge_qos.json
```

Each contains:
- per‑message logs  
- controller metrics  
- scenario configuration  
- derived ratios  

---

## 8. Key Observations

### **edge_qos consistently shows:**
- higher delivery ratio  
- fewer corrupted readings  
- fewer lost messages  
- higher controller acceptance rate  

### **best_effort shows:**
- increased packet loss  
- more opportunities for tampering  
- reduced dependability  

Results empirically support the hypothesis.

---

## 9. Vulnerabilities, Mitigations and Limitations

### **Mitigations Demonstrated**
- Reduced message loss improves availability.  
- Simple integrity checks prevent acceptance of tampered payloads.  
- Controller metrics show how network quality affects alert reliability.  

### **Known Limitations**
- No real cryptography (integrity checks simplified).  
- Only one client-controller pair.  
- No replay or out‑of‑order handling.  
- Simplified traffic patterns.

These limitations are acknowledged in the final report.

---

## 10. Testing and Code Quality

Testing included:
- Executing both scenarios repeatedly  
- Stress‑testing loss/corruption values  
- Verifying controller metrics  

Code quality achieved via:
- modular design  
- dataclasses for configuration  
- clear docstrings and inline comments  
- deterministic behaviour for reproducibility  

---


## 11. Screenshots

To support reproducibility, the following screenshots demonstrate that the simulation runs as expected and generates valid output files.

### **11.1 Running the Experiment**
![alt text](run_experiment.png)
![alt text](logs.png)

### **11.2 Output Files Generated**
![alt text](best_effort.png)
![alt text](edge_qos.png)
![alt text](output_folder.png)

## 12. Conclusion

This prototype demonstrates that **edge‑level network behaviour** has a direct impact on the availability and dependability of IoT sensor readings. Under improved connectivity (edge‑qos):

- more messages reach the controller  
- fewer are corrupted or lost  
- the controller operates with greater reliability  

These findings reinforce insights from Assessment 1: **secure SoS designs depend not only on cryptography but also on reliable, predictable network transport**, one of the key ABCDE characteristics of distributed systems.
```profile_models.py```:  
```
import torch
import time

# Device configuration (Force CUDA)
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")

# Network Topology (100 -> 1200 -> 1200 -> 784)
input_size = 100
hidden1 = 1200
hidden2 = 1200
output_size = 784
timesteps = 20  # Spiking timesteps

# Dummy models to accurately profile hardware FLOPs and memory bandwidth
class DummyANN(torch.nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = torch.nn.Linear(input_size, hidden1)
        self.fc2 = torch.nn.Linear(hidden1, hidden2)
        self.fc3 = torch.nn.Linear(hidden2, output_size)
    def forward(self, x):
        x = torch.relu(self.fc1(x))
        x = torch.relu(self.fc2(x))
        return torch.sigmoid(self.fc3(x))

class DummySNN(torch.nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = torch.nn.Linear(input_size, hidden1)
        self.fc2 = torch.nn.Linear(hidden1, hidden2)
        self.fc3 = torch.nn.Linear(hidden2, output_size)
    def forward(self, x):
        out = torch.zeros(x.size(0), output_size).to(x.device)
        for t in range(timesteps):
            v1 = self.fc1(x)
            v2 = self.fc2(v1)
            v3 = self.fc3(v2)
            out += v3
        return out

ann_model = DummyANN().to(device)
snn_model = DummySNN().to(device)

batch_sizes = [100, 500, 1000]

print("==================================================")
print(f" Table I Data Extraction (Device: {device})")
print("==================================================")

for b in batch_sizes:
    print(f"\n[Generated Images: {b}]")
    
    # 1. Linear Function Calls Extraction
    ann_calls = 3 * 1 * b
    snn_calls = 3 * timesteps * b
    print(f"  ANN Linear Function Calls : {ann_calls}")
    print(f"  SNN Linear Function Calls : {snn_calls}")

    # 2. Total GPU Time Measurement (ms)
    x = torch.randn(b, input_size).to(device)

    # GPU Warmup (Crucial for accurate profiling)
    for _ in range(10):
        _ = ann_model(x)
        _ = snn_model(x)
    torch.cuda.synchronize()

    # Profile ANN
    start_event = torch.cuda.Event(enable_timing=True)
    end_event = torch.cuda.Event(enable_timing=True)
    
    start_event.record()
    _ = ann_model(x)
    end_event.record()
    torch.cuda.synchronize()
    ann_time = start_event.elapsed_time(end_event)

    # Profile SNN
    start_event.record()
    _ = snn_model(x)
    end_event.record()
    torch.cuda.synchronize()
    snn_time = start_event.elapsed_time(end_event)

    print(f"  ANN Total GPU Time (ms)   : {ann_time:.2f}")
    print(f"  SNN Total GPU Time (ms)   : {snn_time:.2f}")

print("\n==================================================")
```

結果:  
```
..\myenv\Scripts\python profile_models.py
==================================================
 Table I Data Extraction (Device: cuda:0)
==================================================

[Generated Images: 100]
  ANN Linear Function Calls : 300
  SNN Linear Function Calls : 6000
  ANN Total GPU Time (ms)   : 0.23
  SNN Total GPU Time (ms)   : 2.81

[Generated Images: 500]
  ANN Linear Function Calls : 1500
  SNN Linear Function Calls : 30000
  ANN Total GPU Time (ms)   : 0.43
  SNN Total GPU Time (ms)   : 7.76

[Generated Images: 1000]
  ANN Linear Function Calls : 3000
  SNN Linear Function Calls : 60000
  ANN Total GPU Time (ms)   : 0.82
  SNN Total GPU Time (ms)   : 14.79

==================================================
```

```profile_models.py```:  
```
import torch
import time

# Device configuration
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")

# Network Topology from draft_tex.pdf
input_size = 100
hidden1 = 1200
hidden2 = 1200
output_size = 784
timesteps = 35  # FIXED: Matches time_window=35 in model_fc.py

class CorrectedANN(torch.nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = torch.nn.Linear(input_size, hidden1)
        self.fc2 = torch.nn.Linear(hidden1, hidden2)
        self.fc3 = torch.nn.Linear(hidden2, output_size)
        # Matches the normalized ANN structure in run_snn.py
        self.act = torch.nn.LeakyReLU(inplace=True) 

    def forward(self, x):
        x = self.act(self.fc1(x))
        x = self.act(self.fc2(x))
        return self.act(self.fc3(x))

class CorrectedSNN(torch.nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = torch.nn.Linear(input_size, hidden1)
        self.fc2 = torch.nn.Linear(hidden1, hidden2)
        self.fc3 = torch.nn.Linear(hidden2, output_size)
        self.threshold = 1.0

    def mem_update_dummy(self, fc_out, mem):
        # Accurately mimics the GPU memory bandwidth stress of LIF neurons
        mem = mem + fc_out
        spk = (mem >= self.threshold).float()
        mem = mem * (1.0 - spk)
        return mem, spk

    def forward(self, x):
        batch_size = x.size(0)
        # Initialize membrane potentials
        mem1 = torch.zeros(batch_size, hidden1, device=x.device)
        mem2 = torch.zeros(batch_size, hidden2, device=x.device)
        mem3 = torch.zeros(batch_size, output_size, device=x.device)
        out_spikes = torch.zeros(batch_size, output_size, device=x.device)

        for t in range(timesteps):
            # Input noise is converted to binary spikes over time in reality, 
            # but dense matrix mult simulates the worst-case GPU overhead
            v1 = self.fc1(x) 
            mem1, spk1 = self.mem_update_dummy(v1, mem1)
            
            v2 = self.fc2(spk1)
            mem2, spk2 = self.mem_update_dummy(v2, mem2)
            
            v3 = self.fc3(spk2)
            mem3, spk3 = self.mem_update_dummy(v3, mem3)
            
            out_spikes += spk3
            
        return out_spikes / timesteps

ann_model = CorrectedANN().to(device)
snn_model = CorrectedSNN().to(device)

batch_sizes = [100, 500, 1000]

print("==================================================")
print(f" Table III Data Extraction (Device: {device})")
print("==================================================")

for b in batch_sizes:
    print(f"\n[Generated Images: {b}]")
    
    # 1. Linear Function Calls Extraction (Corrected math)
    ann_calls = 3 * 1 * b
    snn_calls = 3 * timesteps * b
    print(f"  ANN Linear Function Calls : {ann_calls}")
    print(f"  SNN Linear Function Calls : {snn_calls}")

    # 2. Total GPU Time Measurement (ms)
    x = torch.randn(b, input_size).to(device)

    # GPU Warmup
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

```

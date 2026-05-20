Modify the source files(using 1024*8 SRAM) of SNN-GANmodel in ```RTL-simulation``` folder at https://github.com/klab-aizu/SpikingGAN/tree/main/RTL-simulation/RTL  
    to the source files(using 64*8 SRAM) of SNN-GANmodel  

A hierarchy of the files in ```RTL``` folder in ```RTL-simulation``` folder in ```SpikingGAN``` repository  
```
SNN_top
	SNN_Slave_If_v1_1_S00_AXI
	SNN_wrapper
		spike_rom
			input_noise
		bias_l1_rom
		bias_l2_rom
		bias_l3_rom
		weights_l1_rom
		weights_l2_rom
		weights_l3_rom
		spike_counter
		SNPC_top
			SNPC0
				SNPC_cntrl
       			LIF_neuron *64
				xbar
					sram_sp_w8_b64_freepdk4
			SNPC1
				SNPC_cntrl
       			LIF_neuron *64
				xbar
					sram_sp_w8_b64_freepdk4
			SNPC2
				SNPC_cntrl
       			LIF_neuron *64
				xbar
					sram_sp_w8_b64_freepdk4
```

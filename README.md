# RAMinate

In the future, STT-MRAM will achieve larger capacity and comparable read/write
performance, but incur orders of magnitude greater write energy than DRAM. To
achieve large capacity as well as energy-efficiency, it is necessary to use
both DRAM and STT-MRAM for the main memory of a computer.

**RAMinate** is a hypervisor-based support for hybrid main memory systems composed
of DRAM and byte-addressable non-volatile memory (i.e., especially, STT-MRAM in
mind), that reduces write traffic to STT-MRAM by optimizing page locations between
DRAM and STT-MRAM.

To our knowledge, **this is the first work fully implementing a hypervisor-based
hybrid memory system**.
In contrast to past studies, our mechanism works at a hypervisor, not at the
hardware or operating system level.  It does not require any special program at
the operating system level nor any design changes of the current memory
controller at the hardware level.


Updated June 2018:
Recently, Intel announced that their DIMM-based Optane memory will be on the market in 2019.
The hypervisor mechanism of RAMinate will also work for the hybrid memory system using DRAM and Optane.
I am looking for industry partners to extend RAMinate for actual use as well as academic collaborators to conduct further research in this area. Contact me!

Updated July 2019:
We applied RAMinate to Intel Optane DC and confirmed that RAMinate successfully works for the first commercially-available, byte-addressable memory. See [our prompt report in arXiv](https://arxiv.org/abs/1907.12014).




# Overview



We assume that we have a physical machine with both DRAM and byte-addressable NVM.
NVM is mapped to a particular address range of physical memory space.

Now we create a VM on RAMinate.

In the following example, a VM has 8GB RAM, which is mapped to 1GB DRAM and 7GB STT-MRAM in reality.
At the 2nd line, a pseudo RAM object of Memory Backend Container, dmcon0, is created.
At the 3rd line, a RAM object of Memory Backend DevMem, dram0, is created and overlaid by dmcon0.
It is mapped to a free DRAM space, the 1GB address range of physical memory starting from 0x1100000000.
In the same manner, at the 4th line, a RAM object, mram0, is created from a free MRAM space.
As specified in the offset option, mram0 is placed after dram0 in the Container object.
At the 5th line, the created pseudo RAM object is assigned to NUMA domain 0 of the VM.


```
qemu-system-x86_64 -enable-kvm -hda image.qcow2 -m size=8G \
   -object memory-backend-container,id=dmcon0,size=8G \
   -object memory-backend-devmem,id=dram0,size=1G,phy-offset=0x1100000000,offset=0,parent=dmcon0 \
   -object memory-backend-devmem,id=mram0,size=7G,phy-offset=0x3080000000,offset=0x40000000,parent=dmcon0 \
   -numa node,nodeid=0,memdev=dmcon0
```


After launching the VM, RAMinate periodically detects write-intensive memory
pages and relocates them to DRAM, by monitoring and manipulating Extended Page
Table (EPT) entries of the VM.

Our conference paper details how it works.


# Publication


## RAMinate

This work was firstly presented in ACM Symposium on Cloud Computing 2016 (SoCC2016).
When citing RAMinate, please use the following paper.

RAMinate: Hypervisor-based Virtualization for Hybrid Main Memory Systems  
Takahiro Hirofuchi, Ryousei Takano  
Proceedings in the seventh ACM Symposium on Cloud Computing 2016, pp. 112--125, Oct 2016  
**Best Paper Award** (Only one Best Paper Award in the conference)  
DOI: [10.1145/2987550.2987570](http://doi.acm.org/10.1145/2987550.2987570)  
[paper](assets/socc2016-raminate.pdf),
[slides](assets/socc2016-raminate-talk-slides.pdf) and [poster](assets/socc2016-raminate-poster.pdf)
```
@inproceedings{socc2016raminate,
	author = {Takahiro Hirofuchi and Ryousei Takano},
	title = {RAMinate: Hypervisor-based Virtualization for Hybrid Main Memory Systems},
	booktitle = {Proceedings of the Seventh ACM Symposium on Cloud Computing},
	series = {SoCC '16},
	year = {2016},
	isbn = {978-1-4503-4525-5},
	location = {Santa Clara, CA, USA},
	pages = {112--125},
	numpages = {14},
	url = {http://doi.acm.org/10.1145/2987550.2987570},
	doi = {10.1145/2987550.2987570},
	acmid = {2987570},
	publisher = {ACM},
	address = {New York, NY, USA},
	keywords = {Hybrid Memory, Hypervisor, Non-volatile Memory, STT-MRAM, Virtual Machine},
}
```

## RAMinate and Intel Optane DC

The Preliminary Evaluation of a Hypervisor-based Virtualization Mechanism for Intel Optane DC Persistent Memory Module  
Takahiro Hirofuchi, Ryousei Takano  
arXiv, Computing Research Repository (CoRR), vol. abs/1907.12014, pp. 1-9, July 2019  
[abstract](https://arxiv.org/abs/1907.12014) and [paper](https://arxiv.org/pdf/1907.12014)



# Dissemination

- Invited talk, AIST Technobridge Fair 2016, Oct 20, 2016
- Invited talk, Embedded System Symposium 2016 (ESS2016), Oct 22, 2016
- Invited talk, Networking Group, NII, Dec 12, 2016
- Invited talk, Big Data Infrastracture Workshop, Dec 22, 2016
- Invited talk, Fujitsu Laboratories, Feb 3, 2017


# Contact

Takahiro Hirofuchi, Ph.D.  
National Institute of Advanced Industrial Science and Technology (AIST)  
https://takahiro-hirofuchi.github.io/


# Acknowledgment

This work is partially supported by the IMPULSE project of AIST and JSPS Grant KAKENHI 16K00115.

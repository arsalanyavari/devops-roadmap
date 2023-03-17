# TCP/IP vs OSI

<img align="right" src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/original-seven-layers-of-osi-model.png" width="50%"/>
<p align="justify">
The OSI model (Open Systems Interconnection Model) is a conceptual model used to describe the functions of a network system. This model defines a hierarchical architecture that logically divides the functions required to support system-to-system communication.
</p>
<p align="justify">
There are a total of seven layers that are responsible for specific tasks and functions. It is a reference model that shows how different programs talk to each other on the network and plays an important role in passing messages between systems.
</p>
<p align="justify">
The Internet is not based on OSI, but on the simpler TCP/IP model. However, the 7-layer OSI model is still widely used because it helps visualize and communicate how networks work and helps isolate and troubleshoot network problems.
</p>

<hr>
<p align="justify">
It should be noted that each of these layers is supposed to work according to the predefined contract. Also, according to the placed form, some of the tools available at the network level will work in certain layers; For example, routers will work in layer 3, switches in layer 2 and load balancers in layers 4 and 7, as well as applications and firewalls in layer 7. Each packet may be fetched in different paths and its header may change. Since the packet may change a lot along the way, a header is considered for each of the mentioned layers. The below image shows a good idea of what has been said.
</p>

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/how-data-is-processed-in-OSI-and-TCPIP-models.jpg width="100%"/>


As you can see in the image above, in order for the desired content that is set in the application layer to be placed on the physical link, it is necessary to mount different headers on it. Finally, we will have a sequence of zeros and ones that can be transmitted over the link.


**TCP/IP (Transmission Control Protocol/Internet Protocol)** is specifically designed as a model to offer highly reliable and end-to-end byte stream over an unreliable internetwork. It is a suite of communication protocols used for transmitting data over networks, including the internet. It is the foundation of the internet and is used by virtually every device that connects to the internet.

TCP is responsible for breaking down data into packets and ensuring that they are transmitted reliably and in order. IP provides the addressing and routing information necessary for the packets to reach their destination. Together, TCP/IP provides end-to-end connectivity that allows data to be transmitted over long distances and across different types of networks.

### To compare the network layers in the two mentioned models, you can refer to the below image:

<img align="center" src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/OSI-vs.-TCPIP-models.jpg width="100%"/>

# pysparkcluster_indocker_bynetworkcable
The instruction of deploying Pyspark cluster based on docker between two computers by a network cable.

## Requirement:
  1. Two computer with the physical network port;
  2. Extra network cards (2 or more) for the global Internet access;
  3. A network cable;
  4. Linux (e.g., ubuntu) or MS windows installed in these computers;
  5. Vmware station player, if windows.

## Steps:
  1. Install Operation System for each computer;
  2. Plugin extra network cards to make sure they can access the global Internet;
  3. Plugin the network cable to connect these two;
  4. If both linux: \
    4.1 Open terminals; \
    4.2 Input `sudo su` and fill your password; \
    4.3 Install "ifconfig" and "ping" through `apt install net-tools` and `apt install iputils-ping`; \
    4.4 Set static ip through OS setting as shown in the following figure, in which you can set a static ip (e.g., 192.168.0.2) and subnet mask (e.g., 255.255.255.0); \
    <div align=center><img src="https://user-images.githubusercontent.com/43268820/165883798-6757ffd0-5c53-4cd3-9226-be1ea972f6f2.png" width="900"></div>
    4.5 Vertify ip whether having been changed or not through inputing `ifconfig` in terminal; \
    <div align=center><img src="https://user-images.githubusercontent.com/43268820/165884245-72365f1e-7235-4797-a985-b38227c6e086.png" width="900"></div>
    4.6 Repeat the similar operations in another linux machine, but use another ip within the same network segment, (e.g., 192.168.0.1) and the same subnet mask (e.g., 255.255.255.0); \
    4.7 Vertify two machines' connection through "ping" operation; if appearing "time=xxx.xx ms", it means they are connected; \
    <div align=center><img src="https://user-images.githubusercontent.com/43268820/165884601-7d5839a2-0081-437e-bf2d-0d04134efd49.png" width="900"></div>
 5. If one linux and one Windows: \
    5.1 Linux machine's configration refers to step4; \
    5.2 In Windows, install Vmware station player(VM); \
    5.3 Install linux (e.g., ubuntu) in the VM and install some softwares referring to step 4.3; To use the shared clipboard, install `apt install open-vm-tools open-vm-tools-desktop` in VM. \
    5.4 Network configuration: \
        5.4.1 In Vmware station player, bridge the v-net card with the net card with physical net port (or called as Ethernet), as shown in the following figure; \
        <div align=center><img src="https://user-images.githubusercontent.com/43268820/165894176-eaabd33a-2bba-4265-b509-4549cf5471ab.png" width="900"></div>
        5.4.2 To make VM access global Internet, add one more v-net card using NAT mode;
        <div align=center><img src="https://user-images.githubusercontent.com/43268820/165895085-86436994-6282-4301-bbf0-3fe06268fa3e.png" width="900"></div>
        5.4.3 Set static ip for windows, and you can click Start > Settings > Control Panel > Network and Internet > Network and Sharing Center > Change adapter settings. \
        <div align=center><img src="https://user-images.githubusercontent.com/43268820/165895852-d7cef5cb-3dfe-4c25-b8a8-bdea2f7dae30.png" width="900"></div>
        Right click Ethernet, and set static ip (e.g., 192.168.0.1) and subnet mask (e.g., 255.255.255.0, the same as the above setting); \
        <div align=center><img src="https://user-images.githubusercontent.com/43268820/165896789-7feabff9-ddc1-4609-8c40-7e6037e49e9b.png" width="900"></div>
        5.4.4 Set static ip for VM linux, referring step 4.6, but change the ip as 196.168.0.3; \
        5.4.5 Now three machine, including linux machine, Windows machine and VM machine are located in the same local area network, we can vertify them through "ping": \
        In VM:
        <div align=center><img src="https://user-images.githubusercontent.com/43268820/165898924-16fa25ec-9dd9-43ea-817d-0d4eb0cfe28b.png" width="900"></div>
        In Windows:
        <div align=center><img src="https://user-images.githubusercontent.com/43268820/165899678-5388706e-2fec-430b-b75d-e9458ce94205.png" width="900"></div>
        In linux:
        <div align=center><img src="https://user-images.githubusercontent.com/43268820/165899990-77df93d5-72e8-4fa4-a20a-961f0af8eb05.png" width="900"></div>
  6. Install docker for linux machines (physical machine [PhyM] and VM): \
    6.1 Input `sudo apt-get install ca-certificates curl gnupg lsb-release` in terminal and wait for installation finishing; \
    6.2 Input `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg` and press 'Enter'; \
    6.3 Input `echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null` and press 'Enter'; \
    6.4 Input `sudo apt-get update`; \
    6.5 Input `sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin`; \
    6.6 Input `sudo docker run hello-world` to vertify the installation; \
    6.7 Refer to https://docs.docker.com/engine/install/linux-postinstall/ to do more things for docker，e.g., non-root user, starting on boot.
  7. Install spark and some other softwares as the master node through dockerfile `docker build -t your_image_name:master https://raw.githubusercontent.com/HiCiChlid/geosci-pyenv/main/dockerfile`. \
     Install the slaver node through dockerfile `docker build -t your_image_name:slaver https://raw.githubusercontent.com/HiCiChlid/pysparkcluster_indocker_bynetworkcable/main/dockerfile` \
     You can choose which node to deploy on which machine.
  8. Open net card promiscuous mode for both linux machines，`ip link set eno1 promisc on` for PhyM's ethernet net card named as eno1, and `ip link set ens33 promisc on` for VM's net card named as ens33. If success, "ifconfig" appears PROMISC as shown in red rectangle. <div align=center><img src="https://user-images.githubusercontent.com/43268820/165923712-b55b41fb-0204-4279-9d73-5d697342b417.png" width="900"></div>
  9. Due to both linux machines are connected through the windows machine (192.168.0.1), so the Gateway is 192.168.0.1;
  10. Create docker net card for PhyM `docker network create -d macvlan --subnet=192.168.0.30/24 --gateway=192.168.0.1 -o parent=eno1 -o macvlan_mode=bridge eth0_1`,  (subnet=192.168.0.30, just an example); \
    and similarly, we can create docker net card for VM `docker network create -d macvlan --subnet=192.168.0.40/24 --gateway=192.168.0.1 -o parent=ens33 -o macvlan_mode=bridge eth0_1`, (subnet=192.168.0.40, just an example); \
    and we can check the docker network to find the new net card through `docker network ls`;<div align=center><img src="https://user-images.githubusercontent.com/43268820/165931945-d90a6a29-81c3-461f-8a79-ecc257b3bef2.png" width="900"></div>
  11. Create docker containers for two machines, \
    In PhyM node1 `docker run -it --privileged --net=eth0_1 --ip=192.168.0.30 --name=spark30 your_image_name:slaver /bin/bash`; \
    In PhyM node2 `docker run -it --privileged --net=eth0_1 --ip=192.168.0.31 --name=spark31 your_image_name:slaver /bin/bash`; \
    In VM node1 `docker run -it --privileged --net=eth0_1 --ip=192.168.0.40 --name=spark40 your_image_name:slaver /bin/bash`; \
    In VM node2 `docker run -it --privileged --net=eth0_1 --ip=192.168.0.41 --name=spark41 your_image_name:slaver /bin/bash`;
  12. Check network connections between containers, for example, "spark40" ping "spark30"; or "spark31" ping "spark41"; <div align=center><img src="https://user-images.githubusercontent.com/43268820/165934538-d2d8d1cd-b320-4b6e-a2d7-dfa6d31ee392.png" width="900"></div>
  13

  15. 

  13. 


  
 




    
        
        

       





      



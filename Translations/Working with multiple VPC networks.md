# LAB: Working With Multiple VPC Networks

## Objectives

- Create custom mode VPC networks with firewall rules
- Create VM instances using Compute Engine
- Explore the connectivity for VM instances across VPC networks
- Create a VM instance with multiple network interfaces

## Steps

1. Create custom mode VPC networks with firewall rules
   1. Create the managementnet network
       gcloud compute networks create managementnet --subnet-mode=custom
   
   2. Create the managementsubnet-us subnet
       gcloud compute networks subnets create managementsubnet-us --network=managementnet --region=us-central1 --range=10.130.0.0/20

   3. Create the privatenet network
       gcloud compute networks create privatenet --subnet-mode=custom

   4. Create the privatesubnet-us subnet
       gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24

   5. Create the privatesubnet-eu subnet
       gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20

   6. List the available VPC networks
       gcloud compute networks list

   7. List the available VPC subnets (sorted by VPC network)
       gcloud compute networks subnets list --sort-by=NETWORK

   8. Create the firewall rules for managementnet
       gcloud compute firewall-rules create managementnet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=managementnet --action=ALLOW --rules=icmp,tcp:22,tcp:3389 --source-ranges=0.0.0.0/0

   9. Create the firewall rules for privatenet
       gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=icmp,tcp:22,tcp:3389 --source-ranges=0.0.0.0/0

   10. List all the firewall rules (sorted by VPC network)
        gcloud compute firewall-rules list --sort-by=NETWORK

2. Create VM instances
   1. Create the managementnet-us-vm instance
       gcloud compute instances create managementnet-us-vm --zone=us-central1-c --machine-type=n1-standard-1 --subnet=managementsubnet-us

   2. Create the privatenet-us-vm instance
       gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=n1-standard-1 --subnet=privatesubnet-us

   3. List all the VM instances (sorted by zone)
       gcloud compute instances list --sort-by=ZONE

3. Explore the connectivity between VM instances
   1. Ping the external IP addresses
      - SSH using mynet-us-vm
         gcloud compute ssh mynet-us-vm

      -  Test connectivity to mynet-eu-vm's external IP
          ping -c 3 <Enter: mynet-eu-vm's external IP here>

      - Test connectivity to managementnet-us-vm's external IP
          ping -c 3 <Enter: managementnet-us-vm's external IP here>

      - Test connectivity to privatenet-us-vm's external IP
          ping -c 3 <Enter: privatenet-us-vm's external IP here>
    
   2. Ping the internal IP addresses
      - Test connectivity to mynet-eu-vm's internal IP
         ping -c 3 <Enter: mynet-eu-vm's internal IP here>

      - Test connectivity to managementnet-us-vm's internal IP
         ping -c 3 <Enter: managementnet-us-vm's internal IP here>

      - Test connectivity to privatenet-us-vm's internal IP
         ping -c 3 <Enter: privatenet-us-vm's internal IP here>
        
4. Create the VM instance with multiple network interfaces
   1. Create the vm-appliance instance with network interfaces in privatesubnet-us, managementsubnet-us, and mynetwork
       gcloud compute instances create vm-appliance --zone=us-central1-c --machine-type=n1-standard-4 --subnet=managementsubnet-us --subnet=privatesubnet-us --subnet=mynetwork

   2. Explore the network interface details
      - SSH using vm-appliance
         gcloud compute ssh vm-appliance

      - List the network interfaces within the VM instance
         sudo ifconfig

      - Test connectivity to privatenet-us-vm's internal IP
         ping -c 3 <Enter: privatenet-us-vm's internal IP here>

      - Test to check if the VM has an internal DNS service
         ping -c 3 privatenet-us-vm

      - Test connectivity to managementnet-us-vm's internal IP
         ping -c 3 <Enter: managementnet-us-vm's internal IP here>

      - Test connectivity to mynet-us-vm's internal IP
         ping -c 3 <Enter: mynet-us-vm's internal IP here>

      - List the routes for vm-appliance instance
         ip route

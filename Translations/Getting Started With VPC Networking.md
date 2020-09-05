# LAB: Getting Started With VPC Networking

## Objectives

In this lab, you learn how to perform the following tasks:

- Explore the default VPC network
- Create an auto mode network with firewall rules
- Create VM instances using Compute Engine
- Explore the connectivity for VM instances

## Steps

1. Explore the default network
   1. Delete the Firewall rules
       - gcloud compute firewall-rules delete default-allow-icmp;default-allow-rdp; default-allow-ssh; default-allow-internal

   2. Delete the default network
        - gcloud compute networks delete default

2. Create a VPC network and VM instances
   1. Create an auto mode VPC network with firewall rules
        - gcloud compute networks create mynetwork --subnet-mode=automatic

        - gcloud compute firewall-rules create default-allow-icmp-rdp-ssh-internal --action=ALLOW --direction=INGRESS --network=mynetwork --priority=1000 --source-ranges=0.0.0.0/0 --rules=icmp,tcp:3389,tcp:22

   2. Create a VM instance in us-central1
        - gcloud compute instances create mynet-us-vm --zone=us-central1-c --machine-type=n1-standard-1 --network=mynetwork

   3. Create a VM instance in europe-west1
         - gcloud compute instances create mynet-eu-vm --zone=europe-west1-c --machine-type=n1-standard-1 --network=mynetwork

3. Explore the connectivity for VM instances
   1. Verify connectivity for the VM instances
      a. Use mynet-us-vm to ssh
         - gcloud compute ssh mynet-us-vm

      b. Test connectivity to mynet-eu-vm's internal IP
         - ping -c 3 <Enter: mynet-eu-vm internal IP>

      c. Test connectivity to mynet-eu-vm's external IP
         - ping -c 3 <Enter: mynet-eu-vm external IP>

   2. Remove the allow-icmp firewall rules
      a. Remove allow-icmp firewall rule
         - gcloud compute firewall-rules delete allow-icmp

      b. Test connectivity to mynet-eu-vm's internal IP
         - ping -c 3 <Enter: mynet-eu-vm internal IP>
        
      c. Exit ssh
         - exit

   3. Remove the allow-ssh firewall rules
      a. Remove the allow-ssh firewall rule
         - gcloud compute firewall-rules delete allow-ssh

      b. Try to SSH to mynet-us-vm
         - gcloud compute ssh mynet-us-vm

      c. Exit ssh
         - exit

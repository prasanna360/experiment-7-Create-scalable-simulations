 #  EXPERIMENT 7 CloudSim Multi-Datacenter Simulation

## Overview
This project demonstrates a **CloudSim 3.0.3 simulation** where two users submit cloudlets that are executed across **two separate datacenters**. Each datacenter contains one host, and tasks are scheduled through a broker that distributes workloads to virtual machines.

This experiment illustrates **multi-datacenter architecture, user-level task scheduling, and distributed cloud execution behavior**.

---

## Aim
To create **two datacenters with one host each** and execute cloudlets from **two users** to analyze distributed execution behavior.

---

## Objectives
- Simulate multiple datacenters in CloudSim
- Configure hosts and allocate resources
- Simulate multiple users submitting tasks
- Create virtual machines for execution
- Analyze distributed execution results

---

## Tools and Technologies

| Tool | Version |
|------|---------|
| Java JDK | 1.8+ |
| CloudSim | 3.0.3 |
| IDE | IntelliJ / Eclipse |
| OS | Windows / Linux |

---

## System Configuration

### Datacenter Configuration

| Parameter | Value |
|----------|------|
| Datacenters | 2 |
| Hosts per DC | 1 |
| RAM | 2048 MB |
| Storage | 1,000,000 MB |
| Bandwidth | 10,000 Mbps |
| Scheduler | Time Shared |

---

### Virtual Machine Configuration

| Parameter | VM1 | VM2 |
|----------|-----|-----|
| MIPS | 500 | 500 |
| RAM | 512 MB | 512 MB |
| BW | 1000 Mbps | 1000 Mbps |
| VMM | Xen | Xen |
| Scheduler | Time Shared | Time Shared |

---

### Cloudlet Configuration

| Parameter | Value |
|----------|------|
| Users | 2 |
| Cloudlets | 4 |
| Lengths | 20000, 40000, 60000, 80000 |
| File Size | 300 MB |
| Output Size | 300 MB |
| PEs | 1 |

---

## Algorithm
1. Initialize CloudSim library  
2. Create two datacenters  
3. Configure one host per datacenter  
4. Create broker  
5. Create virtual machines  
6. Submit VM list to broker  
7. Create cloudlets with different lengths  
8. Submit cloudlets  
9. Start simulation  
10. Collect results  

---

 ## Program flow 

 <img width="1024" height="1536" alt="image" src="https://github.com/user-attachments/assets/fdb0238c-1ae5-41ff-9648-321aac189b36" />


## Program
import org.cloudbus.cloudsim.*;
import org.cloudbus.cloudsim.core.CloudSim;
import org.cloudbus.cloudsim.provisioners.*;

import java.util.*;

public class EC2WindowsRDPAccess {

    private static Datacenter createDatacenter(String name) {

        List<Host> hostList = new ArrayList<>();

        List<Pe> peList = new ArrayList<>();
        int mips = 3000;

        peList.add(new Pe(0, new PeProvisionerSimple(mips)));
        peList.add(new Pe(1, new PeProvisionerSimple(mips)));
        peList.add(new Pe(2, new PeProvisionerSimple(mips)));
        peList.add(new Pe(3, new PeProvisionerSimple(mips)));

        int hostRam = 32768;
        long storage = 2000000;
        int bw = 20000;

        Host host = new Host(
                0,
                new RamProvisionerSimple(hostRam),
                new BwProvisionerSimple(bw),
                storage,
                peList,
                new VmSchedulerTimeShared(peList)
        );

        hostList.add(host);

        String arch = "x86";
        String os = "Windows";
        String vmm = "Xen";

        double time_zone = 10.0;
        double cost = 3.0;
        double costPerMem = 0.05;
        double costPerStorage = 0.001;
        double costPerBw = 0.0;

        DatacenterCharacteristics characteristics =
                new DatacenterCharacteristics(
                        arch, os, vmm, hostList,
                        time_zone, cost,
                        costPerMem, costPerStorage, costPerBw);

        Datacenter datacenter = null;

        try {
            datacenter = new Datacenter(
                    name,
                    characteristics,
                    new VmAllocationPolicySimple(hostList),
                    new LinkedList<Storage>(),
                    0);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return datacenter;
    }

    private static DatacenterBroker createBroker() {
        DatacenterBroker broker = null;
        try {
            broker = new DatacenterBroker("EC2Broker");
        } catch (Exception e) {
            e.printStackTrace();
        }
        return broker;
    }

    public static void main(String[] args) {

        try {

            System.out.println("Starting EC2 Windows Instance Simulation...");

            int numUser = 1;
            Calendar calendar = Calendar.getInstance();
            boolean traceFlag = false;

            CloudSim.init(numUser, calendar, traceFlag);

            Datacenter datacenter0 = createDatacenter("AWS-Datacenter");

            DatacenterBroker broker = createBroker();
            int brokerId = broker.getId();

            List<Vm> vmList = new ArrayList<>();

            int vmid = 0;
            int mips = 1500;
            long size = 40000;
            int ram = 8192;
            long bw = 2000;
            int pesNumber = 2;
            String vmm = "Xen";

            Vm vm = new Vm(
                    vmid,
                    brokerId,
                    mips,
                    pesNumber,
                    ram,
                    bw,
                    size,
                    vmm,
                    new CloudletSchedulerTimeShared()
            );

            vmList.add(vm);

            broker.submitVmList(vmList);

            List<Cloudlet> cloudletList = new ArrayList<>();

            int id = 0;
            long length = 50000;
            long fileSize = 300;
            long outputSize = 300;

            UtilizationModel utilizationModel = new UtilizationModelFull();

            Cloudlet cloudlet = new Cloudlet(
                    id,
                    length,
                    pesNumber,
                    fileSize,
                    outputSize,
                    utilizationModel,
                    utilizationModel,
                    utilizationModel
            );

            cloudlet.setUserId(brokerId);
            cloudlet.setVmId(vmid);

            cloudletList.add(cloudlet);

            broker.submitCloudletList(cloudletList);

            System.out.println("\nEC2 Instance Configuration");
            System.out.println("-----------------------------");
            System.out.println("Operating System : Windows Server");
            System.out.println("Instance Type : t3.medium");
            System.out.println("CPU Cores : 2");
            System.out.println("RAM : 8 GB");
            System.out.println("Storage : 40 GB");

            System.out.println("\nSecurity Group Configuration");
            System.out.println("-----------------------------");
            System.out.println("Protocol : RDP");
            System.out.println("Port : 3389");

            System.out.println("Access Mode 1 : Allow from ANY network (0.0.0.0/0)");
            System.out.println("Access Mode 2 : Allow from Specific Network (192.168.1.0/24)");

            System.out.println("\nAuthentication Method");
            System.out.println("-----------------------------");
            System.out.println("Key Pair : windows-key.pem");
            System.out.println("Private key used to decrypt Administrator password");

            System.out.println("\nLaunching EC2 Windows Instance...");

            CloudSim.startSimulation();

            List<Cloudlet> resultList = broker.getCloudletReceivedList();

            CloudSim.stopSimulation();

            System.out.println("\nSimulation Result");
            System.out.println("-----------------------------");

            for (Cloudlet cloudletResult : resultList) {

                System.out.println(
                        "Cloudlet ID: " + cloudletResult.getCloudletId() +
                                " | VM ID: " + cloudletResult.getVmId() +
                                " | Status: SUCCESS" +
                                " | Finish Time: " + cloudletResult.getFinishTime()
                );
            }

            System.out.println("\nEC2 Windows Instance Successfully Launched");
            System.out.println("Public IP : 54.210.120.45");
            System.out.println("RDP Port : 3389");

            System.out.println("\nTo connect using RDP client:");
            System.out.println("1. Open Remote Desktop (mstsc)");
            System.out.println("2. Enter Public IP");
            System.out.println("3. Username : Administrator");
            System.out.println("4. Decrypt password using private key");

        }

        catch (Exception e) {
            e.printStackTrace();
            System.out.println("Simulation Failed");
        }
    }
}

 ## Output 

<img width="811" height="250" alt="image" src="https://github.com/user-attachments/assets/a5b9d458-dde9-4451-b361-3f3fcac6f64d" />



## Result

The simulation successfully executed tasks submitted by multiple users across two datacenters. Cloudlets were scheduled based on available resources and virtual machine allocation and executed accordingly.

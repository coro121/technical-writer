# Configure the Data Distribution Service Modular Kit

The Data Distribution Service (DDS) Modular kit provides robust computing and storage capabilities for efficiently managing traffic through distributed collection and analysis. The kit features eight nodes and can aggregate network traffic over 15 Gbps. Additionally, you can divide the kit into smaller, high-performance sensors that you can deploy across multiple collection points within one or more networks.

This guide demonstrates how to configure an 8-node cluster.

## Hardware prerequisites

- Deployment laptop
- S4112-T switch
- S4112-F switch
- 5-port gigabit ethernet switch
- Thunderbolt adapter
- Gigamon network tap
- Nodes (8)
- Cat 6 cable (9)
- QSFP breakout cable (4)
- SFP DAC cable (3)
- 100G cable

## Connect the T and F switches

The toggle switch (T) allows you to establish a persistent on/off state, making it ideal for long-term control and isolation tasks. In contrast, the momentary switch (F) is designed for temporary connections, enabling quick, on-demand actions such as signal routing or temporary network configurations. Together, these switches provide flexible and precise control over network traffic, ensuring efficient data flow and enhancing your network infrastructure's overall reliability and security.

### Install the T switch

Perform the following actions to connect an operator switch and eight nodes to the T switch.

1. Connect one end of a **CAT6** cable into **port 1** of the T switch. Then, connect the other end to **port 1** of the **Tripp Lite switch**.

    ![Connect the T switch to operator switch.](https://user-images.githubusercontent.com/10658186/266861597-068726ea-a73b-4dac-970e-1312fcd4d21d.png)

2. Connect one end of the remaining **CAT6** cables to ports **5 through 12**. Then, connect the other ends to the **IPMI** ports on each of the **nodes**.

    ![Connect the T switch to 8 nodes.](https://user-images.githubusercontent.com/10658186/266861605-1f488f00-2727-4f07-96a1-f3fd2a811866.png)

3. Connect one **QSFP breakout** cable from **port 13** to **eno 7** ports on each of the four nodes.

    ![Connect to eno 7 on four nodes.](https://user-images.githubusercontent.com/10658186/266861621-a21fb62e-913f-4a84-9b31-56f0c3b20941.png)

4. Connect a second **QSFP breakout** cable from **port 14** to the **eno 7** ports on each of the remaining four nodes.

    ![Connect to eno 7 on remaining four nodes](https://user-images.githubusercontent.com/10658186/266861641-c9789f25-f7fe-4a71-94c9-91dce62e264a.png)

### Install the F switch

Perform the following actions to connect a deployment laptop, Thunderbolt adapter, eight nodes, and the T switch to the F switch.

1. Connect the **Thunderbolt adapter** cable to the **deployment laptop**.

2. Plug one end of the **SFP DAC** cable into **port 1**, and plug the other end into the **Thunderbolt adapter**.

3. Connect a **QSFP breakout** cable from **port 13** to the **eno 8** port on each of the four nodes.

    ![Connect the f-switch to eno 8 on four nodes](https://user-images.githubusercontent.com/10658186/266861685-8bf1bf80-566a-4458-b94e-64ba0da867bf.png)

4. Connect a **QSFP breakout** cable from **port 14** to the **eno 8** port on each of the remaining four nodes.

    ![Connect the f-switch to eno 8 on the remaining four nodes.](https://user-images.githubusercontent.com/10658186/266861698-6dc86f19-f56b-4475-b869-7cd66e1e49dd.png)

5. Connect the **100G** cable from **port 15** on the **F** switch to **port 15** on the **T** switch.

    ![Connect the F switch to the T switch](https://user-images.githubusercontent.com/10658186/266861727-275c3299-0834-42ff-a6f6-35afa919632c.png)

## Connect the Gigamon network tap

Perform the following steps to connect to the Gigamon network tap.

1. Plug the **SFP DAC** cable into **port A** of the **Monitor Tool** section. Then, connect the other end to a node's **eno 8** port.

2. Plug the remaining **SFP DAC** cable into **port B** of the **Monitor Tool**. Then, connect the other end to a node's **eno 7** port.

## Start the system power-up sequence

You must perform the following steps in sequential order to start your system.

1. Power on the switches, deployment laptop, and nodes.

**Note**: Nodes can take up to five minutes to load.

1. Connect to a node using an SSH client.

2. Run the Gluster command-line tool to view the list of available volumes:

    ```bash
    [root@node1 ~]# gluster
    gluster> volume start <volume name>
    ```

3. In separate terminal tabs, use SSH to connect to the remaining nodes.

4. Run the following step to enter maintenance mode:

    ```bash
    systemctl start ovirt-ha-broker; systemctl start ovirt-ha-agent
    ```

5. Run the following step to exit maintenance mode for the hosted engine:

    ```bash
    hosted-engine --set-maintenance --mode=none
    ```

6. Run the following step to verify that the hosted engine virtual machine is loading:

    ```bash
    hosted-engine --vm-status
    ```

7. In a web browser, sign in to the hosted engine by entering `[kit number]acme.mil` in the address bar.

8. Click **Storage > Domains**.

9. Start all virtual machines by clicking **Computer** > **Virtual Machines**.

10. Launch each virtual machine. Then, click **Run**.

## System power-down sequence

The power-down sequence depends on the operating system and platform in the DDS kit. Adapt the following instructions for your specific platform.

1. In a web browser, sign in to the hosted engine. Then, click **Administration Portal**.

2. Launch each virtual machine. Then, click **Compute** > **Virtual Machines** > **Shutdown**.

    **Note:** Wait until all virtual machines have completely shut down before proceeding to the next step.

3. Change all storage domains to maintenance mode, except for the `hosted_storage` domain.

4. Sign in to a node via SSH.

5. Run the following steps to enter maintenance mode and shut down the hosted engine:

    ```bash
    hosted-engine --set-maintenance --mode=global
    hosted-engine --vm-shutdown
    ```

6. Confirm the hosted engine is off by running the following check:

    ```bash
    hosted-engine --vm-status
    ```

7. Open separate terminal tabs and use SSH to connect to the remaining nodes.

8. Shut down all services by running the following step:

    ```bash
    systemctl stop ovirt-ha-broker;systemctl stop ovirt-ha-agent
    ```

9. Disconnect storage on each node by running the following step:

    ```bash
    hosted-engine --disconnect-storage
    ```

10. Run the Gluster command-line tool to view a list of volumes:

    ```bash
    gluster
    ```

11. Run the following step to view all volumes:

    ```bash
    [root@node1 ~]# gluster
    gluster > volume list
    data
    engine
    vmstore
    ```

12. Run the following step to stop each volume:

    ```bash
    volume stop <volume name>
    ```

13. Turn off each system.

## Next steps

Now that your DDS Modular Kit is configured and operational, monitor the system performance regularly to ensure optimal operation. Refer to the troubleshooting section in the user manual if you encounter any issues during operation. For ongoing maintenance and updates, follow the best practices outlined in the user manual to keep your system secure and up to date.
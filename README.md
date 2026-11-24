
# Cisco Configuration Backup, Restore, and IOS Upgrade Lab - Packet Tracer

This lab focuses on backing up and restoring running and startup configurations, understanding how they behave differently when restored from a TFTP server, and backing up and upgrading Cisco IOS images.


topolgy.png

1. When you restore a startup configuration from a TFTP server, is the existing configuration overwritten or merged?
2. What happens when you restore a running configuration? Is it overwritten or merged with the existing configuration?
3. If the running configuration contains a loopback interface that doesn't exist in the configuration being restored, will that loopback interface be removed?
4. What happens when you save the running configuration to the startup configuration?
5. How can you configure a router to boot from a different IOS image while keeping the previous version in flash memory?

**[Download the Packet Tracer Lab](Backup%20configs%20and%20IOS.pkt)**


## 1. Backing Up Configurations and IOS

1. First, verify connectivity between R1 and the TFTP server. Then, copy both the running and startup configurations to the TFTP server.

   ![R1 configuration backup](screenshots/r1.1.png)

2. Use `show flash` to check the current IOS image stored in flash memory, then copy it to the TFTP server.

   ![R1 IOS backup](screenshots/r1.2.png)

   The TFTP server now contains R1's running configuration, startup configuration, and IOS image.

   ![TFTP server files](screenshots/server1.1.png)

3. Do the same for S1 by copying both the running and startup configurations to the TFTP server.

   ![S1 configuration backup](screenshots/s1.1.png)

   Both files are now available on the TFTP server.

   ![TFTP server verification](screenshots/server1.3.png)

4. Copy the switch's IOS image to the TFTP server.

   ![S1 IOS backup](screenshots/s1.2.png)

   The IOS image is now backed up on the TFTP server.

   ![S1 IOS backup verification](screenshots/server1.4.png)

## 2. Restoring Configurations: Running vs. Startup

1. Create a loopback interface on R1 with the IP address `1.1.1.1/32`.

   ![Creating a loopback interface](screenshots/r1.3.png)

   Save the configuration and verify that the loopback interface is included in the startup configuration.

   ![Loopback in startup configuration](screenshots/r1.4.png)

2. Copy the original startup configuration (`R1-start-config`) from the TFTP server back to R1.

   ![Restoring the startup configuration](screenshots/r1.5.png)

   The startup configuration is overwritten, and the loopback interface is no longer present in it.

   ![Verifying the startup configuration](screenshots/r1.6.png)

3. Copy R1's current running configuration (`R1-runv2-config`), which still contains the loopback interface, to the TFTP server. Then, remove the loopback interface from R1.

   ![Backing up and removing the loopback](screenshots/r1.7.png)

4. Copy the running configuration (`R1-runv2-config`) from the TFTP server back to R1.

   The loopback interface appears again because it was included in the restored configuration.

   ![Restoring the running configuration](screenshots/r1.8.png)

5. Copy the original running configuration (`R1-run-config`), which does not contain the loopback interface, from the TFTP server back to R1.

   However, the loopback interface remains in the running configuration.

   This happens because restoring a running configuration merges it with the existing configuration rather than completely overwriting it.

   ![Running configuration merge](screenshots/r1.9.png)

**Conclusion:** Restoring a startup configuration overwrites the existing startup configuration, while restoring a running configuration merges it with the current configuration. Settings that are not included in the restored running configuration are not necessarily removed.

## 3. Upgrading R1's IOS

1. Upgrade R1's IOS from `c2900-universalk9-mz.SPA.151-4.M4.bin` to `c2900-universalk9-mz.SPA.155-3.M4a.bin`.

   ![IOS image on TFTP server](screenshots/server1.5.png)

   Use `show version` to verify the IOS version currently running on R1.

   ![R1 current IOS version](screenshots/r1.10.png)

2. The `show flash` command confirms which files are already stored in flash memory.

   Use `copy tftp flash` to copy `c2900-universalk9-mz.SPA.155-3.M4a.bin` from the TFTP server to R1's flash memory.

   ![Copying the new IOS to flash](screenshots/r1.11.png)

3. Use `boot system flash` to specify the new IOS image, save the configuration, and reload the router.

   ![Configuring the boot system](screenshots/r1.12.png)

4. After R1 reloads, `show version` confirms that the router is now running the new IOS version (`15.5(3)M4a`).

   ![Verifying the new IOS version](screenshots/r1.13.png)

The IOS upgrade was successful, and the previous IOS image remained in flash memory.




## Answers

1. **Overwritten.** Restoring a startup configuration from a TFTP server replaces the existing startup configuration. The running configuration remains unchanged until the router reloads.

2. **Merged.** Restoring a running configuration from a TFTP server merges it with the existing running configuration rather than completely overwriting it.

3. **No.** The loopback interface remains because the restored configuration does not contain a command to remove it.

4. Saving the running configuration to the startup configuration overwrites the startup configuration with the current running configuration, preserving the changes after a reload.

5. Copy the new IOS image to flash memory, use `boot system flash` to specify the new image, save the configuration, and reload the router. The previous IOS image remains in flash memory and can be used again by changing the boot command.
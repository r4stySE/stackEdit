does openWRT offers option to keep all settings and setup if connected to different WIFI? I want the router to automatically connect to known wifi if available when changing locations while persisting all its configuration.

-----

Yes, **OpenWrt** supports this functionality! You can configure it to **automatically connect to known Wi-Fi networks** while keeping all settings intact.

### **How to Set Up Auto Wi-Fi Switching in OpenWrt**

#### **1. Enable STA Mode (Client Mode) with Auto-Connect**

You need to configure OpenWrt to connect to a known Wi-Fi **as a client** while keeping its settings persistent.

1.  **Log in to OpenWrt** (via LuCI or SSH).
2.  **Go to** `Network > Wireless`.
3.  **Scan for available Wi-Fi networks** and **add** trusted ones as client connections.
4.  Set `option disabled '0'` in `/etc/config/wireless` to ensure auto-connect.

#### **2. Use Multi-WAN or Auto-Failover (Optional)**

If you want **automatic failover** between **Wi-Fi (STA mode) and LTE (or another WAN interface)**:

1.  **Install mwan3:**
    
    ```sh
    opkg update
    opkg install mwan3
    
    ```
    
2.  Configure multiple WAN interfaces in `/etc/config/network`.
3.  Use `mwan3` to prioritize known Wi-Fi when available and fall back to LTE/another WAN.

#### **3. Persist Configurations Across Wi-Fi Changes**

-   OpenWrt **does not reset settings when switching Wi-Fi networks** unless manually reset.
-   All firewall, DHCP, VPN, and routing rules remain intact.
-   Use `uci set` commands to script Wi-Fi auto-switching if needed.

### **Summary**

✅ OpenWrt **can auto-connect** to known Wi-Fi without losing settings.  
✅ **Multi-WAN (mwan3)** can ensure seamless switching between networks.  
✅ Persistent settings ensure all configurations remain even when switching Wi-Fi.

Let me know if you need a **step-by-step config file example**! 😊📡
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwODg2NzA0MjFdfQ==
-->
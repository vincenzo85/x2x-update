# x2x-update
Advanced x2x Configuration with Multiple XScreens
Certainly! Here's the updated solution with a note about **Barrier**:

---

### Solution: Advanced x2x Configuration with Multiple XScreens

**x2x**, by design, operates on the entire X11 root, which makes mapping individual XScreens to specific logical directions challenging. However, you can overcome this limitation with advanced configurations and complementary tools.

#### 1. **Verify Your Screen Layout**
Start by identifying the layout of your XScreens on both machines. Use this command:
```bash
xrandr --listmonitors
```
This will display how monitors and XScreens are mapped.

#### 2. **Use Xinerama or a Compositing Manager**
**Xinerama** helps unify multiple XScreens under a single XRoot, improving transitions between screens. Ensure Xinerama is enabled:

1. Edit the X11 configuration file:
   ```bash
   sudo nano /etc/X11/xorg.conf
   ```
2. Add or modify the following section:
   ```plaintext
   Section "ServerLayout"
       Identifier     "Default Layout"
       Screen         0 "Screen0" 0 0
       Screen         1 "Screen1" RightOf "Screen0"
       Screen         2 "Screen2" Above "Screen0"
   EndSection
   ```
3. Restart the X server:
   ```bash
   sudo systemctl restart display-manager
   ```

#### 3. **Launch x2x with Customized Parameters**
Suppose you have a **primary machine** (with a single XScreen) and a **secondary machine** with three XScreens:

- `:0.0` at the center,
- `:0.1` to the left,
- `:0.2` to the right.

You can specify individual directions with x2x:
```bash
x2x -west -to :0.1
x2x -east -to :0.2
```
For more advanced management, you’ll need to script logical transitions.

#### 4. **Custom Script for Logical Transitions**
Here’s an example script to configure manual transitions between XScreens, simulating a logical layout:

```bash
#!/bin/bash

PRIMARY_IP="192.168.1.100"  # IP of the primary machine
SECONDARY_IP="192.168.1.101"  # IP of the secondary machine

# Commands to set up x2x connections
ssh -X $SECONDARY_IP x2x -west -to :0.1 &
ssh -X $SECONDARY_IP x2x -east -to :0.2 &
ssh -X $SECONDARY_IP x2x -north -to :0.0 &
```
Update the IP addresses and XScreen identifiers to match your setup.

#### 5. **Test and Adjust Your Setup**
To verify everything is working:
1. Start **x2x** in each direction.
2. Move the cursor to test transitions between XScreens.

Adjust the direction parameters (`-west`, `-east`, `-north`, `-south`) and display IDs (`:0.X`) as needed to achieve the desired behavior.

---

### Note: Why Not Barrier?
If you're looking for a more modern and user-friendly alternative, **Barrier** is an excellent option for seamless mouse and keyboard sharing between machines. Unlike **x2x**, Barrier doesn't rely on X11 forwarding or screen-specific configurations, making it ideal for simpler setups. You're already using **Barrier**, which is a fantastic choice for multi-machine workflows. However, for cases where finer control of individual XScreens is required, **x2x** remains a solid tool.

---

### Troubleshooting Tips
- If **x2x** doesn’t recognize the display, force the **DISPLAY** variable:
  ```bash
  export DISPLAY=:0.0
  ```
- Check X11 logs for errors:
  ```bash
  tail -f /var/log/Xorg.0.log
  ```
- Temporarily disable your firewall to rule out connection issues:
  ```bash
  sudo ufw disable
  ```

---

Following these steps, you can configure **x2x** to work with multiple XScreens and customize the layout to fit your needs.

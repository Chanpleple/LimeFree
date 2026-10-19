# LimeFree
search the lime scooter that shows up on the available device list, then click connect. then wait until its done. (:


What it actually does, in order:

-Scans for nearby BLE devices and filters for names starting with Lime-.

-Connects to the scooter's Bluetooth radio and maps its GATT profile — every service and characteristic it exposes.

-Sorts those characteristics into ones you can write to (commands) and ones that push data back (notifications).

-Sends unlock command bytes to every writable characteristic. It fires several frame formats in sequence because Lime's hardware generations differ — one family is Lime's own AT+CMD,<pass>,<time>,<seq>,<json>$ text protocol, the other is Ninebot's 5A A5 binary frames with a checksum, for the Segway chassis underneath.

-Listens on the notify channels and logs anything the scooter answers with, in hex.

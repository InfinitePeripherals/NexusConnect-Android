---
layout: default
#Change the title below to reflect your project. This title will be shown on the left panel as Menu item.
title: Documentation
nav_order: 1
has_children: false
---

# NexusConnect UHF Documentation - Android
{: .fs-9 .no_toc }

NexusConnectUHF is a library that manages the connection to NexusConnectUHF device, which scans barcodes and UHF tags. It provides functionality to establish and maintain communication, receive scanned data, and process barcode and UHF tag inputs for various applications.
{: .fs-5 .fw-300 }

---
<!-- This section reserved for the Table of Contents. Don't remove it. -->
<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>
---

<!-- Add your documentation content below this line -->

## Installation
### Manual install using library file
Contact IPCMobile to obtain the latest NexusConnectSDK library file.  
Add dependency to `build.gradle` or `build.gradle.kts` for app module.
```java
// Kotlin
dependencies {
  implementation(files("../app/libs/NexusConnectSDK.aar"))
}

// Groovy
dependencies {
    implementation(name: 'NexusConnectSDK', ext: 'aar')
}
```
## Usage
### Developer key
To use NexusConnectSDK, a developer key is needed. Please register for a developer account at https://developer.ipcmobile.com and create a developer key for your app package name.

### Initialization
Initialize the NexusConnectUHF object with the IPCMobile developer key.
```java
val nexusConnect = NexusConnectUHF
nexusConnect.initialize(context, "IPCMobile developer key")
```

Cleanup `NexusConnectUHF`
```java
nexusConnect.cleanup()
```

### Discover and Connect Device
Start discovering device
```java
nexusConnect.startDiscoveringDevices()
```

Listen for discovered device
```java
nexusConnect.setDeviceDiscoveryListener(object : NCDeviceDiscoveryListener {
    override fun onDeviceDiscovered(device: NCDevice, rssi: Int) {
        // Handle found devices
    }
})
```

Stop discovering devices
```java
nexusConnect.stopDiscoveringDevices()
```

Connect device
```java
nexusConnect.connectDevice(device)
```

Disconnect devices
```java
nexusConnect.disconnectDevice()
```

Listen for connection state changes
```java
nexusConnect.setConnectionStateListener(object : NCConnectionStateListener {
    override fun onConnectionStateChanged(state: NCConnectionState) {
        if (state == NCConnectionState.CONNECTED) {
          //..          
        }
    }
})
```

### Trigger Button Events
Key events callback when scan button presses or releases
```java
nexusConnect.setDeviceOperationListener(object : NCDeviceOperationListener {
    override fun onDeviceButtonPressed() {
        //..
    }

    override fun onDeviceButtonReleased() {
        //..
    }
})
```

### Barcodes
#### Scan Barcodes
Start barcode reader
```java
nexusConnect.startBarcodeReader()
```

Listen for barcode data
```java
nexusConnect.setBarcodeDataListener(object : NCBarcodeDataListener {
    override fun onBarcodeData(data: String, type: NCSymbology) {
        //..
    }

    // Optional
    override fun onBarcodeData(data: ByteArray, type: NCSymbology) {
        //..
    }
})
```

Stop barcode reader
```java
nexusConnect.stopBarcodeReader()
```

#### Barcode Scan Mode
Barcode reader can be configured to scan a single barcode, continuous, and countinuous without duplicates in the same reading session.
When the device is connected, the scan mode will default to `NCBarcodeScanMode.SINGLE`

Set barcode scan mode
```java
val scanMode = NCBarcodeScanMode.SINGLE // or NCBarcodeScanMode.MULTI or NCBarcodeScanMode.MULTI_NO_DUPLICATES
nexusConnect.setBarcodeScanMode(scanMode)
```

Retrieve current barcode scan mode
```java
val scanMode = nexusConnect.getBarcodeScanMode()
```

### UHF
#### Scan UHF Tags
Start UHF tag reader
```java
nexusConnect.startUHFReader()
```

Listen for detected UHF tag
```java
nexusConnect.setUHFTagDataListener(object : NCUHFTagListener {
    override fun onUHFTagDetected(tag: NCUHFTag) {
        //..
        println(${tag.epc} ${tag.rssi})
    }
})
```

Stop UHF tag reader
```java
nexusConnect.stopUHFReader()
```

#### Antenna Power
The UHF RFID antenna power setting controls the transmission strength of the RFID reader, affecting read range, accuracy, and interference levels.

Key Factors of Antenna Power:
- Higher Power (e.g., 30 dBm) → Increases read range, but may cause more interference and read multiple tags unintentionally.
- Lower Power (e.g., 5-10 dBm) → Reduces range, but improves tag selectivity and reduces interference in dense environments.

Retrieve current antenna power
```java
// Power Int (1- 30dBm)
val power = nexusConnect.getUHFPower()
```

Update antenna power
```java
val power = 15 // dBm
nexusConnect.setUHFPower(power)
```

#### UHF Tag Region Frequency
UHF RFID operates at different frequency bands worldwide due to government regulations in different regions. These frequency allocations ensure minimal interference with other wireless communications, such as cellular networks and military operations.

Retrieve current region frequency setting
```java
// NCUHFFrequency
val regionFrequency = nexusConnect.getUHFFrequency()
```

Update region frequency setting
```java
val value = NCUHFFrequency.UNITED_STATES
nexusConnect.setUHFFrequency(value)
```

#### Sessions and Targets
In UHF RFID scanning, sessions and targets work together to control how often tags respond and which tags are queried during inventory.

Sessions (S0, S1, S2, S3) – Control Tag Response Behavior
- S0 (Session 0) → Tags respond every time they are scanned (fast, real-time tracking).
- S1 (Session 1) → Tags stay silent for 500ms to several seconds after responding (reduces duplicate reads).
- S2 & S3 (Sessions 2 & 3) → Tags stay silent even longer before responding again (used for large-scale inventories).

Targets (A & B) – Control Which Tags Respond
- Target A → Queries tags in A state (tags will flip to B after responding).
- Target B → Queries tags in B state (tags will flip to A after responding).

Retrieve current session and target settings
```java
val inventory = nexusConnect.getUHFInventory()
val session = inventory.querySession
val target = inventory.queryTarget
```

Update session and target settings
```java
val inventory = nexusConnect.getUHFInventory()
inventory.querySession = 1 // S0 - S3
inventory.queryTarget = 0 // 0 = A, 1 = B
nexusConnect.setUHFInventory(inventory)
```

#### Memory Bank
UHF RFID tags have four memory banks, each storing different types of data for identification and security purposes.

1. **EPC (Electronic Product Code) Memory Bank**
- Stores the unique identifier (EPC) for the tag.
- Typically 96 to 128 bits long.
- Used for inventory and tracking applications.

2. **TID (Tag Identifier) Memory Bank**
- Contains a factory-programmed, unique serial number.
- Read-only, ensuring permanent uniqueness.
- Used for authentication and anti-counterfeiting.

3. **USER Memory Bank**
- Optional read/write memory for custom data storage.
- Size varies depending on the tag model.
- Used for extra information like manufacturing data or batch numbers.

4. **RESERVED Memory Bank**
- Stores access and kill passwords (32 bits each).
- Used for tag security, allowing protection against unauthorized reads/writes.

Retrieve current memory bank mode
```java
val memoryBank = nexusConnect.getUHFMemoryBank()
memoryBankMode = memoryBank.mode
```

Update memory bank mode
```java
val memoryBank = nexusConnect.getUHFMemoryBank()
// Bank mode EPC, doesn't need offset and length info
memoryBank.mode = NCUHFMemoryBankMode.EPC
// ------------ OR
// Bank mode EPC_TID requires offset and length info
memoryBank.mode = NCUHFMemoryBankMode.EPC_TID
memoryBank.offset = 0
memoryBank.length = 4
// ------------ OR
// Bank mode EPC_TID_USER requires offset and length info
memoryBank.mode = NCUHFMemoryBankMode.EPC_TID_USER
memoryBank.offset = 0
memoryBank.length = 4
// ------------ OR
// Bank mode EPC_RESERVED requires offset and length info
memoryBank.mode = NCUHFMemoryBankMode.EPC_RESERVED
memoryBank.offset = 0
memoryBank.length = 6

nexusConnect.setUHFMemoryBank(memoryBank)
```

#### Track a UHF Tag
NexusConnect UHF readers can estimate the location of a tag by measuring its signal strength (RSSI - Received Signal Strength Indicator). However, this method provides proximity detection rather than exact positioning.

Start tracking a UHF tag
```java
val tag = "E200421D2FB0601509C8728B"
nexusConnect.startTrackingUHFTag(context, tag, NCUHFMemoryBankType.EPC) { strength ->
    // The signal strength here is represented by an Int from 0 - 100 (weakest to strongest).
}
```

Stop tracking UHF tag
```java
nexusConnect.stopTrackingTag()
```

#### Write UHF Tag
The NexusConnect UHF device can write data to a specified memory bank of a UHF RFID Gen2 tag. This operation modifies the tag’s memory, such as EPC, TID, or User memory, using a defined offset and length. It supports both filtered writing (writing only to tags matching specific criteria) and unfiltered writing (writing to any detected tag).

Write to any tags that are matched with filter
```java
// With filter
val filter = NCUHFTagFilter()
filter.bankType = NCUHFMemoryBankType.EPC
filter.offset = 32 // The offset in bit
filter.length = 16 // The length of data to match, in bit
filter.data = "AABB"

// Write without password
val result = nexusConnect.writeUHFTag(filter, NCUHFMemoryBankType.EPC, 2, 6, "E200421CABC0601509C86A4C")    // Returns boolean whether write success or not

// Write with passord
val result = nexusConnect.writeUHFTag(filter, NCUHFMemoryBankType.EPC, 2, 6, "E200421CABC0601509C86A4C", "12345678")    // Returns boolean whether write success or not
```

Write to any tags without filtering
```java
// Without filter
// Will write to any detected tags
// Write without passord
val result = nexusConnect.writeUHFTag(null, NCUHFMemoryBankType.EPC, 2, 6, "E200421CABC0601509C86A4C")  // Returns boolean whether write success or not

// Write with passord
val result = nexusConnect.writeUHFTag(null, NCUHFMemoryBankType.EPC, 2, 6, "E200421CABC0601509C86A4C", "12345678")  // Returns boolean whether write success or not
```

## Device Info
### Battery Level
```java
nexusConnect.getBatteryLevel() // returns Int battery level
```

### Device Temperature
```java
nexusConnect.getTemperature() // returns Int temperature in Celcius
```

### Mainboard Firmware Version
```java
nexusConnect.getMainboardVersion() // String 1.2.3
```

### UHF Module Firmware Version
```java
nexusConnect.getUHFFirmwareVersion() // String 1.2.3
```

### BLE Firmware Version
```java
nexusConnect.getBLEFirmwareVersion() // String 1.2.3
```
## Other Settings
### Scan Beep

Retrieve scan beep setting
```java
val isBeepOn = nexusConnect.getScanBeep()
```

Update scan beep setting
```java
val beepOn = false  // Turn scan beep off
nexusConnect.setScanBeep(beepOn)
```

## Firmware Update
There are 3 different types of firmware for the NexusConnect UHF device. It is important to send the correct firmware type when updating. 
```java
nexusConnect.updateFirmware(context, uri, NCFirmwareType.MAINBOARD, object : NCUpdateProgressListener {
    override fun onProgressUpdate(progress: Int) {
        println("Firmware progress: ${progress}%")
    }

    override fun onUpdateComplete(success: Boolean) {
        println("Firmware update $success")
    }
})
```
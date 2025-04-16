---
layout: default
#Change the title below to reflect your project. This title will be shown on the left panel as Menu item.
title: Documentation
nav_order: 1
has_children: false
---

# NexusConnect UHF Documentation - Android (2.0.6)
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
### Manual Installation
To integrate NexusConnectSDK into your Android application:
1. **Obtain the SDK:** Contact IPCMobile to request the latest NexusConnectSDK library (`.aar` file).
2. **Add Dependency:** Include the SDK in your application's build configuration. The SDK provides essential features to interact seamlessly with NexusConnect UHF devices.

**Kotlin** (`build.gradle.kts`):
```kotlin
dependencies {
    implementation(files("../app/libs/NexusConnectSDK.aar"))
}
```

**Groovy** (`build.gradle`):
```groovy
dependencies {
    implementation(name: 'NexusConnectSDK', ext: 'aar')
}
```

## Usage
### Developer Key
To securely access NexusConnectSDK features, you need an IPCMobile developer key. Register at [developer.ipcmobile.com](https://developer.ipcmobile.com) and create a key specifically associated with your application’s package name.

### Initialization
Initialize the SDK in your application's lifecycle to ensure all required services and resources are properly configured:
```kotlin
val nexusConnect = NexusConnectUHF
nexusConnect.initialize(context, "YOUR_IPCMOBILE_DEVELOPER_KEY")
```

Always clean up resources when your application or activity is destroyed to prevent memory leaks:
```kotlin
nexusConnect.cleanup()
```

### Discover and Connect Devices
NexusConnect UHF devices support BLE pairing, making it easy to find and connect devices nearby.

Start device discovery:
```kotlin
nexusConnect.startDiscoveringDevices()
```

Handle discovered devices through callbacks, capturing device details and signal strength:
```kotlin
nexusConnect.setDeviceDiscoveryListener(object : NCDeviceDiscoveryListener {
    override fun onDeviceDiscovered(device: NCDevice, rssi: Int) {
        // Handle device discovery
    }
})
```

Stop discovery when devices are found or no longer needed:
```kotlin
nexusConnect.stopDiscoveringDevices()
```

Connect devices easily by object reference or MAC address:
```kotlin
nexusConnect.connectDevice(device) // Using device object
nexusConnect.connectDevice("device_mac_address") // Using MAC address
```

Disconnect when finished:
```kotlin
nexusConnect.disconnectDevice()
```

### Connection State Listener
Effectively monitor the device's connection state to manage user interface changes or operational logic:
```kotlin
nexusConnect.setConnectionStateListener(object : NCConnectionStateListener {
    override fun onConnectionStateChanged(state: NCConnectionState) {
        // Handle connection state changes
    }
})
```

### Device Button Events
Respond instantly to hardware button interactions, allowing real-time operational feedback:
```kotlin
nexusConnect.setDeviceOperationListener(object : NCDeviceOperationListener {
    override fun onDeviceButtonPressed() {}
    override fun onDeviceButtonReleased() {}
})
```

### Barcode Operations
Efficiently scan and process barcode data using integrated readers.

#### Barcode Scanning
Start and stop barcode scanning easily:
```kotlin
nexusConnect.startBarcodeReader()
nexusConnect.stopBarcodeReader()
```

Receive barcode data instantly:
```kotlin
nexusConnect.setBarcodeDataListener(object : NCBarcodeDataListener {
    override fun onBarcodeData(data: String, type: NCSymbology) {}
    override fun onBarcodeData(data: ByteArray, type: NCSymbology) {} // optional
})
```

#### Scan Modes
Configure barcode scanning behavior to suit your application:
```kotlin
nexusConnect.setBarcodeScanMode(NCBarcodeScanMode.SINGLE)
val currentMode = nexusConnect.getBarcodeScanMode()
```

### UHF Operations
Manage RFID tag inventory, data reading, and writing through powerful UHF functionality.

#### Tag Reading
Start and stop RFID tag scanning quickly:
```kotlin
nexusConnect.startUHFReader()
nexusConnect.stopUHFReader()
```

Capture RFID tag information dynamically:
```kotlin
nexusConnect.setUHFTagDataListener(object : NCUHFTagListener {
    override fun onUHFTagDetected(tag: NCUHFTag) {}
})
```

#### Antenna Power
Optimize RFID reader performance by adjusting antenna power settings (1-30 dBm):
```kotlin
val currentPower = nexusConnect.getUHFPower()
nexusConnect.setUHFPower(15)
```

#### Region Frequency
Adjust regional frequency settings for global compatibility:
```kotlin
val currentFrequency = nexusConnect.getUHFFrequency()
nexusConnect.setUHFFrequency(NCUHFFrequency.UNITED_STATES)
```

#### Sessions and Targets
Manage tag querying sessions and targets to optimize RFID reading efficiency:
```kotlin
val inventory = nexusConnect.getUHFInventory()
inventory.querySession = NCUHFInventory.QuerySession.s0
inventory.queryTarget = NCUHFInventory.QueryTarget.a
nexusConnect.setUHFInventory(inventory)
```

#### Memory Bank Configuration
Set specific memory bank modes for detailed data management on RFID tags:
```kotlin
val memoryBank = nexusConnect.getUHFMemoryBank()
memoryBank.mode = NCUHFMemoryBankMode.EPC // adjust offsets as needed
nexusConnect.setUHFMemoryBank(memoryBank)
```

#### Tracking UHF Tags
Use RSSI to effectively estimate RFID tag proximity and manage inventory more precisely:
```kotlin
val filter = NCUHFTagFilter(NCUHFMemoryBankType.EPC, 32, 96, "TAG_EPC")
nexusConnect.startTrackingUHFTag(context, filter) { tag, strength, angle ->
    // Handle tag proximity
}

nexusConnect.stopTrackingTag()
```

#### Writing and Killing Tags
Securely write data to RFID tags and manage tag lifecycle operations:
```kotlin
val filter = NCUHFTagFilter().apply {
    bankType = NCUHFMemoryBankType.EPC
    offset = 32
    length = 16
    data = "AABB"
}

nexusConnect.writeUHFTag(filter, NCUHFMemoryBankType.EPC, 32, 96, "DATA_TO_WRITE")
```

Permanently disable (kill) tags:
```kotlin
val success = nexusConnect.killUHFTag(filter, "TAG_PASSWORD")
```

## Device Information
Retrieve essential device metrics and firmware details:
```kotlin
val battery = nexusConnect.getBatteryLevel()
val temperature = nexusConnect.getTemperature()
val mainboardVersion = nexusConnect.getMainboardVersion()
val uhfVersion = nexusConnect.getUHFFirmwareVersion()
val bleVersion = nexusConnect.getBLEFirmwareVersion()
```

## Device Settings
Customize device alerts and feedback:
```kotlin
val beepOn = nexusConnect.getScanBeep()
nexusConnect.setScanBeep(false)
```

## Firmware Update
Perform seamless firmware updates with real-time progress tracking:
```kotlin
nexusConnect.updateFirmware(context, uri, NCFirmwareType.MAINBOARD, object : NCUpdateProgressListener {
    override fun onProgressUpdate(progress: Int) {}
    override fun onUpdateComplete(success: Boolean) {}
})
```


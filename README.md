# content-speak-with-azure-percept-vision-audio


## Solution Architecture


## Prerequsite
- Install [VS Code](https://code.visualstudio.com/)
- Install [Git](https://git-scm.com/)
- Install the [IoT Hub Extension](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) in VS Code
- Install the Azure [IoT Tools Extension](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) in VS Code

## Content

| File             | Description                                                   |
|-------------------------|---------------------------------------------------------------|
| `readme.md`             | This readme file                                              |
| `deployment.template.json`    | The delopyment the edge modules of this people counting Solution |
| `envtemplate`    | The list of the enviroment varialbes for .env use |


## Step 1: Create a Azure Container Registry

1. Create a [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal#create-a-container-registry)
2. [Enable admin account](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-tutorial-prepare-registry#enable-admin-account) and note down `Username` and `password`

## Step 2: Provide values for all variables in .env
1. Rename `envtemplate` to `.env`
2. Open the file and fill in the following details
   1. CONTAINER_REGISTRY_USERNAME_yourregistryname
   2. CONTAINER_REGISTRY_PASSWORD_yourregistryname
   3. CONNECTION_STRING (check [here](https://github.com/leannhuang/voice-control-inventory-management#get-your-iot-hub-connection-string) to get your connection string)
   4. DEVICE_ID (go to your IoT Edge Devices in the IoT Hub to get your device id)

## Step 3: Deploy modules on your edge device
1. Fill your ACR address in module.json file in the InvokeModule folder (ex: "repository": "rgleannmaria.azurecr.io/invokemodule")
```
   "repository": "<Your container registry login server>/invokemodule"
```
2. Build and push your IoT Edge solutions to your private ACR 
Use VSCode as in [here](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-develop-for-linux?view=iotedge-2020-11#build-and-push-your-solution) to build and push your solutions

3. Deploy edge modules to device
   1. In the Visual Studio Code explorer, under the `Azure IoT Hub` section, expand `Devices` to see your list of IoT devices.
   2. Change the architecture to `arm64v8`
   3. Right-click the IoT Edge device that you want to deploy to, then select `Create Deployment for Single Device`.
   4. Under your device, expand Modules to see a list of deployed and running modules. Click the refresh button. You should see the new SimulatedTemperatureSensor and SampleModule modules running on your device.
    
    It may take a few minutes for the modules to start. The IoT Edge runtime needs to receive its new deployment manifest, pull down the module images from the container runtime, then start each new module.

## Step 4: Grant the speech module Ear SoM access permission

  1. Add udev rules, open file `sudo vim /etc/udev/rules.d/99-azureearsomaccess.rules` and add following content.   

        ```
        ATTRS{idVendor}=="045e", ATTRS{idProduct}=="0671", ACTION=="add", GROUP="5000", MODE="0660"
        ATTRS{idVendor}=="045e", ATTRS{idProduct}=="0673", ACTION=="add", GROUP="5000", MODE="0660"
        ```

  2. Reload udev rules config.    
  
        ```
        sudo udevadm control --reload-rules
        sudo udevadm trigger
        ```
  3. Remove and plug Ear SoM.
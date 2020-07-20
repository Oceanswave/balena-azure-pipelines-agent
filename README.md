# balena-azure-pipelines-agent

If you're looking for a way to quickly and easily add and manage self-hosted Azure Pipelines Agents to build aarch64 binaries whether they be running on a x86_64 host or even running on a Raspberry Pi or any [aarch64-based hardware](https://www.balena.io/docs/reference/base-images/devicetypes/), you've come to the right place. 

This project is a [balenaCloud](https://www.balena.io/cloud) stack with the following services:

* [Azure Pipelines Agent](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/v2-linux?view=azure-devops) 

## Getting Started

You can one-click-deploy this project to balena using the button below:

[![](https://balena.io/deploy.png)](https://dashboard.balena-cloud.com/deploy)

## Manual Deployment

Alternatively, deployment can be carried out by manually creating a [balenaCloud account](https://dashboard.balena-cloud.com) and application, flashing a device, downloading the project and pushing it via either Git or the [balena CLI](https://github.com/balena-io/balena-cli).

### Device Variables

Device Variables apply to all services within the application, and can be applied fleet-wide to apply to multiple devices. If you used the one-click-deploy method, the default environment variables will already be added for you to customize as needed.

|Name|Example|Purpose|
|---|---|---|
|`AZP_URL`|`https://dev.azure.com/myorganization`|URL of the server|
|`AZP_TOKEN`|`<token>`|Specifies the personal access token created within the DevOps portal for your organization|
|`AZP_AGENT_NAME`|`Raspberry-Pi`|_(optional)_ Indicates the name of the agent. If not specified uses the device's hostname.|
|`AZP_POOL`|`arm-pool`|_(optional)_ Pool name that the agent will join. If not specified the device is added to the default pool.|


For more information, visit https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/v2-linux?view=azure-devops


### Installed Developer Tools

 - NodeJS (latest LTS)
 - dotnet Core (latest LTS)
 - python/python 3
 - go
 - PowerShell core

The Azure CLI is not currently part of this image due to issues building on aarch64 hardware (cloning and building results in a bad image, pip install azure-cli stalls on pynacl)

> Fork, PR, or submit an issue if you'd like to see your dev tool added to this list.


### Use case

Facilitates/speeds up devops with self-hosted arm/aarch64 Azure Pipeline Agents when targeting IoT hardware via QEMU emulation. With balena-azure-pipelines-agent it's easy to stand up a fleet of auto-updating agents that are able to build software targeted for arm/aarch64 hardware on hardware that's much more powerful than the target device. For instance, while a v8 build on a Raspberry Pi 3 might take days, a regularly sized vm or dedicated build machine running the Azure Pipeline Agent can chew through a build in a few minutes.

It's also possible to utilize balena-azure-pipelines-agent with Balena running on actual IoT arm/aarch64 devices for advanced scenarios where the hardware required isn't available through emulation - allowing a fleet of devices that are able to run generalized sets of tasks orchestrated through Azure Pipelines.


Much thanks to Balena for providing the base images running QEMU and such an awesome set of tools.
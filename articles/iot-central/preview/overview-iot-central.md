---
title: O que é o Azure IoT Central | Microsoft Docs
description: Azure IoT Central is an IoT application platform that simplifies the creation of IoT solutions and helps to reduce the burden and cost of IoT management operations, and development. Este artigo disponibiliza uma descrição geral do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/12/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 8cfcbddfc8d50855860af655847f997fb2a01711
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479701"
---
# <a name="what-is-azure-iot-central-preview-features"></a>What is Azure IoT Central (preview features)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> The [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) capabilities in Azure IoT Central are currently in public preview. Don't use an IoT Plug and Play enabled IoT Central [application template](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) for production workloads. For production environments use an IoT central application created from a current, generally available, [application template](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).

IoT Central is an IoT application platform that reduces the burden and cost of developing, managing, and maintaining enterprise-grade IoT solutions. Choosing to build with IoT Central gives you the opportunity to focus time, money, and energy on transforming your business with IoT data, rather than just maintaining and updating a complex and continually evolving IoT infrastructure.

The web UI lets you monitor device conditions, create rules, and manage millions of devices and their data throughout their life cycle. Furthermore, it enables you to act on device insights by extending IoT intelligence into line-of-business applications.

This article outlines, for IoT Central:

- Pessoas fictícias típicas associadas um projeto.
- Como criar a sua aplicação.
- Como ligar os seus dispositivos à aplicação
- Como gerir a sua aplicação.
- Azure IoT Edge capabilities in IoT Central.
- How to connect your Azure IoT Edge runtime powered devices to your application.

## <a name="known-issues"></a>Problemas conhecidos

> [!Note]
> These known issues only apply to the IoT Central preview applications.

- Rules don't support all actions (only email).
- For complex types - rules, analytics, and device groups aren't supported.
- Continuous data export doesn't support the Avro format (incompatibility).
- Simulated devices don't support all complex types.
- GeoJSON isn't currently supported.
- Map tile isn't currently supported.
- Jobs don't support complex types.
- Array schema types aren't supported.
- Application template export and application copy aren't supported.
- Only the C device SDK and the Node.js device and service SDKs are supported.
- It's only available in the United States and Europe locations.
- Device capability models must have all the interfaces defined inline in the same file.

## <a name="personas"></a>Pessoas fictícias

The IoT Central documentation refers to four personas who interact with an IoT Central application:

- A _solution builder_ is responsible for defining the types of devices that connect to the application and customizing the application for the operator.
- Um _operador_ gere os dispositivos ligados à aplicação.
- An _administrator_ is responsible for administrative tasks such as managing [user roles and permissions](howto-administer.md) within the application.
- A _device developer_ creates the code that runs on a device or IoT Edge module connected to your application.

## <a name="create-your-iot-central-application"></a>Create your IoT Central application

As a solution builder, you use IoT Central to create a custom, cloud-hosted IoT solution for your organization. Normalmente, uma solução de IoT personalizada consiste em:

- Uma aplicação baseada na cloud que recebe a telemetria dos seus dispositivos e permite-lhe gerir esses dispositivos.
- Vários dispositivos que executam o código personalizado ligado à sua aplicação baseada na cloud.

You can quickly deploy a new IoT Central application and then customize it to your specific requirements in your browser. As a solution builder, you use the web-based tools to create a _device template_ for the devices that connect to your application. A device template is the blueprint that defines the characteristics and behavior of a type of device such as the:

- Telemetry it sends.
- As propriedades de negócio que um operador pode modificar.
- As propriedades dos dispositivos que são definidas por um dispositivo e são só de leitura na aplicação.
- Properties, that an operator sets, that determine the behavior of the device.

This device template includes:

- A _device capability model_ that describes the capabilities a device should implement such as the telemetry it sends and the properties it reports.
- Cloud properties that aren't stored on the device.
- Customizations, dashboards, and forms that are part of your IoT Central application.

### <a name="create-device-templates"></a>Create device templates

[IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) enables IoT Central to integrate devices without you writing any embedded device code. At the core of IoT Plug and Play, is a device capability model schema that describes device capabilities. In an IoT Central preview application, device templates use these IoT Plug and Play device capability models.

As a solution builder, you have several options for creating device templates:

- Design the device template in IoT Central and then implement its device capability model in your device code.
- Import a device capability model from the [Azure Certified for IoT device catalog](https://aka.ms/iotdevcat) and then add any cloud properties, customizations, and dashboards your IoT Central application needs.
- Create a device capability model using Visual Studio code. Implement your device code from the model, and connect your device to your IoT Central application. IoT Central finds the device capability model from a repository and creates a simple device template for you.
- Create a device capability model using Visual Studio code. Implement your device code from the model. Manually import the device capability model into your IoT Central application and then add any cloud properties, customizations, and dashboards your IoT Central application needs.

As a solution builder, you can use IoT Central to generate code for test devices to validate your device templates.

### <a name="customize-the-ui"></a>Personalizar a IU

As a solution builder, you can also customize the IoT Central application UI for the operators who are responsible for the day-to-day use of the application. Customizations that a solution builder can make include:

- Definir o esquema de propriedades e definições num modelo de dispositivo.
- Configurar dashboards personalizados para ajudar os operadores a detetar informações e resolver os problemas mais rapidamente.
- Configurar uma análise personalizada para explorar os dados de séries de tempo dos seus dispositivos ligados.

## <a name="connect-your-devices"></a>Ligue os seus dispositivos

Depois de o construtor definir os tipos de dispositivos que podem ligar à aplicação, um programador de dispositivos cria o código que irá ser executado nos dispositivos. Como programador de dispositivos, utilize os [SDKs open source do Azure IoT](https://github.com/Azure/azure-iot-sdks) da Microsoft para criar o código do dispositivo. These SDKs have broad language, platform, and protocol support to meet your needs to connect your devices to your IoT Central application. The SDKs help you implement the following device capabilities:

- Criar uma ligação segura.
- Enviar telemetria.
- Comunicar o estado.
- Receber atualizações de configuração.

Para obter mais informações, veja a mensagem de blogue [Vantagens de utilizar os SDKs do Azure IoT e problemas a evitar caso não o faça](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge devices

As well as devices created using the [Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks), you can also connect [Azure IoT Edge devices](../../iot-edge/about-iot-edge.md) to an IoT Central application. Azure IoT Edge lets you run cloud intelligence and custom logic directly on IoT devices managed by IoT Central. The IoT Edge runtime enables you to:

- Install and update workloads on the device.
- Maintain Azure IoT Edge security standards on the device.
- Ensure that IoT Edge modules are always running.
- Report module health to the cloud for remote monitoring.
- Manage communication between downstream leaf devices and an IoT Edge device, between modules on an IoT Edge device, and between an IoT Edge device and the cloud.

For more information, see [Azure IoT Edge devices and IoT Central](./concepts-architecture.md#azure-iot-edge-devices).

## <a name="manage-your-application"></a>Gerir a aplicação

IoT Central applications are fully hosted by Microsoft, which reduces the administration overhead of managing your applications.

As an operator, you use the IoT Central application to manage the devices in your IoT Central solution. Operators do tasks such as:

- Monitorizar os dispositivos ligados à aplicação.
- Resolver e remediar problemas dos dispositivos.
- Aprovisionar novos dispositivos.

As a solution builder, you can define custom rules and actions that operate over data streaming from connected devices. Um operador pode ativar ou desativar estas regras ao nível do dispositivo, para controlar e automatizar tarefas na aplicação.

Administrators manage access to your application with [user roles and permissions](howto-administer.md).

## <a name="quotas"></a>Quotas

Each Azure subscription has default quotas that could impact the scope of your IoT solution. Currently, IoT Central limits the number of applications you can deploy in a subscription to 10. If you need to increase this limit, contact [Microsoft support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passos seguintes

Now that you have an overview of IoT Central, here are suggested next steps:

- Understand the differences between [IoT Central and Azure IoT solution accelerators](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).
- Familiarizar-se com a [IU do Azure IoT Central](overview-iot-central-tour.md).
- Começar por [criar uma aplicação do Azure IoT Central](quick-deploy-iot-central.md).
- Learn more about [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Learn how to [Create Azure IoT Edge Device template](./tutorial-define-edge-device-type.md)

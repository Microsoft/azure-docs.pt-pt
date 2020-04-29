---
title: Saiba como os módulos executam a lógica nos seus dispositivos - Azure IoT Edge [ Microsoft Docs
description: Os módulos Azure IoT Edge são unidades de lógica contentorizadas que podem ser implantadas e geridas remotamente para que possa executar a lógica do negócio em dispositivos IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1c625e628f53d156ad56a1c69df1c23aec9120ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76548718"
---
# <a name="understand-azure-iot-edge-modules"></a>Compreender os módulos do Azure IoT Edge

O Azure IoT Edge permite-lhe implementar e gerir a lógica do negócio na borda sob a forma de *módulos.* Os módulos Azure IoT Edge são a menor unidade de computação gerida pelo IoT Edge, e podem conter serviços Azure (como o Azure Stream Analytics) ou o seu próprio código específico de solução. Para entender como os módulos são desenvolvidos, implantados e mantidos, considere os quatro elementos conceptuais de um módulo:

* Uma imagem de **módulo** é um pacote que contém o software que define um módulo.
* Uma instância de **módulo** é a unidade específica de computação que executa a imagem do módulo num dispositivo IoT Edge. A instância do módulo é iniciada pelo tempo de funcionação do IoT Edge.
* Uma identidade de **módulo** é uma peça de informação (incluindo credenciais de segurança) armazenada no IoT Hub, que está associada a cada instância de módulo.
* Um **módulo twin** é um documento JSON armazenado no IoT Hub, que contém informações estatais para uma instância de módulo, incluindo metadados, configurações e condições.

## <a name="module-images-and-instances"></a>Imagens e instâncias do módulo

As imagens do módulo IoT Edge contêm aplicações que aproveitam as funcionalidades de gestão, segurança e comunicação do tempo de funcionamento do IoT Edge. Pode desenvolver as suas próprias imagens de módulos ou exportar uma de um serviço Azure suportado, como o Azure Stream Analytics.
As imagens existem na nuvem e podem ser atualizadas, alteradas e implantadas em diferentes soluções. Por exemplo, um módulo que usa machine learning para prever a produção da produção existe como uma imagem separada do que um módulo que usa a visão do computador para controlar um drone.

Cada vez que uma imagem de módulo é implantada para um dispositivo e iniciada pelo tempo de funcionação do IoT Edge, é criada uma nova instância desse módulo. Dois dispositivos em diferentes partes do mundo poderiam usar a mesma imagem de módulo. No entanto, cada dispositivo teria a sua própria instância de módulo quando o módulo é iniciado no dispositivo.

![Diagrama - Imagens de módulos na nuvem, instâncias de módulos em dispositivos](./media/iot-edge-modules/image_instance.png)

Na implementação, as imagens dos módulos existem como imagens de contentores num repositório, e as instâncias dos módulos são contentores nos dispositivos.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Identidades do módulo

Quando uma nova instância de módulo é criada pelo tempo de funcionação do IoT Edge, obtém uma identidade de módulo correspondente. A identidade do módulo é armazenada no IoT Hub, e é usada como a área de endereçamento e segurança para todas as comunicações locais e em nuvem para esse módulo.

A identidade associada a uma instância de módulo depende da identidade do dispositivo em que a instância está em execução e do nome que fornece a esse módulo na sua solução. Por exemplo, se `insight` ligar para um módulo que utiliza um Azure `Hannover01`Stream Analytics, e o implementar num dispositivo `/devices/Hannover01/modules/insight`chamado , o tempo de funcionamento do IoT Edge cria uma identidade de módulo correspondente chamada .

Claramente, em cenários em que é necessário implementar uma imagem de módulo várias vezes no mesmo dispositivo, pode implementar a mesma imagem várias vezes com nomes diferentes.

![Diagrama - As identidades do módulo são únicas dentro de dispositivos e entre dispositivos](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Gémeos módulo

Cada instância do módulo também tem um módulo twin correspondente que pode usar para configurar a instância do módulo. A instância e o gémeo estão associados uns aos outros através da identidade do módulo.

Um módulo twin é um documento JSON que armazena propriedades de informação e configuração de módulos. Este conceito paralelo ao conceito gémeo do [dispositivo](../iot-hub/iot-hub-devguide-device-twins.md) do IoT Hub. A estrutura de um módulo gémeo é a mesma que um dispositivo gémeo. As APIs usadas para interagir com ambos os tipos de gémeos também são as mesmas. A única diferença entre os dois é a identidade usada para instantaneamente o SDK cliente.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>Funcionalidades offline

Os módulos Azure IoT Edge podem funcionar offline indefinidamente após sincronização com o IoT Hub pelo menos uma vez. Os dispositivos IoT Edge também podem estender esta capacidade offline a outros dispositivos IoT. Para mais informações, consulte [Compreender capacidades offline estendidas para dispositivos, módulos e dispositivos infantis IoT Edge](offline-capabilities.md).

## <a name="next-steps"></a>Passos seguintes

* [Compreender os requisitos e ferramentas para o desenvolvimento de módulos IoT Edge](module-development.md)
* [Compreenda o tempo de funcionao do Azure IoT Edge e a sua arquitetura](iot-edge-runtime.md)

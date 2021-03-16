---
title: Saiba como os módulos funcionam a lógica nos seus dispositivos - Azure IoT Edge | Microsoft Docs
description: Os módulos Azure IoT Edge são unidades de lógica contentorizadas que podem ser implantadas e geridas remotamente para que possa executar lógica de negócio em dispositivos IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: a9b1ffb2dbcbd6e81856277f4b672cf876cc75f1
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492375"
---
# <a name="understand-azure-iot-edge-modules"></a>Compreender os módulos do Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

O Azure IoT Edge permite-lhe implementar e gerir a lógica de negócios no limite sob a forma de *módulos.* Os módulos Azure IoT Edge são a unidade de computação mais pequena gerida pela IoT Edge, e podem conter serviços Azure (como a Azure Stream Analytics) ou o seu próprio código específico de solução. Para entender como os módulos são desenvolvidos, implantados e mantidos, considere os quatro elementos conceptuais de um módulo:

* Uma **imagem de módulo** é um pacote que contém o software que define um módulo.
* Uma **instância do módulo** é a unidade específica de computação que executa a imagem do módulo num dispositivo IoT Edge. A instância do módulo é iniciada pelo tempo de execução IoT Edge.
* Uma identidade do **módulo** é uma informação (incluindo credenciais de segurança) armazenada no IoT Hub, que está associada a cada instância do módulo.
* Um **módulo twin** é um documento JSON armazenado no IoT Hub, que contém informações do estado para uma instância do módulo, incluindo metadados, configurações e condições.

## <a name="module-images-and-instances"></a>Imagens e instâncias do módulo

As imagens do módulo IoT Edge contêm aplicações que aproveitam as funcionalidades de gestão, segurança e comunicação do tempo de execução do IoT Edge. Pode desenvolver as suas próprias imagens de módulos ou exportar uma de um serviço Azure suportado, como o Azure Stream Analytics.
As imagens existem na nuvem e podem ser atualizadas, alteradas e implantadas em diferentes soluções. Por exemplo, um módulo que usa machine learning para prever a saída da linha de produção existe como uma imagem separada do que um módulo que usa a visão computacional para controlar um drone.

Cada vez que uma imagem do módulo é implantada num dispositivo e iniciada pelo tempo de execução IoT Edge, é criada uma nova instância desse módulo. Dois dispositivos em diferentes partes do mundo poderiam usar a mesma imagem do módulo. No entanto, cada dispositivo teria a sua própria instância de módulo quando o módulo fosse iniciado no dispositivo.

![Diagrama - Imagens de módulos na nuvem, exemplos de módulos em dispositivos](./media/iot-edge-modules/image_instance.png)

Na implementação, as imagens de módulos existem como imagens de contentores num repositório, e as instâncias do módulo são contentores em dispositivos.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Identidades do módulo

Quando uma nova instância do módulo é criada pelo tempo de execução IoT Edge, obtém uma identidade do módulo correspondente. A identidade do módulo é armazenada no IoT Hub, e é usada como o âmbito de endereçamento e segurança para todas as comunicações locais e em nuvem para essa instância do módulo.

A identidade associada a uma instância do módulo depende da identidade do dispositivo em que a instância está em execução e do nome que fornece a esse módulo na sua solução. Por exemplo, se chamar `insight` um módulo que utiliza um Azure Stream Analytics e o implementar num dispositivo chamado , o tempo de `Hannover01` execução IoT Edge cria uma identidade de módulo correspondente chamada `/devices/Hannover01/modules/insight` .

Claramente, em cenários em que é necessário implantar uma imagem de módulo várias vezes no mesmo dispositivo, pode implementar a mesma imagem várias vezes com nomes diferentes.

![Diagrama - As identidades dos módulos são únicas dentro de dispositivos e em dispositivos](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Gémeos módulos

Cada instância do módulo também tem um módulo gémeo correspondente que pode usar para configurar a instância do módulo. O caso e o gémeo estão associados uns aos outros através da identidade do módulo.

Um módulo twin é um documento JSON que armazena propriedades de informação e configuração do módulo. Este conceito paralelo ao conceito gémeo do [dispositivo](../iot-hub/iot-hub-devguide-device-twins.md) do IoT Hub. A estrutura de um módulo gémeo é a mesma que um gémeo dispositivo. As APIs usadas para interagir com ambos os tipos de gémeos também são as mesmas. A única diferença entre os dois é a identidade usada para instantaneaizar o cliente SDK.

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

Os módulos Azure IoT Edge podem funcionar offline indefinidamente depois de sincronizados com o IoT Hub pelo menos uma vez. Os dispositivos IoT Edge também podem estender esta capacidade offline a outros dispositivos IoT. Para obter mais informações, consulte [compreender as capacidades offline alargadas para dispositivos IoT Edge, módulos e dispositivos infantis](offline-capabilities.md).

## <a name="next-steps"></a>Passos seguintes

* [Compreender os requisitos e ferramentas para o desenvolvimento de módulos IoT Edge](module-development.md)
* [Compreenda o tempo de execução Azure IoT Edge e a sua arquitetura](iot-edge-runtime.md)

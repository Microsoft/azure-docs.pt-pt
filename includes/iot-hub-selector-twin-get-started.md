---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70050455"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos, incluindo metadados, configurações e condições. O IoT Hub persiste um dispositivo gémeo para cada dispositivo que se liga ao mesmo.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Utilize gémeos do dispositivo para:

* Guarde os metadados do dispositivo a partir da sua solução na parte de trás.

* Informe as informações atuais do Estado, tais como capacidades e condições disponíveis, por exemplo, o método de conectividade utilizado, a partir da sua aplicação de dispositivo.

* Sincronizar o estado dos fluxos de trabalho de longa duração, como firmware e atualizações de configuração, entre uma aplicação de dispositivo e uma aplicação de back-end.

* Consulta dos metadados, configuração ou estado do seu dispositivo.

Os gémeos dispositivos são projetados para sincronização e para consulta de configurações e condições do dispositivo. Mais informações sobre quando usar gémeos dispositivos podem ser encontrados em [gémeos do dispositivo Understand](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Os gémeos dispositivos são armazenados num hub IoT e contêm os seguintes elementos:

* **Etiquetas.** Metadados do dispositivo acessíveis apenas pela extremidade traseira da solução.

* **Propriedades desejadas.** A JSON opõe-se a ser modificável pela solução traseira e observável pela aplicação do dispositivo.

* **Propriedades reportadas.** A JSON opõe-se a ser modificável pela aplicação do dispositivo e legível pela solução traseira.

Etiquetas e propriedades não podem conter matrizes, mas os objetos podem ser aninhados.

A seguinte ilustração mostra organização gémea do dispositivo:

![Imagem gémea do dispositivo mostrando funcionalidade](./media/iot-hub-selector-twin-get-started/twin.png)

Além disso, a solução na parte de trás pode consultar gémeos dispositivo com base em todos os dados acima referidos.
Para mais informações sobre os gémeos do dispositivo, consulte [compreender os gémeos do dispositivo.](../articles/iot-hub/iot-hub-devguide-device-twins.md) Para mais informações sobre consulta, consulte a linguagem de [consulta do IoT Hub.](../articles/iot-hub/iot-hub-devguide-query-language.md)


Este tutorial mostrar-lhe como:

* Crie uma aplicação de back-end que adicione tags a um dispositivo twin, e uma aplicação simulada de dispositivo que reporta o seu canal de conectividade como uma propriedade reportada no dispositivo twin.

* Dispositivos de consulta da sua aplicação back-end utilizando filtros nas etiquetas e propriedades previamente criadas.

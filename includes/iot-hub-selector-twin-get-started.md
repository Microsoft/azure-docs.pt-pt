---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "70050455"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos, incluindo metadados, configurações e condições. O IoT Hub persiste num dispositivo gémeo para cada dispositivo que se conecta ao mesmo.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Utilize gémeos do dispositivo para:

* Guarde os metadados do dispositivo a partir da parte traseira da solução.

* Informe as informações atuais do estado, tais como capacidades e condições disponíveis, por exemplo, o método de conectividade utilizado, a partir da aplicação do seu dispositivo.

* Sincronizar o estado dos fluxos de trabalho de longa duração, como atualizações de firmware e configuração, entre uma aplicação de dispositivo e uma aplicação de back-end.

* Consultar os metadados, configuração ou estado do seu dispositivo.

Os gémeos do dispositivo são projetados para sincronização e para consulta de configurações e condições do dispositivo. Mais informações sobre quando usar o dispositivo gémeos podem ser encontrados em [Gémeos dispositivos Understand](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Os gémeos do dispositivo são armazenados num hub IoT e contêm os seguintes elementos:

* **Etiquetas.** Metadados do dispositivo acessíveis apenas pela solução traseira.

* **Propriedades desejadas**. Os objetos JSON são modificáveis pela solução traseira e observáveis pela aplicação do dispositivo.

* **Propriedades reportadas**. JSON objetos modificáveis pela aplicação do dispositivo e legíveis pela parte traseira da solução.

As etiquetas e propriedades não podem conter matrizes, mas os objetos podem ser aninhados.

A seguinte ilustração mostra organização gémea do dispositivo:

![Imagem dupla do dispositivo mostrando funcionalidade](./media/iot-hub-selector-twin-get-started/twin.png)

Além disso, a solução traseira pode consultar os gémeos do dispositivo com base em todos os dados acima.
Para obter mais informações sobre os gémeos do dispositivo, consulte [os gémeos do dispositivo Understand](../articles/iot-hub/iot-hub-devguide-device-twins.md). Para obter mais informações sobre a consulta, consulte [o idioma de consulta IoT Hub](../articles/iot-hub/iot-hub-devguide-query-language.md).


Este tutorial mostrar-lhe como:

* Crie uma aplicação back-end que adiciona tags a um dispositivo twin, e uma aplicação de dispositivo simulado que reporta o seu canal de conectividade como uma propriedade reportada no twin do dispositivo.

* Dispositivos de consulta a partir da sua aplicação back-end usando filtros nas etiquetas e propriedades anteriormente criadas.

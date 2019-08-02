---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: eef073f5f4d1eb39fd5ccd8dafacd7074158fa37
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667854"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). O Hub IoT persiste um dispositivo "r" para cada dispositivo que se conecta a ele.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Use dispositivos gêmeos para:

* Armazene os metadados do dispositivo do seu back-end da solução.

* Relatar informações de estado atual, como as condições e os recursos disponíveis (por exemplo, o método de conectividade usado) do seu aplicativo de dispositivo.

* Sincronizar o estado de fluxos de trabalho de execução longa (como atualizações de firmware e configuração) entre um aplicativo de dispositivo e um aplicativo de back-end.

* Consulte os metadados, a configuração ou o estado do dispositivo.

Dispositivos gêmeos são projetados para sincronização e para consultar configurações de dispositivo e condições. Mais informações sobre quando usar dispositivos gêmeos podem ser encontradas em [entender dispositivos gêmeos](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Dispositivos gêmeos são armazenados em um hub IoT e contêm:

* *marcas*, metadados de dispositivo acessíveis somente pelo back-end da solução;

* *Propriedades*desejadas, objetos JSON modificáveis pelo back-end da solução e observáveis pelo aplicativo do dispositivo; e

* *Propriedades*relatadas, objetos JSON modificáveis pelo aplicativo do dispositivo e legíveis pelo back-end da solução. As marcas e as propriedades não podem conter matrizes, mas os objetos podem ser aninhados.

![Imagem de dispositivo de entrelaçamento mostrando a funcionalidade](./media/iot-hub-selector-twin-get-started/twin.png)

Além disso, o back-end da solução pode consultar dispositivos gêmeos com base em todos os dados acima.
Consulte [entender dispositivos gêmeos](../articles/iot-hub/iot-hub-devguide-device-twins.md) para obter mais informações sobre o dispositivo gêmeos e a referência de [linguagem de consulta do Hub IOT](../articles/iot-hub/iot-hub-devguide-query-language.md) para consulta.


Este tutorial mostrar-lhe como:

* Crie um aplicativo de back-end que adiciona *marcas* a um dispositivo e um aplicativo de dispositivo simulado que relata seu canal de conectividade como uma *Propriedade* relatada no dispositivo.

* Consulte dispositivos de seu aplicativo de back-end usando filtros nas marcas e propriedades criadas anteriormente.

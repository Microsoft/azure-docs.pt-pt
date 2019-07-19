---
title: Criar a ponte de dispositivo de IoT Central do Azure | Microsoft Docs
description: Crie a ponte de dispositivo IoT Central para conectar outras nuvens IoT (Sigfox, partícula, a rede de coisas, etc.) ao seu aplicativo IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: c98574bc3447482429d7a028c6b47197e08e2e38
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850223"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Crie a ponte de dispositivo IoT Central para conectar outras nuvens IoT ao IoT Central

*Este tópico se aplica aos administradores do.*

A ponte de dispositivo IoT Central é uma solução de software livre que conecta sua Sigfox, partícula, a rede de coisas e outras nuvens ao seu aplicativo IoT Central. Se você estiver usando dispositivos de acompanhamento de ativos conectados à rede de área de baixo consumo de energia do Sigfox ou usando dispositivos de monitoramento de qualidade de ar na nuvem do dispositivo de partículas ou usando dispositivos de monitoramento de umidade de solo no TTN, você pode aproveitar diretamente o poder do IoT Central usando a ponte de dispositivo IoT Central. A ponte do dispositivo conecta outras nuvens IoT com IoT Central encaminhando os dados que os dispositivos enviam para as outras nuvens por meio do seu aplicativo IoT Central. Em seu aplicativo IoT Central, você pode criar regras e executar análises nesses dados, criar fluxos de trabalho em Microsoft Flow e aplicativos lógicos do Azure, exportar esses dados e muito mais. Obter a [ponte de dispositivo IOT central](https://aka.ms/iotcentralgithubdevicebridge) do github

## <a name="what-is-it-and-how-does-it-work"></a>O que é e como funciona?
A ponte de dispositivo IoT Central é uma solução de software livre no GitHub. Ele está pronto para começar com um botão "implantar no Azure" que implanta um modelo de Azure Resource Manager personalizado com vários recursos do Azure em sua assinatura do Azure. Os recursos incluem:
-   Aplicativo de funções do Azure
-   Conta de Armazenamento do Azure
-   Plano de Consumo
-   Azure Key Vault

O aplicativo de funções é a parte crucial da ponte do dispositivo. Ele recebe solicitações HTTP POST de outras plataformas IoT ou de qualquer plataforma personalizada por meio de uma integração simples de webhook. Fornecemos exemplos que mostram como se conectar a nuvens Sigfox, partícula e TTN. Você pode estender facilmente essa solução para se conectar à sua nuvem de IoT personalizada se sua plataforma puder enviar solicitações HTTP POST para seu aplicativo de funções.
O aplicativo de funções transforma os dados em um formato aceito pelo IoT Central e encaminha-os por meio de APIs de DPS.

![Captura de tela do Azure Functions](media/howto-build-iotc-device-bridge/azfunctions.png)

Se seu aplicativo IoT Central reconhecer o dispositivo pela ID do dispositivo na mensagem encaminhada, uma nova medição será exibida para esse dispositivo. Se a ID do dispositivo nunca foi vista pelo seu aplicativo IoT Central, seu aplicativo de funções tentará registrar um novo dispositivo com essa ID de dispositivo e ele será exibido como um "dispositivo não associado" em seu aplicativo IoT Central. 

## <a name="how-do-i-set-it-up"></a>Como fazer configurá-lo?
As instruções são listadas em detalhes no arquivo LEIAme no repositório do GitHub. 

## <a name="pricing"></a>Preços
Os recursos do Azure serão hospedados em sua assinatura do Azure. Você pode saber mais sobre os preços no [arquivo Leiame](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Passos Seguintes
Agora que você aprendeu como criar a ponte de dispositivo IoT Central, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Gerir os seus dispositivos](howto-manage-devices.md)

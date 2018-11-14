---
title: Criar a ponte de dispositivo Azure IoT Central | Documentos da Microsoft
description: Crie o IoT Central ponte de dispositivo e ligue-se noutras clouds de IoT (Sigfox, nuvem de partículas, etc. de rede de coisas a) à sua aplicação IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 11/8/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 83f053a8815f31803f536920497fdc42e72d2a2d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629273"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Criar o IoT Central ponte de dispositivo e ligue-se noutras clouds de IoT ao IoT Central

*Este tópico aplica-se aos administradores.*

A ponte de dispositivo do IoT Central é uma solução de código-fonte aberto que se liga a sua Sigfox, nuvem de partículas, a rede de coisas e noutras clouds para a sua aplicação IoT Central. Se estiver a utilizar o controlo de dispositivos ligados a baixa-Power-toda a rede do Sigfox de recursos ou a utilizar a qualidade de ar monitorizar dispositivos na Cloud de dispositivo de nuvem de partículas ou usando moisture solo monitorização de dispositivos no TTN, diretamente pode tirar partido do poder da IoT Central usando a ponte de dispositivo do IoT Central. A ponte de dispositivo liga-se noutras clouds de IoT com o IoT Central ao encaminhar os dados de que seus dispositivos enviam para as outras nuvens por meio de à sua aplicação IoT Central. Na sua aplicação IoT Central, pode criar regras e executar análises em dados, criar fluxos de trabalho no Microsoft Flow e o Azure Logic apps, exportar esses dados e muito mais. Obter o [bridge de dispositivo do IoT Central](https://aka.ms/iotcentralgithubdevicebridge) do Github

## <a name="what-is-it-and-how-does-it-work"></a>O que é e como ele funciona?
A ponte de dispositivo do IoT Central é uma solução de código-fonte aberto no Github. Ele está pronto para começar com um botão "Implementar no Azure", que implementa um modelo personalizado do Azure Resource Manager com vários recursos do Azure na sua subscrição do Azure. Os recursos incluem:
-   Aplicação de funções do Azure
-   Conta de Armazenamento do Azure
-   Plano de serviço de aplicações (escalão S1)
-   O Azure Key Vault a aplicação de funções é a parte crítica da ponte de dispositivo. Ele recebe pedidos de HTTP POST de outras plataformas de IoT ou qualquer plataformas personalizadas através de uma integração de webhook simples. Fornecemos exemplos que mostram como ligar a nuvens Sigfox, nuvem de partículas e TTN. Pode expandir facilmente esta solução para ligar a sua cloud da IoT personalizada, se a sua plataforma pode enviar pedidos de HTTP POST para a aplicação de funções.
A aplicação de função transforma os dados num formato Aceito pelo centro de IoT e reencaminha-lo ao longo através de APIs de pontos de distribuição.

![Captura de ecrã das funções do Azure](media/howto-build-iotc-device-bridge/azfunctions.png)

Se a sua aplicação do Centro de IoT reconhece o ID de dispositivo por dispositivo na mensagem reencaminhada, será apresentada uma nova medida para esse dispositivo. Se o ID de dispositivo nunca já foi visto pela sua aplicação IoT Central, a aplicação de funções irá tentar registar um novo dispositivo com o ID desse dispositivo e este será apresentado como um "dispositivos não associados" na sua aplicação IoT Central. 

## <a name="how-do-i-set-it-up"></a>Como posso configurá-lo?
As instruções estão listadas em detalhes no ficheiro Leia-me no repositório do Github. 

## <a name="pricing"></a>Preços
Isso é tudo alojado na sua subscrição do Azure. A maior parte do custo estimado dos recursos aprovisionados é proveniente de [preço de um plano de serviço de aplicações padrão]( https://azure.microsoft.com/en-us/pricing/details/app-service/windows/). Pode saber mais sobre isso e potenciais formas de reduzi-lo no ficheiro Leia-me.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como criar o IoT Central bridge de dispositivo, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Gerir os seus dispositivos](howto-manage-devices.md)
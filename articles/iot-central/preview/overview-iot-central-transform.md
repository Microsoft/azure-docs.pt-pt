---
title: Visão geral da transformação
description: Saiba mais sobre como transformar
author: viv-liu
ms.author: viviali
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 3df839738aaf28de3b32eee6e30f15376a00cc0d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434876"
---
# <a name="transform-your-iot-data-preview-features"></a>Transformar seus dados de IoT (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Como uma plataforma de aplicativos, o IoT Central fornece várias facetas-chave para ajudá-lo a transformar seus dados de IoT em informações de negócios que orientam os resultados acionáveis. O IoT Central fornece maneiras de estender seus dados de IoT a sistemas externos para criar integrações com aplicativos de linha de negócios e aplicativos personalizados. Você pode monitorar a integridade e as operações do dispositivo criando regras que notificam técnicos por meio de um serviço móvel. Você pode gerar informações de negócios específicas com análise personalizada e aprendizado de máquina exportando dados de telemetria brutos para serviços no Azure. Você pode criar serviços e ferramentas que monitoram e controlem dispositivos e gerenciem seu aplicativo IoT Central usando as APIs públicas. 

![Transformação em IoT Central visão geral](media/overview-iot-central-transform/transform.png)

## <a name="monitor-device-health-and-operations-using-rules"></a>Monitorar a integridade e as operações do dispositivo usando regras
Depois que os computadores estiverem conectados e enviando dados, você poderá identificar quais computadores estão enfrentando problemas ou enviar mensagens de erro para que eles possam ser corrigidos normalmente com tempo de inatividade mínimo. Você pode criar regras em seu aplicativo IoT Central para monitorar a telemetria que seus dispositivos estão enviando e alertá-lo quando um limite tiver sido atingido ou uma mensagem de evento específica for enviada. Você pode configurar ações como ações de email e WebHooks para suas regras para notificar as pessoas certas e os sistemas downstream corretos. Saiba mais sobre as regras aqui.

## <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Executar análise personalizada e processamento em seus dados exportados
Você pode gerar informações de negócios altamente personalizadas, como determinar tendências de eficiência de máquina ou prever o uso futuro de energia em um chão de fábrica, criando pipelines de análise personalizados para processar a telemetria bruta de IoT e armazenar o resultado final. Você pode criar exportações de dados em seu aplicativo IoT Central para exportar informações de telemetria, propriedades de dispositivo e ciclo de vida, além de alterações de modelo de dispositivo para outros serviços do Azure, para que você possa analisar, armazenar e visualizar os dados nas ferramentas que são mais valiosas para você. Saiba mais sobre a exportação de dados aqui.

## <a name="build-custom-iot-solutions-and-integrations-with-apis"></a>Crie soluções e integrações de IoT personalizadas com APIs
Você pode criar soluções de IoT como aplicativos do Mobile Companion que podem controlar dispositivos remotamente e configurar novos dispositivos ou integrações personalizadas com aplicativos de linha de negócios existentes para interagir com seus dispositivos e dados de IoT que são adaptados ao seu negócio. Você pode usar IoT Central como o backbone para modelagem de dispositivo, integração, gerenciamento e acesso a dados. Saiba mais sobre a API pública neste módulo de aprendizagem.

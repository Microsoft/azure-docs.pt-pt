---
title: Pré-requisitos do sistema
description: Obtenha pré-requisitos do sistema necessários para executar Azure Defender para IoT.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 78d5948554ae531c4b2f77d67bb916d5290db943
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780944"
---
# <a name="system-prerequisites"></a>Pré-requisitos do sistema
Este artigo lista os pré-requisitos do sistema para executar o Azure Defender para IoT.

## <a name="minimum-requirements"></a>Requisitos mínimos

- Os interruptores de rede que suportam a monitorização do tráfego através da porta SPAN.
- Aparelhos de hardware para sensores NTA.
- O papel de Colaborador de Assinatura Azure. É necessário apenas durante o embarque para definir dispositivos comprometidos e ligação ao Azure Sentinel.
- Papel **de contribuinte** Azure IoT Hub (Nível Livre ou Standard) para gestão ligada à nuvem. Certifique-se de que a função **Azure Defender para IoT** está ativada.
- Para suporte a nível de dispositivos Defender-IoT-micro-agente, o Defender para agentes IoT suporta uma lista crescente de dispositivos e plataformas. Consulte a [lista de plataformas apoiadas.](how-to-deploy-agent.md)

## <a name="supported-service-regions"></a>Regiões de serviço apoiadas

O Defensor da IoT encaminha todo o tráfego de todas as regiões europeias para o centro de dados regional da Europa Ocidental. Liga o tráfego de todas as regiões restantes para o centro de dados regional dos EUA.

Para mais informações, consulte [as regiões apoiadas pelo IoT Hub.](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)

## <a name="see-also"></a>Ver também

- [Identificar as aplicações necessárias](how-to-identify-required-appliances.md)
- [Sobre o Azure Defender para a configuração da rede IoT](how-to-set-up-your-network.md)

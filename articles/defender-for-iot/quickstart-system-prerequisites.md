---
title: Pré-requisitos do sistema
description: Obtenha pré-requisitos do sistema necessários para executar Azure Defender para IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 8ee3afcae69ca6c082452e590eb8370bcc122af4
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844513"
---
# <a name="system-prerequisites"></a>Pré-requisitos do sistema
Este artigo lista os pré-requisitos do sistema para executar o Azure Defender para IoT.

## <a name="minimum-requirements"></a>Requisitos mínimos

- Os interruptores de rede que suportam a monitorização do tráfego através da porta SPAN.
- Aparelhos de hardware para sensores NTA.
- O papel de Colaborador de Assinatura Azure. É necessário apenas durante o embarque para definir dispositivos comprometidos e ligação ao Azure Sentinel.
- Papel **de contribuinte** Azure IoT Hub (Nível Livre ou Standard) para gestão ligada à nuvem. Certifique-se de que a função **Azure Defender para IoT** está ativada.
- Para suporte a módulos de segurança ao nível do dispositivo, o Defender para agentes IoT suporta uma lista crescente de dispositivos e plataformas. Consulte a [lista de plataformas apoiadas.](how-to-deploy-agent.md)

## <a name="supported-service-regions"></a>Regiões de serviço apoiadas

O Defensor da IoT encaminha todo o tráfego de todas as regiões europeias para o centro de dados regional da Europa Ocidental. Liga o tráfego de todas as regiões restantes para o centro de dados regional dos EUA.

Para mais informações, consulte [as regiões apoiadas pelo IoT Hub.](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)

## <a name="see-also"></a>Veja também

- [Identificar os aparelhos necessários](how-to-identify-required-appliances.md)
- [Sobre o Azure Defender para a configuração da rede IoT](how-to-set-up-your-network.md)

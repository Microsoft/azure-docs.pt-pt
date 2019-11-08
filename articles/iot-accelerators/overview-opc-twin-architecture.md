---
title: Arquitetura de "OPC" – Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da arquitetura do OPC. Ele descreve a descoberta, a ativação, a navegação e o monitoramento do servidor.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b8d4424c92ff24c36650e34a5d050bdc5f0f9091
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819844"
---
# <a name="opc-twin-architecture"></a>Arquitetura de "OPC"

Os diagramas a seguir ilustram a arquitetura do OPC.

## <a name="discover-and-activate"></a>Descobrir e ativar

1. O operador habilita a verificação de rede no módulo ou faz uma única descoberta usando uma URL de descoberta. Os pontos de extremidade descobertos e as informações do aplicativo são enviados por telemetria para o agente de integração para processamento.  O agente de integração do dispositivo OPC UA processa eventos de descoberta de servidor do OPC UA enviados pelo módulo de IoT Edge do OPC "quando estiver no modo de descoberta ou de verificação. Os eventos de descoberta resultam no registro do aplicativo e nas atualizações no registro do dispositivo OPC UA.

   ![Como o OPC myworks funciona](media/overview-opc-twin-architecture/opc-twin1.png)

1. O operador inspeciona o certificado do ponto de extremidade descoberto e ativa o ponto de extremidade registrado para acesso. 

   ![Como o OPC myworks funciona](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Procurar e monitorar

1. Depois de ativado, o operador pode usar a API REST do serviço de entrelaçamento para navegar ou inspecionar o modelo de informações do servidor, as variáveis de objeto de leitura/gravação e os métodos de chamada.  O usuário usa uma API do OPC UA simplificada expressa totalmente em HTTP e JSON.

   ![Como o OPC myworks funciona](media/overview-opc-twin-architecture/opc-twin3.png)

1. A interface REST do serviço de entrelaçamento também pode ser usada para criar itens monitorados e assinaturas no editor OPC. O editor OPC permite que a telemetria seja enviada de sistemas de servidor OPC UA para o Hub IoT. Para obter mais informações sobre o editor OPC, consulte [o que é o editor OPC](overview-opc-publisher.md).

   ![Como o OPC myworks funciona](media/overview-opc-twin-architecture/opc-twin4.png)

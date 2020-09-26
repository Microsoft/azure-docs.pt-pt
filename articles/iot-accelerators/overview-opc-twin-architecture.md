---
title: OPC Twin arquitetura - Azure / Microsoft Docs
description: Este artigo fornece uma visão geral da arquitetura OPC Twin. Descreve sobre a descoberta, ativação, navegação e monitorização do servidor.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2504ba6262ba281d4049d89b03d2b3bc60061669
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91281801"
---
# <a name="opc-twin-architecture"></a>Arquitetura OPC Twin

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Os seguintes diagramas ilustram a arquitetura OPC Twin.

## <a name="discover-and-activate"></a>Descubra e ative

1. O operador permite a verificação da rede no módulo ou faz uma descoberta única usando um URL de descoberta. Os pontos finais descobertos e as informações da aplicação são enviados via telemetria ao agente de bordo para processamento.  O agente de bordo do dispositivo OPC UA processa os eventos de descoberta do servidor OPC UA enviados pelo módulo OPC Twin IoT Edge quando em modo de descoberta ou digitalização. Os eventos de descoberta resultam em registo de aplicações e atualizações no registo de dispositivos OPC UA.

   ![Como funciona o OPC Twin](media/overview-opc-twin-architecture/opc-twin1.png)

1. O operador inspeciona o certificado do ponto final descoberto e ativa o ponto final registado para acesso. 

   ![Como funciona o OPC Twin](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Procurar e monitorizar

1. Uma vez ativado, o operador pode utilizar a API de serviço Twin para navegar ou inspecionar o modelo de informação do servidor, ler/escrever variáveis de objetos e métodos de chamada.  O utilizador utiliza uma API UA UA simplificada expressa inteiramente em HTTP e JSON.

   ![Como funciona o OPC Twin](media/overview-opc-twin-architecture/opc-twin3.png)

1. A interface REST de serviço duplo também pode ser usada para criar itens monitorizados e subscrições no Editor OPC. O OPC Publisher permite que a telemetria seja enviada dos sistemas de servidores OPC UA para o IoT Hub. Para obter mais informações sobre a OPC Publisher, consulte [o Que é a Editora OPC.](overview-opc-publisher.md)

   ![Como funciona o OPC Twin](media/overview-opc-twin-architecture/opc-twin4.png)

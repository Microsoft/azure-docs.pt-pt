---
title: OPC Twin architecture - Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da arquitetura OPC Twin. Descreve sobre a descoberta, ativação, navegação e monitorização do servidor.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 602d2f77564709294d28b797ff3f03f12b3b32d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646983"
---
# <a name="opc-twin-architecture"></a>Arquitetura OPC Twin

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Os seguintes diagramas ilustram a arquitetura OPC Twin.

## <a name="discover-and-activate"></a>Descubra e ative

1. O operador permite a verificação da rede no módulo ou faz uma descoberta única usando um URL de descoberta. Os pontos finais descobertos e as informações da aplicação são enviados via telemetria ao agente de bordo para processamento.  O agente de bordo do dispositivo OPC UA processa os eventos de descoberta do servidor OPC UA enviados pelo módulo OPC Twin IoT Edge quando em modo de descoberta ou digitalização. Os eventos de descoberta resultam em registo de aplicações e atualizações no registo de dispositivos OPC UA.

   ![Diagrama que mostra a arquitetura OPC Twin com o módulo OPC Twin IoT Edge no modo de descoberta ou digitalização.](media/overview-opc-twin-architecture/opc-twin1.png)

1. O operador inspeciona o certificado do ponto final descoberto e ativa o ponto final registado para acesso. 

   ![Diagrama que mostra a arquitetura OPC Twin com a IoT Edge "Identidade gémea".](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Procurar e monitorizar

1. Uma vez ativado, o operador pode utilizar a API de serviço Twin para navegar ou inspecionar o modelo de informação do servidor, ler/escrever variáveis de objetos e métodos de chamada.  O utilizador utiliza uma API UA UA simplificada expressa inteiramente em HTTP e JSON.

   ![Diagrama que mostra a configuração da arquitetura OPC Twin para navegar e inspecionar o modelo de informação do servidor.](media/overview-opc-twin-architecture/opc-twin3.png)

1. A interface REST de serviço duplo também pode ser usada para criar itens monitorizados e subscrições no Editor OPC. O OPC Publisher permite que a telemetria seja enviada dos sistemas de servidores OPC UA para o IoT Hub. Para obter mais informações sobre a OPC Publisher, consulte [o Que é a Editora OPC.](overview-opc-publisher.md)

   ![Como funciona o OPC Twin](media/overview-opc-twin-architecture/opc-twin4.png)

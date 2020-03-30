---
title: OPC Twin architecture - Azure [ Microsoft Docs
description: Este artigo fornece uma visão geral da arquitetura OPC Twin. Descreve sobre a descoberta, ativação, navegação e monitorização do servidor.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b8d4424c92ff24c36650e34a5d050bdc5f0f9091
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "73819844"
---
# <a name="opc-twin-architecture"></a>Arquitetura gémea oPC

Os seguintes diagramas ilustram a arquitetura OPC Twin.

## <a name="discover-and-activate"></a>Descubra e ative

1. O operador permite a digitalização da rede no módulo ou faz uma descoberta única usando um URL de descoberta. Os pontos finais descobertos e as informações de aplicação são enviadas via telemetria para o agente de bordo para processamento.  O agente de onboarding do dispositivo OPC UA processa eventos de descoberta de servidores OPC UA enviados pelo módulo OPC Twin IoT Edge quando em modo de descoberta ou digitalização. Os eventos de descoberta resultam no registo de aplicações e atualizações no registo de dispositivos OPC UA.

   ![Como funciona a OPC Twin](media/overview-opc-twin-architecture/opc-twin1.png)

1. O operador inspeciona o certificado do ponto final descoberto e ativa o fio final registado para acesso. 

   ![Como funciona a OPC Twin](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Procurar e monitorizar

1. Uma vez ativado, o operador pode utilizar o serviço Twin REST API para navegar ou inspecionar o modelo de informação do servidor, ler/escrever variáveis de objetos e métodos de chamada.  O utilizador utiliza um OPC UA API simplificado expresso na ÍNTEGRA em HTTP e JSON.

   ![Como funciona a OPC Twin](media/overview-opc-twin-architecture/opc-twin3.png)

1. A interface REST de serviço duplo também pode ser usada para criar itens e subscrições monitorizados na Editora OPC. A Editora OPC permite que a telemetria seja enviada dos sistemas de servidores OPC UA para ioT Hub. Para mais informações sobre a OPC Publisher, consulte [o que é o OPC Publisher](overview-opc-publisher.md).

   ![Como funciona a OPC Twin](media/overview-opc-twin-architecture/opc-twin4.png)

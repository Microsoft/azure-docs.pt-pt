---
title: O que é o OPC duplo - Azure | Documentos da Microsoft
description: Descrição geral do OPC duplo
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9daf1a7e58af23cb78705691217bf9709359c4d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496817"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-device-management"></a>O que é a gestão de dispositivos do Azure IoT aberto plataforma comunicações (OPC)?

Duplo de OPC é composta por microsserviços que utilizam o Azure IoT Edge e IoT Hub para ligar a cloud e a rede de fábrica. Duplo de OPC fornece deteção, o registro e o controlo remoto dos dispositivos industriais através de REST APIs. Duplo de OPC não necessita de uma arquitetura de unificação de OPC (OPC UA) SDK, é independente de linguagem de programação e pode ser incluído num fluxo de trabalho sem servidor. Este artigo descreve vários casos de utilização do OPC duplo.

## <a name="discovery-and-control"></a>Deteção e controlo
Pode usar o duplo de OPC para simples para deteção e o Registro.

### <a name="simple-discovery-and-registration"></a>Deteção Simple e de registo
Duplo de OPC permite que os operadores de fábrica analisar a rede de fábrica, para que possam ser detetados e registados servidores OPC UA. Como alternativa, operadores de factory também manualmente podem registar os dispositivos de OPC UA através de um URL de deteção conhecidos. Por exemplo, para ligar a todos os dispositivos de OPC UA depois do gateway do IoT Edge com um módulo duplo de OPC foi instalado no chão de fábrica, o operador de fábrica pode acionar remotamente uma análise da rede e observar, visualmente, todos os servidores OPC UA. 

### <a name="simple-control"></a>Controle simples
Duplo de OPC permite que os operadores de fábrica reagir a eventos e reconfigurar suas máquinas do chão de fábrica da cloud, automática ou manualmente, sem perder tempo. Duplo de OPC fornece APIs REST para chamar os serviços no servidor OPC UA, navegue também o seu espaço de endereços em relação às variáveis de leitura/escrita e executar métodos. Por exemplo, um boiler utiliza temperatura KPI para controlar a linha de produção. O sensor de temperatura publica a alteração nos dados com o publicador OPC. O operador de fábrica recebe o alerta que a temperatura atingiu o limiar. A linha de produção arrefece automaticamente através do OPC duplo. O operador de fábrica é notificado o interessantes para baixo.

## <a name="authentication"></a>Authentication
Pode usar o duplo de OPC para simples para autenticação e para uma experiência de programação simples.

### <a name="simple-authentication"></a>Autenticação simples 
Duplo de OPC utiliza autenticação baseada no Azure Active Directory AAD e a auditoria a partir de ponto final. Por exemplo, o OPC duplo permite à aplicação a ser incorporada duplo de OPC para determinar o que um operador tem executada numa máquina. No lado do computador, é por meio de auditoria do OPC UA. No lado da nuvem, é por meio de armazenar um imutável cliente log de auditoria e a autenticação do AAD na REST API.

### <a name="simple-developer-experience"></a>Experiência de programação simples 
Duplo de OPC pode ser utilizado com aplicações escritas em qualquer linguagem de programação através de REST APIs. Como os programadores integrar um cliente de OPC UA numa solução, dados de conhecimento do OPC UA SDK não é necessário. Duplo de OPC pode se integrar perfeitamente em arquiteturas sem servidor, sem monitoração de estado. Por exemplo, um pilha completa desenvolvedor da web que desenvolve um aplicativo para um dashboard de eventos de alarme e pode escrever a lógica para responder a eventos em JavaScript ou TypeScript com duplo de OPC sem o conhecimento de C, C#, ou a implementação completa de pilha OPC UA. 

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu sobre OPC duplo e seus usos, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [O que é o OPC Cofre](overview-opc-twin-architecture.md)
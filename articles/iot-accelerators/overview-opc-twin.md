---
title: O que é o OPC "Azure" | Microsoft Docs
description: Visão geral do OPC.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c0d824e23a98aa14081fbd21bd6a9fbec5d583e0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815949"
---
# <a name="what-is-opc-twin"></a>O que é OPC.

O OPC myQuery consiste em microserviços que usam Azure IoT Edge e Hub IoT para conectar a nuvem e a rede de fábrica. O OPC Myoferece descoberta, registro e controle remoto de dispositivos industriais por meio de APIs REST. O OPC myQuery não requer um SDK do OPC UA (Unified Architecture), é independente da linguagem de programação e pode ser incluído em um fluxo de trabalho sem servidor. Este artigo descreve vários casos de uso do OPC.

## <a name="discovery-and-control"></a>Descoberta e controle
Você pode usar a "OPC" para o registro e a descoberta simples.

### <a name="simple-discovery-and-registration"></a>Descoberta e registro simples
O OPC maup permite que os operadores de fábrica verifiquem a rede de fábrica, para que os servidores OPC UA possam ser descobertos e registrados. Como alternativa, os operadores de fábrica também podem registrar manualmente os dispositivos OPC UA usando uma URL de descoberta conhecida. Por exemplo, para se conectar a todos os dispositivos OPC UA depois que o gateway de IoT Edge com um módulo OPC myficar instalado no chão de fábrica, o operador de fábrica pode disparar remotamente uma verificação da rede e ver visualmente todos os servidores do OPC UA. 

### <a name="simple-control"></a>Controle simples
O OPC maup permite que os operadores de fábrica reajam a eventos e reconfigurem seus computadores de chão de fábrica da nuvem de forma automática ou manual. O OPC myfornece APIs REST para invocar serviços no servidor OPC UA, procurar seu espaço de endereço, bem como variáveis de leitura/gravação e métodos de execução. Por exemplo, um preistor usa KPI de temperatura para controlar a linha de produção. O sensor de temperatura publica a alteração nos dados usando o editor OPC. O operador de fábrica recebe o alerta de que a temperatura atingiu o limite. A linha de produção é esfriada automaticamente por meio de OPC. O operador de fábrica é notificado do resfriamento.

## <a name="authentication"></a>Authentication
Você pode usar o OPC "para autenticação simples" e para uma experiência de desenvolvedor simples.

### <a name="simple-authentication"></a>Autenticação simples 
O OPC myusa a autenticação baseada no AAD (Azure Active Directory) e a auditoria de ponta a ponta. Por exemplo, o OPC 10 permite que o aplicativo seja criado sobre o OPC 10 para determinar o que um operador executou em um computador. No lado do computador, é por meio da auditoria de OPC UA. No lado da nuvem, é por meio do armazenamento de um log de auditoria de cliente imutável e da autenticação do AAD na API REST.

### <a name="simple-developer-experience"></a>Experiência de desenvolvedor simples 
O OPC myserial pode ser usado com aplicativos escritos em qualquer linguagem de programação por meio de APIs REST. À medida que os desenvolvedores integram um cliente OPC UA a uma solução, o conhecimento do SDK do OPC UA não é necessário. O OPC maup pode integrar-se perfeitamente às arquiteturas sem estado e não-servidor. Por exemplo, um desenvolvedor de pilha da Web completo que desenvolve um aplicativo para um evento de alarme e de eventos pode escrever a lógica para responder a eventos em JavaScript ou TypeScript usando o OPC "sem C#o conhecimento de C, ou a implementação completa de pilha do OPC UA. 

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre o OPC e seus usos, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [O que é o cofre do OPC](overview-opc-vault.md)

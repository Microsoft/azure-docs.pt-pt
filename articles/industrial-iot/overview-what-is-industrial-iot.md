---
title: Visão geral do Azure Industrial IoT
description: Este artigo fornece uma visão geral da IoT Industrial. Explica a conectividade do piso da loja e os componentes de segurança no IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787952"
---
# <a name="what-is-industrial-iot-iiot"></a>O que é IoT Industrial (IIoT)?

O IIoT (Internet Industrial das Coisas) aumenta a eficiência industrial através da aplicação de IoT na indústria transformadora.

![Propriedade industrial](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>Melhorar a eficiência industrial
Aumente a sua produtividade operacional e rentabilidade com a Azure Industrial IoT. Reduza o processo moroso de acesso aos ativos no local. Ligue e monitorize os seus equipamentos e dispositivos industriais na nuvem - incluindo as suas máquinas que já operam no chão da fábrica. Analise os seus dados IoT para obter informações que o ajudem a aumentar o desempenho de todo o site.

## <a name="industrial-iot-components"></a>Componentes IoT industriais

**Dispositivos IoT Edge** Um dispositivo IoT Edge é composto por Módulos Edge Runtime e Edge. 
- *Os Módulos Edge* são recipientes estivadores, que são a unidade mais pequena de computação, como a OPC Publisher e a OPC Twin. 
- *O dispositivo edge* é utilizado para implantar tais módulos, que funcionam como mediadores entre o servidor OPC UA e o IoT Hub na nuvem. Mais informações sobre ioT Edge [estão aqui.](https://azure.microsoft.com/services/iot-edge/)

**Hub IoT** O IoT Hub funciona como um centro de mensagens central para a comunicação bidis entre a aplicação IoT e os dispositivos que gere. É uma plataforma de nuvem aberta e flexível como um serviço que suporta SDKs de código aberto e vários protocolos. Leia mais sobre o IoT Hub [aqui.](https://azure.microsoft.com/services/iot-hub/)

**Módulos de borda industrial**
- *Editor OPC*: A Editora OPC funciona dentro da IoT Edge. Conecta-se aos servidores da UA OPC e publica dados de telemetria codificados JSON destes servidores no formato OPC UA "Pub/Sub" ao Azure IoT Hub. Todos os protocolos de transporte suportados pelo cliente SDK do Azure IoT Hub podem ser utilizados, ou seja, HTTPS, AMQP e MQTT.
- *OPC Twin*: O OPC Twin é composto por microserviços que utilizam O Azure IoT Edge e IoT Hub para ligar a nuvem e a rede de fábrica. O OPC Twin fornece descoberta, registo e controlo remoto de dispositivos industriais através de APIs REST. OPC Twin não requer um OPC Uniified Architecture (OPC UA) SDK. É agnóstico de linguagem de programação, e pode ser incluído num fluxo de trabalho sem servidor.
- *Discovery*: O módulo de descoberta, representado pela identidade do descobridor, fornece serviços de descoberta no limite, que incluem a descoberta do servidor OPC UA. Se a descoberta estiver configurada e ativada, o módulo enviará os resultados de uma sonda de digitalização através do caminho de telemetria IoT Edge e IoT Hub para o serviço de onboarding. O serviço processa os resultados e atualiza todas as identidades relacionadas no Registo.


**Descubra, registe e gere os seus Ativos Industriais com a Azure** O Azure Industrial IoT permite que os operadores de fábrica descubram servidores OPC UA ativados numa rede de fábrica e registem-nos no Azure IoT Hub. O pessoal das operações pode subscrever e reagir a eventos no chão da fábrica de qualquer parte do mundo. As APIs REST dos Microservices espelham os serviços OPC UA. São protegidos através da autenticação e autorização da AAUTH apoiadas pelo Azure Ative Directory (AAD). Isto permite que as suas aplicações em nuvem naveguem em espaços de endereço do servidor ou leiam/escrevam variáveis e executem métodos usando HTTPS e cargas simples de OPC UA JSON.

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o que é o IoT Industrial, pode ler sobre a Plataforma IoT Industrial e a Editora OPC:

> [!div class="nextstepaction"]
> [O que é a Plataforma Industrial IoT?](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [O que é a Editora OPC?](overview-what-is-opc-publisher.md)
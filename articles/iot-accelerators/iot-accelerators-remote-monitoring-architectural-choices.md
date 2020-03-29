---
title: Escolhas arquitetónicas de solução de monitorização remota - Azure / Microsoft Docs
description: Este artigo descreve as escolhas arquitetónicas e técnicas feitas na Monitorização Remota
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447190"
---
# <a name="remote-monitoring-architectural-choices"></a>Escolhas arquitetónicas da Monitorização Remota

O acelerador de solução de monitorização remota Azure IoT é um acelerador de soluções licenciado pelo MIT. Para ajudá-lo a acelerar o seu processo de desenvolvimento de IoT, mostra cenários comuns de IoT tais como:

- Conectividade dos dispositivos
- Gestão de dispositivos
- Processamento de fluxos

A solução de monitorização remota segue a arquitetura de [referência Azure IoT](https://aka.ms/iotrefarchitecture)recomendada.

Este artigo descreve as principais escolhas arquitetónicas e técnicas feitas em cada um dos subsistemas de Monitorização Remota. No entanto, as escolhas técnicas que a Microsoft fez na solução de Monitorização Remota não são a única forma de implementar uma solução IoT de monitorização remota. Deve considerar a implementação técnica como uma linha de base para a construção de uma aplicação bem sucedida e deve modificá-la para:

- Ajuste as competências e experiência disponíveis na sua organização.
- Satisfaça as suas necessidades verticais de aplicação.

## <a name="architectural-choices"></a>Opções de arquitetura

A arquitetura que a Microsoft recomenda para uma aplicação IoT é nativa, microserviço e baseada em servidores. Deve construir os diferentes subsistemas de uma aplicação IoT como serviços discretos que pode implementar e escalar de forma independente. Estes atributos permitem uma maior escala, mais flexibilidade na atualização dos subsistemas individuais e proporcionam a flexibilidade para escolher uma tecnologia adequada para cada subsistema.

Pode implementar microserviços utilizando mais do que uma tecnologia. Por exemplo, pode escolher qualquer uma das seguintes opções para implementar um microserviço:

- Utilize uma tecnologia de contentores como o Docker com tecnologia sem servidores, como as Funções Azure.
- Acolhou os seus microserviços em serviços PaaS, como os Serviços de Aplicações Azure.

## <a name="technology-choices"></a>Opções de tecnologia

Esta secção detalha as escolhas tecnológicas feitas na solução de Monitorização Remota para cada um dos subsistemas principais.

![Diagrama do núcleo](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Cloud Gateway

O Hub Azure IoT é utilizado como porta de entrada de nuvem de solução de monitorização remota. [O IoT Hub](https://azure.microsoft.com/services/iot-hub/) oferece uma comunicação segura e bidirecional com dispositivos.

Para a conectividade do dispositivo IoT, pode utilizar:

- O [dispositivo IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) implementa uma aplicação de cliente nativo para o seu dispositivo. Os SDKs oferecem invólucros em torno da IoT Hub REST API e lidam com cenários como repetições.
- A integração com o Azure IoT Edge para implementar e gerir módulos personalizados em contentores nos seus dispositivos.
- A integração com a gestão automática de dispositivos no IoT Hub para gerir dispositivos conectados a granel.

### <a name="stream-processing"></a>Processamento de fluxos

Para o processamento de fluxo, a solução de monitorização remota utiliza o Azure Stream Analytics para processamento de regras complexas. Se quiser usar regras mais simples, existe um microserviço personalizado com suporte para um simples processamento de regras, embora esta configuração não faça parte da implementação fora da caixa. A arquitetura de referência recomenda funções Azure para processamento simples de regras e Azure Stream Analytics para processamento de regras complexas.

### <a name="storage"></a>Storage

Para armazenamento, o acelerador de solução de monitorização remota utiliza tanto a Azure Time Series Insights como a Azure Cosmos DB. A Azure Time Series Insights armazena as mensagens que chegam através do IoT Hub a partir dos seus dispositivos conectados. O acelerador de solução utiliza o Azure Cosmos DB para todos os outros armazenamentos, tais como armazenamento frio, definições de regras, alertas e configurações de configuração.

Azure Cosmos DB é a solução de armazenamento quente recomendada para aplicações IoT. No entanto, soluções como a Azure Time Series Insights e o Azure Data Lake são apropriadas para muitos casos de utilização. Com o Azure Time Series Insights, pode obter informações mais profundas sobre os seus dados de sensores de série seleção de tempo, detetando tendências e anomalias. Esta funcionalidade permite-lhe realizar análises de causa-raiz e evitar tempos de paragem dispendiosos.

> [!NOTE]
> A Time Series Insights não está atualmente disponível na nuvem Azure China. Novas implementações de aceleradores de solução de monitorização remota na nuvem Azure China utilizam cosmos DB para todo o armazenamento.

### <a name="business-integration"></a>Integração de negócios

A integração empresarial na solução de Monitorização Remota limita-se à geração de alertas, que são colocados em armazenamento quente. Conecte a solução com as Apps Lógicas Azure para implementar cenários de integração de negócios mais profundos.

### <a name="user-interface"></a>Interface de Utilizador

A UI web é construída com JavaScript React. O React oferece um quadro web de UI da indústria comumente usado e é semelhante a outros quadros populares, como o Angular.

### <a name="runtime-and-orchestration"></a>Tempo de execução e orquestração

A solução de monitorização remota utiliza os recipientes Docker para executar os subsistemas com Kubernetes como o orquestrador para escala horizontal. Esta arquitetura permite definições de escala individual para cada subsistema. No entanto, esta arquitetura incorre em custos de DevOps para manter as máquinas e contentores virtuais atualizados e seguros.

Alternativas ao Docker incluem hospedagem de microserviços em serviços PaaS, como o Azure App Service. Alternativas a Kubernetes incluem orquestradores como Service Fabric, DC/OS ou Swarm.

## <a name="next-steps"></a>Passos seguintes

* Implemente [aqui](https://www.azureiotsolutions.com/)a sua solução de monitorização remota .
* Explore o código GitHub em [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) e [Java.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/)  
* Saiba mais sobre a Arquitetura de Referência IoT [aqui.](https://aka.ms/iotrefarchitecture)

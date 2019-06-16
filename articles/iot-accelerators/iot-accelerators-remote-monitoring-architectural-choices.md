---
title: Remotas solução escolhas arquitetónicas da monitorização - Azure | Documentos da Microsoft
description: Este artigo descreve as opções de arquiteturais e técnicas feitas na monitorização remota
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447190"
---
# <a name="remote-monitoring-architectural-choices"></a>Escolhas arquitetónicas da monitorização remotas

O acelerador de solução de monitorização remota do Azure IoT é um código fonte aberto, MIT licenciado, acelerador de solução. Para ajudar a acelerar o processo de desenvolvimento de IoT, mostra os cenários de IoT comuns, tais como:

- Conectividade dos dispositivos
- Gestão de dispositivos
- Processamento de fluxos

A solução de monitorização remota segue o recomendado [arquitetura de referência do Azure IoT](https://aka.ms/iotrefarchitecture).

Este artigo descreve as opções de arquiteturais e técnicas principais feitas em cada um dos subsistemas de monitorização remota. No entanto, as opções técnicas Microsoft feita na solução de monitorização remota não são a única forma de implementar uma solução de IoT de monitorização remota. Deve considerar a implementação técnica como uma linha de base para a criação de um aplicativo com êxito e deve modificá-lo para:

- Ajustar as habilidades disponíveis e experiência em sua organização.
- Atender às necessidades da sua aplicação vertical.

## <a name="architectural-choices"></a>Opções de arquitetura

A arquitetura que a Microsoft recomenda-se para uma aplicação IoT é a cloud nativos, microsserviços, e com base em sem servidor. Deve criar os diferentes subsistemas de uma aplicação IoT como serviços discretos que pode implementar e dimensionar de forma independente. Esses atributos ativar escala maior, mais flexibilidade na atualização subsistemas individuais e fornecem a flexibilidade de escolher uma tecnologia apropriada para cada subsistema.

Pode implementar microsserviços que utilizam a tecnologia mais do que um. Por exemplo, pode escolher qualquer uma das seguintes opções para implementar um microsserviço:

- Use uma tecnologia de contentor, como o Docker com a tecnologia sem servidor, como as funções do Azure.
- Aloje os microsserviços em serviços de PaaS como os serviços de aplicações do Azure.

## <a name="technology-choices"></a>Opções de tecnologia

Esta secção fornece detalhes sobre as opções de tecnologia feitas na solução de monitorização remota para cada um nos principais subsistemas.

![Diagrama de núcleo](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Gateway de nuvem

O IoT Hub do Azure é utilizado como o gateway de nuvem de solução de monitorização remota. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) oferece comunicação bidirecional e segura com dispositivos.

Para a conectividade do dispositivo de IoT, pode utilizar:

- O [SDKs de dispositivo do IoT Hub](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) para implementar uma aplicação cliente nativa para o seu dispositivo. Os SDKs oferecem wrappers em torno da API de REST do Hub IoT e manipulam os cenários, tais como as repetições.
- A integração com o Azure IoT Edge para implementar e gerir módulos personalizados em execução nos contentores nos seus dispositivos.
- A integração com o gerenciamento automático de dispositivos no IoT Hub para gerir dispositivos ligados em massa.

### <a name="stream-processing"></a>Processamento de fluxos

Para processamento de fluxo, a solução de monitorização remota utiliza o Azure Stream Analytics para o processamento da regra complexa. Se pretender utilizar as regras mais simples, há um microsserviço personalizado com suporte para o processamento da regra simples, embora esta configuração não fazem parte da implementação de out-of-the-box. A arquitetura de referência recomenda as funções do Azure para o processamento da regra simples e o Azure Stream Analytics para o processamento da regra complexa.

### <a name="storage"></a>Armazenamento

Para armazenamento, o acelerador de solução de monitorização remota utiliza o Azure Time Series Insights e o Azure Cosmos DB. O Azure Time Series Insights armazena as mensagens recebidas pelo IoT Hub dos seus dispositivos ligados. O solution accelerator utiliza o Azure Cosmos DB para todos os outro armazenamento como armazenamento esporádico, definições de regras, alertas e as definições de configuração.

O Azure Cosmos DB é a solução de armazenamento de acesso pouco frequente recomendado para fins gerais para aplicações de IoT. No entanto, as soluções como o Azure Time Series Insights e o Azure Data Lake são adequadas para muitos casos de utilização. Com o Azure Time Series Insights, pode obter informações mais detalhadas sobre os seus dados de séries temporais sensor ao detetar tendências e anomalias. Esta funcionalidade permite-lhe realizar análises de causa raiz e evitar períodos de indisponibilidade dispendiosos.

> [!NOTE]
> O Time Series Insights não está atualmente disponível na cloud do Azure China. Novas implementações de acelerador de solução de monitorização remota na cloud do Azure China utilizem Cosmos DB para todo o armazenamento.

### <a name="business-integration"></a>Integração de negócios

Integração de negócios na solução de monitorização remota está limitada à geração de alertas, que são colocadas no armazenamento de acesso pouco frequente. Conecte-se a solução Azure Logic Apps para implementar cenários de integração de negócios mais aprofundados.

### <a name="user-interface"></a>Interface do usuário

O web IU é criada com reagir de JavaScript. React oferece uma estrutura de interface do Usuário da web de setor frequentemente utilizadas e é semelhante a outras arquiteturas populares, como o Angular.

### <a name="runtime-and-orchestration"></a>Tempo de execução e orquestração

A solução de monitorização remota utiliza contentores do Docker para executar os subsistemas com o Kubernetes como o orchestrator para o dimensionamento horizontal. Esta arquitetura permite que as definições de dimensionamento individuais para cada subsistema. No entanto, esta arquitetura de incorrer em custos de DevOps para manter as máquinas virtuais e contentores, atualizados e seguros.

As alternativas ao Docker incluem alojamento microsserviços em serviços de PaaS, como o serviço de aplicações do Azure. Alternativas para o Kubernetes incluem orquestradores, como o Service Fabric, DC/OS ou Swarm.

## <a name="next-steps"></a>Passos Seguintes

* Implemente a sua solução de monitorização remota [aqui](https://www.azureiotsolutions.com/).
* Explore o código do GitHub na [c#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) e [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Saiba mais sobre a arquitetura de referência do IoT [aqui](https://aka.ms/iotrefarchitecture).

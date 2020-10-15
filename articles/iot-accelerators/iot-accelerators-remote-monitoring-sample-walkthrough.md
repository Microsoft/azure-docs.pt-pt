---
title: Visão geral do acelerador de solução de monitorização remota - Azure / Microsoft Docs
description: Este artigo fornece uma visão geral de alguns dos elementos-chave da solução de Monitorização Remota para que possa compreender como funciona.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: f9477b11b52431211dc91a0ea6ff0767a9d495be
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073602"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Descrição geral do acelerador de soluções de Monitorização Remota

O acelerador de [solução](../iot-accelerators/about-iot-accelerators.md) de monitorização remota implementa uma solução de monitorização de ponta a ponta para várias máquinas em locais remotos. A solução combina serviços-chave do Azure para fornecer uma implementação genérica do cenário de negócios. Pode utilizar a solução como um ponto de partida para a sua própria implementação e [personalizá-la](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) para satisfazer os seus requisitos comerciais específicos.

Este artigo percorre alguns dos elementos-chave da solução de Monitorização Remota para que possa compreender como funciona. Estes conhecimentos ajudam a:

* Resolver problemas na solução.
* Planear a forma de personalizar a solução para satisfazer os seus próprios requisitos específicos.
* Estruturar a sua própria solução de IoT que utiliza os serviços do Azure.

O código do acelerador de solução de monitorização remota está disponível no GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama que se segue descreve os componentes lógicos do acelerador de solução de monitorização remota sobreposto na [arquitetura IoT:](../iot-fundamentals/iot-introduction.md)

![Arquitetura lógica](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Por que microserviços?

A arquitetura cloud tem evoluído desde que a Microsoft lançou os primeiros aceleradores de solução. [Os microserviços](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) surgiram como uma prática comprovada para alcançar a escala e a flexibilidade sem sacrificar a velocidade de desenvolvimento. Vários serviços da Microsoft usam este padrão arquitetónico internamente com grandes resultados de fiabilidade e escalabilidade. Os aceleradores de solução atualizados colocam estas aprendizagens em prática para que também possa beneficiar delas.

> [!TIP]
> Para saber mais sobre as arquiteturas de microsserviços, veja [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Arquitetura de Aplicações .NET) e [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microsserviços: uma revolução nas aplicações com tecnologia da cloud).

## <a name="device-connectivity"></a>Conectividade dos dispositivos

A solução inclui os seguintes componentes na parte de conectividade do dispositivo da arquitetura lógica:

### <a name="real-devices"></a>Dispositivos reais

Pode ligar dispositivos reais à solução. Pode implementar o comportamento dos seus dispositivos simulados utilizando os SDKs do dispositivo Azure IoT.

Pode providenciar dispositivos reais a partir do painel de instrumentos no portal da solução.

### <a name="device-simulation-microservice"></a>Microserviço de simulação de dispositivo

A solução inclui o [microserviço](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) de simulação do dispositivo que lhe permite gerir um conjunto de dispositivos simulados a partir do portal da solução para testar o fluxo de ponta a ponta na solução. Os dispositivos simulados:

* Gerem telemetria dispositivo-nuvem.
* Responda às chamadas de método nuvem-a-dispositivo do IoT Hub.

O microserviço fornece um ponto final RESTful para criar, iniciar e parar as simulações. Cada simulação consiste num conjunto de dispositivos virtuais de diferentes tipos, que enviam telemetria e respondem a chamadas de método.

Pode providenciar dispositivos simulados a partir do painel de instrumentos no portal da solução.

### <a name="iot-hub"></a>IoT Hub

O [hub IoT](../iot-hub/index.yml) ingere telemetria enviada de ambos os dispositivos reais e simulados para a nuvem. O hub IoT disponibiliza a telemetria aos serviços na solução IoT para processamento.

O hub IoT na solução também:

* Mantém um registo de identidade que armazena os IDs e as chaves de autenticação de todos os dispositivos autorizados a ligar-se ao portal.
* Invoca métodos nos seus dispositivos em nome do acelerador de solução.
* Mantém dispositivos duplos para todos os dispositivos registados. Um dispositivo duplo armazena os valores de propriedades comunicados por um dispositivo. Um dispositivo duplo também armazena as propriedades pretendidas, definidas no portal da solução, para o dispositivo obter da próxima vez que estabelecer ligação.
* Agenda tarefas para definir propriedades para vários dispositivos ou invocar métodos em vários dispositivos.

## <a name="data-processing-and-analytics"></a>Processamento e análise dos dados

A solução inclui os seguintes componentes no processamento de dados e parte analítica da arquitetura lógica:

### <a name="iot-hub-manager-microservice"></a>Microserviço ioT Hub manager

A solução inclui o [microserviço do gestor IoT Hub](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) para lidar com interações com o seu hub IoT, tais como:

* Criar e gerir dispositivos IoT.
* A gerir os gémeos do dispositivo.
* Invocando métodos em dispositivos.
* Gerir credenciais de IoT.

Este serviço também executa consultas IoT Hub para recuperar dispositivos pertencentes a grupos definidos pelo utilizador.

O microserviço fornece um ponto final RESTful para gerir dispositivos e gémeos de dispositivos, invocar métodos e executar consultas IoT Hub.

### <a name="device-telemetry-microservice"></a>Microserviço de telemetria do dispositivo

O [micro serviço de telemetria do dispositivo](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) fornece um ponto final RESTful para o acesso de leitura à telemetria do dispositivo armazenado em Time Series Insights. O ponto final RESTful também permite operações CRUD sobre regras e ler/escrever acesso para definições de alarme a partir do armazenamento.

### <a name="storage-adapter-microservice"></a>Microserviço do adaptador de armazenamento

O [microserviço do adaptador](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) de armazenamento gere pares de valores-chave, abstratando a semântica do serviço de armazenamento, e apresentando uma interface simples para armazenar dados de qualquer formato usando Azure Cosmos DB.

Os valores são organizados em coleções. Pode trabalhar em valores individuais ou obter coleções inteiras. Estruturas de dados complexas são serializadas pelos clientes e geridas como uma simples carga de texto.

O serviço fornece um ponto final RESTful para operações CRUD em pares de valor-chave. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

As implementações do acelerador de soluções utilizam [o Azure Cosmos DB](../cosmos-db/index.yml) para armazenar regras, alertas, configurações de configuração e todos os outros armazenamentos frios.

### <a name="azure-stream-analytics-manager-microservice"></a>Microserviço do gestor Azure Stream Analytics

O [microservice do Azure Stream Analytics](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) gere os empregos do Azure Stream Analytics (ASA), incluindo definir a sua configuração, iniciar e parar e monitorizar o seu estado.

O trabalho da ASA é suportado por dois conjuntos de dados de referência. Um conjunto de dados define regras e define-se grupos de dispositivos. As regras de referência são geradas a partir da informação gerida pelo micro serviço de telemetria do dispositivo. O microserviço do gestor Azure Stream Analytics transforma as regras de telemetria em lógica de processamento de fluxo.

O dispositivo agruba dados de referência para identificar que grupo de regras aplicar a uma mensagem de telemetria recebida. Os grupos de dispositivos são geridos pelo microserviço de configuração e utilizam consultas duplas do dispositivo Azure IoT Hub.

Os trabalhos da ASA fornecem a telemetria dos dispositivos conectados à Time Series Insights para armazenamento e análise.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[O Azure Stream Analytics](../stream-analytics/index.yml) é um motor de processamento de eventos que permite examinar grandes volumes de streaming de dados a partir de dispositivos.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[A Azure Time Series Insights](../time-series-insights/index.yml) armazena a telemetria dos dispositivos ligados ao acelerador de solução. Também permite visualizar e consultar a telemetria do dispositivo na solução web UI.

### <a name="configuration-microservice"></a>Microserviço de configuração

O [microserviço de configuração](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) fornece um ponto final RESTful para operações CRUD em grupos de dispositivos, definições de solução e configurações do utilizador no acelerador de solução. Funciona com o microserviço do adaptador de armazenamento para persistir os dados de configuração.

### <a name="authentication-and-authorization-microservice"></a>Microserviço de autenticação e autorização

O [microserviço de autenticação e autorização](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) gere os utilizadores autorizados a aceder ao acelerador de solução. A gestão do utilizador pode ser feita utilizando qualquer fornecedor de serviços de identidade que suporte [o OpenId Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

As implementações do acelerador de soluções utilizam [o Azure Ative Directory](../active-directory/index.yml) como fornecedor openID connect. O Azure Ative Directory armazena informações dos utilizadores e fornece certificados para validar assinaturas simbólicas JWT.

## <a name="presentation"></a>Apresentação

A solução inclui os seguintes componentes na parte de apresentação da arquitetura lógica:

A [interface do utilizador web é uma aplicação React Javascript](https://github.com/Azure/pcs-remote-monitoring-webui). O pedido:

* Usa o Javascript React apenas e funciona inteiramente no navegador.
* É estilo com CSS.
* Interage com microserviços virados para o público através de chamadas AJAX.

A interface do utilizador apresenta toda a funcionalidade do acelerador de solução e interage com outros microserviços tais como:

* O microserviço de autenticação e autorização para proteger os dados do utilizador.
* O ioT Hub gerente microservice para listar e gerir os dispositivos IoT.

A interface do utilizador integra o explorador Azure Time Series Insights para permitir a consulta e análise da telemetria do dispositivo.

O microserviço de configuração permite que a interface do utilizador armazene e recupere as definições de configuração.

## <a name="next-steps"></a>Passos seguintes

Se quiser explorar o código fonte e a documentação do desenvolvedor, comece com um dos dois repositórios do GitHub:

* [Acelerador de solução para monitorização remota com Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Acelerador de solução para monitorização remota com Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagramas de arquitetura de solução detalhada:
* [Acelerador de solução para arquitetura de monitorização remota.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture)

Para obter mais informações conceptuais sobre o acelerador de solução de monitorização remota, consulte [Personalizar o acelerador de solução.](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
---
title: Visão geral do acelerador de solução de monitoramento remoto-Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de alguns dos principais elementos da solução de monitoramento remoto para permitir que você entenda como ele funciona.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 33005bc286f7dc2c0ebed74bd9df0309e71346f5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824840"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Descrição geral do acelerador de soluções de Monitorização Remota

O [acelerador de solução](../iot-accelerators/about-iot-accelerators.md) de monitoramento remoto implementa uma solução de monitoramento de ponta a ponta para vários computadores em locais remotos. A solução combina serviços-chave do Azure para fornecer uma implementação genérica do cenário de negócios. Você pode usar a solução como um ponto de partida para sua própria implementação e [personalizá](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) -la para atender aos seus requisitos de negócios específicos.

Este artigo orienta você por alguns dos principais elementos da solução de monitoramento remoto para permitir que você entenda como ele funciona. Estes conhecimentos ajudam a:

* Resolver problemas na solução.
* Planear a forma de personalizar a solução para satisfazer os seus próprios requisitos específicos.
* Estruturar a sua própria solução de IoT que utiliza os serviços do Azure.

O código do acelerador da solução de monitoramento remoto está disponível no GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama a seguir descreve os componentes lógicos do acelerador de solução de monitoramento remoto sobreposto na [arquitetura de IOT](../iot-fundamentals/iot-introduction.md):

![Arquitetura lógica](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Por que os microserviços?

A arquitetura de nuvem evoluiu desde que a Microsoft lançou os primeiros aceleradores de solução. Os [microserviços](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) surgiram como uma prática comprovada para alcançar a escala e a flexibilidade sem sacrificar a velocidade de desenvolvimento. Vários serviços da Microsoft usam esse padrão de arquitetura internamente com ótimos resultados de confiabilidade e escalabilidade. Os aceleradores de solução atualizados colocam esses aprendizados em prática para que você também possa se beneficiar deles.

> [!TIP]
> Para saber mais sobre as arquiteturas de microsserviços, veja [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Arquitetura de Aplicações .NET) e [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microsserviços: uma revolução nas aplicações com tecnologia da cloud).

## <a name="device-connectivity"></a>Conectividade dos dispositivos

A solução inclui os seguintes componentes na parte de conectividade do dispositivo da arquitetura lógica:

### <a name="real-devices"></a>Dispositivos reais

Você pode conectar dispositivos reais à solução. Você pode implementar o comportamento de seus dispositivos simulados usando os SDKs do dispositivo IoT do Azure.

Você pode provisionar dispositivos reais no painel no portal de solução.

### <a name="device-simulation-microservice"></a>Microserviço de simulação de dispositivo

A solução inclui o [microserviço de simulação de dispositivo](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) que permite que você gerencie um pool de dispositivos simulados do portal de solução para testar o fluxo de ponta a ponta na solução. Os dispositivos simulados:

* Gerar telemetria do dispositivo para a nuvem.
* Responder a chamadas de método da nuvem para o dispositivo do Hub IoT.

O Microservice fornece um ponto de extremidade RESTful para criar, iniciar e parar simulações. Cada simulação consiste em um conjunto de dispositivos virtuais de tipos diferentes, que enviam telemetria e respondem a chamadas de método.

Você pode provisionar dispositivos simulados no painel no portal de solução.

### <a name="iot-hub"></a>IoT Hub

O [Hub IOT](../iot-hub/index.yml) ingeri a telemetria enviada dos dispositivos reais e simulados para a nuvem. O Hub IoT disponibiliza a telemetria para os serviços no back-end da solução IoT para processamento.

O hub IoT na solução também:

* Mantém um registro de identidade que armazena as IDs e chaves de autenticação de todos os dispositivos com permissão para se conectar ao Portal.
* Invoca métodos em seus dispositivos em nome do Solution Accelerator.
* Mantém dispositivos duplos para todos os dispositivos registados. Um dispositivo duplo armazena os valores de propriedades comunicados por um dispositivo. Um dispositivo duplo também armazena as propriedades pretendidas, definidas no portal da solução, para o dispositivo obter da próxima vez que estabelecer ligação.
* Agenda tarefas para definir propriedades para vários dispositivos ou invocar métodos em vários dispositivos.

## <a name="data-processing-and-analytics"></a>Processamento e análise dos dados

A solução inclui os seguintes componentes na parte de análise e processamento de dados da arquitetura lógica:

### <a name="iot-hub-manager-microservice"></a>Microserviço do Gerenciador do Hub IoT

A solução inclui o [microserviço do Gerenciador de Hub IOT](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) para lidar com interações com o Hub IOT, como:

* Criando e Gerenciando dispositivos IoT.
* Gerenciando dispositivos gêmeos.
* Invocando métodos em dispositivos.
* Gerenciando credenciais de IoT.

Esse serviço também executa consultas de Hub IoT para recuperar dispositivos que pertencem a grupos definidos pelo usuário.

O Microservice fornece um ponto de extremidade RESTful para gerenciar dispositivos e dispositivo gêmeos, invocar métodos e executar consultas de Hub IoT.

### <a name="device-telemetry-microservice"></a>Microserviço de telemetria do dispositivo

O [microserviço de telemetria do dispositivo](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) fornece um ponto de extremidade RESTful para acesso de leitura à telemetria do dispositivo armazenada no time Series insights. O ponto de extremidade RESTful também permite operações CRUD em regras e acesso de leitura/gravação para definições de alarme do armazenamento.

### <a name="storage-adapter-microservice"></a>Microserviço de adaptador de armazenamento

O [microserviço do adaptador de armazenamento](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) gerencia os pares chave-valor, abstraindo a semântica do serviço de armazenamento e apresentando uma interface simples para armazenar dados de qualquer formato usando Azure Cosmos DB.

Os valores são organizados em coleções. Você pode trabalhar em valores individuais ou buscar coleções inteiras. As estruturas de dados complexas são serializadas pelos clientes e são gerenciadas como uma carga de texto simples.

O serviço fornece um ponto de extremidade RESTful para operações CRUD em pares chave-valor. os

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

As implantações do Solution Accelerator usam [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) para armazenar regras, alertas, definições de configuração e todos os outros armazenamentos frios.

### <a name="azure-stream-analytics-manager-microservice"></a>Microserviço do Azure Stream Analytics Manager

O [microserviço do Gerenciador de Azure Stream Analytics](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) gerencia Azure Stream Analytics (asa), incluindo a definição de sua configuração, a inicialização e a interrupção e o monitoramento de seu status.

O trabalho ASA tem suporte de dois conjuntos de dados de referência. Um conjunto de dados define regras e uma define grupos de dispositivos. Os dados de referência de regras são gerados com base nas informações gerenciadas pelo microserviço de telemetria do dispositivo. O microserviço do Gerenciador de Azure Stream Analytics transforma as regras de telemetria na lógica de processamento do fluxo.

Os dados de referência de grupos de dispositivos são usados para identificar o grupo de regras a ser aplicado a uma mensagem de telemetria de entrada. Os grupos de dispositivos são gerenciados pelo microserviço de configuração e usam consultas do dispositivo do Hub IoT do Azure.

Os trabalhos do ASA entregam a telemetria dos dispositivos conectados para Time Series Insights para armazenamento e análise.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) é um mecanismo de processamento de eventos que permite examinar grandes volumes de fluxo de dados de dispositivos.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series insights](https://docs.microsoft.com/azure/time-series-insights/) armazena a telemetria dos dispositivos conectados ao Solution Accelerator. Ele também permite visualizar e consultar a telemetria do dispositivo na interface do usuário da solução.

> [!NOTE]
> O Time Series Insights não está disponível atualmente na nuvem do Azure na China. Novas implantações do acelerador de solução de monitoramento remoto na nuvem do Azure China usam Cosmos DB para todo o armazenamento.

### <a name="configuration-microservice"></a>Microserviço de configuração

O [microserviço de configuração](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) fornece um ponto de extremidade RESTful para operações CRUD em grupos de dispositivos, configurações de solução e configurações de usuário no Solution Accelerator. Ele funciona com o microserviço de adaptador de armazenamento para manter os dados de configuração.

### <a name="authentication-and-authorization-microservice"></a>Microserviço de autenticação e autorização

O [microserviço de autenticação e autorização](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) gerencia os usuários autorizados a acessar o Solution Accelerator. O gerenciamento de usuários pode ser feito usando qualquer provedor de serviços de identidade que ofereça suporte [ao OpenID Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

As implantações do Solution Accelerator usam [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) como um provedor do OpenID Connect. Azure Active Directory armazena informações de usuário e fornece certificados para validar assinaturas de token JWT.

## <a name="presentation"></a>Presentation

A solução inclui os seguintes componentes na parte de apresentação da arquitetura lógica:

A [interface do usuário da Web é um aplicativo do JavaScript reagir](https://github.com/Azure/pcs-remote-monitoring-webui). O aplicativo:

* Usa somente reagir do JavaScript e é executado inteiramente no navegador.
* É estilizado com CSS.
* Interage com os microserviços públicos por meio de chamadas AJAX.

A interface do usuário apresenta toda a funcionalidade do Solution Accelerator e interage com outros microserviços, como:

* O microserviço de autenticação e autorização para proteger os dados do usuário.
* O microserviço do Gerenciador do Hub IoT para listar e gerenciar os dispositivos IoT.

A interface do usuário integra o Azure Time Series Insights Explorer para habilitar a consulta e a análise da telemetria do dispositivo.

O microserviço de configuração permite que a interface do usuário armazene e recupere definições de configuração.

## <a name="next-steps"></a>Passos seguintes

Se você quiser explorar o código-fonte e a documentação do desenvolvedor, comece com um dos dois repositórios GitHub:

* [Solution Accelerator para monitoramento remoto com o Azure IOT (.net)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Solution Accelerator para monitoramento remoto com o Azure IOT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagramas detalhados da arquitetura da solução:
* [Solution Accelerator para arquitetura de monitoramento remoto](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Para obter mais informações conceituais sobre o acelerador de solução de monitoramento remoto, consulte [Personalizar o Solution Accelerator](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).

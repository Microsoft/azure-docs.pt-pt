---
title: Visão geral do acelerador de soluções de monitorização remota - Azure [ Microsoft Docs
description: Este artigo fornece uma visão geral de alguns dos elementos-chave da solução de Monitorização Remota para que possa compreender como funciona.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: f501eb55f72811063ddf1d8e02a0ce2137d598f3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546317"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Descrição geral do acelerador de soluções de Monitorização Remota

O acelerador de [solução](../iot-accelerators/about-iot-accelerators.md) de monitorização remota implementa uma solução de monitorização de ponta a ponta para várias máquinas em locais remotos. A solução combina serviços-chave do Azure para fornecer uma implementação genérica do cenário de negócios. Pode utilizar a solução como um ponto de partida para a sua própria implementação e [personalizá-la](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) para satisfazer os seus requisitos comerciais específicos.

Este artigo acompanha-o através de alguns dos elementos-chave da solução de Monitorização Remota para que possa compreender como funciona. Estes conhecimentos ajudam a:

* Resolver problemas na solução.
* Planear a forma de personalizar a solução para satisfazer os seus próprios requisitos específicos.
* Estruturar a sua própria solução de IoT que utiliza os serviços do Azure.

O código acelerador de solução de monitorização remota está disponível no GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama seguinte descreve os componentes lógicos do acelerador de solução de monitorização remota sobrepostos na [arquitetura IoT:](../iot-fundamentals/iot-introduction.md)

![Arquitetura lógica](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Por que microserviços?

A arquitetura cloud evoluiu desde que a Microsoft lançou os primeiros aceleradores de soluções. [Os microserviços](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) surgiram como uma prática comprovada para alcançar escala e flexibilidade sem sacrificar a velocidade de desenvolvimento. Vários serviços da Microsoft utilizam este padrão arquitetónico internamente com grande fiabilidade e resultados de escalabilidade. Os aceleradores de soluções atualizados colocam estas aprendizagens em prática para que você também possa beneficiar deles.

> [!TIP]
> Para saber mais sobre as arquiteturas de microsserviços, veja [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Arquitetura de Aplicações .NET) e [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microsserviços: uma revolução nas aplicações com tecnologia da cloud).

## <a name="device-connectivity"></a>Conectividade dos dispositivos

A solução inclui os seguintes componentes na parte da conectividade do dispositivo da arquitetura lógica:

### <a name="real-devices"></a>Dispositivos reais

Pode ligar dispositivos reais à solução. Pode implementar o comportamento dos seus dispositivos simulados utilizando os SDKs do dispositivo Azure IoT.

Pode fornecer dispositivos reais a partir do painel de instrumentos no portal da solução.

### <a name="device-simulation-microservice"></a>Microserviço de simulação de dispositivo

A solução inclui o microserviço de [simulação](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) do dispositivo que lhe permite gerir um conjunto de dispositivos simulados a partir do portal de solução para testar o fluxo de ponta a ponta na solução. Os dispositivos simulados:

* Gere telemetria dispositivo-nuvem.
* Responda às chamadas do método nuvem-a-dispositivo do IoT Hub.

O microserviço fornece um ponto final RESTful para que crie, inicie e pare simulações. Cada simulação consiste num conjunto de dispositivos virtuais de diferentes tipos, que enviam telemetria e respondem a chamadas de métodos.

Pode fornecer dispositivos simulados a partir do painel de instrumentos no portal da solução.

### <a name="iot-hub"></a>IoT Hub

O [hub IoT](../iot-hub/index.yml) ingere a telemetria enviada tanto dos dispositivos reais como simulados para a nuvem. O hub IoT disponibiliza a telemetria aos serviços no backend da solução IoT para processamento.

O hub IoT na solução também:

* Mantém um registo de identidade que armazena as identidades e as chaves de autenticação de todos os dispositivos autorizados a ligar ao portal.
* Invoca métodos nos seus dispositivos em nome do acelerador de soluções.
* Mantém dispositivos duplos para todos os dispositivos registados. Um dispositivo duplo armazena os valores de propriedades comunicados por um dispositivo. Um dispositivo duplo também armazena as propriedades pretendidas, definidas no portal da solução, para o dispositivo obter da próxima vez que estabelecer ligação.
* Agenda tarefas para definir propriedades para vários dispositivos ou invocar métodos em vários dispositivos.

## <a name="data-processing-and-analytics"></a>Processamento e análise dos dados

A solução inclui os seguintes componentes no tratamento de dados e na parte da análise da arquitetura lógica:

### <a name="iot-hub-manager-microservice"></a>Microserviço de gestor de IoT Hub

A solução inclui o microserviço do [gestor ioT Hub](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) para lidar com interações com o seu hub IoT, tais como:

* Criação e gestão de dispositivos IoT.
* Gerir gémeos de dispositivos.
* Invocando métodos em dispositivos.
* Gerir credenciais ioT.

Este serviço também executa consultas ioT Hub para recuperar dispositivos pertencentes a grupos definidos pelo utilizador.

O microserviço fornece um ponto final RESTful para gerir dispositivos e gémeos dispositivos, invocar métodos e executar consultas ioT Hub.

### <a name="device-telemetry-microservice"></a>Microserviço de telemetria do dispositivo

O microserviço de [telemetria](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) do dispositivo fornece um ponto final RESTful para o acesso à telemetria do dispositivo armazenado em Time Series Insights. O ponto final RESTful também permite operações de CRUD sobre regras e acesso de leitura/escrita para definições de alarme a partir do armazenamento.

### <a name="storage-adapter-microservice"></a>Microserviço adaptador de armazenamento

O [microserviço adaptador](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) de armazenamento gere pares de valor-chave, abstraindo a semântica do serviço de armazenamento e apresentando uma interface simples para armazenar dados de qualquer formato usando o Azure Cosmos DB.

Os valores são organizados em coleções. Pode trabalhar em valores individuais ou obter coleções inteiras. Estruturas de dados complexas são serializadas pelos clientes e geridas como simples carga de texto.

O serviço fornece um ponto final RESTful para operações crud em pares de valor-chave. values

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

As implementações do acelerador de solução utilizam [o Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) para armazenar regras, alertas, configurações de configuração e todos os outros armazenamentos a frio.

### <a name="azure-stream-analytics-manager-microservice"></a>Microserviço de gestor de Análise De Fluxo Azure

O microserviço do gestor do [Azure Stream Analytics](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) gere os trabalhos do Azure Stream Analytics (ASA), incluindo a definição da sua configuração, iniciando-os e impedindo-os, e monitorizando o seu estado.

O trabalho da ASA é suportado por dois conjuntos de dados de referência. Um conjunto de dados define regras e define grupos de dispositivos. Os dados de referência das regras são gerados a partir da informação gerida pelo microserviço de telemetria do dispositivo. O microserviço do gestor da Azure Stream Analytics transforma regras de telemetria em lógica de processamento de fluxo.

Os grupos de dispositivos são utilizados para identificar qual o grupo de regras a aplicar a uma mensagem de telemetria que chega. Os grupos de dispositivos são geridos pelo microserviço de configuração e utilizam consultas gémeas do dispositivo Azure IoT Hub.

Os trabalhos da ASA fornecem a telemetria dos dispositivos conectados aos Insights da Série Time para armazenamento e análise.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[O Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) é um motor de processamento de eventos que permite examinar grandes volumes de dados que transmitem a partir de dispositivos.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[A Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) armazena a telemetria dos dispositivos ligados ao acelerador de soluções. Também permite visualizar e consultar a telemetria do dispositivo na ui web de soluções.

### <a name="configuration-microservice"></a>Microserviço de configuração

O microserviço de [configuração](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) fornece um ponto final RESTful para operações CRUD em grupos de dispositivos, definições de solução e configurações de utilizador no acelerador de solução. Funciona com o microserviço do adaptador de armazenamento para persistir os dados de configuração.

### <a name="authentication-and-authorization-microservice"></a>Autenticação e autorização microserviço

O microserviço de [autenticação e autorização](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) gere os utilizadores autorizados a aceder ao acelerador de soluções. A gestão do utilizador pode ser feita utilizando qualquer fornecedor de serviço de identidade que suporte o [OpenId Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

As implementações do acelerador de soluções utilizam o [Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/) como fornecedor OpenID Connect. O Azure Ative Directory armazena informações dos utilizadores e fornece certificados para validar assinaturas simbólicas jWT.

## <a name="presentation"></a>Apresentação

A solução inclui os seguintes componentes na parte de apresentação da arquitetura lógica:

A [interface do utilizador web é uma aplicação React Javascript](https://github.com/Azure/pcs-remote-monitoring-webui). O pedido:

* Usa javascript Reagir apenas e funciona inteiramente no navegador.
* É estilo com CSS.
* Interage com os microserviços virados para o público através de chamadas AJAX.

A interface do utilizador apresenta toda a funcionalidade do acelerador de soluções, e interage com outros microserviços tais como:

* O microserviço de autenticação e autorização para proteger os dados dos utilizadores.
* O microserviço do gestor IoT Hub para listar e gerir os dispositivos IoT.

A interface do utilizador integra o explorador de Insights da Série De Tempo Azure para permitir a consulta e análise da telemetria do dispositivo.

O microserviço de configuração permite à interface do utilizador armazenar e recuperar as definições de configuração.

## <a name="next-steps"></a>Passos seguintes

Se quiser explorar o código fonte e documentação do desenvolvedor, comece com um dos dois repositórios GitHub:

* [Acelerador de solução para monitorização remota com Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Acelerador de solução para monitorização remota com Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagramas de arquitetura de soluções detalhadas:
* [Acelerador de soluções para a arquitetura de monitorização remota.](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture)

Para obter informações mais conceptuais sobre o acelerador de soluções de monitorização remota, consulte [Personalizar o acelerador de solução](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).

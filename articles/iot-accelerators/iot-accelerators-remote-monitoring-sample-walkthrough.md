---
title: Remoto monitorização solution accelerator descrição geral - Azure | Documentos da Microsoft
description: Uma visão geral do solution accelerator monitorização remota.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: af09ea39f373d518d5600e3fa46adc378fd9236d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176141"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Descrição geral do acelerador de soluções da Monitorização Remota

A monitorização remota [acelerador de solução](../iot-accelerators/about-iot-accelerators.md) implementa uma solução de monitorização ponto a ponto para várias máquinas em localizações remotas. A solução combina serviços-chave do Azure para fornecer uma implementação genérica do cenário de negócios. Pode utilizar a solução como um ponto de partida para a sua própria implementação e [personalizar](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) para atender às suas próprias necessidades comerciais específicas.

Este artigo orienta-o através de alguns dos elementos-chave da solução de monitorização remota para que possa compreender como funciona. Estes conhecimentos ajudam a:

* Resolver problemas na solução.
* Planear a forma de personalizar a solução para satisfazer os seus próprios requisitos específicos.
* Estruturar a sua própria solução de IoT que utiliza os serviços do Azure.

O código de acelerador de solução de monitorização remota está disponível no GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama seguinte descreve os componentes lógicos de monitorização remota solution accelerator sobreposto a [arquitetura de IoT](../iot-fundamentals/iot-introduction.md):

![Arquitetura lógica](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Por que motivo microsserviços?

Arquitetura de nuvem evoluiu desde o lançamento do Microsoft solution accelerators primeiro. [Microsserviços](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) surgiram como uma prática comprovada para alcançar dimensionamento e flexibilidade sem sacrificar a rapidez do desenvolvimento. Vários serviços do Microsoft utilizam este padrão de arquitetura internamente com confiabilidade excelente e resultados de escalabilidade. Os Aceleradores de solução atualizada colocar esses aprendizados em prática para que também pode se beneficiar dos mesmos.

> [!TIP]
> Para saber mais sobre as arquiteturas de microsserviços, veja [arquitetura de aplicações de .NET](https://www.microsoft.com/net/learn/architecture) e [Microsserviços: Uma revolução de aplicação com a tecnologia de cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="device-connectivity"></a>Conectividade dos dispositivos

A solução inclui os seguintes componentes na parte de conectividade do dispositivo da arquitetura lógica:

### <a name="real-devices"></a>Dispositivos reais

Pode ligar dispositivos reais à solução. Pode implementar o comportamento dos seus dispositivos simulados com o Azure IoT device SDKs.

Pode aprovisionar dispositivos reais, a partir do dashboard no portal da solução.

### <a name="device-simulation-microservice"></a>Microsserviços de simulação de dispositivo

A solução inclui a [microsserviços de simulação de dispositivo](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) que permite-lhe gerir um conjunto de dispositivos simulados a partir do portal da solução para testar o fluxo de ponto a ponto da solução. Os dispositivos simulados:

* Gere a telemetria do dispositivo para a cloud.
* Responda às chamadas de método de cloud para dispositivo IoT Hub.

Os microsserviços fornece um ponto de extremidade RESTful para que possa criar, iniciar e parar as simulações. Cada simulação consiste num conjunto de dispositivos virtuais de diferentes tipos, que enviam telemetria e respondem a chamadas de método.

Pode aprovisionar dispositivos simulados a partir do dashboard no portal da solução.

### <a name="iot-hub"></a>IoT Hub

O [IoT hub](../iot-hub/index.yml) ingere telemetria enviada a partir de dispositivos reais ou simulados para a cloud. O IoT hub disponibiliza a telemetria para os serviços no back-end de solução de IoT, para processamento.

O hub IoT na solução também:

* Mantém um registo de identidades que armazena os IDs e chaves de autenticação de todos os dispositivos autorizados a ligar para o portal.
* Invoca métodos nos seus dispositivos em nome do solution accelerator.
* Mantém dispositivos duplos para todos os dispositivos registados. Um dispositivo duplo armazena os valores de propriedades comunicados por um dispositivo. Um dispositivo duplo também armazena as propriedades pretendidas, definidas no portal da solução, para o dispositivo obter da próxima vez que estabelecer ligação.
* Agenda tarefas para definir propriedades para vários dispositivos ou invocar métodos em vários dispositivos.

## <a name="data-processing-and-analytics"></a>Processamento e análise dos dados

A solução inclui os seguintes componentes no processamento de dados e a parte de análise da arquitetura lógica:

### <a name="iot-hub-manager-microservice"></a>Microsserviços de Gestor do IoT Hub

A solução inclui a [microsserviços de Gestor do IoT Hub](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) para lidar com as interações com o seu hub IoT, tais como:

* Criar e gerir dispositivos IoT.
* Gestão de dispositivos duplos.
* Invocar métodos nos dispositivos.
* Gerir credenciais de IoT.

Este serviço também é executado o IoT Hub consultas para obter os dispositivos que pertencem a grupos definidos pelo utilizador.

Os microsserviços fornece um ponto de extremidade RESTful para gerir dispositivos e os dispositivos duplos, invocar métodos e executar consultas de IoT Hub.

### <a name="device-telemetry-microservice"></a>Microsserviços de telemetria do dispositivo

O [microsserviços de telemetria do dispositivo](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) fornece um ponto de extremidade RESTful para acesso de leitura a telemetria do dispositivo armazenado no Time Series Insights. O ponto de extremidade RESTful também permite que as operações de CRUD em regras e acesso de leitura/gravação para as definições de alarme de armazenamento.

### <a name="storage-adapter-microservice"></a>Microsserviços de placa de armazenamento

O [microsserviços de placa de armazenamento](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) gere pares de chave-valor, abstraindo a semântica do serviço de armazenamento e apresentar uma interface simples para armazenar dados de qualquer formato, com o Azure Cosmos DB.

Valores estão organizados em coleções. Pode trabalhar em valores individuais ou coleções inteiras de obter. Estruturas de dados complexos são serializadas pelos clientes e geridas como o payload de texto simples.

O serviço fornece um ponto de extremidade RESTful para operações CRUD em pares chave-valor. Valores

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

As implementações de acelerador de solução utilizem [do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) para armazenar as regras, alertas, as definições de configuração e todos os outro armazenamento amovível.

### <a name="azure-stream-analytics-manager-microservice"></a>Microsserviços de Gestor do Stream Analytics do Azure

O [microsserviços do Azure Stream Analytics manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) gere as tarefas do Azure Stream Analytics (ASA), incluindo a definição de suas configurações, iniciar e como pará-los e respetivo estado de monitorização.

A tarefa ASA é suportada por dois conjuntos de dados de referência. Um conjunto de dados define as regras e um deles define os grupos de dispositivos. Os dados de referência de regras são gerados a partir de informações geridas pelos microsserviços de telemetria do dispositivo. Os microsserviços de Gestor do Azure Stream Analytics transforma as regras de telemetria em lógica de processamento de fluxos.

Os dados de referência de grupos do dispositivo são utilizados para identificar que grupo de regras para aplicar a uma mensagem de telemetria de entrada. Os grupos de dispositivos são geridos pelos microsserviços de configuração e utilizam consultas de gémeos de dispositivo do IoT Hub do Azure.

As tarefas ASA enviar a telemetria dos dispositivos ligados, para o Time Series Insights para armazenamento e análise.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[O Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) é um motor de processamento de eventos que lhe permite examinar elevados volumes de dados de transmissão em fluxo a partir de dispositivos.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[O Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) arquivos a telemetria dos dispositivos ligada ao solution accelerator. Ele também permite visualizar e consultar a telemetria dos dispositivos na IU da web solução.

> [!NOTE]
> O Time Series Insights não está atualmente disponível na cloud do Azure China. Novas implementações de acelerador de solução de monitorização remota na cloud do Azure China utilizem Cosmos DB para todo o armazenamento.

### <a name="configuration-microservice"></a>Microsserviços de configuração

O [microsserviços de configuração](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) fornece um ponto de extremidade RESTful para operações CRUD em grupos de dispositivos, definições de solução e configurações de usuário do solution Accelerator. Ele funciona com os microsserviços de placa de armazenamento para manter os dados de configuração.

### <a name="authentication-and-authorization-microservice"></a>Microsserviços de autenticação e autorização

O [microsserviços de autenticação e autorização](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) gerencia os usuários autorizados a aceder o solution accelerator. Gestão de utilizadores pode ser feita usando qualquer fornecedor de serviços de identidade que suporta [OpenId Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

As implementações de acelerador de solução utilizem [do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) como fornecedor de OpenID Connect. O Azure Active Directory armazena informações de utilizador e fornece certificados para validar JWT assinaturas do token.

## <a name="presentation"></a>Apresentação

A solução inclui os seguintes componentes na parte da arquitetura lógica de apresentação:

O [interface de utilizador web é um aplicativo de Javascript reagir](https://github.com/Azure/pcs-remote-monitoring-webui). A aplicação:

* Utiliza apenas reagir de Javascript e executada inteiramente no navegador.
* É incluído um estilo com CSS.
* Interage com os microsserviços destinado ao públicos por meio de chamadas do AJAX.

A interface do usuário apresenta todas as funcionalidades do acelerador de solução e interage com outros microsserviços, tais como:

* Os microsserviços de autenticação e autorização para proteger os dados de utilizador.
* Microsserviços de Gestor do IoT Hub para listar e gerir os dispositivos de IoT.

A interface do usuário integra-se o Explorador do Azure Time Series Insights para ativar a consulta e análise de telemetria do dispositivo.

Os microsserviços de configuração permite que a interface do usuário armazenar e obter as definições de configuração.

## <a name="next-steps"></a>Passos Seguintes

Se quiser explorar a documentação de código e desenvolvedor de origem, comece com um dos repositórios do GitHub dois:

* [Solution accelerator para monitorização remota com IoT do Azure (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Solution accelerator para monitorização remota com o Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagramas de arquitetura de solução detalhadas:
* [Solution accelerator para a arquitetura de monitorização remota](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Para obter mais informações concetuais sobre o acelerador de solução de monitorização remota, consulte [personalizar o solution accelerator](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).

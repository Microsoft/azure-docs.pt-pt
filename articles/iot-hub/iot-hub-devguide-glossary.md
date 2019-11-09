---
title: Glossário de termos do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – um glossário explicando alguns dos termos comuns usados nos artigos do Hub IoT do Azure.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: 6c4215933b3816b445c55282bae9a36e88c0311f
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890534"
---
# <a name="glossary-of-iot-hub-terms"></a>Glossário dos termos do Hub IoT
Este artigo lista alguns dos termos comuns usados nos artigos do Hub IoT.

## <a name="advanced-message-queueing-protocol"></a>Protocolo avançado de enfileiramento de mensagens
O [protocolo de enfileiramento de mensagens avançado (AMQP)](https://www.amqp.org/) é um dos protocolos de mensagens com suporte do [Hub IOT](#iot-hub) para comunicação com dispositivos. Para obter mais informações sobre os protocolos de mensagens que o Hub IoT suporta, consulte [Enviar e receber mensagens com o Hub IOT](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Gerenciamento automático de dispositivo
O gerenciamento automático de dispositivos no Hub IoT do Azure automatiza muitas das tarefas repetitivas e complexas de gerenciar as frotas de dispositivos grandes durante todo o ciclo de vida deles. Com o gerenciamento automático de dispositivos, você pode direcionar um conjunto de dispositivos com base em suas propriedades, definir uma configuração desejada e permitir que o Hub IoT atualize dispositivos sempre que eles chegarem ao escopo.  Consiste em [configurações automáticas de dispositivo](iot-hub-auto-device-config.md) e [IOT Edge implantações automáticas](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Configuração automática do dispositivo
O back-end da solução pode usar [configurações automáticas de dispositivo](iot-hub-auto-device-config.md) para atribuir as propriedades desejadas a um conjunto de [gêmeos de dispositivo](#device-twin) e status de relatório usando métricas de sistema e métricas personalizadas. 

## <a name="azure-classic-cli"></a>CLI clássica do Azure
A [CLI clássica do Azure](../cli-install-nodejs.md) é uma ferramenta de comando de plataforma cruzada, de software livre, baseada em Shell para criar e gerenciar recursos no Microsoft Azure. Esta versão da CLI deve ser usada somente para implantações clássicas.

## <a name="azure-cli"></a>CLI do Azure
O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2) é uma ferramenta de comando de plataforma cruzada, de software livre, baseada em Shell para criar e gerenciar recursos no Microsoft Azure.


## <a name="azure-iot-device-sdks"></a>SDKs do dispositivo IoT do Azure
Há _SDKs de dispositivo_ disponíveis para vários idiomas que permitem que você crie [aplicativos de dispositivo](#device-app) que interagem com um hub IOT. Os tutoriais do Hub IoT mostram como usar esses SDKs de dispositivo. Você pode encontrar o código-fonte e obter mais informações sobre os SDKs do dispositivo neste [repositório](https://github.com/Azure/azure-iot-sdks)github.

## <a name="azure-iot-service-sdks"></a>SDKs do serviço IoT do Azure
Há _SDKs de serviço_ disponíveis para vários idiomas que permitem que você crie [aplicativos de back-end](#back-end-app) que interagem com um hub IOT. Os tutoriais do Hub IoT mostram como usar esses SDKs de serviço. Você pode encontrar o código-fonte e obter mais informações sobre os SDKs de serviço neste [repositório](https://github.com/Azure/azure-iot-sdks)github.

## <a name="azure-iot-tools"></a>Ferramentas do Azure IoT
As [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) são uma extensão de Visual Studio Code de software livre entre plataformas que ajuda você a gerenciar o Hub IOT do Azure e os dispositivos no vs Code. Com as ferramentas de IoT do Azure, os desenvolvedores de IoT podiam desenvolver um projeto de IoT em VS Code com facilidade.

## <a name="azure-portal"></a>Portal do Azure
O [portal do Microsoft Azure](https://portal.azure.com) é um local central em que você pode provisionar e gerenciar seus recursos do Azure. Ele organiza seu _conteúdo usando as_folhas.

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) é uma coleção de cmdlets que você pode usar para gerenciar o Azure com o Windows PowerShell. Você pode usar os cmdlets para criar, testar, implantar e gerenciar soluções e serviços fornecidos por meio da plataforma Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite que você trabalhe com os recursos em sua solução como um grupo. Você pode implantar, atualizar ou excluir os recursos de sua solução em uma única operação coordenada.

## <a name="azure-service-bus"></a>Service Bus do Azure
O [barramento de serviço](../service-bus/index.md) fornece comunicação habilitada para nuvem com mensagens corporativas e comunicação retransmitida que ajuda você a conectar soluções locais com a nuvem. Alguns tutoriais do Hub IoT fazem uso de [filas](../service-bus-messaging/service-bus-messaging-overview.md)do barramento de serviço.

## <a name="azure-storage"></a>Storage do Azure
O [armazenamento do Azure](../storage/common/storage-introduction.md) é uma solução de armazenamento em nuvem. Ele inclui o serviço de armazenamento de BLOBs que você pode usar para armazenar dados de objeto não estruturados. Alguns tutoriais do Hub IoT usam o armazenamento de BLOBs.

## <a name="back-end-app"></a>Aplicativo de back-end
No contexto do [Hub IOT](#iot-hub), um aplicativo de back-end é um aplicativo que se conecta a um dos pontos de extremidade voltados para o serviço em um hub IOT. Por exemplo, um aplicativo de back-end pode recuperar mensagens do [dispositivo para a nuvem](#device-to-cloud) ou gerenciar o [registro de identidade](#identity-registry). Normalmente, um aplicativo de back-end é executado na nuvem, mas, em muitos dos tutoriais, os aplicativos de back-end são aplicativos de console em execução no seu computador de desenvolvimento local.

## <a name="built-in-endpoints"></a>Pontos de extremidade internos
Todo Hub IoT inclui um [ponto de extremidade](iot-hub-devguide-endpoints.md) interno que é compatível com o Hub de eventos. Você pode usar qualquer mecanismo que funcione com hubs de eventos para ler mensagens do dispositivo para a nuvem desse ponto de extremidade.

## <a name="cloud-gateway"></a>Gateway de nuvem
Um gateway de nuvem permite a conectividade para dispositivos que não podem se conectar diretamente ao [Hub IOT](#iot-hub). Um gateway de nuvem é hospedado na nuvem em contraste com um [Gateway de campo](#field-gateway) que é executado localmente para seus dispositivos. Um caso de uso típico para um gateway de nuvem é implementar a conversão de protocolo para seus dispositivos.

## <a name="cloud-to-device"></a>Nuvem para dispositivo
Refere-se a mensagens enviadas de um hub IoT para um dispositivo conectado. Muitas vezes, essas mensagens são comandos que instruem o dispositivo a realizar uma ação. Para obter mais informações, consulte [Enviar e receber mensagens com o Hub IOT](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Configuração
No contexto da [configuração automática do dispositivo](iot-hub-auto-device-config.md), uma configuração no Hub IOT define a configuração desejada para um conjunto de dispositivos gêmeos e fornece um conjunto de métricas para relatar o status e o progresso.

## <a name="connection-string"></a>Cadeia de ligação
Você usa cadeias de conexão em seu código de aplicativo para encapsular as informações necessárias para se conectar a um ponto de extremidade. Uma cadeia de conexão normalmente inclui o endereço do ponto de extremidade e as informações de segurança, mas os formatos de cadeia de conexão variam entre os serviços. Há dois tipos de cadeia de conexão associados ao serviço do Hub IoT:
- As *cadeias de conexão do dispositivo* permitem que os dispositivos se conectem aos pontos de extremidade voltados para o dispositivo em um hub IOT.
- As *cadeias de conexão do Hub IOT* permitem que os aplicativos de back-end se conectem aos pontos de extremidade voltados para o serviço em um hub IOT.

## <a name="custom-endpoints"></a>Pontos de extremidade personalizados
Você pode criar [pontos de extremidade](iot-hub-devguide-endpoints.md) personalizados em um hub IOT para entregar mensagens expedidas por uma [regra de roteamento](#routing-rules). Os pontos de extremidade personalizados se conectam diretamente a um hub de eventos, uma fila do barramento de serviço ou um tópico do barramento de serviço.

## <a name="custom-gateway"></a>Gateway personalizado
Um gateway permite a conectividade para dispositivos que não podem se conectar diretamente ao [Hub IOT](#iot-hub). Você pode usar Azure IoT Edge para criar gateways personalizados que implementam lógica personalizada para tratar mensagens, conversões de protocolo personalizadas e outros processamentos na borda.

## <a name="data-point-message"></a>Mensagem de ponto de dados
Uma mensagem de ponto de dados é uma mensagem de [dispositivo para nuvem](#device-to-cloud) que contém dados de [telemetria](#telemetry) , como velocidade de vento ou temperatura.

## <a name="desired-configuration"></a>Configuração desejada
No contexto de um [dispositivo](iot-hub-devguide-device-twins.md), a configuração desejada refere-se ao conjunto completo de propriedades e metadados no dispositivo de texto a ser sincronizado com o dispositivo.

## <a name="desired-properties"></a>Propriedades desejadas
No contexto de um [dispositivo](iot-hub-devguide-device-twins.md), as propriedades desejadas são uma subseção do dispositivo "r" que é usado com [Propriedades relatadas](#reported-properties) para sincronizar a configuração ou condição do dispositivo. As propriedades desejadas só podem ser definidas por um [aplicativo de back-end](#back-end-app) e são observadas pelo [aplicativo do dispositivo](#device-app).

## <a name="device-to-cloud"></a>Dispositivo para nuvem
Refere-se a mensagens enviadas de um dispositivo conectado para o [Hub IOT](#iot-hub). Essas mensagens podem ser [de ponto de dados](#data-point-message) ou de mensagens [interativas](#interactive-message) . Para obter mais informações, consulte [Enviar e receber mensagens com o Hub IOT](iot-hub-devguide-messaging.md).

## <a name="device"></a>Dispositivo
No contexto do IoT, um dispositivo é normalmente um dispositivo de computação independente de pequena escala que pode coletar dados ou controlar outros dispositivos. Por exemplo, um dispositivo pode ser um dispositivo de monitoramento ambiental ou um controlador para os sistemas de água e ventilação em uma estufa. O [Catálogo de dispositivos](https://catalog.azureiotsolutions.com/) fornece uma lista de dispositivos de hardware certificados para trabalhar com o [Hub IOT](#iot-hub).

## <a name="device-app"></a>Aplicativo do dispositivo
Um aplicativo de dispositivo é executado em seu [dispositivo](#device) e lida com a comunicação com o [Hub IOT](#iot-hub). Normalmente, você usa um dos [SDKs do dispositivo IOT do Azure](#azure-iot-device-sdks) ao implementar um aplicativo de dispositivo. Em muitos dos tutoriais de IoT, você usa um [dispositivo simulado](#simulated-device) para sua conveniência.

## <a name="device-condition"></a>Condição do dispositivo
Refere-se às informações de estado do dispositivo, como o método de conectividade em uso no momento, conforme relatado por um [aplicativo de dispositivo](#device-app). Os [aplicativos de dispositivo](#device-app) também podem relatar seus recursos. Você pode consultar informações de condição e capacidade usando o dispositivo gêmeos.

## <a name="device-data"></a>Dados do dispositivo
Os dados do dispositivo referem-se aos dados por dispositivo armazenados no [registro de identidade](#identity-registry)do Hub IOT. É possível importar e exportar esses dados.

## <a name="device-explorer"></a>Explorador de dispositivos
O [Gerenciador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) é uma ferramenta que é executada no Windows e permite que você gerencie seus dispositivos no [registro de identidade](#identity-registry). A ferramenta também pode enviar e receber mensagens para seus dispositivos.

## <a name="device-identity"></a>Identidade do dispositivo
A identidade do dispositivo é o identificador exclusivo atribuído a cada dispositivo registrado no [registro de identidade](#identity-registry).

## <a name="device-management"></a>Gestão de dispositivos
O gerenciamento de dispositivos abrange o ciclo de vida completo associado ao gerenciamento dos dispositivos em sua solução de IoT, incluindo planejamento, provisionamento, configuração, monitoramento e desativação.

## <a name="device-management-patterns"></a>Padrões da gestão de dispositivos
O [Hub IOT](#iot-hub) permite padrões comuns de gerenciamento de dispositivos, incluindo a reinicialização, a execução de redefinições de fábrica e a execução de atualizações de firmware em seus dispositivos.

## <a name="device-rest-api"></a>API REST do dispositivo
Você pode usar a [API REST do dispositivo](https://docs.microsoft.com/rest/api/iothub/device) de um dispositivo para enviar mensagens do dispositivo para a nuvem para um hub IOT e receber mensagens da [nuvem para o dispositivo](#cloud-to-device) de um hub IOT. Normalmente, você deve usar um dos [SDKs de dispositivo](#azure-iot-device-sdks) de nível superior, conforme mostrado nos tutoriais do Hub IOT.

## <a name="device-provisioning"></a>Provisionamento de dispositivos
O provisionamento de dispositivos é o processo de adicionar os dados iniciais do [dispositivo](#device-data) às lojas em sua solução. Para permitir que um novo dispositivo se conecte ao seu hub, você deve adicionar uma ID de dispositivo e chaves ao [registro de identidade](#identity-registry)do Hub IOT. Como parte do processo de provisionamento, talvez seja necessário inicializar dados específicos do dispositivo em outros repositórios de soluções.

## <a name="device-twin"></a>Dispositivo duplo
Um [dispositivo](iot-hub-devguide-device-twins.md) "r" é um documento JSON que armazena informações de estado do dispositivo, como metadados, configurações e condições. O [Hub IOT](#iot-hub) persiste um dispositivo "r" para cada dispositivo que você provisiona em seu hub IOT. Dispositivos gêmeos permitem sincronizar condições de [dispositivo](#device-condition) e configurações entre o dispositivo e o back-end da solução. Você pode consultar dispositivos gêmeos para localizar dispositivos específicos e consultar o status de operações de longa execução.

## <a name="direct-method"></a>Método direto
Um [método direto](iot-hub-devguide-direct-methods.md) é uma maneira de disparar um método a ser executado em um dispositivo invocando uma API no Hub IOT.

## <a name="endpoint"></a>Ponto Final
Um hub IoT expõe vários [pontos de extremidade](iot-hub-devguide-endpoints.md) que permitem que seus aplicativos se conectem ao Hub IOT. Há pontos de extremidade voltados para o dispositivo que permitem que os dispositivos executem operações como o envio de mensagens do [dispositivo para a nuvem](#device-to-cloud) e o recebimento de mensagens da [nuvem para o dispositivo](#cloud-to-device) . Há pontos de extremidade de gerenciamento voltados para o serviço que permitem que os [aplicativos de back-end](#back-end-app) executem operações como gerenciamento de [identidade do dispositivo](#device-identity) e gerenciamento de dispositivos. Há [pontos de extremidade internos](#built-in-endpoints) voltados para o serviço para a leitura de mensagens do dispositivo para a nuvem. Você pode criar [pontos de extremidade personalizados](#custom-endpoints) para receber mensagens do dispositivo para a nuvem expedidas por uma [regra de roteamento](#routing-rules).

## <a name="event-hubs-service"></a>Serviço de hubs de eventos
Os [hubs de eventos](../event-hubs/event-hubs-what-is-event-hubs.md) são um serviço de entrada de dados altamente escalonável que pode incluir milhões de eventos por segundo. O serviço permite processar e analisar grandes quantidades de dados produzidos por seus dispositivos e aplicativos conectados. Para uma comparação com o serviço do Hub IoT, consulte [comparação entre o Hub IOT do Azure e os hubs de eventos do Azure](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Ponto de extremidade compatível com o Hub de eventos
Para ler as mensagens do [dispositivo para a nuvem](#device-to-cloud) enviadas ao seu hub IOT, você pode se conectar a um ponto de extremidade em seu hub e usar qualquer método compatível com o Hub de eventos para ler essas mensagens. Os métodos compatíveis com o Hub de eventos incluem o uso de SDKs e [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)de [hubs de eventos](../event-hubs/event-hubs-programming-guide.md) .

## <a name="field-gateway"></a>Gateway de campo
Um gateway de campo permite a conectividade para dispositivos que não podem se conectar diretamente ao [Hub IOT](#iot-hub) e normalmente é implantado localmente com seus dispositivos. Para obter mais informações, consulte [o que é o Hub IOT do Azure?](about-iot-hub.md)

## <a name="free-account"></a>Conta gratuita
Você pode criar uma [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/) para concluir os tutoriais do Hub IOT e experimentar o serviço do Hub IOT (e outros serviços do Azure).

## <a name="gateway"></a>Gateway
Um gateway permite a conectividade para dispositivos que não podem se conectar diretamente ao [Hub IOT](#iot-hub). Consulte também [Gateway de campo](#field-gateway), [Gateway de nuvem](#cloud-gateway)e [Gateway personalizado](#custom-gateway).

## <a name="identity-registry"></a>Registro de identidade
O [registro de identidade](iot-hub-devguide-identity-registry.md) é o componente interno de um hub IOT que armazena informações sobre os dispositivos individuais com permissão para se conectar a um hub IOT.

## <a name="interactive-message"></a>Mensagem interativa
Uma mensagem interativa é uma mensagem da [nuvem para o dispositivo](#cloud-to-device) que dispara uma ação imediata no back-end da solução. Por exemplo, um dispositivo pode enviar um alarme sobre uma falha que deve ser conectada automaticamente a um sistema de CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
O Hub IoT é um serviço do Azure totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. Para obter mais informações, consulte [o que é o Hub IOT do Azure?](about-iot-hub.md) Usando sua [assinatura do Azure](#subscription), você pode criar hubs IOT para lidar com suas cargas de trabalho de mensagens de IOT.

## <a name="iot-hub-metrics"></a>Métricas do Hub IoT
As [métricas do Hub IOT](iot-hub-metrics.md) fornecem dados sobre o estado dos hubs IOT em sua [assinatura do Azure](#subscription). As métricas do Hub IoT permitem avaliar a integridade geral do serviço e os dispositivos conectados a ele. As métricas do Hub IoT podem ajudá-lo a ver o que está acontecendo com seu hub IoT e investigar problemas de causa raiz sem precisar contatar o suporte do Azure.

## <a name="iot-hub-query-language"></a>Linguagem de consulta do Hub IoT
A [linguagem de consulta do Hub IOT](iot-hub-devguide-query-language.md) é uma linguagem semelhante a SQL que permite consultar o [](#job) e o dispositivo gêmeos.

## <a name="iot-hub-resource-rest-api"></a>API REST de recurso do Hub IoT
Você pode usar a [API REST de recursos do Hub IOT](https://docs.microsoft.com/rest/api/iothub/iothubresource) para gerenciar os hubs IOT em sua [assinatura do Azure](#subscription) executando operações como criar, atualizar e excluir hubs.

## <a name="iot-solution-accelerators"></a>Aceleradores de soluções IoT
Os aceleradores de solução do Azure IoT agrupam vários serviços do Azure em soluções. Essas soluções permitem que você comece rapidamente com implementações de ponta a ponta de cenários comuns de IoT. Para obter mais informações, consulte [o que são aceleradores de solução do Azure IOT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>A extensão de IoT para CLI do Azure 
[A extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) é uma ferramenta de linha de comando de plataforma cruzada. A ferramenta permite que você gerencie seus dispositivos no [registro de identidade](#identity-registry), envie e receba mensagens e arquivos de seus dispositivos e monitore as operações do Hub IOT.

## <a name="job"></a>Tarefa
O back-end da solução pode usar [trabalhos](iot-hub-devguide-jobs.md) para agendar e acompanhar atividades em um conjunto de dispositivos registrados com o Hub IOT. As atividades incluem a atualização de [propriedades desejadas](#desired-properties)do dispositivo, atualizando as [marcas](#tags)do dispositivo e invocando [métodos diretos](#direct-method). O [Hub IOT](#iot-hub) também usa o para [importar e exportar](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) do [registro de identidade](#identity-registry).

## <a name="modules"></a>Módulos
No lado do dispositivo, os SDKs do dispositivo do Hub IoT permitem criar [módulos](iot-hub-devguide-module-twins.md) em que cada um abre uma conexão independente com o Hub IOT. Essa funcionalidade permite que você use namespaces separados para diferentes componentes em seu dispositivo.

A identidade do módulo e o módulo "r" fornecem os mesmos recursos que o [dispositivo](#device-twin) e a [identidade do dispositivo](#device-identity) , mas com uma granularidade mais fina. Essa granularidade mais fina permite que dispositivos compatíveis, como dispositivos baseados no sistema operacional ou dispositivos de firmware, gerenciem vários componentes, Isolem a configuração e as condições de cada um desses componentes.

## <a name="module-identity"></a>Identidade do módulo
A identidade do módulo é o identificador exclusivo atribuído a cada módulo que pertence a um dispositivo. A identidade do módulo também é registrada no [registro de identidade](#identity-registry).

## <a name="module-twin"></a>Módulo...
Semelhante ao do dispositivo, um módulo "r" é um documento JSON que armazena informações de estado do módulo, como metadados, configurações e condições. O Hub IoT persiste um módulo "r" para cada identidade de módulo que você provisiona em uma identidade de dispositivo em seu hub IoT. O módulo gêmeos permite que você sincronize as condições e configurações do módulo entre o módulo e o back-end da solução. Você pode consultar o módulo gêmeos para localizar módulos específicos e consultar o status de operações de longa execução.

## <a name="mqtt"></a>MQTT
[MQTT](https://mqtt.org/) é um dos protocolos de mensagens que o [Hub IOT](#iot-hub) dá suporte para comunicação com dispositivos. Para obter mais informações sobre os protocolos de mensagens que o Hub IoT suporta, consulte [Enviar e receber mensagens com o Hub IOT](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorização de operações
O [monitoramento de operações](iot-hub-operations-monitoring.md) do Hub IOT permite monitorar o status das operações no Hub IOT em tempo real. O [Hub IOT](#iot-hub) rastreia eventos em várias categorias de operações. Você pode optar por enviar eventos de uma ou mais categorias para um ponto de extremidade do Hub IoT para processamento. Você pode monitorar os dados em busca de erros ou configurar o processamento mais complexo com base em padrões de dados.

## <a name="physical-device"></a>Dispositivo físico
Um dispositivo físico é um dispositivo real, como um Raspberry Pi, que se conecta a um hub IoT. Para sua conveniência, muitos dos tutoriais do Hub IoT usam [dispositivos simulados](#simulated-device) para permitir que você execute amostras em seu computador local.

## <a name="primary-and-secondary-keys"></a>Chaves primárias e secundárias
Quando você se conecta a um ponto de extremidade voltado para o dispositivo ou para o serviço em um hub IoT, sua [cadeia de conexão](#connection-string) inclui a chave para conceder a você acesso. Quando você adiciona um dispositivo ao [registro de identidade](#identity-registry) ou adiciona uma [política de acesso compartilhado](#shared-access-policy) ao seu hub, o serviço gera uma chave primária e secundária. Ter duas chaves permite que você passe de uma chave para outra ao atualizar uma chave sem perder o acesso ao Hub IoT.

## <a name="protocol-gateway"></a>Gateway de protocolo
Um gateway de protocolo normalmente é implantado na nuvem e fornece serviços de conversão de protocolo para dispositivos que se conectam ao [Hub IOT](#iot-hub). Para obter mais informações, consulte [o que é o Hub IOT do Azure?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Quotas e limitação
Há várias [cotas](iot-hub-devguide-quotas-throttling.md) que se aplicam ao uso do [Hub IOT](#iot-hub), muitas das cotas variam com base na camada do Hub IOT. O [Hub IOT](#iot-hub) também [aplica restrições](iot-hub-devguide-quotas-throttling.md) ao uso do serviço em tempo de execução.

## <a name="reported-configuration"></a>Configuração relatada
No contexto de um [dispositivo](iot-hub-devguide-device-twins.md), a configuração relatada refere-se ao conjunto completo de propriedades e metadados no dispositivo de cópia de texto que deve ser relatado para o back-end da solução.

## <a name="reported-properties"></a>Propriedades relatadas
No contexto de um [dispositivo](iot-hub-devguide-device-twins.md)"r", as propriedades relatadas são uma subseção do dispositivo de texto usado com [as propriedades desejadas](#desired-properties) para sincronizar a configuração ou condição do dispositivo. As propriedades relatadas só podem ser definidas pelo [aplicativo do dispositivo](#device-app) e podem ser lidas e consultadas por um [aplicativo de back-end](#back-end-app).

## <a name="resource-group"></a>Grupo de recursos
O [Azure Resource Manager](#azure-resource-manager) usa grupos de recursos para agrupar recursos relacionados. Você pode usar um grupo de recursos para executar operações em todos os recursos no grupo simultaneamente.

## <a name="retry-policy"></a>Política de repetição
Você usa uma política de repetição para lidar com [erros transitórios](/azure/architecture/best-practices/transient-faults) ao se conectar a um serviço de nuvem.

## <a name="routing-rules"></a>Regras de encaminhamento
Você configura [as regras de roteamento](iot-hub-devguide-messages-read-custom.md) em seu hub IOT para rotear mensagens do dispositivo para a nuvem para um [ponto de extremidade interno](#built-in-endpoints) ou para [pontos de extremidades personalizados](#custom-endpoints) para processamento pelo back-end da solução.

## <a name="sasl-plain"></a>SASL PLAIN
SASL PLAIN é um protocolo que o protocolo AMQP usa para transferir tokens de segurança.

## <a name="service-rest-api"></a>API REST do serviço
Você pode usar a [API REST do serviço](https://docs.microsoft.com/rest/api/iothub/service) do back-end da solução para gerenciar seus dispositivos. A API permite que você recupere e atualize Propriedades de conexão de [dispositivo](#device-twin) , invoque [métodos diretos](#direct-method)e agende [trabalhos](#job). Normalmente, você deve usar um dos [SDKs de serviço](#azure-iot-service-sdks) de nível superior, conforme mostrado nos tutoriais do Hub IOT.

## <a name="shared-access-signature"></a>Assinatura de acesso compartilhado
As SAS (assinaturas de acesso compartilhado) são um mecanismo de autenticação baseado em hashes seguros SHA-256 ou URIs. A autenticação SAS tem dois componentes: uma _política de acesso compartilhado_ e uma _assinatura de acesso compartilhado_ (geralmente chamada de token). Um dispositivo usa SAS para autenticar com um hub IoT. Os [aplicativos de back-end](#back-end-app) também usam SAS para autenticar com os pontos de extremidade voltados para o serviço em um hub IOT. Normalmente, você inclui o token SAS na [cadeia de conexão](#connection-string) que um aplicativo usa para estabelecer uma conexão com um hub IOT.

## <a name="shared-access-policy"></a>Política de acesso compartilhado
Uma política de acesso compartilhado define as permissões concedidas a qualquer pessoa que tenha uma [chave primária ou secundária](#primary-and-secondary-keys) válida associada a essa política. Você pode gerenciar as políticas de acesso compartilhado e as chaves para o Hub no [portal](#azure-portal).

## <a name="simulated-device"></a>Dispositivo simulado
Para sua conveniência, muitos dos tutoriais do Hub IoT usam dispositivos simulados para permitir que você execute amostras em seu computador local. Por outro lado, um [dispositivo físico](#physical-device) é um dispositivo real, como um Raspberry Pi, que se conecta a um hub IOT.

## <a name="solution"></a>Solução
Uma _solução_ pode se referir a uma solução do Visual Studio que inclui um ou mais projetos. Uma _solução_ também pode se referir a uma solução de IOT que inclui elementos como dispositivos, [aplicativos de dispositivo](#device-app), um hub IOT, outros serviços do Azure e [aplicativos de back-end](#back-end-app).

## <a name="subscription"></a>Subscrição
Uma assinatura do Azure é o local em que a cobrança ocorre. Cada recurso do Azure que você cria ou o serviço do Azure que você usa está associado a uma única assinatura. Muitas cotas também se aplicam ao nível de uma assinatura.

## <a name="system-properties"></a>Propriedades do sistema
No contexto de um [dispositivo](iot-hub-devguide-device-twins.md), as propriedades do sistema são somente leitura e incluem informações sobre o uso do dispositivo, como a hora da última atividade e o estado da conexão.

## <a name="tags"></a>Etiquetas
No contexto de um [dispositivo](iot-hub-devguide-device-twins.md), as marcas são metadados de dispositivo armazenados e recuperados pelo back-end da solução na forma de um documento JSON. As marcas não são visíveis para aplicativos em um dispositivo.

## <a name="telemetry"></a>Telemetria
Os dispositivos coletam dados de telemetria, como velocidade do vento ou temperatura, e usam mensagens de ponto de dados para enviar a telemetria para um hub IoT.

## <a name="token-service"></a>Serviço de token
Você pode usar um serviço de token para implementar um mecanismo de autenticação para seus dispositivos. Ele usa uma política de [acesso compartilhado](#shared-access-policy) do Hub IOT com permissões **DeviceConnect** para criar tokens no *escopo do dispositivo* . Esses tokens permitem que um dispositivo se conecte ao Hub IoT. Um dispositivo usa um mecanismo de autenticação personalizado para autenticar com o serviço de token. Se o dispositivo for autenticado com êxito, o serviço de token emitirá um token SAS para o dispositivo usar para acessar o Hub IoT.

## <a name="twin-queries"></a>Consultas de entrelaçamento
As [consultas de dispositivo e de módulo de](iot-hub-devguide-query-language.md) pesquisa usam a linguagem de consulta do Hub IOT do tipo SQL para recuperar informações de seu dispositivo gêmeos ou módulo gêmeos. Você pode usar a mesma linguagem de consulta do Hub IoT para recuperar [](#job) informações sobre a execução em seu hub IOT.

## <a name="twin-synchronization"></a>Sincronização de entrelaçar
A sincronização de entrelaçamento usa as [propriedades desejadas](#desired-properties) em seu dispositivo gêmeos ou módulo gêmeos para configurar seus dispositivos ou módulos e recuperar [as propriedades relatadas](#reported-properties) deles para armazenar no.

## <a name="x509-client-certificate"></a>Certificado de cliente X. 509
Um dispositivo pode usar um certificado X. 509 para autenticar com o [Hub IOT](#iot-hub). O uso de um certificado X. 509 é uma alternativa ao uso de um [token SAS](#shared-access-signature).

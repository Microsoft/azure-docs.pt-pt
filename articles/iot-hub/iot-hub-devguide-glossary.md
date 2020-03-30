---
title: Glossário azure IoT Hub de termos Microsoft Docs
description: Guia de desenvolvedores - um glossário explicando alguns dos termos comuns usados nos artigos do Azure IoT Hub.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: 3471bd7297118dc75fb97390c4601179f61e6416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497540"
---
# <a name="glossary-of-iot-hub-terms"></a>Glossário dos termos do Hub IoT
Este artigo enumera alguns dos termos comuns utilizados nos artigos do IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Protocolo avançado de fila de mensagens
O Protocolo avançado de [fila de mensagens (AMQP)](https://www.amqp.org/) é um dos protocolos de mensagens que [o IoT Hub](#iot-hub) suporta para comunicar com dispositivos. Para obter mais informações sobre os protocolos de mensagens que o IoT Hub suporta, consulte [Enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Gestão automática de Dispositivos
A Gestão Automática de Dispositivos no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gerir grandes frotas de dispositivos ao longo de toda a sua vida. Com a Gestão Automática de Dispositivos, pode direcionar um conjunto de dispositivos com base nas suas propriedades, definir uma configuração desejada e deixar os dispositivos de atualização do IoT Hub sempre que entram em alcance.  Consiste em [configurações automáticas](iot-hub-auto-device-config.md) de dispositivos e [implementações automáticas IoT Edge](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Configuração automática do dispositivo
A sua solução na parte de trás pode usar [configurações automáticas](iot-hub-auto-device-config.md) do dispositivo para atribuir propriedades desejadas a um conjunto de [gémeos](#device-twin) do dispositivo e reportar o estado usando métricas do sistema e métricas personalizadas. 

## <a name="azure-classic-cli"></a>CLI clássica do Azure
O [Azure classic CLI](../cli-install-nodejs.md) é uma ferramenta de comando de plataforma cruzada, open-source, baseada em conchas, para criar e gerir recursos no Microsoft Azure. Esta versão do CLI deve ser utilizada apenas para implementações clássicas.

## <a name="azure-cli"></a>CLI do Azure
O [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) é uma ferramenta de comando transversal, de código aberto, baseada em conchas, para criar e gerir recursos no Microsoft Azure.


## <a name="azure-iot-device-sdks"></a>SDKs do dispositivo Azure IoT
Existem _SDKs_ de dispositivos disponíveis para vários idiomas que lhe permitem criar aplicações de [dispositivos](#device-app) que interagem com um hub IoT. Os tutoriais do IoT Hub mostram-lhe como usar estes SDKs do dispositivo. Pode encontrar o código fonte e mais informações sobre os SDKs do dispositivo neste [repositório](https://github.com/Azure/azure-iot-sdks)GitHub .

## <a name="azure-iot-service-sdks"></a>Serviço Azure IoT SDKs
Existem _SDKs_ de serviço disponíveis para vários idiomas que lhe permitem criar [aplicações de back-end](#back-end-app) que interagem com um hub IoT. Os tutoriais do IoT Hub mostram-lhe como usar estes SDKs de serviço. Pode encontrar o código fonte e mais informações sobre os SDKs de serviço neste [repositório](https://github.com/Azure/azure-iot-sdks)GitHub .

## <a name="azure-iot-tools"></a>Ferramentas do Azure IoT
O [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) é uma extensão de código de estúdio visual de código visual de código de código de código de código visual de código de código de código de código de código de código de código visual de código aberto que o ajuda a gerir o Azure IoT Hub e dispositivos em Código VS. Com as Ferramentas Azure IoT, os desenvolvedores de IoT poderiam desenvolver o projeto IoT em Código VS com facilidade.

## <a name="azure-portal"></a>Portal do Azure
O [portal Microsoft Azure](https://portal.azure.com) é um local central onde pode fornecer e gerir os seus recursos Azure. Organiza o seu conteúdo com _lâminas._

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) é uma coleção de cmdlets que pode usar para gerir o Azure com o Windows PowerShell. Pode utilizar os cmdlets para criar, testar, implementar e gerir soluções e serviços entregues através da plataforma Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[O Azure Resource Manager](../azure-resource-manager/management/overview.md) permite-lhe trabalhar com os recursos da sua solução como grupo. Pode implementar, atualizar ou eliminar os recursos para a sua solução numa única operação coordenada.

## <a name="azure-service-bus"></a>Service Bus do Azure
[O Service Bus](../service-bus/index.md) fornece comunicação ativada pela nuvem com mensagens empresariais e comunicação retransmitida que o ajuda a ligar soluções no local com a nuvem. Alguns tutoriais IoT Hub usam filas de [ônibus](../service-bus-messaging/service-bus-messaging-overview.md)de serviço.

## <a name="azure-storage"></a>Storage do Azure
[O Armazenamento Azure](../storage/common/storage-introduction.md) é uma solução de armazenamento em nuvem. Inclui o serviço de armazenamento Blob que pode utilizar para armazenar dados de objetos não estruturados. Alguns tutoriais IoT Hub usam armazenamento de bolhas.

## <a name="back-end-app"></a>App back-end
No contexto do [IoT Hub,](#iot-hub)uma aplicação back-end é uma aplicação que se conecta a um dos pontos finais virados para o serviço num hub IoT. Por exemplo, uma aplicação back-end pode recuperar mensagens [dispositivo-to-cloud](#device-to-cloud) ou gerir o [registo de identidade](#identity-registry). Normalmente, uma aplicação de back-end corre na nuvem, mas em muitos dos tutoriais as aplicações back-end são aplicações de consola que estão a funcionar na sua máquina de desenvolvimento local.

## <a name="built-in-endpoints"></a>Pontos finais incorporados
Cada hub IoT inclui um [ponto final](iot-hub-devguide-endpoints.md) incorporado que é compatível com o Event Hub. Pode utilizar qualquer mecanismo que funcione com os Hubs de Eventos para ler mensagens dispositivo-nuvem a partir deste ponto final.

## <a name="cloud-gateway"></a>Porta de entrada de nuvem
Um gateway de nuvem permite conectividade para dispositivos que não conseguem ligar-se diretamente ao [IoT Hub](#iot-hub). Um portal de nuvem é hospedado na nuvem em contraste com um gateway de [campo](#field-gateway) que funciona localmente para os seus dispositivos. Um caso de utilização típico para um gateway em nuvem é implementar a tradução protocolar para os seus dispositivos.

## <a name="cloud-to-device"></a>Nuvem-a-dispositivo
Refere-se a mensagens enviadas de um hub IoT para um dispositivo conectado. Muitas vezes, estas mensagens são comandos que instruem o dispositivo a tomar uma ação. Para mais informações, consulte [Enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Configuração
No contexto da [configuração automática](iot-hub-auto-device-config.md)do dispositivo, uma configuração dentro do IoT Hub define a configuração desejada para um conjunto de dispositivos gémeos e fornece um conjunto de métricas para reportar o estado e o progresso.

## <a name="connection-string"></a>Cadeia de ligação
Utiliza cordas de ligação no código da aplicação para encapsular as informações necessárias para se ligar a um ponto final. Uma cadeia de ligação normalmente inclui o endereço do ponto final e informações de segurança, mas os formatos de cordas de ligação variam entre os serviços. Existem dois tipos de cadeia de ligação associadas ao serviço IoT Hub:
- As cordas de *ligação* do dispositivo permitem que os dispositivos se conectem aos pontos finais virados para o dispositivo num hub IoT.
- As cordas de *ligação IoT Hub* permitem que as aplicações de back-end se conectem aos pontos finais virados para o serviço num hub IoT.

## <a name="custom-endpoints"></a>Pontos finais personalizados
Pode criar [pontos finais personalizados](iot-hub-devguide-endpoints.md) num hub IoT para entregar mensagens enviadas por uma regra de [encaminhamento](#routing-rules). Os pontos finais personalizados ligam-se diretamente a um hub de eventos, uma fila de ônibus de serviço ou um tópico de ônibus de serviço.

## <a name="custom-gateway"></a>Gateway personalizado
Um portal permite a conectividade para dispositivos que não conseguem ligar-se diretamente ao [IoT Hub](#iot-hub). Você pode usar O Edge Azure IoT para construir gateways personalizados que implementam lógica personalizada para lidar com mensagens, conversões de protocolopersonalizados e outros processamentos no limite.

## <a name="data-point-message"></a>Mensagem de ponto de dados
Uma mensagem de ponto de dados é uma mensagem [dispositivo-nuvem](#device-to-cloud) que contém dados de [telemetria,](#telemetry) tais como velocidade do vento ou temperatura.

## <a name="desired-configuration"></a>Configuração desejada
No contexto de um [dispositivo twin](iot-hub-devguide-device-twins.md), a configuração desejada refere-se ao conjunto completo de propriedades e metadados no dispositivo twin que deve ser sincronizado com o dispositivo.

## <a name="desired-properties"></a>Propriedades desejadas
No contexto de um [dispositivo twin,](iot-hub-devguide-device-twins.md)as propriedades desejadas são uma subsecção do dispositivo twin que é usado com [propriedades reportadas](#reported-properties) para sincronizar a configuração ou condição do dispositivo. As propriedades desejadas só podem ser definidas por uma [aplicação de back-end](#back-end-app) e são observadas pela [aplicação](#device-app)do dispositivo .

## <a name="device-to-cloud"></a>Dispositivo-para-nuvem
Refere-se a mensagens enviadas de um dispositivo conectado para [IoT Hub](#iot-hub). Estas mensagens podem ser mensagens [de ponto de dados](#data-point-message) ou [interativas.](#interactive-message) Para mais informações, consulte [Enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Dispositivo
No contexto do IoT, um dispositivo é tipicamente um dispositivo de computação em pequena escala e autónomo que pode recolher dados ou controlar outros dispositivos. Por exemplo, um dispositivo pode ser um dispositivo de monitorização ambiental, ou um controlador para os sistemas de rega e ventilação numa estufa. O catálogo do [dispositivo](https://catalog.azureiotsolutions.com/) fornece uma lista de dispositivos de hardware certificados para trabalhar com [o IoT Hub](#iot-hub).

## <a name="device-app"></a>Aplicação do dispositivo
Uma aplicação de dispositivo funciona no seu [dispositivo](#device) e lida com a comunicação com o seu [hub IoT](#iot-hub). Normalmente, utiliza-se um dos [SDKs do dispositivo Azure IoT](#azure-iot-device-sdks) quando implementa uma aplicação de dispositivo. Em muitos dos tutoriais IoT, você usa um [dispositivo simulado](#simulated-device) para conveniência.

## <a name="device-condition"></a>Condição do dispositivo
Refere-se a informações do estado do dispositivo, como o método de conectividade atualmente em uso, conforme relatado por uma [aplicação](#device-app)de dispositivo . [As aplicações](#device-app) de dispositivos também podem reportar as suas capacidades. Pode consultar informações sobre a condição e a capacidade utilizando gémeos dispositivos.

## <a name="device-data"></a>Dados do dispositivo
Os dados do dispositivo referem-se aos dados por dispositivo armazenados no [registo de identidade](#identity-registry)do IoT Hub . É possível importar e exportar estes dados.

## <a name="device-explorer"></a>Explorador de dispositivos
O explorador de [dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) é uma ferramenta que funciona no Windows e permite-lhe gerir os seus dispositivos no [registo de identidade](#identity-registry). A ferramenta também pode enviar e receber mensagens para os seus dispositivos.

## <a name="device-identity"></a>Identidade do dispositivo
A identidade do dispositivo é o identificador único atribuído a todos os dispositivos registados no [registo de identidade](#identity-registry).

## <a name="device-management"></a>Gestão de dispositivos
A gestão do dispositivo engloba todo o ciclo de vida associado à gestão dos dispositivos na sua solução IoT, incluindo planeamento, provisionamento, configuração, monitorização e aposentação.

## <a name="device-management-patterns"></a>Padrões da gestão de dispositivos
[O hub IoT](#iot-hub) permite padrões comuns de gestão de dispositivos, incluindo reiniciar, realizar resets de fábrica e realizar atualizações de firmware nos seus dispositivos.

## <a name="device-rest-api"></a>Dispositivo REST API
Pode utilizar a API DO [REPOUSAR](https://docs.microsoft.com/rest/api/iothub/device) do Dispositivo a partir de um dispositivo para enviar mensagens de dispositivo-nuvem para um hub IoT e receber mensagens [cloud-to-device](#cloud-to-device) a partir de um hub IoT. Normalmente, deve utilizar um dos [SDKs](#azure-iot-device-sdks) de dispositivo de nível mais elevado, como mostra os tutoriais ioT Hub.

## <a name="device-provisioning"></a>Fornecimento de dispositivos
O fornecimento do dispositivo é o processo de adição dos dados iniciais do [dispositivo](#device-data) às lojas na sua solução. Para permitir que um novo dispositivo se ligue ao seu hub, deve adicionar um ID do dispositivo e chaves ao [registo de identidade](#identity-registry)IoT Hub . Como parte do processo de fornecimento, poderá ser necessário inicializar dados específicos do dispositivo noutras lojas de soluções.

## <a name="device-twin"></a>Dispositivo duplo
Um [dispositivo twin](iot-hub-devguide-device-twins.md) é o documento JSON que armazena informações estatais do dispositivo, tais como metadados, configurações e condições. [O IoT Hub](#iot-hub) persiste um dispositivo gémeo para cada dispositivo que você fornecer no seu hub IoT. Os gémeos do dispositivo permitem sincronizar [as condições](#device-condition) e configurações do dispositivo entre o dispositivo e a extremidade traseira da solução. Pode consultar gémeos de dispositivos para localizar dispositivos específicos e consultar o estado das operações de longo prazo.

## <a name="direct-method"></a>Método direto
Um [método direto](iot-hub-devguide-direct-methods.md) é uma forma de desencadear um método de execução num dispositivo invocando uma API no seu hub IoT.

## <a name="endpoint"></a>Ponto Final
Um hub IoT expõe [vários pontos finais](iot-hub-devguide-endpoints.md) que permitem que as suas aplicações se conectem ao hub IoT. Existem pontos finais virados para dispositivos que permitem que os dispositivos realizem operações como o envio de mensagens [de dispositivo-nuvem](#device-to-cloud) e a receção de mensagens [cloud-to-device.](#cloud-to-device) Existem pontos finais de gestão virados para o serviço que permitem que [as aplicações back-end](#back-end-app) realizem operações como a gestão de identidade do [dispositivo](#device-identity) e a gestão de twin dispositivos. Existem [pontos finais incorporados virados](#built-in-endpoints) para o serviço para a leitura de mensagens dispositivo-nuvem. Pode criar [pontos finais personalizados](#custom-endpoints) para receber mensagens de dispositivo-nuvem enviadas por uma regra de [encaminhamento](#routing-rules).

## <a name="event-hubs-service"></a>Serviço De Hubs de Eventos
[O Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) é um serviço de entrada de dados altamente escalável que pode ingerir milhões de eventos por segundo. O serviço permite-lhe processar e analisar as quantidades massivas de dados produzidos pelos seus dispositivos e aplicações conectados. Para uma comparação com o serviço IoT Hub, consulte [A Comparação do Hub Azure IoT e dos Hubs de Eventos Azure.](iot-hub-compare-event-hubs.md)

## <a name="event-hub-compatible-endpoint"></a>Ponto final compatível com o Hub de Eventos
Para ler mensagens [de dispositivo-nuvem](#device-to-cloud) enviadas para o seu hub IoT, pode ligar-se a um ponto final no seu hub e utilizar qualquer método compatível com o Event Hub para ler essas mensagens. Os métodos compatíveis com o Hub de Eventos incluem a utilização dos [SDKs](../event-hubs/event-hubs-programming-guide.md) de Hubs de Eventos e [do Azure Stream Analytics.](../stream-analytics/stream-analytics-introduction.md)

## <a name="field-gateway"></a>Porta de entrada de campo
Um gateway de campo permite conectividade para dispositivos que não conseguem ligar-se diretamente ao [IoT Hub](#iot-hub) e que é normalmente implantado localmente com os seus dispositivos. Para mais informações, consulte [o que é o Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Conta gratuita
Pode criar uma [conta Azure gratuita](https://azure.microsoft.com/pricing/free-trial/) para completar os tutoriais IoT Hub e experimentar com o serviço IoT Hub (e outros serviços Azure).

## <a name="gateway"></a>Gateway
Um portal permite a conectividade para dispositivos que não conseguem ligar-se diretamente ao [IoT Hub](#iot-hub). Consulte também [o Field Gateway,](#field-gateway) [cloud Gateway](#cloud-gateway)e [Custom Gateway.](#custom-gateway)

## <a name="identity-registry"></a>Registo de identidade
O [registo de identidade](iot-hub-devguide-identity-registry.md) é o componente incorporado de um hub IoT que armazena informações sobre os dispositivos individuais autorizados a ligar-se a um hub IoT.

## <a name="interactive-message"></a>Mensagem interativa
Uma mensagem interativa é uma mensagem [cloud-to-device](#cloud-to-device) que desencadeia uma ação imediata na extremidade traseira da solução. Por exemplo, um dispositivo pode enviar um alarme sobre uma falha que deve ser automaticamente iniciada num sistema CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub
O IoT Hub é um serviço Azure totalmente gerido que permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. Para mais informações, consulte [o que é o Azure IoT Hub?](about-iot-hub.md) Utilizando a sua [subscrição Azure,](#subscription)pode criar hubs IoT para lidar com as suas cargas de trabalho de mensagens IoT.

## <a name="iot-hub-metrics"></a>Métricas do Hub IoT
[As métricas do IoT Hub](iot-hub-metrics.md) dão-lhe dados sobre o estado dos hubs IoT na sua [subscrição Azure](#subscription). As métricas do IoT Hub permitem avaliar a saúde geral do serviço e os dispositivos ligados ao mesmo. As métricas do IoT Hub podem ajudá-lo a ver o que se passa com o seu hub IoT e investigar problemas de causa-raiz sem precisar de contactar o suporte do Azure.

## <a name="iot-hub-query-language"></a>Linguagem de consulta do Hub IoT
A linguagem de [consulta IoT Hub](iot-hub-devguide-query-language.md) é uma linguagem semelhante a [](#job) SQL que lhe permite consultar os seus gémeos e dispositivos.

## <a name="iot-hub-resource-rest-api"></a>IoT Hub Recurso REST API
Você pode usar a [IoT Hub Resource REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) para gerir os hubs IoT na sua [subscrição Azure](#subscription) executando operações como criar, atualizar e eliminar centros.

## <a name="iot-solution-accelerators"></a>Aceleradores de soluções IoT
Os aceleradores de soluções Azure IoT combinam vários serviços Azure em soluções. Estas soluções permitem-lhe começar rapidamente com implementações de ponta a ponta de cenários comuns de IoT. Para mais informações, consulte os aceleradores de [soluções Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>A extensão IoT para O CLI Azure 
[A extensão IoT para O CLI Azure](https://github.com/Azure/azure-iot-cli-extension) é uma ferramenta de linha de comando transversal. A ferramenta permite-lhe gerir os seus dispositivos no [registo de identidade,](#identity-registry)enviar e receber mensagens e ficheiros dos seus dispositivos e monitorizar as suas operações de hub IoT.

## <a name="job"></a>Tarefa
A sua solução na parte de trás pode usar [trabalhos](iot-hub-devguide-jobs.md) para agendar e rastrear atividades num conjunto de dispositivos registados com o seu hub IoT. As atividades incluem atualizar [as propriedades desejadas](#desired-properties)pelo dispositivo, atualizar [as duas etiquetas](#tags)do dispositivo e invocar [métodos diretos.](#direct-method) [O IoT Hub](#iot-hub) também utiliza a [importação e exportação](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) do registo de [identidade.](#identity-registry)

## <a name="modules"></a>Módulos
Do lado do dispositivo, os SDKs do dispositivo IoT Hub permitem criar [módulos](iot-hub-devguide-module-twins.md) onde cada um abre uma ligação independente ao IoT Hub. Esta funcionalidade permite-lhe utilizar espaços de nome separados para diferentes componentes no seu dispositivo.

A identidade do módulo e o módulo twin fornecem as mesmas capacidades que a identidade do [dispositivo](#device-identity) e [o dispositivo twin,](#device-twin) mas com uma granularidade mais fina. Esta granularidade mais fina permite que dispositivos capazes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware que gerem vários componentes, isole a configuração e as condições para cada um desses componentes.

## <a name="module-identity"></a>Identidade do módulo
A identidade do módulo é o identificador único atribuído a cada módulo que pertença a um dispositivo. A identidade do módulo também está registada no [registo de identidade.](#identity-registry)

## <a name="module-twin"></a>Módulo gémeo
Semelhante ao dispositivo twin, um módulo twin é um documento JSON que armazena informações estatais de módulos, tais como metadados, configurações e condições. O IoT Hub persiste um módulo twin para cada identidade de módulo que você disponibiliza sob uma identidade de dispositivo no seu hub IoT. Os gémeos módulos permitem sincronizar as condições e configurações do módulo entre o módulo e a extremidade traseira da solução. Pode consultar gémeos de módulos para localizar módulos específicos e consultar o estado das operações de longo prazo.

## <a name="mqtt"></a>MQTT
[O MQTT](https://mqtt.org/) é um dos protocolos de mensagens que [o IoT Hub](#iot-hub) suporta para comunicar com dispositivos. Para obter mais informações sobre os protocolos de mensagens que o IoT Hub suporta, consulte [Enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorização de operações
A monitorização das [operações](iot-hub-operations-monitoring.md) do IoT Hub permite-lhe monitorizar o estado das operações no seu hub IoT em tempo real. [O IoT Hub](#iot-hub) acompanha eventos em várias categorias de operações. Pode optar por enviar eventos de uma ou mais categorias para um ponto final do IoT Hub para processamento. Pode monitorizar os dados para erros ou configurar um processamento mais complexo com base em padrões de dados.

## <a name="physical-device"></a>Dispositivo físico
Um dispositivo físico é um dispositivo real, como um Raspberry Pi que se conecta a um hub IoT. Por conveniência, muitos dos tutoriais do IoT Hub utilizam [dispositivos simulados](#simulated-device) para permitir-lhe executar amostras na sua máquina local.

## <a name="primary-and-secondary-keys"></a>Chaves primárias e secundárias
Quando se liga a um ponto final virado para o dispositivo ou virado para o serviço num hub IoT, a sua cadeia de [ligação](#connection-string) inclui a chave para lhe conceder acesso. Quando adiciona um dispositivo ao [registo de identidade](#identity-registry) ou adiciona uma política de acesso [partilhado](#shared-access-policy) ao seu hub, o serviço gera uma chave primária e secundária. Ter duas teclas permite-lhe rolar de uma tecla para outra quando atualiza uma tecla sem perder acesso ao hub IoT.

## <a name="protocol-gateway"></a>Gateway protocole
Um gateway protocole é normalmente implantado na nuvem e fornece serviços de tradução protocolar para dispositivos que ligam ao [IoT Hub](#iot-hub). Para mais informações, consulte [o que é o Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Quotas e limitação
Existem várias [quotas](iot-hub-devguide-quotas-throttling.md) que se aplicam à sua utilização do [IoT Hub,](#iot-hub)muitas das quotas variam em função do nível do hub IoT. [O IoT Hub](#iot-hub) também aplica [acelerações](iot-hub-devguide-quotas-throttling.md) à sua utilização do serviço em tempo de execução.

## <a name="reported-configuration"></a>Configuração reportada
No contexto de um [dispositivo twin](iot-hub-devguide-device-twins.md), a configuração reportada refere-se ao conjunto completo de propriedades e metadados no dispositivo twin que deve ser reportado à extremidade traseira da solução.

## <a name="reported-properties"></a>Propriedades reportadas
No contexto de um [dispositivo twin,](iot-hub-devguide-device-twins.md)as propriedades reportadas são uma subsecção do dispositivo twin usado com [propriedades desejadas](#desired-properties) para sincronizar a configuração ou condição do dispositivo. As propriedades reportadas só podem ser definidas pela [aplicação](#device-app) do dispositivo e podem ser lidas e consultadas por uma [aplicação de back-end](#back-end-app).

## <a name="resource-group"></a>Grupo de recursos
[O Azure Resource Manager](#azure-resource-manager) utiliza grupos de recursos para agrupar recursos relacionados. Você pode usar um grupo de recursos para realizar operações em todos os recursos do grupo simultaneamente.

## <a name="retry-policy"></a>Política de repetição
Usa uma política de retry para lidar com [erros transitórios](/azure/architecture/best-practices/transient-faults) quando se conecta a um serviço de nuvem.

## <a name="routing-rules"></a>Regras de encaminhamento
Configura as regras de [encaminhamento](iot-hub-devguide-messages-read-custom.md) no seu hub IoT para direcionar as mensagens de dispositivo-nuvem para um [ponto final incorporado](#built-in-endpoints) ou para [pontos finais personalizados](#custom-endpoints) para processamento pela sua solução na extremidade traseira.

## <a name="sasl-plain"></a>SASL PLAIN
SASL PLAIN é um protocolo que o protocolo AMQP utiliza para transferir fichas de segurança.

## <a name="service-rest-api"></a>API REST do serviço
Pode utilizar a [API](https://docs.microsoft.com/rest/api/iothub/service/configuration) do Serviço REST a partir da extremidade traseira da solução para gerir os seus dispositivos. A API permite-lhe recuperar e atualizar propriedades [gémeas](#device-twin) do dispositivo, invocar [métodos diretos](#direct-method)e agendar [trabalhos.](#job) Normalmente, você deve usar um dos [SDKs](#azure-iot-service-sdks) de serviço de nível mais alto, como mostrado nos tutoriais IoT Hub.

## <a name="shared-access-signature"></a>Assinatura de acesso partilhado
As Assinaturas de Acesso Partilhado (SAS) são um mecanismo de autenticação baseado em hashes ou URIs seguros SHA-256. A autenticação SAS tem dois componentes: uma Política de _Acesso Partilhado_ e uma Assinatura de _Acesso Partilhado_ (muitas vezes chamada de símbolo). Um dispositivo utiliza SAS para autenticar com um hub IoT. [As aplicações back-end](#back-end-app) também usam o SAS para autenticar com os pontos finais virados para o serviço num hub IoT. Normalmente, você inclui o token SAS na cadeia de [conexão](#connection-string) que uma aplicação usa para estabelecer uma ligação a um hub IoT.

## <a name="shared-access-policy"></a>Política de acesso partilhado
Uma política de acesso partilhado define as permissões concedidas a quem tiver uma [chave primária ou secundária](#primary-and-secondary-keys) válida associada a essa política. Você pode gerir as políticas de acesso partilhado e chaves para o seu hub no [portal](#azure-portal).

## <a name="simulated-device"></a>Dispositivo simulado
Por conveniência, muitos dos tutoriais do IoT Hub utilizam dispositivos simulados para permitir-lhe executar amostras na sua máquina local. Em contraste, um [dispositivo físico](#physical-device) é um dispositivo real, como um Raspberry Pi que se conecta a um hub IoT.

## <a name="solution"></a>Solução
Uma _solução_ pode referir-se a uma solução de Estúdio Visual que inclui um ou mais projetos. Uma _solução_ também pode referir-se a uma solução IoT que inclui elementos como dispositivos, [aplicações de dispositivos,](#device-app)um hub IoT, outros serviços Azure e [aplicações back-end.](#back-end-app)

## <a name="subscription"></a>Subscrição
Uma subscrição Azure é onde a faturação ocorre. Cada recurso Azure que cria ou o serviço Azure que utiliza está associado a uma única subscrição. Muitas quotas também se aplicam ao nível de uma subscrição.

## <a name="system-properties"></a>Propriedades do sistema
No contexto de um [dispositivo twin](iot-hub-devguide-device-twins.md), as propriedades do sistema são apenas de leitura e incluem informações sobre o uso do dispositivo, como o tempo de atividade e o estado de conexão.

## <a name="tags"></a>Etiquetas
No contexto de um [dispositivo twin](iot-hub-devguide-device-twins.md), as etiquetas são metadados do dispositivo armazenados e recuperados pela solução na extremidade traseira sob a forma de um documento JSON. As etiquetas não são visíveis para aplicações num dispositivo.

## <a name="telemetry"></a>Telemetria
Os dispositivos recolhem dados de telemetria, como velocidade do vento ou temperatura, e usam mensagens de ponto de dados para enviar a telemetria para um centro IoT.

## <a name="token-service"></a>Serviço de fichas
Pode utilizar um serviço simbólico para implementar um mecanismo de autenticação para os seus dispositivos. Utiliza uma política de [acesso partilhado](#shared-access-policy) IoT Hub com permissões **DeviceConnect** para criar tokens *com mira de dispositivo.* Estas fichas permitem que um dispositivo se conectem ao seu hub IoT. Um dispositivo utiliza um mecanismo de autenticação personalizado para autenticar com o serviço de fichas. Se o dispositivo autenticar com sucesso, o serviço token emite um token SAS para o dispositivo utilizar para aceder ao seu hub IoT.

## <a name="twin-queries"></a>Consultas gémeas
[As consultas de dispositivo e módulo twin](iot-hub-devguide-query-language.md) usam a linguagem de consulta IoT Hub semelhante ao SQL para obter informações dos gémeos do seu dispositivo ou gémeos módulos. Você pode usar a mesma linguagem de consulta [](#job) IoT Hub para obter informações sobre correr no seu hub IoT.

## <a name="twin-synchronization"></a>Sincronização gémea
A sincronização gémea utiliza as [propriedades desejadas](#desired-properties) no seu dispositivo gémeos ou gémeos módulos para configurar os seus dispositivos ou módulos e recuperar [propriedades reportadas](#reported-properties) deles para armazenar no twin.

## <a name="x509-client-certificate"></a>X.509 certificado de cliente
Um dispositivo pode utilizar um certificado X.509 para autenticar com [ioT Hub](#iot-hub). A utilização de um certificado X.509 é uma alternativa à utilização de um [símbolo SAS](#shared-access-signature).

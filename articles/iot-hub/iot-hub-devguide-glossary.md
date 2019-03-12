---
title: Glossário do IoT Hub do Azure dos termos | Documentos da Microsoft
description: Guia do programador - um glossário de termos comuns relacionados ao IoT Hub do Azure.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: ac7043d2967fcf3f083c09622c39f4649f04f6e7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551675"
---
# <a name="glossary-of-iot-hub-terms"></a>Glossário de termos do IoT Hub
Este artigo lista alguns dos termos comuns utilizados nos artigos do IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Protocolo de colocação em fila de mensagens avançados
[Advanced Message colocação em fila Protocol (AMQP)](https://www.amqp.org/) é um do messaging protocolos que [IoT Hub](#iot-hub) oferece suporte para comunicação com dispositivos. Para obter mais informações sobre os protocolos de mensagens que o IoT Hub suporta, consulte [enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Gestão Automática de Dispositivos
Gestão automática de dispositivos no IoT Hub do Azure automatiza muitas das tarefas repetitivas e complexas de gerenciamento de frotas de dispositivo grandes sobre a totalidade dos respetivos ciclos de vida. Gestão de dispositivos automático, pode um conjunto de dispositivos com base nas respetivas propriedades de destino, definir uma configuração desejada e permitir que o IoT Hub, atualize os dispositivos sempre que eles vão para o âmbito.  Consiste [configurações de dispositivos automático](iot-hub-auto-device-config.md) e [implementações automáticas do IoT Edge](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Configuração do dispositivo automática
Pode utilizar a sua solução de back-end [configurações de dispositivos automático](iot-hub-auto-device-config.md) para atribuir as propriedades pretendidas para um conjunto de [dispositivos duplos](#device-twin) e comunicar o estado com as métricas do sistema e métricas personalizadas. 

## <a name="azure-classic-cli"></a>CLI clássica do Azure
O [CLI clássica do Azure](../cli-install-nodejs.md) é uma ferramenta de várias plataformas, código-fonte aberto, com base no shell de comando para criar e gerir recursos no Microsoft Azure. Esta versão da CLI deve ser utilizada para implementações clássicas apenas.

## <a name="azure-cli"></a>CLI do Azure
O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2) é uma ferramenta de várias plataformas, código-fonte aberto, com base no shell de comando para criar e gerir recursos no Microsoft Azure.


## <a name="azure-iot-device-sdks"></a>SDKs de dispositivo de IoT do Azure
Existem _SDKs do dispositivo_ disponível para vários idiomas que permitem que crie [aplicações de dispositivos](#device-app) que interagem com um hub IoT. Os tutoriais do IoT Hub mostram-lhe como utilizar estes SDKs de dispositivo. Pode encontrar o código-fonte e obter mais informações sobre os SDKs do dispositivo neste GitHub [repositório](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-service-sdks"></a>SDKs de serviço de IoT do Azure
Existem _SDKs de serviço_ disponível para vários idiomas que permitem que crie [aplicações de back-end](#back-end-app) que interagem com um hub IoT. Os tutoriais do IoT Hub mostram-lhe como utilizar estes SDKs de serviço. Pode encontrar o código-fonte e obter mais informações sobre os SDKs de serviço neste GitHub [repositório](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-tools"></a>Ferramentas do Azure IoT
O [ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) é uma extensão de código do Visual Studio para várias plataformas, o código-fonte aberto que lhe permite gerir o IoT Hub do Azure e de dispositivos no VS Code. Com ferramentas de IoT do Azure, os programadores de IoT poderiam desenvolver o projeto de IoT no VS Code com facilidade.

## <a name="azure-portal"></a>Portal do Azure
O [portal do Microsoft Azure](https://portal.azure.com) é um local central onde pode aprovisionar e gerir recursos do Azure. Esta lista organiza seu conteúdo usando _painéis_.

## <a name="azure-powershell"></a>Azure PowerShell
[O Azure PowerShell](/powershell/azure/overview) é uma coleção de cmdlets que pode utilizar para gerir o Azure com o Windows PowerShell. Pode utilizar os cmdlets para criar, testar, implementar e gerir soluções e serviços disponibilizados através da plataforma do Azure.

## <a name="azure-resource-manager"></a>Gestor de Recursos do Azure
[O Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite-lhe trabalhar com os recursos na sua solução como um grupo. Pode implementar, atualizar ou eliminar os recursos da sua solução numa operação única e coordenada.

## <a name="azure-service-bus"></a>Azure Service Bus
[Do Service Bus](../service-bus/index.md) fornece comunicação com mensagens empresariais à capacidade de nuvem e de comunicação retransmitida que lhe permite ligar soluções no local à cloud. Alguns tutoriais do IoT Hub tornam a utilizar o Service Bus [filas](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Armazenamento do Azure
[O armazenamento do Azure](../storage/common/storage-introduction.md) é uma solução de armazenamento na cloud. Ele inclui o serviço de armazenamento de BLOBs que pode utilizar para armazenar dados de objetos não estruturados. Alguns tutoriais do IoT Hub utilizam o armazenamento de Blobs.

## <a name="back-end-app"></a>Aplicação de back-end
No contexto do [IoT Hub](#iot-hub), uma aplicação de back-end é uma aplicação que se conecta a um dos pontos finais com acesso de serviço num IoT hub. Por exemplo, poderá obter uma aplicação de back-end [dispositivo-para-cloud](#device-to-cloud) mensagens ou gerir a [registo de identidade](#identity-registry). Normalmente, uma aplicação de back-end é executado na cloud, mas em muitos dos tutoriais, as aplicações de back-end são aplicações de consola em execução no computador de desenvolvimento local.

## <a name="built-in-endpoints"></a>Pontos finais incorporados
Cada hub IoT inclui um incorporado [ponto final](iot-hub-devguide-endpoints.md) ou seja assim. Pode usar qualquer mecanismo que funciona com os Hubs de eventos para ler mensagens do dispositivo-para-cloud a partir deste ponto final.

## <a name="cloud-gateway"></a>Gateway de nuvem
Um gateway de cloud permite uma conectividade de dispositivos que não é possível ligar diretamente à [IoT Hub](#iot-hub). Um gateway de nuvem está alojado na cloud em comparação com uma [gateway de campo](#field-gateway) que executa o local para os seus dispositivos. Um caso de utilização típicos para um gateway de nuvem é implementar a tradução do protocolo para os seus dispositivos.

## <a name="cloud-to-device"></a>Nuvem para dispositivo
Refere-se às mensagens enviadas a partir de um hub IoT para um dispositivo ligado. Muitas vezes, essas mensagens são comandos que instruem o dispositivo execute uma ação. Para obter mais informações, consulte [enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Configuração
No contexto do [configuração do dispositivo automática](iot-hub-auto-device-config.md), uma configuração de dentro do IoT Hub define a configuração pretendida para um conjunto de dispositivos duplos e fornece um conjunto de métricas para comunicar o estado e progresso.

## <a name="connection-string"></a>Cadeia de ligação
Utilizar cadeias de ligação no seu código de aplicação para encapsular as informações necessárias para ligar a um ponto de extremidade. Uma cadeia de ligação inclui normalmente o endereço do ponto final e informações de segurança, mas a cadeia de ligação formatos variam entre serviços. Existem dois tipos de cadeia de ligação associado ao serviço IoT Hub:
- *Cadeias de ligação do dispositivo* permitir que os dispositivos liguem aos pontos finais com acesso de dispositivo num IoT hub.
- *Cadeias de ligação do IoT Hub* para ativar as aplicações de back-end ligar para os pontos finais de serviço com acesso num IoT hub.

## <a name="custom-endpoints"></a>Pontos finais personalizados
Pode criar personalizado [pontos de extremidade](iot-hub-devguide-endpoints.md) num hub IoT para entregar mensagens distribuídas por um [regra de encaminhamento](#routing-rules). Ligar pontos finais personalizados diretamente para um hub de eventos, uma fila do Service Bus ou um tópico do Service Bus.

## <a name="custom-gateway"></a>Gateway personalizado
Um gateway permite uma conectividade de dispositivos que não é possível ligar diretamente à [IoT Hub](#iot-hub). Pode utilizar o Azure IoT Edge para criar gateways personalizadas que implementam a lógica personalizada para lidar com mensagens, conversões de protocolo personalizado e de outro processamento na periferia.

## <a name="data-point-message"></a>Mensagem de ponto de dados
Uma mensagem de ponto de dados é um [dispositivo-para-cloud](#device-to-cloud) mensagem que contém [telemetria](#telemetry) dados, tais como velocidade do vento ou temperatura.

## <a name="desired-configuration"></a>Configuração pretendida
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), desired configuration refere-se ao conjunto completo de propriedades e metadados no dispositivo duplo que deve ser sincronizado com o dispositivo.

## <a name="desired-properties"></a>Propriedades pretendidas
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), assim o desejar propriedades é uma subsecção do dispositivo duplo que é utilizado com [propriedades comunicadas](#reported-properties) para sincronizar a configuração do dispositivo ou condição. As propriedades pretendidas só podem ser definidas um [aplicação de back-end](#back-end-app) e são observado pela [aplicação de dispositivo](#device-app).

## <a name="device-to-cloud"></a>Dispositivo para a nuvem
Refere-se às mensagens enviadas a partir de um dispositivo ligado ao [IoT Hub](#iot-hub). Essas mensagens podem ser [ponto de dados](#data-point-message) ou [interativas](#interactive-message) mensagens. Para obter mais informações, consulte [enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Dispositivo
No contexto da IoT, um dispositivo é, normalmente, um dispositivo de computação do autónomo em pequena escala, que pode recolher dados ou outros dispositivos de controlo. Por exemplo, um dispositivo pode ser um dispositivo de monitorização ambiental, ou um controlador para os sistemas de "watering hole" e ventilação num greenhouse. O [catálogo de dispositivos](https://catalog.azureiotsolutions.com/) fornece uma lista de dispositivos de hardware certificado para trabalhar com [IoT Hub](#iot-hub).

## <a name="device-app"></a>Aplicação de dispositivo
Uma aplicação de dispositivo é executado no seu [dispositivo](#device) e trata a comunicação com o seu [IoT hub](#iot-hub). Normalmente, utilize um da [do Azure IoT device SDKs](#azure-iot-device-sdks) quando implementar uma aplicação de dispositivo. Muitos dos tutoriais IoT, vai utilizar um [dispositivo simulado](#simulated-device) para sua comodidade.

## <a name="device-condition"></a>Condição de dispositivo
Refere-se a informações de estado do dispositivo, como o método de conectividade atualmente em uso, conforme comunicado por uma [aplicação de dispositivo](#device-app). [Aplicações de dispositivos](#device-app) também pode comunicar as respetivas capacidades. Pode consultar as informações de condição e a capacidade de utilizar dispositivos duplos.

## <a name="device-data"></a>Dados do dispositivo
Dados de dispositivo refere-se aos dados por dispositivo armazenados no IoT Hub [registo de identidade](#identity-registry). É possível importar e exportar estes dados.

## <a name="device-explorer"></a>Explorador de dispositivos
O [Explorador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) é uma ferramenta que é executado no Windows e permite-lhe gerir os dispositivos no [registo de identidade](#identity-registry). A ferramenta também pode enviar e receber mensagens para os seus dispositivos.

## <a name="device-identity"></a>Identidade do dispositivo
A identidade de dispositivo é o identificador exclusivo atribuído para todos os dispositivos registados no [registo de identidade](#identity-registry).

## <a name="device-management"></a>Gestão de dispositivos
Gestão de dispositivos abrange o ciclo de vida completo associado à gestão de dispositivos na sua solução de IoT, incluindo o planejamento, aprovisionamento, configuração, monitorização e extinção.

## <a name="device-management-patterns"></a>Padrões da gestão de dispositivos
[IoT hub](#iot-hub) permite padrões de gestão de dispositivos comuns incluindo reiniciar, efetuar reposições de fábrica e a execução de atualizações de firmware nos seus dispositivos.

## <a name="device-rest-api"></a>REST API do dispositivo
Pode utilizar o [API REST do dispositivo](https://docs.microsoft.com/rest/api/iothub/device) de um dispositivo para enviar mensagens dispositivo-para-cloud para um hub IoT e receber [cloud-para-dispositivo](#cloud-to-device) mensagens de um hub IoT. Normalmente, deve usar uma do nível mais elevado [SDKs de dispositivo](#azure-iot-device-sdks) conforme mostrado nos tutoriais do IoT Hub.

## <a name="device-provisioning"></a>Aprovisionamento de dispositivos
Aprovisionamento de dispositivos é o processo de adição inicial [dados de dispositivo](#device-data) às lojas em sua solução. Para ativar um novo dispositivo ligar ao seu hub, tem de adicionar um ID de dispositivo e as chaves para o IoT Hub [registo de identidade](#identity-registry). Como parte do processo de aprovisionamento, poderá ter de inicializar dados específicos do dispositivo em outros armazenamentos de solução.

## <a name="device-twin"></a>Dispositivo duplo
R [dispositivo duplo](iot-hub-devguide-device-twins.md) é um documento JSON que armazena informações de estado do dispositivo como metadados, configurações e condições. [IoT Hub](#iot-hub) cria um dispositivo duplo para cada dispositivo que for aprovisionado do seu hub IoT. Os gémeos de dispositivos permitem-lhe sincronizar [condições de dispositivo](#device-condition) e configurações entre o dispositivo e a solução de back-end. Pode consultar dispositivos duplos para localizar dispositivos específicos e consultar o estado das operações de longa execução.

## <a name="direct-method"></a>Método direto
R [método direto](iot-hub-devguide-direct-methods.md) é uma forma para que possa acionar um método para executar num dispositivo ao invocar uma API no seu hub IoT.

## <a name="endpoint"></a>Ponto Final
Um IoT hub expõe várias [pontos de extremidade](iot-hub-devguide-endpoints.md) que permitem que as suas aplicações para ligar ao IoT hub. Existem pontos finais com acesso de dispositivo que permitem que os dispositivos realizar operações como o envio [dispositivo-para-cloud](#device-to-cloud) mensagens e receber [cloud-para-dispositivo](#cloud-to-device) mensagens. Existem pontos de extremidade do serviço com acesso à gestão que permitem [aplicações de back-end](#back-end-app) para executar operações, como [identidade de dispositivo](#device-identity) gestão e de gestão do dispositivo duplo. Existem voltado para o serviço [pontos finais incorporados](#built-in-endpoints) para ler mensagens do dispositivo para a cloud. Pode criar [os pontos finais personalizados](#custom-endpoints) para receber mensagens dispositivo-para-cloud expedidas por um [regra de encaminhamento](#routing-rules).

## <a name="event-hubs-service"></a>Serviço de Hubs de eventos
[Os Hubs de eventos](../event-hubs/event-hubs-what-is-event-hubs.md) é um serviço de entrada de dados altamente dimensionável que pode ingerir milhões de eventos por segundo. O serviço permite-lhe processar e analisar enormes quantidades de dados produzidos pelos seus dispositivos e aplicações ligados. Para uma comparação com o serviço IoT Hub, veja [comparação do IoT Hub do Azure e Hubs de eventos](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Ponto final compatível com Hubs de Eventos
Para ler [dispositivo-para-cloud](#device-to-cloud) as mensagens enviadas ao seu hub IoT, pode ligar a um ponto final no seu hub e utilizar qualquer método compatível com o Event Hub para ler essas mensagens. Métodos de compatível com o Hub de eventos incluem a utilização a [SDKs de Hubs de eventos](../event-hubs/event-hubs-programming-guide.md) e [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Gateway de campo
Um gateway de campo permite uma conectividade de dispositivos que não é possível ligar diretamente à [IoT Hub](#iot-hub) e é normalmente implementado localmente nos seus dispositivos. Para obter mais informações, consulte [o que é o IoT Hub do Azure?](about-iot-hub.md)

## <a name="free-account"></a>Conta gratuita
Pode criar uma [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/) para concluir os tutoriais do IoT Hub e experimentar o serviço IoT Hub (e outros serviços do Azure).

## <a name="gateway"></a>Gateway
Um gateway permite uma conectividade de dispositivos que não é possível ligar diretamente à [IoT Hub](#iot-hub). Consulte também [Gateway de campo](#field-gateway), [Gateway de nuvem](#cloud-gateway), e [Gateway personalizado](#custom-gateway).

## <a name="identity-registry"></a>Registo de identidade
O [registo de identidade](iot-hub-devguide-identity-registry.md) é o componente interno de um hub IoT que armazena informações sobre os dispositivos dos utilizadores autorizado a ligar a um hub IoT.

## <a name="interactive-message"></a>Mensagem interativa
Uma mensagem de interativa é um [cloud-para-dispositivo](#cloud-to-device) mensagem que aciona uma ação imediata na solução de back-end. Por exemplo, um dispositivo pode enviar um alarme sobre uma falha que deve ter automaticamente sessão iniciada sistema CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>Hub IoT
O IoT Hub é um serviço totalmente gerido do Azure que permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução de back-end. Para obter mais informações, consulte [o que é o IoT Hub do Azure?](about-iot-hub.md) Utilizar o seu [subscrição do Azure](#subscription), pode criar os hubs IoT para processar o seu IoT cargas de trabalho de mensagens.

## <a name="iot-hub-metrics"></a>Métricas do IoT Hub
[Métricas do IoT Hub](iot-hub-metrics.md) dão-lhe dados sobre o estado dos hubs de IoT em seu [subscrição do Azure](#subscription). Métricas do IoT Hub permitem-lhe avaliar o estado de funcionamento geral do serviço e os dispositivos ligados ao mesmo. Métricas do IoT Hub podem ajudá-lo a ver o que está acontecendo com o seu hub IoT e investigar problemas de causa raiz sem a necessidade de contactar o suporte do Azure.

## <a name="iot-hub-query-language"></a>Linguagem de consulta do IoT Hub
O [linguagem de consulta do IoT Hub](iot-hub-devguide-query-language.md) é uma linguagem de tipo SQL que lhe permite consultar seu [](#job) e dispositivos duplos.

## <a name="iot-hub-resource-rest-api"></a>REST API do IoT Hub recursos
Pode utilizar o [API do REST de recursos do IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource) para gerir os hubs IoT no seu [subscrição do Azure](#subscription) efetuar operações como criar, atualizar e excluir os hubs.

## <a name="iot-solution-accelerators"></a>Aceleradores de soluções de IoT
Aceleradores de solução de IoT do Azure reúne, vários serviços do Azure em soluções. Estas soluções permitem que comece a trabalhar rapidamente com implementações de ponto-a-ponto dos cenários de IoT comuns. Para obter mais informações, consulte [quais são os Aceleradores de solução de IoT do Azure?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>A extensão de IoT para a CLI do Azure 
[A extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) é uma ferramenta de linha de comando, de várias plataformas. A ferramenta permite-lhe gerir os dispositivos no [registo de identidade](#identity-registry), enviar e receber mensagens e arquivos de seus dispositivos e monitorizar as operações do hub IoT.

## <a name="job"></a>Tarefa
Pode utilizar a sua solução de back-end [tarefas](iot-hub-devguide-jobs.md) para agendar e monitorizar atividades num conjunto de dispositivos registados com o seu hub IoT. As atividades incluem a atualizar o dispositivo duplo [propriedades pretendidas](#desired-properties), a atualização do dispositivo duplo [etiquetas](#tags)e a invocação [métodos diretos](#direct-method). [IoT Hub](#iot-hub) também utiliza para [importar para e exportar](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) da [registo de identidade](#identity-registry).

## <a name="modules"></a>Módulos
No lado do dispositivo, os SDKs do dispositivo do IoT Hub permitem que crie [módulos](iot-hub-devguide-module-twins.md) onde cada um deles é aberta uma ligação independente para o IoT Hub. Esta funcionalidade permite-lhe utilizar espaços de nomes separados para diferentes componentes no seu dispositivo.

Identidade do módulo e módulo duplo fornecem as mesmas capacidades do [identidade do dispositivo](#device-identity) e [dispositivo duplo](#device-twin) mas a uma granularidade mais fina. Este granularidade mais fina permite compatível com dispositivos, como dispositivos baseados no sistema operativo ou de dispositivos com firmware gerir vários componentes, para isolar a configuração e condições para cada um desses componentes.

## <a name="module-identity"></a>Identidade do módulo
A identidade do módulo é o identificador exclusivo atribuído a cada módulo que pertencem a um dispositivo. Identidade do módulo também é registrada no [registo de identidade](#identity-registry).

## <a name="module-twin"></a>Módulo duplo
Assim como o dispositivo duplo, um duplo do módulo é um documento JSON que armazena informações de estado do módulo como metadados, configurações e condições. O IoT Hub cria um módulo duplo para cada identidade de módulo que for aprovisionado sob uma identidade de dispositivo do seu hub IoT. Duplos de módulo permitem-lhe sincronizar condições de módulo e configurações entre o módulo e o back-end de solução. Pode consultar duplos de módulo para localizar os módulos específicos e consultar o estado das operações de longa execução.

## <a name="mqtt"></a>MQTT
[MQTT](https://mqtt.org/) é um do messaging protocolos que [IoT Hub](#iot-hub) oferece suporte para comunicação com dispositivos. Para obter mais informações sobre os protocolos de mensagens que o IoT Hub suporta, consulte [enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorização de operações
IoT Hub [monitorização de operações](iot-hub-operations-monitoring.md) permite-lhe monitorizar o estado das operações no hub IoT em tempo real. [IoT Hub](#iot-hub) regista os eventos em várias categorias de operações. Pode participar no envio de eventos de um ou mais categorias para um ponto de final do IoT Hub para processamento. Pode monitorizar os dados de erros ou configurar o processamento mais complexo com base nos padrões de dados.

## <a name="physical-device"></a>Dispositivo físico
Um dispositivo físico é um dispositivo real, como um Raspberry Pi que se liga a um hub IoT. Para sua comodidade, muitos dos tutoriais do IoT Hub usam [dispositivos simulados](#simulated-device) que lhe permite executar os exemplos no seu computador local.

## <a name="primary-and-secondary-keys"></a>Chaves primárias e secundárias
Quando se liga a um ponto de final voltado para o dispositivo ou serviço com acesso num IoT hub, sua [cadeia de ligação](#connection-string) incluem a chave para lhe conceder acesso. Ao adicionar um dispositivo para o [registo de identidade](#identity-registry) ou adicionar um [partilhado a política de acesso](#shared-access-policy) ao seu hub, o serviço gera uma chave primária e secundária. Ter duas chaves permite-lhe fazer o rollover de uma chave para outra quando atualizar uma chave sem perderem o acesso ao IoT hub.

## <a name="protocol-gateway"></a>Gateway de protocolo
Um gateway de protocolo é normalmente implementado na cloud e fornece serviços de tradução para os dispositivos ligados a protocolo [IoT Hub](#iot-hub). Para obter mais informações, consulte [o que é o IoT Hub do Azure?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Quotas e limitação
Existem várias [quotas](iot-hub-devguide-quotas-throttling.md) que se aplicam à sua utilização dos [IoT Hub](#iot-hub), muitas das quotas variam consoante o escalão do IoT hub. [IoT Hub](#iot-hub) também se aplica [regula](iot-hub-devguide-quotas-throttling.md) à utilização do serviço em tempo de execução.

## <a name="reported-configuration"></a>Configuração comunicada
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), comunicado configuração refere-se ao conjunto completo de propriedades e metadados no dispositivo duplo que deve ser informado à solução de back-end.

## <a name="reported-properties"></a>Propriedades comunicadas
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), reportou propriedades é uma subsecção do dispositivo duplo utilizado com [propriedades pretendidas](#desired-properties) para sincronizar a configuração do dispositivo ou condição. Propriedades comunicadas só podem ser definidas [aplicação do dispositivo](#device-app) e pode ler e consultados por um [aplicação de back-end](#back-end-app).

## <a name="resource-group"></a>Grupo de recursos
[O Azure Resource Manager](#azure-resource-manager) utiliza grupos de recursos para agrupar os recursos relacionados. Pode utilizar um grupo de recursos para realizar operações em todos os recursos no grupo em simultâneo.

## <a name="retry-policy"></a>Política de repetição
Utilizar uma política de repetição para processar [erros transitórios](/azure/architecture/best-practices/transient-faults) ao ligar a um serviço cloud.

## <a name="routing-rules"></a>Regras de encaminhamento
Configura [regras de encaminhamento](iot-hub-devguide-messages-read-custom.md) do seu hub IoT para encaminhar mensagens dispositivo-para-cloud para um [ponto final incorporado](#built-in-endpoints) ou a [pontos finais personalizados](#custom-endpoints) para processamento através do seu back-end de solução .

## <a name="sasl-plain"></a>SASL SIMPLES
SASL simples é um protocolo que o protocolo AMQP utiliza-se para transferir os tokens de segurança.

## <a name="service-rest-api"></a>API REST do Serviço
Pode utilizar o [API REST do serviço](https://docs.microsoft.com/rest/api/iothub/service) da solução de back-end para gerir os seus dispositivos. A API permite-lhe obter e atualizar [dispositivo duplo](#device-twin) propriedades, invocar [métodos diretos](#direct-method)e a agenda [tarefas](#job). Normalmente, deve usar uma do nível mais elevado [SDKs de serviço](#azure-iot-service-sdks) conforme mostrado nos tutoriais do IoT Hub.

## <a name="shared-access-signature"></a>Assinatura de acesso partilhado
Assinaturas de acesso partilhado (SAS) são um mecanismo de autenticação com base nos hashes de seguros de SHA-256 ou URIs. Autenticação do SAS tem dois componentes: um _política de acesso partilhado_ e uma _assinatura de acesso partilhado_ (muitas vezes chamado de um token). Um dispositivo utiliza SAS para autenticar com um hub IoT. [Aplicações de back-end](#back-end-app) também utilizar a SAS para autenticar com os pontos finais de serviço com acesso num IoT hub. Normalmente, incluir o token SAS no [cadeia de ligação](#connection-string) que uma aplicação utiliza para estabelecer uma ligação para um hub IoT.

## <a name="shared-access-policy"></a>Política de acesso partilhado
Uma política de acesso partilhado define as permissões concedidas a qualquer pessoa que tenha um válido [chave primária ou secundária](#primary-and-secondary-keys) associados a essa política. Pode gerir as chaves e políticas de acesso partilhado do hub no [portal](#azure-portal).

## <a name="simulated-device"></a>Dispositivo de simulação
Para sua comodidade, muitos dos tutoriais do IoT Hub utilizam dispositivos simulados que lhe permite executar os exemplos no seu computador local. Por outro lado, um [dispositivo físico](#physical-device) é um dispositivo real, como um Raspberry Pi que se liga a um hub IoT.

## <a name="solution"></a>Solução
R _solução_ pode fazer referência a uma solução do Visual Studio inclui um ou mais projetos. R _solução_ também pode se referir a uma solução de IoT que inclui elementos, tais como dispositivos [aplicações de dispositivos](#device-app), um hub IoT, outros serviços do Azure, e [aplicações de back-end](#back-end-app).

## <a name="subscription"></a>Subscrição
Uma subscrição do Azure é onde ocorre a faturação. Cada recurso do Azure é criar ou utilizar o serviço do Azure está associado uma única subscrição. Quotas muitos também se aplicam ao nível de uma subscrição.

## <a name="system-properties"></a>Propriedades do sistema
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), propriedades do sistema são só de leitura e inclui informações sobre a utilização do dispositivo, como o último Estado de ligação e de tempo de atividade.

## <a name="tags"></a>Etiquetas
No contexto de um [dispositivo duplo](iot-hub-devguide-device-twins.md), as etiquetas são metadados do dispositivo, armazenados e recuperados pela solução de back-end na forma de um documento JSON. As etiquetas não estão visíveis para aplicações num dispositivo.

## <a name="telemetry"></a>Telemetria
Os dispositivos recolhem dados de telemetria, como velocidade do vento ou temperatura e utilizam mensagens de ponto de dados para enviar a telemetria para um hub IoT.

## <a name="token-service"></a>Serviço de token
Pode utilizar um serviço de token para implementar um mecanismo de autenticação para os seus dispositivos. Ele usa um IoT Hub [partilhado a política de acesso](#shared-access-policy) com **DeviceConnect** permissões para criar *no âmbito do dispositivo* tokens. Estes tokens permitem que um dispositivo para ligar ao seu hub IoT. Um dispositivo utiliza um mecanismo de autenticação personalizados para autenticar com o serviço de token. Se o dispositivo é autenticado com êxito, o serviço de token emite um token SAS para o dispositivo utilize para aceder ao seu hub IoT.

## <a name="twin-queries"></a>Consultas de gémeos
[Consultas de gémeos de dispositivo e módulo](iot-hub-devguide-query-language.md) utilizar a linguagem de consulta do Hub de IoT de tipo SQL para obter informações a partir dos dispositivos duplos ou duplos de módulo. Pode utilizar a mesma linguagem de consulta do IoT Hub para obter informações sobre [](#job) em execução no seu hub IoT.

## <a name="twin-synchronization"></a>Sincronização de duplo
Duplo sincronização utiliza a [propriedades pretendidas](#desired-properties) no seu dispositivos duplos ou duplos de módulo para configurar seus dispositivos ou os módulos e recuperar [propriedades comunicadas](#reported-properties) dos mesmos para armazenar no duplo.

## <a name="x509-client-certificate"></a>Certificado de cliente X.509
Um dispositivo pode utilizar um certificado X.509 para autenticar com o [IoT Hub](#iot-hub). O uso de um certificado X.509 é uma alternativa à utilização uma [SAS token](#shared-access-signature).

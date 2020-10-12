---
title: Azure IoT Hub glossário de termos / Microsoft Docs
description: Guia do desenvolvedor - um glossário que explica alguns dos termos comuns usados nos artigos do Azure IoT Hub.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2b7a81357cc6ee2608d2acd3051dcac3e5d0e07e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87023471"
---
# <a name="glossary-of-iot-hub-terms"></a>Glossário dos termos do IoT Hub

Este artigo lista alguns dos termos comuns usados nos artigos do IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Protocolo avançado de fila de mensagens

[O Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) é um dos protocolos de mensagens que o [IoT Hub](#iot-hub) suporta para comunicar com dispositivos. Para obter mais informações sobre os protocolos de mensagens que o IoT Hub suporta, consulte [Enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Gestão Automática de Dispositivos

A Gestão Automática de Dispositivos no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gestão de grandes frotas de dispositivos ao longo de todo o seu ciclo de vida. Com a Gestão Automática de Dispositivos, pode direcionar um conjunto de dispositivos com base nas suas propriedades, definir uma configuração desejada e deixar que os dispositivos de atualização do IoT Hub sempre que eles entram no âmbito.  Consiste em [configurações automáticas](iot-hub-auto-device-config.md) de dispositivos e [implementações automáticas IoT Edge](../iot-edge/how-to-deploy-at-scale.md).

## <a name="automatic-device-configuration"></a>Configuração automática do dispositivo

A sua solução traseira pode usar [configurações automáticas](iot-hub-auto-device-config.md) do dispositivo para atribuir as propriedades desejadas a um conjunto de [gémeos](#device-twin) do dispositivo e reportar o estado usando métricas do sistema e métricas personalizadas. 

## <a name="azure-classic-cli"></a>CLI clássica do Azure

O [Azure classic CLI](../cli-install-nodejs.md) é uma ferramenta de comando cross-platform, open-source, baseada em conchas, para criar e gerir recursos no Microsoft Azure. Esta versão do CLI deve ser utilizada apenas para implementações clássicas.

## <a name="azure-cli"></a>CLI do Azure

O [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) é uma ferramenta de comando transversal, de código aberto, baseada em conchas, para criar e gerir recursos no Microsoft Azure.

## <a name="azure-iot-device-sdks"></a>SDKs de dispositivo Azure IoT

Existem _SDKs de dispositivos_ disponíveis para vários idiomas que permitem criar aplicações de [dispositivos](#device-app) que interagem com um hub IoT. Os tutoriais do IoT Hub mostram-lhe como usar estes SDKs deste dispositivo. Pode encontrar o código-fonte e mais informações sobre os SDKs do dispositivo neste [repositório](https://github.com/Azure/azure-iot-sdks)GitHub .

## <a name="azure-iot-explorer"></a>Explorador Azure IoT

O [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) é utilizado para visualizar a telemetria que o dispositivo está a enviar, trabalhar com as propriedades do dispositivo e chamar comandos. Também pode utilizar o explorador para interagir e testar os seus dispositivos, e para gerir dispositivos de tomada e reprodução.

## <a name="azure-iot-service-sdks"></a>SDKs de serviço Azure IoT

Existem _SDKs_ de serviço disponíveis para vários idiomas que lhe permitem criar [aplicações de back-end](#back-end-app) que interagem com um hub IoT. Os tutoriais IoT Hub mostram-lhe como usar estes SDKs de serviço. Pode encontrar o código fonte e mais informações sobre os SDKs de serviço neste [repositório](https://github.com/Azure/azure-iot-sdks)GitHub .

## <a name="azure-iot-tools"></a>Ferramentas do Azure IoT

O [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) é uma extensão de código de estúdio visual de código visual de código visual de código aberto de plataforma aberta que o ajuda a gerir o Azure IoT Hub e dispositivos em CÓDIGO VS. Com a Azure IoT Tools, os desenvolvedores de IoT poderiam desenvolver o projeto IoT em VS Code com facilidade.

## <a name="azure-portal"></a>Portal do Azure

O [portal Microsoft Azure](https://portal.azure.com) é um local central onde podes abastecer e gerir os teus recursos Azure. Organiza o seu conteúdo com _lâminas._

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](/powershell/azure/) é uma coleção de cmdlets que pode usar para gerir o Azure com o Windows PowerShell. Pode utilizar os cmdlets para criar, testar, implementar e gerir soluções e serviços prestados através da plataforma Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager

[O Azure Resource Manager](../azure-resource-manager/management/overview.md) permite-lhe trabalhar com os recursos na sua solução como grupo. Pode implementar, atualizar ou eliminar os recursos da sua solução numa única operação coordenada.

## <a name="azure-service-bus"></a>Service Bus do Azure

[O Service Bus](../service-bus/index.md) fornece comunicação ativada em nuvem com mensagens empresariais e comunicação transmitida que o ajuda a conectar soluções no local com a nuvem. Alguns tutoriais do IoT Hub usam [as filas](../service-bus-messaging/service-bus-messaging-overview.md)de Service Bus.

## <a name="azure-storage"></a>Storage do Azure

[O Azure Storage](../storage/common/storage-introduction.md) é uma solução de armazenamento em nuvem. Inclui o serviço blob storage que pode utilizar para armazenar dados de objetos não estruturados. Alguns tutoriais do IoT Hub usam armazenamento de bolhas.

## <a name="back-end-app"></a>App back-end

No contexto do [IoT Hub,](#iot-hub)uma aplicação back-end é uma aplicação que se conecta a um dos pontos finais voltados para o serviço num hub IoT. Por exemplo, uma aplicação de back-end pode recuperar mensagens [de dispositivo para nuvem](#device-to-cloud) ou gerir o registo de [identidade](#identity-registry). Normalmente, uma aplicação back-end corre na nuvem, mas em muitos dos tutoriais as aplicações de back-end são aplicações de consola que estão a ser executados na sua máquina de desenvolvimento local.

## <a name="built-in-endpoints"></a>Pontos finais embutidos

Cada hub IoT inclui um [ponto final](iot-hub-devguide-endpoints.md) incorporado que é compatível com o Event Hub. Pode utilizar qualquer mecanismo que funcione com os Centros de Eventos para ler mensagens dispositivo-a-nuvem a partir deste ponto final.

## <a name="cloud-gateway"></a>Gateway de nuvens

Um gateway de nuvem permite a conectividade para dispositivos que não podem ligar-se diretamente ao [IoT Hub](#iot-hub). Um gateway de nuvens está hospedado na nuvem em contraste com um [gateway de campo](#field-gateway) que corre localmente para os seus dispositivos. Um caso de utilização típico de um gateway em nuvem é implementar a tradução de protocolos para os seus dispositivos.

## <a name="cloud-to-device"></a>Nuvem-para-dispositivo

Refere-se a mensagens enviadas de um hub IoT para um dispositivo conectado. Muitas vezes, estas mensagens são comandos que instruem o dispositivo a tomar uma ação. Para mais informações, consulte [Enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Configuração

No contexto da [configuração automática](iot-hub-auto-device-config.md)do dispositivo, uma configuração dentro do IoT Hub define a configuração desejada para um conjunto de dispositivos gémeos e fornece um conjunto de métricas para reportar o estado e o progresso.

## <a name="connection-string"></a>Cadeia de ligação

Utiliza cadeias de ligação no código da aplicação para encapsular as informações necessárias para se ligar a um ponto final. Uma cadeia de ligação normalmente inclui o endereço do ponto final e informações de segurança, mas os formatos de cadeia de ligação variam entre os serviços. Existem dois tipos de cadeia de ligação associadas ao serviço IoT Hub:

- *As cordas de ligação* do dispositivo permitem que os dispositivos se conectem aos pontos finais virados para o dispositivo num hub IoT.

- *As cadeias de ligação IoT Hub* permitem que as aplicações de back-end se conectem aos pontos finais voltados para o serviço num hub IoT.

## <a name="custom-endpoints"></a>Pontos finais personalizados

Pode criar [pontos finais personalizados](iot-hub-devguide-endpoints.md) num hub IoT para entregar mensagens despachadas por uma [regra de encaminhamento](#routing-rules). Os pontos finais personalizados ligam-se diretamente a um hub de Eventos, a uma fila de autocarros de serviço ou a um tópico de Service Bus.

## <a name="custom-gateway"></a>Porta de entrada personalizada

Um gateway permite a conectividade para dispositivos que não podem ligar-se diretamente ao [IoT Hub](#iot-hub). Você pode usar Azure IoT Edge para construir gateways personalizados que implementam lógica personalizada para lidar com mensagens, conversões de protocolo personalizado e outros processamentos no limite.

## <a name="data-point-message"></a>Mensagem de ponto de dados

Uma mensagem de ponto de dados é uma mensagem [de dispositivo para nuvem](#device-to-cloud) que contém dados de [telemetria,](#telemetry) como velocidade do vento ou temperatura.

## <a name="desired-configuration"></a>Configuração desejada

No contexto de um [dispositivo gémeo,](iot-hub-devguide-device-twins.md)a configuração desejada refere-se ao conjunto completo de propriedades e metadados no dispositivo twin que devem ser sincronizados com o dispositivo.

## <a name="desired-properties"></a>Propriedades desejadas

No contexto de um [dispositivo gémeo,](iot-hub-devguide-device-twins.md)as propriedades desejadas são uma subsecção do dispositivo twin que é usada com [propriedades relatadas](#reported-properties) para sincronizar a configuração ou condição do dispositivo. As propriedades desejadas só podem ser definidas por uma [aplicação de back-end](#back-end-app) e são observadas pela aplicação do [dispositivo.](#device-app)

## <a name="device-to-cloud"></a>Dispositivo-a-nuvem

Refere-se a mensagens enviadas de um dispositivo ligado ao [IoT Hub](#iot-hub). Estas mensagens podem ser [ponto de dados](#data-point-message) ou mensagens [interativas.](#interactive-message) Para mais informações, consulte [Enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Dispositivo

No contexto do IoT, um dispositivo é tipicamente um dispositivo de computação autónomo e de pequena escala que pode recolher dados ou controlar outros dispositivos. Por exemplo, um dispositivo pode ser um dispositivo de monitorização ambiental, ou um controlador para os sistemas de rega e ventilação numa estufa. O catálogo de [dispositivos](https://catalog.azureiotsolutions.com/) fornece uma lista de dispositivos de hardware certificados para trabalhar com [o IoT Hub](#iot-hub).

## <a name="device-app"></a>Aplicação do dispositivo

Uma aplicação de dispositivo funciona no seu [dispositivo](#device) e trata da comunicação com o seu [hub IoT](#iot-hub). Normalmente, utiliza-se um dos [SDKs do dispositivo Azure IoT](#azure-iot-device-sdks) quando implementa uma aplicação para dispositivos. Em muitos dos tutoriais de IoT, você usa um [dispositivo simulado](#simulated-device) para conveniência.

## <a name="device-condition"></a>Condição do dispositivo

Consulte as informações do estado do dispositivo, como o método de conectividade atualmente em uso, conforme reportado por uma [aplicação do dispositivo](#device-app). [As aplicações do dispositivo](#device-app) também podem reportar as suas capacidades. Pode consultar informações sobre a condição e a capacidade utilizando gémeos do dispositivo.

## <a name="device-data"></a>Dados do dispositivo

Os dados do dispositivo referem-se aos dados por dispositivo armazenados no [registo de identidade](#identity-registry)do IoT Hub . É possível importar e exportar estes dados.

## <a name="device-explorer"></a>Explorador de dispositivos

O explorador do dispositivo foi substituído pelo [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer), que é utilizado para visualizar a telemetria que o dispositivo está a enviar, trabalhar com as propriedades do dispositivo e chamar comandos. Também pode utilizar o explorador para interagir e testar os seus dispositivos, e para gerir dispositivos de tomada e reprodução.

## <a name="device-identity"></a>Identidade do dispositivo

A identidade do dispositivo é o identificador único atribuído a todos os dispositivos registados no [registo de identidade.](#identity-registry)

## <a name="device-management"></a>Gestão de dispositivos

A gestão do dispositivo engloba todo o ciclo de vida associado à gestão dos dispositivos na sua solução IoT, incluindo planeamento, provisionamento, configuração, monitorização e aposentação.

## <a name="device-management-patterns"></a>Padrões da gestão de dispositivos

[O hub IoT](#iot-hub) permite padrões comuns de gestão de dispositivos, incluindo reiniciar, executar resets de fábrica e realizar atualizações de firmware nos seus dispositivos.

## <a name="device-rest-api"></a>API de repouso do dispositivo

Pode utilizar a [API do Device REST](https://docs.microsoft.com/rest/api/iothub/device) a partir de um dispositivo para enviar mensagens dispositivo-a-nuvem para um hub IoT e receber mensagens [nuvem-dispositivo](#cloud-to-device) a partir de um hub IoT. Normalmente, deve utilizar um dos [SDKs](#azure-iot-device-sdks) de dispositivo de nível superior, como mostrado nos tutoriais do IoT Hub.

## <a name="device-provisioning"></a>Provisão de dispositivos

O fornecimento de dispositivos é o processo de adição dos [dados iniciais](#device-data) do dispositivo às lojas na sua solução. Para permitir a ligação de um novo dispositivo ao seu hub, deve adicionar um ID do dispositivo e chaves ao [registo de identidade](#identity-registry)do IoT Hub . Como parte do processo de provisionamento, poderá ser necessário inicializar dados específicos do dispositivo noutras lojas de soluções.

## <a name="device-twin"></a>Dispositivo duplo

Um [dispositivo gémeo](iot-hub-devguide-device-twins.md) é o documento JSON que armazena informações do estado do dispositivo, tais como metadados, configurações e condições. [O IoT Hub](#iot-hub) persiste num dispositivo gémeo para cada dispositivo que fornece no seu hub IoT. Os gémeos do dispositivo permitem sincronizar [as condições](#device-condition) e configurações do dispositivo entre o dispositivo e a parte traseira da solução. Pode consultar os gémeos do dispositivo para localizar dispositivos específicos e consultar o estado das operações de longa duração.

## <a name="direct-method"></a>Método direto

Um [método direto](iot-hub-devguide-direct-methods.md) é uma forma de desencadear um método para executar num dispositivo invocando uma API no seu hub IoT.

## <a name="endpoint"></a>Ponto final

Um hub IoT expõe vários [pontos finais](iot-hub-devguide-endpoints.md) que permitem que as suas aplicações se conectem ao hub IoT. Existem pontos finais virados para dispositivos que permitem aos dispositivos realizar operações como o envio de mensagens [de dispositivo para nuvem](#device-to-cloud) e receber mensagens [cloud-to-device.](#cloud-to-device) Existem pontos finais de gestão virados para o serviço que permitem que [as aplicações de back-end](#back-end-app) realizem operações como a gestão [da identidade](#device-identity) do dispositivo e a gestão de twins de dispositivos. Existem [pontos finais incorporados voltados](#built-in-endpoints) para o serviço para a leitura de mensagens dispositivo-a-nuvem. Pode criar [pontos finais personalizados](#custom-endpoints) para receber mensagens dispositivo-a-nuvem despachadas por uma [regra de encaminhamento](#routing-rules).

## <a name="event-hubs-service"></a>Serviço de Centros de Eventos

[O Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) é um serviço de entrada de dados altamente escalável que pode ingerir milhões de eventos por segundo. O serviço permite-lhe processar e analisar as quantidades massivas de dados produzidos pelos seus dispositivos e aplicações conectados. Para uma comparação com o serviço IoT Hub, consulte [a Comparação do Azure IoT Hub e dos Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Ponto final compatível com o Centro de Eventos

Para ler mensagens de dispositivo para nuvem enviadas para o seu hub IoT, pode [ligar-se](#device-to-cloud) a um ponto final no seu hub e utilizar qualquer método compatível com o Event Hub para ler essas mensagens. Os métodos compatíveis com o Centro de Eventos incluem a utilização dos [SDKs do Event Hubs](../event-hubs/event-hubs-programming-guide.md) e [do Azure Stream Analytics.](../stream-analytics/stream-analytics-introduction.md)

## <a name="field-gateway"></a>Porta de entrada de campo

Um gateway de campo permite a conectividade para dispositivos que não podem ligar-se diretamente ao [IoT Hub](#iot-hub) e é normalmente implantado localmente com os seus dispositivos. Para mais informações, veja [o que é Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Conta gratuita

Você pode criar uma [conta Azure gratuita](https://azure.microsoft.com/pricing/free-trial/) para completar os tutoriais IoT Hub e experimentar com o serviço IoT Hub (e outros serviços Azure).

## <a name="gateway"></a>Gateway

Um gateway permite a conectividade para dispositivos que não podem ligar-se diretamente ao [IoT Hub](#iot-hub). Consulte também [o Field Gateway,](#field-gateway) [Cloud Gateway](#cloud-gateway)e Custom [Gateway](#custom-gateway).

## <a name="identity-registry"></a>Registo de identidade

O [registo de identidade](iot-hub-devguide-identity-registry.md) é o componente incorporado de um hub IoT que armazena informações sobre os dispositivos individuais autorizados a ligar-se a um hub IoT.

## <a name="interactive-message"></a>Mensagem interativa

Uma mensagem interativa é uma mensagem [nuvem-para-dispositivo](#cloud-to-device) que desencadeia uma ação imediata na parte traseira da solução. Por exemplo, um dispositivo pode enviar um alarme sobre uma falha que deve ser automaticamente iniciada num sistema CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub

O IoT Hub é um serviço Azure totalmente gerido que permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. Para mais informações, veja [o que é Azure IoT Hub?](about-iot-hub.md) Utilizando a sua [subscrição Azure,](#subscription)pode criar hubs IoT para lidar com as suas cargas de trabalho de mensagens IoT.

## <a name="iot-hub-metrics"></a>Métricas IoT Hub

[As métricas IoT Hub](iot-hub-metrics.md) dão-lhe dados sobre o estado dos hubs IoT na sua [subscrição Azure](#subscription). As métricas IoT Hub permitem avaliar a saúde geral do serviço e os dispositivos que lhe estão ligados. As métricas do IoT Hub podem ajudá-lo a ver o que se passa com o seu hub IoT e investigar problemas de causa de raiz sem precisar de contactar o suporte do Azure.

## <a name="iot-hub-query-language"></a>IoT Hub linguagem de consulta

A [linguagem de consulta IoT Hub](iot-hub-devguide-query-language.md) é uma linguagem semelhante ao SQL que lhe permite consultar os seus gémeos [Job](#job) e device.

## <a name="iot-hub-resource-rest-api"></a>IoT Hub Resource REST API

Pode utilizar a [API IoT Hub Resource REST](https://docs.microsoft.com/rest/api/iothub/iothubresource) para gerir os hubs IoT na sua [subscrição Azure,](#subscription) realizando operações como criar, atualizar e eliminar centros.

## <a name="iot-solution-accelerators"></a>Aceleradores de soluções IoT

Aceleradores de solução Azure IoT embalam vários serviços Azure em soluções. Estas soluções permitem-lhe começar rapidamente com implementações de ponta a ponta de cenários comuns de IoT. Para mais informações, veja quais são os aceleradores de [solução Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>A extensão IoT para Azure CLI 

[A extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension) é uma ferramenta de linha de comando transversal. A ferramenta permite-lhe gerir os seus dispositivos no [registo de identidade,](#identity-registry)enviar e receber mensagens e ficheiros dos seus dispositivos e monitorizar as operações do seu hub IoT.

## <a name="job"></a>Tarefa

A sua solução back-end pode usar [empregos](iot-hub-devguide-jobs.md) para agendar e rastrear atividades num conjunto de dispositivos registados no seu hub IoT. As atividades incluem a atualização das [propriedades duplas desejadas](#desired-properties)do dispositivo, a atualização de [etiquetas gémeas](#tags)do dispositivo e a invocação de [métodos diretos.](#direct-method) [O IoT Hub](#iot-hub) também utiliza para [importar e exportar](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) do registo de [identidade.](#identity-registry)

## <a name="modules"></a>Módulos

Do lado do dispositivo, os SDKs do dispositivo IoT Hub permitem criar [módulos](iot-hub-devguide-module-twins.md) onde cada um abre uma ligação independente ao IoT Hub. Esta funcionalidade permite-lhe utilizar espaços de nome separados para diferentes componentes do seu dispositivo.

A identidade do módulo e o módulo gémeo fornecem as mesmas capacidades que a identidade do [dispositivo](#device-identity) e [o dispositivo gémeo,](#device-twin) mas com uma granularidade mais fina. Esta granularidade mais fina permite que dispositivos capazes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware que gerem vários componentes, isolem a configuração e as condições para cada um desses componentes.

## <a name="module-identity"></a>Identidade do módulo

A identidade do módulo é o identificador único atribuído a cada módulo que pertence a um dispositivo. A identidade do módulo também está registada no [registo de identidade.](#identity-registry)

## <a name="module-twin"></a>Módulo gémeo

Semelhante ao dispositivo twin, um módulo twin é o documento JSON que armazena informações do estado do módulo, tais como metadados, configurações e condições. O IoT Hub persiste num módulo gémeo para cada identidade de módulo que fornece sob uma identidade do dispositivo no seu hub IoT. Os gémeos módulos permitem sincronizar as condições e configurações do módulo entre o módulo e a parte traseira da solução. Pode consultar os gémeos do módulo para localizar módulos específicos e consultar o estado das operações de longa duração.

## <a name="mqtt"></a>MQTT

[O MQTT](https://mqtt.org/) é um dos protocolos de mensagens que o [IoT Hub](#iot-hub) suporta para comunicar com dispositivos. Para obter mais informações sobre os protocolos de mensagens que o IoT Hub suporta, consulte [Enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorização de operações

A [monitorização das operações](iot-hub-operations-monitoring.md) do IoT Hub permite-lhe monitorizar o estado das operações no seu hub IoT em tempo real. [O IoT Hub](#iot-hub) acompanha eventos em várias categorias de operações. Pode optar pelo envio de eventos de uma ou mais categorias para um ponto final do IoT Hub para processamento. Pode monitorizar os dados por erros ou configurar um processamento mais complexo com base em padrões de dados.

## <a name="physical-device"></a>Dispositivo físico

Um dispositivo físico é um dispositivo real, como um Raspberry Pi que se conecta a um hub IoT. Por conveniência, muitos dos tutoriais do IoT Hub usam [dispositivos simulados](#simulated-device) para que possa executar amostras na sua máquina local.

## <a name="primary-and-secondary-keys"></a>Chaves primárias e secundárias

Quando se liga a um ponto final virado para o dispositivo ou virado para o serviço num hub IoT, a sua cadeia de [ligação](#connection-string) inclui a chave para lhe dar acesso. Quando adiciona um dispositivo ao [registo de identidade](#identity-registry) ou adiciona uma política de acesso [partilhado](#shared-access-policy) ao seu hub, o serviço gera uma chave primária e secundária. Ter duas teclas permite-lhe rolar de uma chave para outra quando atualiza uma chave sem perder o acesso ao hub IoT.

## <a name="protocol-gateway"></a>Gateway de protocolo

Um portal de protocolo é normalmente implantado na nuvem e fornece serviços de tradução protocolar para dispositivos que se ligam ao [IoT Hub](#iot-hub). Para mais informações, veja [o que é Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Quotas e limitação

Existem várias [quotas](iot-hub-devguide-quotas-throttling.md) que se aplicam à sua utilização do [IoT Hub,](#iot-hub)muitas das quotas variam em função do nível do hub IoT. [O IoT Hub](#iot-hub) também aplica [aceleradores](iot-hub-devguide-quotas-throttling.md) à utilização do serviço em tempo de execução.

## <a name="reported-configuration"></a>Configuração reportada

No contexto de um [dispositivo gémeo,](iot-hub-devguide-device-twins.md)a configuração reportada refere-se ao conjunto completo de propriedades e metadados no twin do dispositivo que devem ser reportados à extremidade traseira da solução.

## <a name="reported-properties"></a>Propriedades reportadas

No contexto de um [dispositivo gémeo,](iot-hub-devguide-device-twins.md)as propriedades relatadas são uma subsecção do dispositivo twin utilizado com [propriedades desejadas](#desired-properties) para sincronizar a configuração ou condição do dispositivo. As propriedades reportadas só podem ser definidas pela [aplicação](#device-app) do dispositivo e podem ser lidas e consultadas por uma [aplicação de back-end](#back-end-app).

## <a name="resource-group"></a>Grupo de recursos

[O Azure Resource Manager](#azure-resource-manager) utiliza grupos de recursos para agrupar recursos relacionados. Pode utilizar um grupo de recursos para realizar operações em todos os recursos do grupo simultaneamente.

## <a name="retry-policy"></a>Política de Repetição

Utilize uma política de repetição para lidar com [erros transitórios](/azure/architecture/best-practices/transient-faults) quando se liga a um serviço de nuvem.

## <a name="routing-rules"></a>Regras de encaminhamento

Configura [as regras de encaminhamento](iot-hub-devguide-messages-read-custom.md) no seu hub IoT para encaminhar mensagens dispositivo-a-nuvem para um [ponto final incorporado](#built-in-endpoints) ou para [pontos finais personalizados](#custom-endpoints) para processamento através da sua solução back end end end.

## <a name="sasl-plain"></a>PLANÍCIE SASL

SASL PLAIN é um protocolo que o protocolo AMQP usa para transferir fichas de segurança.

## <a name="service-rest-api"></a>API REST do serviço

Pode utilizar a [API de assistência](https://docs.microsoft.com/rest/api/iothub/service/configuration) rest a partir da parte traseira da solução para gerir os seus dispositivos. A API permite-lhe recuperar e atualizar propriedades [gémeas](#device-twin) do dispositivo, invocar [métodos diretos](#direct-method)e agendar [trabalhos.](#job) Normalmente, deve utilizar um dos [SDKs](#azure-iot-service-sdks) de serviço de alto nível, como mostrado nos tutoriais do IoT Hub.

## <a name="shared-access-signature"></a>Assinatura de acesso partilhado

As Assinaturas de Acesso Partilhado (SAS) são um mecanismo de autenticação baseado em hashes ou URIs seguros SHA-256. A autenticação SAS tem dois componentes: uma _Política de Acesso Partilhado_ e uma Assinatura de Acesso _Partilhado_ (muitas vezes chamada de símbolo). Um dispositivo utiliza SAS para autenticar com um hub IoT. [As aplicações back-end](#back-end-app) também usam SAS para autenticar com os pontos finais voltados para o serviço num hub IoT. Normalmente, você inclui o token SAS na [cadeia de conexão](#connection-string) que uma aplicação usa para estabelecer uma ligação a um hub IoT.

## <a name="shared-access-policy"></a>Política de acesso compartilhado

Uma política de acesso partilhado define as permissões concedidas a qualquer pessoa que tenha uma [chave primária ou secundária](#primary-and-secondary-keys) válida associada a essa política. Pode gerir as políticas de acesso partilhado e as chaves do seu hub no [portal](#azure-portal).

## <a name="simulated-device"></a>Dispositivo simulado

Por conveniência, muitos dos tutoriais do IoT Hub usam dispositivos simulados para que possa executar amostras na sua máquina local. Em contraste, um [dispositivo físico](#physical-device) é um dispositivo real, como um Raspberry Pi que se conecta a um hub IoT.

## <a name="solution"></a>Solução
Uma _solução_ pode referir-se a uma solução visual Studio que inclui um ou mais projetos. Uma _solução_ também pode referir-se a uma solução IoT que inclui elementos como dispositivos, [aplicações para dispositivos,](#device-app)um hub IoT, outros serviços Azure e [aplicações de back-end.](#back-end-app)

## <a name="subscription"></a>Subscrição

Uma subscrição do Azure é onde a faturação ocorre. Cada recurso Azure que cria ou o serviço Azure que utiliza está associado a uma única subscrição. Muitas quotas também se aplicam ao nível de uma subscrição.

## <a name="system-properties"></a>Propriedades do sistema

No contexto de um [dispositivo gémeo,](iot-hub-devguide-device-twins.md)as propriedades do sistema são apenas de leitura e incluem informações sobre o uso do dispositivo, como o último tempo de atividade e estado de ligação.

## <a name="tags"></a>Etiquetas

No contexto de um [dispositivo gémeo,](iot-hub-devguide-device-twins.md)as etiquetas são metadados do dispositivo armazenados e recuperados pela solução traseira sob a forma de um documento JSON. As etiquetas não são visíveis para aplicações num dispositivo.

## <a name="telemetry"></a>Telemetria

Os dispositivos recolhem dados de telemetria, como velocidade do vento ou temperatura, e utilizam mensagens de ponto de dados para enviar a telemetria para um hub IoT.

## <a name="token-service"></a>Serviço Token

Pode utilizar um serviço de fichas para implementar um mecanismo de autenticação para os seus dispositivos. Utiliza uma [política de acesso compartilhado](#shared-access-policy) IoT Hub com permissões **DeviceConnect** para criar fichas com âmbito de *dispositivo.* Estes tokens permitem que um dispositivo se conecte ao seu hub IoT. Um dispositivo utiliza um mecanismo de autenticação personalizado para autenticar com o serviço token. Se o dispositivo autenticar com sucesso, o serviço token emite um token SAS para o dispositivo utilizar para aceder ao seu hub IoT.

## <a name="twin-queries"></a>Consultas gémeas

[As consultas de módulo e módulo usam](iot-hub-devguide-query-language.md) a linguagem de consulta IoT Hub semelhante ao SQL para obter informações dos gémeos do seu dispositivo ou gémeos módulos. Você pode usar o mesmo idioma de consulta IoT Hub para obter informações sobre um [Trabalho](#job) em execução no seu hub IoT.

## <a name="twin-synchronization"></a>Sincronização gémea

A sincronização gémea utiliza as [propriedades desejadas](#desired-properties) no seu dispositivo gémeos ou gémeos módulos para configurar os seus dispositivos ou módulos e recuperar [propriedades relatadas](#reported-properties) dos mesmos para armazenar no gémeo.

## <a name="x509-client-certificate"></a>Certificado de cliente X.509

Um dispositivo pode utilizar um certificado X.509 para autenticar com [o IoT Hub](#iot-hub). A utilização de um certificado X.509 é uma alternativa à utilização de um [token SAS](#shared-access-signature).

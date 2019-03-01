---
title: Compreender o registo de identidade do IoT Hub do Azure | Documentos da Microsoft
description: Guia do programador - descrição do registo de identidade do IoT Hub e como usá-lo para gerir os seus dispositivos. Inclui informações sobre a importação e exportação de identidades de dispositivos em massa.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.openlocfilehash: 935635c474190413545d1a2731c367a691bfa56d
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010265"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Compreender o registo de identidade do IoT hub

Cada hub IoT tem um registo de identidades que armazena informações sobre os dispositivos e os módulos autorizados a ligar ao IoT hub. Antes de um dispositivo ou o módulo pode ligar a um hub IoT, tem de existir uma entrada para esse dispositivo ou o módulo no registo de identidade do hub IoT. Um dispositivo ou o módulo também tem de autenticar com o hub IoT com base nas credenciais armazenadas no registo de identidade.

O ID de dispositivo ou módulo armazenado no registo de identidade diferencia maiúsculas de minúsculas.

Num alto nível, o registo de identidade é uma coleção com capacidade de REST de recursos de identidade de dispositivo ou o módulo. Quando adiciona uma entrada no registo de identidade, o IoT Hub cria um conjunto de recursos por dispositivo, como a fila que contém as mensagens em trânsito do cloud-para-dispositivo.

Utilize o registo de identidade quando precisar de:

* Aprovisionar dispositivos ou módulos que se ligam ao seu hub IoT.
* Controlar o acesso por-/ por-módulo dispositivo para dispositivo ou pontos finais de módulo destinado ao seu hub.

> [!NOTE]
> * Registo de identidade não contém quaisquer metadados específicos do aplicativo.
> * Módulo duplo de identidade e o módulo está em pré-visualização pública. Abaixo funcionalidade serão suportados na identidade do módulo quando ele é geral disponíveis.
>

## <a name="identity-registry-operations"></a>Operações de registo de identidade

O registo de identidade do IoT Hub expõe as seguintes operações:

* Criar a identidade de dispositivo ou um módulo
* Atualizar a identidade de dispositivo ou um módulo
* Obter a identidade de dispositivo ou módulo por ID
* Eliminar a identidade de dispositivo ou um módulo
* Lista de até 1000 identidades
* Identidades de dispositivos de exportação para o armazenamento de Blobs do Azure
* Importar identidades de dispositivos de armazenamento de Blobs do Azure

Todas essas operações podem usar a simultaneidade otimista, conforme especificado na [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> A única forma de recuperar todas as identidades no registo de identidade de um hub IoT está a utilizar o [exportar](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) funcionalidade.

Um registo de identidades do IoT Hub:

* Não contém quaisquer metadados de aplicação.
* Pode ser acedido como um dicionário, utilizando o **deviceId** ou **moduleId** como a chave.
* Não suporta consultas expressivas.

Uma solução de IoT normalmente possui um armazenamento específico da solução separado, que contém metadados específicos do aplicativo. Por exemplo, o arquivo de solução específica numa solução de criação inteligente seria gravar o ambiente no qual um sensor de temperatura é implementado.

> [!IMPORTANT]
> Utilize apenas o registo de identidade para gestão de dispositivos e o aprovisionamento de operações. Operações de alto débito em tempo de execução não devem depender realizar operações no registo de identidade. Por exemplo, a verificação do Estado de ligação de um dispositivo antes de enviar um comando não é um padrão suportado. Certifique-se de verificar a [taxas de limitação](iot-hub-devguide-quotas-throttling.md) para o registo de identidade e o [heartbeat de dispositivo](iot-hub-devguide-identity-registry.md#device-heartbeat) padrão.

## <a name="disable-devices"></a>Desativar dispositivos

Pode desativar dispositivos através da atualização do **estado** propriedade de uma identidade no registo de identidade. Normalmente, utiliza esta propriedade em dois cenários:

* Durante um processo de orquestração de aprovisionamento. Para obter mais informações, consulte [aprovisionamento de dispositivos](iot-hub-devguide-identity-registry.md#device-provisioning).

* Se, por qualquer motivo, acha que um dispositivo estiver comprometido ou se tornou não autorizado.

Esta funcionalidade não está disponível para os módulos.

## <a name="import-and-export-device-identities"></a>Importação e exportação de identidades do dispositivo

Usar operações assíncronas no [ponto final de fornecedor de recursos do IoT Hub](iot-hub-devguide-endpoints.md) para exportar as identidades de dispositivos em massa de registo de identidade de um hub IoT. Exportações são tarefas de longa execução que utilizem um contentor de BLOBs fornecido pelo cliente para guardar os dados de identidade de dispositivo de leitura do registo de identidades.

Usar operações assíncronas no [ponto final de fornecedor de recursos do IoT Hub](iot-hub-devguide-endpoints.md) para importar em massa de identidades do dispositivo ao registo de identidade de um hub IoT. Importações são tarefas de longa execução que utilizem dados num contentor do blob fornecido pelo cliente para escrever dados de identidade de dispositivo no registo de identidade.

Para obter mais informações sobre a importação e exportação APIs, veja [fornecedor de recursos do IoT Hub REST APIs](/rest/api/iothub/iothubresource). Para saber mais sobre a execução de importar e exportar tarefas, consulte [gestão de identidades de dispositivo do IoT Hub em massa](iot-hub-bulk-identity-mgmt.md).

## <a name="device-provisioning"></a>Aprovisionamento de dispositivos

Os dados do dispositivo que armazena uma determinada solução de IoT depende de requisitos específicos da solução. No entanto, como mínimo, uma solução tem de armazenar chaves de autenticação e de identidades do dispositivo. O IoT Hub do Azure inclui um registo de identidade que pode armazenar valores para cada dispositivo, como IDs, chaves de autenticação e códigos de estado. Uma solução pode utilizar outros serviços do Azure como armazenamento de tabelas, armazenamento de BLOBs ou do Cosmos DB para armazenar quaisquer dados adicionais de dispositivos.

*Aprovisionamento de dispositivos* é o processo de adicionar os dados do dispositivo inicial para as lojas na sua solução. Para ativar um novo dispositivo ligar ao seu hub, tem de adicionar um ID de dispositivo e as chaves para o registo de identidade do IoT Hub. Como parte do processo de aprovisionamento, poderá ter de inicializar dados específicos do dispositivo em outros armazenamentos de solução. Também pode utilizar o serviço de aprovisionamento de dispositivos do Azure IoT Hub para ativar o zero touch, just-in-time aprovisionamento um ou mais hubs IoT sem que seja necessária intervenção humana. Para obter mais informações, consulte a [documentação do serviço de aprovisionamento](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Heartbeat do dispositivo

O registo de identidade do IoT Hub contém um campo chamado **connectionState**. Utilize apenas a **connectionState** campo durante o desenvolvimento e depuração. Soluções de IoT não devem consultar o campo no tempo de execução. Por exemplo, não consultar o **connectionState** campo para verificar se um dispositivo está ligado antes de enviar uma mensagem de cloud para o dispositivo ou um SMS. Recomendamos a subscrever o [ **dispositivo desligado** eventos](iot-hub-event-grid.md#event-types) no Event Grid para receber alertas e monitorizar o estado de ligação do dispositivo. Utilize esta opção [tutorial](iot-hub-how-to-order-connection-state-events.md) para saber como integrar eventos de dispositivo ligado e desligado de dispositivo do IoT Hub na sua solução de IoT.

Se a sua solução de IoT precisa de saber se um dispositivo estiver ligado, pode implementar o *padrão de heartbeat*.
O padrão de heartbeat, o dispositivo envia mensagens do dispositivo para a cloud, pelo menos, uma vez cada valor fixo de tempo (por exemplo, pelo menos uma vez a cada hora). Por conseguinte, mesmo se um dispositivo não tem quaisquer dados para enviar, ainda envia uma mensagem de dispositivo-para-cloud vazia (normalmente com uma propriedade que o identifica como um heartbeat). No lado do serviço, a solução mantém um mapa com o último heartbeat recebido para cada dispositivo. Se a solução não receber uma mensagem heartbeat dentro do tempo esperado do dispositivo, parte do princípio de que existe um problema com o dispositivo.

Uma implementação mais complexa pode incluir as informações a partir [do Azure Monitor](../azure-monitor/index.yml) e [do Azure Resource Health](../service-health/resource-health-overview.md) para identificar dispositivos que estão a tentar estabelecer ligação ou comunicar, mas a falhar, verifique [Monitorizar com diagnósticos](iot-hub-monitor-resource-health.md) guia. Quando implementa o padrão de heartbeat, certifique-se de verificar [IoT Hub Quotas e limitações](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Se uma solução de IoT utiliza o estado da ligação unicamente para determinar se deve enviar mensagens da cloud para dispositivo e as mensagens não são difundir para grandes conjuntos de dispositivos, considere utilizar a mais simples *curto de tempo de expiração* padrão. Este padrão alcança o mesmo resultado que mantenha um registo de estado de ligação do dispositivo usando o padrão de heartbeat, ao mesmo tempo a ser mais eficiente. Se solicitar as confirmações de mensagem, o IoT Hub pode notificá-lo sobre os dispositivos que são capazes de receber mensagens e que não são.

## <a name="device-and-module-lifecycle-notifications"></a>Notificações de ciclo de vida do dispositivo e o módulo

IoT Hub pode notificar a sua solução de IoT quando uma identidade é criada ou eliminada através do envio de notificações de ciclo de vida. Para fazer isso, sua solução de IoT precisa de criar uma rota e definir a origem de dados igual ao *DeviceLifecycleEvents* ou *ModuleLifecycleEvents*. Por predefinição, não existem notificações de ciclo de vida são enviadas, ou seja, nenhuma dessas rotas existem previamente. A mensagem de notificação inclui as propriedades e o corpo.

Propriedades: Propriedades do sistema de mensagens têm o prefixo a `$` símbolo.

Mensagem de notificação para o dispositivo:

| Name | Value |
| --- | --- |
|$content-type | application/json |
|$iothub-enqueuedtime |  Tempo quando a notificação foi enviada |
|$iothub-message-source | deviceLifecycleEvents |
|$content-encoding | utf-8 |
|opType | **createDeviceIdentity** ou **deleteDeviceIdentity** |
|hubName | Nome do IoT Hub |
|deviceId | ID do dispositivo |
|operationTimestamp | Timestamp de ISO8601 da operação |
|iothub-message-schema | deviceLifecycleNotification |

Corpo: Esta secção está no formato JSON e representa o duplo da identidade de dispositivo criado. Por exemplo,

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Mensagem de notificação para o módulo:

| Name | Value |
| --- | --- |
$content-type | application/json |
$iothub-enqueuedtime |  Tempo quando a notificação foi enviada |
$iothub-message-source | moduleLifecycleEvents |
$content-encoding | utf-8 |
opType | **createModuleIdentity** ou **deleteModuleIdentity** |
hubName | Nome do IoT Hub |
moduleId | ID do módulo |
operationTimestamp | Timestamp de ISO8601 da operação |
iothub-message-schema | moduleLifecycleNotification |

Corpo: Esta secção está no formato JSON e representa o duplo da identidade do módulo criado. Por exemplo,

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Propriedades de identidade de dispositivo

Identidades do dispositivo são representadas como documentos JSON com as seguintes propriedades:

| Propriedade | Opções | Descrição |
| --- | --- | --- |
| deviceId |atualizações necessárias, só de leitura no |Uma cadeia de maiúsculas e minúsculas (até 128 carateres de comprimento) de carateres de alfanuméricos ASCII de 7 bits mais determinados caracteres especiais: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |necessário, só de leitura |Uma IoT hub-gerado, maiúsculas e minúsculas cadeia de caracteres até 128 carateres de comprimento. Este valor é utilizado para distinguir os dispositivos com o mesmo **deviceId**, quando tiver sido eliminados e recriados. |
| etag |necessário, só de leitura |Uma cadeia de caracteres que representa o num ETag fraco para a identidade de dispositivo, como por [RFC7232](https://tools.ietf.org/html/rfc7232). |
| autenticação |opcional |Um objeto composto que contém os materiais de segurança e informações de autenticação. |
| auth.symkey |opcional |Um objeto composto que contém um site primário e uma chave secundária, armazenada em formato base64. |
| status |obrigatório |Um indicador de acesso. Pode ser **Enabled** ou **desativado**. Se **ativado**, o dispositivo tem permissão para ligar. Se **desativado**, este dispositivo não é possível aceder a qualquer ponto de final voltado para o dispositivo. |
| statusReason |opcional |Uma cadeia de caracteres 128 que armazena o motivo para o estado de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime |só de leitura |Um indicador temporal, que mostra a data e hora da última atualização de estado. |
| connectionState |só de leitura |Um campo que indica o estado da ligação: ambos **ligado** ou **desligado**. Este campo representa a visão do IoT Hub do Estado de ligação do dispositivo. **Importante**: Este campo deve ser usado apenas para fins de desenvolvimento/depuração. O estado de ligação é atualizado apenas para dispositivos que utilizem MQTT ou AMQP. Além disso, baseia-se no nível de protocolo pings (MQTT pings ou pings AMQP) e pode ter um atraso máximo de apenas 5 minutos. Por esses motivos, pode haver falsos positivos, como dispositivos comunicados como ligado mas que está ligado à Internet. |
| connectionStateUpdatedTime |só de leitura |Um indicador temporal, que mostra a data e hora da última o estado de ligação foi atualizado. |
| lastActivityTime |só de leitura |Um indicador temporal, que mostra a data e hora da última o dispositivo ligado, recebida ou enviada uma mensagem. |

> [!NOTE]
> Estado da ligação só pode representar a vista do IoT Hub do Estado da ligação. Atualizações para este estado podem sofrer um atraso, dependendo das condições de rede e configurações.

> [!NOTE]
> O dispositivo SDKs não suportam atualmente utilizar o `+` e `#` carateres no **deviceId**.

## <a name="module-identity-properties"></a>Propriedades de identidade do módulo

Identidades do módulo são representadas como documentos JSON com as seguintes propriedades:

| Propriedade | Opções | Descrição |
| --- | --- | --- |
| deviceId |atualizações necessárias, só de leitura no |Uma cadeia de maiúsculas e minúsculas (até 128 carateres de comprimento) de carateres de alfanuméricos ASCII de 7 bits mais determinados caracteres especiais: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| moduleId |atualizações necessárias, só de leitura no |Uma cadeia de maiúsculas e minúsculas (até 128 carateres de comprimento) de carateres de alfanuméricos ASCII de 7 bits mais determinados caracteres especiais: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |necessário, só de leitura |Uma IoT hub-gerado, maiúsculas e minúsculas cadeia de caracteres até 128 carateres de comprimento. Este valor é utilizado para distinguir os dispositivos com o mesmo **deviceId**, quando tiver sido eliminados e recriados. |
| etag |necessário, só de leitura |Uma cadeia de caracteres que representa o num ETag fraco para a identidade de dispositivo, como por [RFC7232](https://tools.ietf.org/html/rfc7232). |
| autenticação |opcional |Um objeto composto que contém os materiais de segurança e informações de autenticação. |
| auth.symkey |opcional |Um objeto composto que contém um site primário e uma chave secundária, armazenada em formato base64. |
| status |obrigatório |Um indicador de acesso. Pode ser **Enabled** ou **desativado**. Se **ativado**, o dispositivo tem permissão para ligar. Se **desativado**, este dispositivo não é possível aceder a qualquer ponto de final voltado para o dispositivo. |
| statusReason |opcional |Uma cadeia de caracteres 128 que armazena o motivo para o estado de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime |só de leitura |Um indicador temporal, que mostra a data e hora da última atualização de estado. |
| connectionState |só de leitura |Um campo que indica o estado da ligação: ambos **ligado** ou **desligado**. Este campo representa a visão do IoT Hub do Estado de ligação do dispositivo. **Importante**: Este campo deve ser usado apenas para fins de desenvolvimento/depuração. O estado de ligação é atualizado apenas para dispositivos que utilizem MQTT ou AMQP. Além disso, baseia-se no nível de protocolo pings (MQTT pings ou pings AMQP) e pode ter um atraso máximo de apenas 5 minutos. Por esses motivos, pode haver falsos positivos, como dispositivos comunicados como ligado mas que está ligado à Internet. |
| connectionStateUpdatedTime |só de leitura |Um indicador temporal, que mostra a data e hora da última o estado de ligação foi atualizado. |
| lastActivityTime |só de leitura |Um indicador temporal, que mostra a data e hora da última o dispositivo ligado, recebida ou enviada uma mensagem. |

> [!NOTE]
> O dispositivo SDKs não suportam atualmente utilizar o `+` e `#` carateres no **deviceId** e **moduleId**.

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador do IoT Hub incluem:

* [Pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descreve vários pontos de extremidade que cada hub IoT expõe para operações de tempo de execução e gestão.

* [Quotas e limitação](iot-hub-devguide-quotas-throttling.md) descreve as quotas e limitação comportamentos que se aplicam ao serviço IoT Hub.

* [Azure SDKs de dispositivo e de serviços de IoT](iot-hub-devguide-sdks.md) indica o idioma de vários SDKs, pode utilizar ao desenvolver aplicações de dispositivos e de serviços que interagem com o IoT Hub.

* [Linguagem de consulta do IoT Hub](iot-hub-devguide-query-language.md) descreve a linguagem de consulta, pode usar para recuperar informações a partir do IoT Hub sobre os seus dispositivos duplos e trabalhos.

* [Suporte para MQTT do IoT Hub](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como utilizar o registo de identidade do IoT Hub, poderá estar interessado nos seguintes tópicos de guia de programadores de IoT Hub:

* [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md)

* [Utilizar dispositivos duplos para sincronizar o estado e configurações](iot-hub-devguide-device-twins.md)

* [Invocar um método direto num dispositivo](iot-hub-devguide-direct-methods.md)

* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte o tutorial seguinte do IoT Hub:

* [Introdução ao Hub IoT do Azure](quickstart-send-telemetry-dotnet.md)

Para explorar, utilizando o serviço de aprovisionamento de dispositivos do IoT Hub para ativar o aprovisionamento sem toque e just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](https://azure.microsoft.com/documentation/services/iot-dps)

---
title: Compreenda o registo de identidade do Azure IoT Hub ! Microsoft Docs
description: Guia do desenvolvedor - descrição do registo de identidade do IoT Hub e como usá-lo para gerir os seus dispositivos. Inclui informações sobre a importação e exportação de identidades de dispositivos a granel.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 2d9b0d97fa1823314f5109a1c7fc79054806c148
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146931"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Compreenda o registo de identidade no seu hub IoT

Cada hub IoT tem um registo de identidade que armazena informações sobre os dispositivos e módulos autorizados a ligar ao hub IoT. Antes de um dispositivo ou módulo poder ligar-se a um hub IoT, deve haver uma entrada para esse dispositivo ou módulo no registo de identidade do hub IoT. Um dispositivo ou módulo também deve autenticar com o hub IoT com base em credenciais armazenadas no registo de identidade.

O dispositivo ou iD do módulo armazenado no registo de identidade é sensível a casos.

A um nível elevado, o registo de identidade é uma coleção de recursos de identidade de dispositivo ou módulos capazes de REST. Quando adiciona uma entrada no registo de identidade, o IoT Hub cria um conjunto de recursos por dispositivo, tais como a fila que contém mensagens nuvem-a-dispositivo a bordo.

Utilize o registo de identidade quando precisar:

* Dispositivos ou módulos de fornecimento que se ligam ao seu hub IoT.
* Controle o acesso por dispositivo/módulo ao dispositivo do seu hub ou pontos finais virados para o módulo.

> [!NOTE]
> * O registo de identidade não contém nenhum metadados específicos da aplicação.
> * A identidade do módulo e o módulo gémeo estão em pré-visualização pública. Abaixo a funcionalidade será suportada na identidade do módulo quando estiver disponível em geral.
>

## <a name="identity-registry-operations"></a>Operações de registo de identidade

O registo de identidade IoT Hub expõe as seguintes operações:

* Criar identidade de dispositivo ou módulo
* Atualizar identidade de dispositivo ou módulo
* Recuperar identidade do dispositivo ou módulo por ID
* Eliminar identidade de dispositivo ou módulo
* Listar até 1000 identidades
* Identidades do dispositivo de exportação para o armazenamento de bolhas Azure
* Identidades do dispositivo de importação do armazenamento de bolhas Azure

Todas estas operações podem utilizar a concordância otimista, conforme especificado no [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> A única maneira de recuperar todas as identidades no registo de identidade de um hub IoT é usar a funcionalidade [Exportação.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

Um registo de identidade IoT Hub:

* Não contém nenhum metadados de aplicação.
* Pode ser acedido como um dicionário, utilizando o **dispositivoId** ou **móduloId** como chave.
* Não suporta consultas expressivas.

Uma solução IoT normalmente tem uma loja específica de solução separada que contém metadados específicos da aplicação. Por exemplo, a loja específica para a solução numa solução de construção inteligente registaria a sala onde um sensor de temperatura é implantado.

> [!IMPORTANT]
> Utilize apenas o registo de identidade para operações de gestão e provisionamento de dispositivos. As operações de elevado rendimento no tempo de execução não devem depender da realização de operações no registo de identidade. Por exemplo, verificar o estado de ligação de um dispositivo antes de enviar um comando não é um padrão suportado. Certifique-se de verificar [as taxas de aceleração](iot-hub-devguide-quotas-throttling.md) do registo de identidade e o padrão [de batimentocardíaco](iot-hub-devguide-identity-registry.md#device-heartbeat) do dispositivo.

## <a name="disable-devices"></a>Desativar dispositivos

Pode desativar os dispositivos atualizando a propriedade de **estado** de uma identidade no registo de identidade. Normalmente, você usa esta propriedade em dois cenários:

* Durante um processo de orquestração de provisionamento. Para obter mais informações, consulte [o Provisionamento de Dispositivos](iot-hub-devguide-identity-registry.md#device-provisioning).

* Se, por qualquer motivo, achar que um dispositivo está comprometido ou se não tiver sido autorizado.

Esta funcionalidade não está disponível para módulos.

## <a name="import-and-export-device-identities"></a>Identidades dos dispositivos de importação e exportação

Utilize operações assíncronos no ponto final do [fornecedor de recursos IoT Hub](iot-hub-devguide-endpoints.md) para exportar identidades de dispositivos a granel a partir do registo de identidade de um hub IoT. As exportações são empregos de longa duração que utilizam um recipiente de bolhas fornecido pelo cliente para guardar os dados de identidade do dispositivo lidos a partir do registo de identidade.

Utilize operações assíncronos no ponto final do [fornecedor de recursos IoT Hub](iot-hub-devguide-endpoints.md) para importar identidades de dispositivos a granel para o registo de identidade de um hub IoT. As importações são empregos de longa duração que utilizam dados num recipiente de bolhas fornecidos pelo cliente para escrever dados de identidade do dispositivo no registo de identidade.

Para obter mais informações sobre as APIs de importação e exportação, consulte [o fornecedor de recursos IoT Hub REST APIs](/rest/api/iothub/iothubresource). Para saber mais sobre a gestão de postos de trabalho de importação e exportação, consulte [a gestão a granel das identidades dos dispositivos IoT Hub.](iot-hub-bulk-identity-mgmt.md)

As identidades do dispositivo também podem ser exportadas e importadas de um Hub IoT através da API de serviço através da [API REST](/rest/api/iothub/service/jobs/createimportexportjob) ou de um dos [SDKs de serviço IoT](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)Hub .

## <a name="device-provisioning"></a>Provisão de dispositivos

Os dados do dispositivo que uma determinada solução IoT armazena dependem dos requisitos específicos dessa solução. Mas, no mínimo, uma solução deve armazenar identidades do dispositivo e chaves de autenticação. O Azure IoT Hub inclui um registo de identidade que pode armazenar valores para cada dispositivo, tais como IDs, chaves de autenticação e códigos de estado. Uma solução pode usar outros serviços Azure, tais como armazenamento de mesa, armazenamento de bolhas ou Cosmos DB para armazenar quaisquer dados adicionais do dispositivo.

*O fornecimento de dispositivos* é o processo de adição dos dados iniciais do dispositivo às lojas na sua solução. Para permitir que um novo dispositivo se conecte ao seu hub, tem de adicionar um ID do dispositivo e chaves ao registo de identidade do IoT Hub. Como parte do processo de provisionamento, poderá ser necessário inicializar dados específicos do dispositivo noutras lojas de soluções. Também pode utilizar o Serviço de Provisionamento de Dispositivos Azure IoT Hub para permitir o fornecimento de zero toques, just-in-time, para um ou mais hubs IoT sem necessidade de intervenção humana. Para saber mais, consulte a documentação do [serviço de fornecimento.](https://azure.microsoft.com/documentation/services/iot-dps)

## <a name="device-heartbeat"></a>Batimento cardíaco do dispositivo

O registo de identidade IoT Hub contém um campo chamado **connectionState** . Utilize apenas o **campo de ligaçãoEse** durante o desenvolvimento e depuragem. As soluções IoT não devem consultar o campo no tempo de execução. Por exemplo, não consulte o campo **de ligaçãoEve** para verificar se um dispositivo está ligado antes de enviar uma mensagem nuvem-para-dispositivo ou um SMS. Recomendamos a subscrição do [ **evento desligado**](iot-hub-event-grid.md#event-types) do dispositivo na Grelha de Eventos para obter alertas e monitorizar o estado de ligação do dispositivo. Utilize este [tutorial](iot-hub-how-to-order-connection-state-events.md) para aprender a integrar eventos ligados ao dispositivo conectados e desligados do dispositivo a partir do IoT Hub na sua solução IoT.

Se a sua solução IoT precisar de saber se um dispositivo está ligado, pode implementar o *padrão de batimentocardíaco* .
No padrão de batimentos cardíacos, o dispositivo envia mensagens dispositivo-a-nuvem pelo menos uma vez a cada quantidade fixa de tempo (por exemplo, pelo menos uma vez a cada hora). Portanto, mesmo que um dispositivo não tenha quaisquer dados para enviar, ainda envia uma mensagem vazia de dispositivo para nuvem (geralmente com uma propriedade que o identifica como um batimento cardíaco). Do lado do serviço, a solução mantém um mapa com o último batimento cardíaco recebido para cada dispositivo. Se a solução não receber uma mensagem de batimento cardíaco dentro do tempo esperado do dispositivo, assume que existe um problema com o dispositivo.

Uma implementação mais complexa poderia incluir as informações do [Azure Monitor](../azure-monitor/index.yml) e [da Azure Resource Health](../service-health/resource-health-overview.md) para identificar dispositivos que estão a tentar ligar ou comunicar, mas falhando. Para saber mais, consulte [o Monitor IoT Hub](monitor-iot-hub.md) e verifique a [saúde dos recursos do IoT Hub.](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health) Quando implementar o padrão de batimentos cardíacos, certifique-se de verificar [IoT Hub Quotas e Throttles](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Se uma solução IoT utilizar o estado de ligação apenas para determinar se envia mensagens nuvem-para-dispositivo e as mensagens não são transmitidas para grandes conjuntos de dispositivos, considere usar o padrão de tempo de *validade de curto prazo* mais simples. Este padrão obtém o mesmo resultado que manter um registo estatal de ligação do dispositivo usando o padrão de batimentocardíaco, ao mesmo tempo que é mais eficiente. Se solicitar avisos de mensagens, o IoT Hub pode notificá-lo sobre quais os dispositivos que podem receber mensagens e quais não são.

## <a name="device-and-module-lifecycle-notifications"></a>Notificações do ciclo de vida do dispositivo e do módulo

O IoT Hub pode notificar a sua solução IoT quando uma identidade é criada ou eliminada através do envio de notificações de ciclo de vida. Para tal, a sua solução IoT necessita de criar uma rota e de definir a Fonte de Dados igual a *DeviceLifecycleEvents* ou *ModuleLifecycleEvents* . Por padrão, não são enviadas notificações de ciclo de vida, ou seja, não existem tais rotas pré-existentes. A mensagem de notificação inclui propriedades e corpo.

Propriedades: As propriedades do sistema de mensagens estão prefixadas com o `$` símbolo.

Mensagem de notificação para o dispositivo:

| Name | Valor |
| --- | --- |
|$content tipo | application/json |
|$iothub-enquestime |  Hora em que a notificação foi enviada |
|$iothub-mensagem-fonte | dispositivoLifecycleS |
|$content codificação | utf-8 |
|opType | **criarDeviceIdentity** ou **eliminarDeviceDentity** |
|nome hub | Nome do Hub IoT |
|deviceId | ID do dispositivo |
|operaçãoTimestamp | ISO8601 timetamp de funcionamento |
|iothub-mensagem-schema | dispositivoLifecycleNotificação |

Corpo: Esta secção está no formato JSON e representa o gémeo da identidade do dispositivo criado. Por exemplo,

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
Mensagem de notificação para módulo:

| Name | Valor |
| --- | --- |
$content tipo | application/json |
$iothub-enquestime |  Hora em que a notificação foi enviada |
$iothub-mensagem-fonte | móduloLifecycleEvents |
$content codificação | utf-8 |
opType | **criarModuleIdentity** ou **excluirModuleIdentity** |
nome hub | Nome do Hub IoT |
moduleId | ID do módulo |
operaçãoTimestamp | ISO8601 timetamp de funcionamento |
iothub-mensagem-schema | móduloNotificação de Vida |

Corpo: Esta secção está em formato JSON e representa o gémeo da identidade do módulo criado. Por exemplo,

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

## <a name="device-identity-properties"></a>Propriedades de identidade do dispositivo

As identidades do dispositivo são representadas como documentos JSON com as seguintes propriedades:

| Propriedade | Opções | Description |
| --- | --- | --- |
| deviceId |necessário, ler apenas sobre atualizações |Uma corda sensível a casos (até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits, além de certos caracteres especiais: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| geraçãoId |necessário, apenas ler |Uma corda ioT gerada por um hub, sensível a casos até 128 caracteres de comprimento. Este valor é utilizado para distinguir dispositivos com o mesmo **dispositivoId,** quando estes foram eliminados e recriados. |
| etag |necessário, apenas ler |Uma corda que representa um ETag fraco para a identidade do dispositivo, de acordo com [o RFC7232](https://tools.ietf.org/html/rfc7232). |
| auth |opcional |Um objeto composto contendo informações de autenticação e materiais de segurança. |
| auth.symkey |opcional |Um objeto composto contendo uma chave primária e secundária, armazenado no formato base64. |
| status |obrigatório |Um indicador de acesso. Pode ser **ativado** ou **desativado.** Se **ativado,** o dispositivo pode ligar-se. Se **estiver desativado,** este dispositivo não pode aceder a nenhum ponto final virado para o dispositivo. |
| statusReason |opcional |Uma cadeia de 128 caracteres que armazena a razão para o estado de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime |read-only |Um indicador temporal, mostrando a data e a hora da última atualização do estado. |
| ligação Estado |read-only |Um campo indicando o estado da ligação: **ligado** ou **desligado** . Este campo representa a visão do Hub IoT do estado de ligação do dispositivo. **Importante** : Este campo deve ser utilizado apenas para fins de desenvolvimento/depuragem. O estado de ligação é atualizado apenas para dispositivos que utilizem MQTT ou AMQP. Além disso, baseia-se em pings de nível de protocolo (pings MQTT, ou pings AMQP), e pode ter um atraso máximo de apenas 5 minutos. Por estas razões, pode haver falsos positivos, como dispositivos relatados como conectados, mas que estão desligados. |
| conexãoStateUpdatedTime |read-only |Um indicador temporal, mostrando a data e a última vez que o estado de ligação foi atualizado. |
| última Hora de Atividade |read-only |Um indicador temporal, mostrando a data e a última vez que o dispositivo ligou, recebeu ou enviou uma mensagem. Esta propriedade é eventualmente consistente, mas pode ser adiada até 5 a 10 minutos. Por esta razão, não deve ser usado em cenários de produção. |

> [!NOTE]
> O estado de ligação só pode representar a visão do Hub IoT do estado da ligação. As atualizações a este estado podem ser atrasadas, dependendo das condições e configurações da rede.

> [!NOTE]
> Atualmente, os SDKs do dispositivo não suportam a utilização do `+` e dos caracteres no `#` **dispositivoId** .

## <a name="module-identity-properties"></a>Propriedades de identidade do módulo

As identidades dos módulos são representadas como documentos JSON com as seguintes propriedades:

| Propriedade | Opções | Description |
| --- | --- | --- |
| deviceId |necessário, ler apenas sobre atualizações |Uma corda sensível a casos (até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits, além de certos caracteres especiais: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| moduleId |necessário, ler apenas sobre atualizações |Uma corda sensível a casos (até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits, além de certos caracteres especiais: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| geraçãoId |necessário, apenas ler |Uma corda ioT gerada por um hub, sensível a casos até 128 caracteres de comprimento. Este valor é utilizado para distinguir dispositivos com o mesmo **dispositivoId,** quando estes foram eliminados e recriados. |
| etag |necessário, apenas ler |Uma corda que representa um ETag fraco para a identidade do dispositivo, de acordo com [o RFC7232](https://tools.ietf.org/html/rfc7232). |
| auth |opcional |Um objeto composto contendo informações de autenticação e materiais de segurança. |
| auth.symkey |opcional |Um objeto composto contendo uma chave primária e secundária, armazenado no formato base64. |
| status |obrigatório |Um indicador de acesso. Pode ser **ativado** ou **desativado.** Se **ativado,** o dispositivo pode ligar-se. Se **estiver desativado,** este dispositivo não pode aceder a nenhum ponto final virado para o dispositivo. |
| statusReason |opcional |Uma cadeia de 128 caracteres que armazena a razão para o estado de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime |read-only |Um indicador temporal, mostrando a data e a hora da última atualização do estado. |
| ligação Estado |read-only |Um campo indicando o estado da ligação: **ligado** ou **desligado** . Este campo representa a visão do Hub IoT do estado de ligação do dispositivo. **Importante** : Este campo deve ser utilizado apenas para fins de desenvolvimento/depuragem. O estado de ligação é atualizado apenas para dispositivos que utilizem MQTT ou AMQP. Além disso, baseia-se em pings de nível de protocolo (pings MQTT, ou pings AMQP), e pode ter um atraso máximo de apenas 5 minutos. Por estas razões, pode haver falsos positivos, como dispositivos relatados como conectados, mas que estão desligados. |
| conexãoStateUpdatedTime |read-only |Um indicador temporal, mostrando a data e a última vez que o estado de ligação foi atualizado. |
| última Hora de Atividade |read-only |Um indicador temporal, mostrando a data e a última vez que o dispositivo ligou, recebeu ou enviou uma mensagem. |

> [!NOTE]
> Atualmente, os SDKs do dispositivo não suportam utilizando o `+` e os caracteres no `#` **dispositivoId** e **móduloId** .

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia de desenvolvimento do IoT Hub incluem:

* [Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais que cada hub IoT expõe para operações de tempo de execução e gestão.

* [O estrangulamento e as quotas](iot-hub-devguide-quotas-throttling.md) descrevem as quotas e comportamentos de estrangulamento que se aplicam ao serviço IoT Hub.

* [O dispositivo e serviço Azure IoT lista](iot-hub-devguide-sdks.md) os vários SDKs de linguagem que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* [IoT Hub idioma de consulta](iot-hub-devguide-query-language.md) descreve a linguagem de consulta que você pode usar para obter informações do IoT Hub sobre os seus gémeos e empregos do seu dispositivo.

* [O suporte do IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar o registo de identidade do IoT Hub, poderá estar interessado nos seguintes tópicos de guia de desenvolvimento do IoT Hub:

* [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md)

* [Use gémeos do dispositivo para sincronizar estado e configurações](iot-hub-devguide-device-twins.md)

* [Invocar um método direto num dispositivo](iot-hub-devguide-direct-methods.md)

* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte o seguinte tutorial IoT Hub:

* [Introdução ao Hub IoT do Azure](quickstart-send-telemetry-dotnet.md)

Para explorar utilizando o serviço de provisionamento de dispositivos IoT Hub para permitir o fornecimento de zero toques, just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](https://azure.microsoft.com/documentation/services/iot-dps)
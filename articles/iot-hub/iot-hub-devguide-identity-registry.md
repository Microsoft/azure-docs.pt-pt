---
title: Compreender o registo de identidade do Azure IoT Hub [ Registo de identidade Do Hub Azure IoT] Microsoft Docs
description: Guia de desenvolvimento - descrição do registo de identidade IoT Hub e como usá-lo para gerir os seus dispositivos. Inclui informações sobre a importação e exportação de identidades de dispositivos a granel.
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
ms.openlocfilehash: 2ef259bf76815fdf8672b696d2260fe6a143b798
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730182"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Compreenda o registo de identidade no seu centro ioT

Cada hub IoT tem um registo de identidade que armazena informações sobre os dispositivos e módulos autorizados a ligar-se ao hub IoT. Antes que um dispositivo ou módulo possa ligar-se a um hub IoT, deve haver uma entrada para esse dispositivo ou módulo no registo de identidade do centro IoT. Um dispositivo ou módulo também deve autenticar com o hub IoT com base em credenciais armazenadas no registo de identidade.

O dispositivo ou o iD do módulo armazenado no registo de identidade é sensível a casos.

A um nível elevado, o registo de identidade é uma coleção capaz de REST de dispositivos ou recursos de identidade de módulos. Ao adicionar uma entrada no registo de identidade, o IoT Hub cria um conjunto de recursos por dispositivo, como a fila que contém mensagens cloud-to-device a bordo.

Utilize o registo de identidade quando for necessário:

* Dispositivos de fornecimento ou módulos que se ligam ao seu hub IoT.
* Controle o acesso por dispositivo/módulo ao dispositivo do seu hub ou aos pontos finais virados para o módulo.

> [!NOTE]
> * O registo de identidade não contém metadados específicos da aplicação.
> * A identidade do módulo e o módulo twin estão em pré-visualização pública. Abaixo a funcionalidade será suportada na identidade do módulo quando estiver disponível em geral.
>

## <a name="identity-registry-operations"></a>Operações de registo de identidade

O registo de identidade do IoT Hub expõe as seguintes operações:

* Criar identidade de dispositivo ou módulo
* Atualizar dispositivo ou identidade de módulo
* Recuperar identidade de dispositivo ou módulo por ID
* Eliminar identidade de dispositivo ou módulo
* Lista até 1000 identidades
* Identidades de dispositivos de exportação para armazenamento de blob Azure
* Identidades de dispositivos de importação do armazenamento de blob Azure

Todas estas operações podem utilizar uma moeda otimista, conforme especificado no [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> A única maneira de recuperar todas as identidades no registo de identidade de um centro ioT é usar a funcionalidade [Export.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

Um registo de identidade do IoT Hub:

* Não contém metadados de aplicação.
* Pode ser acedido como um dicionário, utilizando o **dispositivoId** ou **móduloId** como chave.
* Não suporta consultas expressivas.

Uma solução IoT normalmente tem uma loja separada específica de solução que contém metadados específicos da aplicação. Por exemplo, a loja específica da solução numa solução de construção inteligente registaria a sala em que é implantado um sensor de temperatura.

> [!IMPORTANT]
> Utilize apenas o registo de identidade para operações de gestão e provisionamento de dispositivos. As operações de alta suficiência em tempo de execução não devem depender da realização de operações no registo de identidade. Por exemplo, verificar o estado de ligação de um dispositivo antes de enviar um comando não é um padrão suportado. Certifique-se de verificar as taxas de [estrangulamento](iot-hub-devguide-quotas-throttling.md) para o registo de identidade e o padrão de batimentocardíaco do [dispositivo.](iot-hub-devguide-identity-registry.md#device-heartbeat)

## <a name="disable-devices"></a>Desativar dispositivos

Pode desativar os dispositivos atualizando a propriedade **de** um estado de uma identidade no registo de identidade. Normalmente, você usa esta propriedade em dois cenários:

* Durante um processo de provisão de orquestração. Para mais informações, consulte o [Fornecimento de Dispositivos](iot-hub-devguide-identity-registry.md#device-provisioning).

* Se, por alguma razão, achar que um dispositivo está comprometido ou se tornou não autorizado.

Esta funcionalidade não está disponível para módulos.

## <a name="import-and-export-device-identities"></a>Identidades de dispositivos de importação e exportação

Utilize operações assíncronas no ponto final do fornecedor de [recursos IoT Hub](iot-hub-devguide-endpoints.md) para exportar identidades de dispositivos a granel a partir do registo de identidade de um hub IoT. As exportações são trabalhos de longa duração que utilizam um contentor de blob fornecido pelo cliente para guardar dados de identidade do dispositivo lidos a partir do registo de identidade.

Utilize operações assíncronas no ponto final do fornecedor de [recursos IoT Hub](iot-hub-devguide-endpoints.md) para importar identidades de dispositivos a granel para o registo de identidade de um hub IoT. As importações são trabalhos de longa duração que utilizam dados num contentor de blob fornecido pelo cliente para escrever dados de identidade do dispositivo no registo de identidade.

Para obter mais informações sobre as APIs de importação e exportação, consulte o fornecedor de [recursos do IoT Hub REST APIs](/rest/api/iothub/iothubresource). Para saber mais sobre a gestão de postos de trabalho na importação e exportação, consulte a [gestão a granel das identidades do dispositivo IoT Hub.](iot-hub-bulk-identity-mgmt.md)

As identidades do dispositivo também podem ser exportadas e importadas a partir de um Hub IoT através da API de serviço através da [API REST](/rest/api/iothub/service/jobclient/createimportexportjob) ou de um dos [SDKs](/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-service-sdks)de Serviço IoT Hub .

## <a name="device-provisioning"></a>Fornecimento de dispositivos

Os dados do dispositivo que uma determinada solução IoT armazenam depende dos requisitos específicos dessa solução. Mas, no mínimo, uma solução deve armazenar identidades do dispositivo e chaves de autenticação. O Azure IoT Hub inclui um registo de identidade que pode armazenar valores para cada dispositivo, como IDs, chaves de autenticação e códigos de estado. Uma solução pode utilizar outros serviços Azure, tais como armazenamento de mesa, armazenamento de blob ou Cosmos DB para armazenar quaisquer dados adicionais do dispositivo.

*O fornecimento* do dispositivo é o processo de adição dos dados iniciais do dispositivo às lojas na sua solução. Para permitir que um novo dispositivo se ligue ao seu hub, deve adicionar um ID do dispositivo e chaves ao registo de identidade do IoT Hub. Como parte do processo de fornecimento, poderá ser necessário inicializar dados específicos do dispositivo noutras lojas de soluções. Também pode utilizar o Serviço de Provisionamento de Dispositivos Hub Azure IoT para permitir o fornecimento de zero toques, just-in-time a um ou mais hubs IoT sem necessitar de intervenção humana. Para saber mais, consulte a documentação do serviço de [fornecimento.](https://azure.microsoft.com/documentation/services/iot-dps)

## <a name="device-heartbeat"></a>Batimento cardíaco do dispositivo

O registo de identidade do IoT Hub contém um campo chamado **connection State**. Utilize apenas o campo **de ligação Estado** durante o desenvolvimento e a depuração. As soluções IoT não devem consultar o campo no tempo de execução. Por exemplo, não consulte o campo **de ligaçãoEstado** para verificar se um dispositivo está ligado antes de enviar uma mensagem cloud-to-device ou um SMS. Recomendamos que subscreva o evento desligado do [ **dispositivo** ](iot-hub-event-grid.md#event-types) na Rede de Eventos para obter alertas e monitorizar o estado de ligação do dispositivo. Utilize este [tutorial](iot-hub-how-to-order-connection-state-events.md) para aprender a integrar eventos conectados e desligados do dispositivo do IoT Hub na sua solução IoT.

Se a sua solução IoT precisar de saber se um dispositivo está ligado, pode implementar o padrão de *batimentocardíaco*.
No padrão do batimento cardíaco, o dispositivo envia mensagens dispositivo-nuvem pelo menos uma vez a cada período de tempo fixo (por exemplo, pelo menos uma vez por hora). Portanto, mesmo que um dispositivo não tenha nenhum dado para enviar, ainda envia uma mensagem de dispositivo-nuvem vazia (geralmente com uma propriedade que o identifica como um batimento cardíaco). Do lado do serviço, a solução mantém um mapa com o último batimento cardíaco recebido para cada dispositivo. Se a solução não receber uma mensagem de batimento cardíaco dentro do tempo esperado do dispositivo, assume que existe um problema com o dispositivo.

Uma implementação mais complexa poderia incluir a informação do [Azure Monitor](../azure-monitor/index.yml) e [da Azure Resource Health](../service-health/resource-health-overview.md) para identificar dispositivos que estão a tentar ligar ou comunicar, mas falhando, verificar o Monitor com o guia de [diagnóstico.](iot-hub-monitor-resource-health.md) Quando implementar o padrão de batimentocardíaco, certifique-se de verificar as [quotas e os aceleradores do Hub IoT](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Se uma solução IoT utilizar o estado de ligação apenas para determinar se deve enviar mensagens cloud-to-device, e as mensagens não são transmitidas para grandes conjuntos de dispositivos, considere usar o padrão de tempo de *validade curto* mais simples. Este padrão obtém o mesmo resultado que manter um registo estatal de ligação ao dispositivo usando o padrão do batimento cardíaco, enquanto é mais eficiente. Se solicitar avisos de mensagens, o IoT Hub pode notificá-lo sobre quais os dispositivos que podem receber mensagens e que não são.

## <a name="device-and-module-lifecycle-notifications"></a>Notificações de ciclo de vida de dispositivos e módulos

O IoT Hub pode notificar a sua solução IoT quando uma identidade é criada ou eliminada através do envio de notificações de ciclo de vida. Para tal, a sua solução IoT precisa de criar uma rota e de definir a Fonte de Dados igual a *DeviceLifecycleEvents* ou *ModuleLifecycleEvents*. Por padrão, não são enviadas notificações de ciclo de vida, ou seja, não existem tais rotas antes. A mensagem de notificação inclui propriedades e corpo.

Propriedades: As propriedades do sistema `$` de mensagens são pré-fixadas com o símbolo.

Mensagem de notificação para o dispositivo:

| Nome | Valor |
| --- | --- |
|$content | application/json |
|$iothub enqueuedtime |  Hora da notificação ser enviada |
|$iothub-fonte de mensagem | dispositivoLifecycleEvents |
|$content-codificação | utf-8 |
|opType | **criarIdentidade de Dispositivoou** **excluirIdentidade do Dispositivo** |
|hubName | Nome do Hub IoT |
|deviceId | ID do dispositivo |
|operaçãoTimestamp | Carimbo de tempo ISO8601 de operação |
|iothub-mensagem-schema | dispositivoLifecycleNotification |

Corpo: Esta secção encontra-se em formato JSON e representa o gémeo da identidade do dispositivo criado. Por exemplo,

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

| Nome | Valor |
| --- | --- |
$content | application/json |
$iothub enqueuedtime |  Hora da notificação ser enviada |
$iothub-fonte de mensagem | móduloLifecycleEvents |
$content-codificação | utf-8 |
opType | **criarMóduloIdentidade** ou **excluirIdentidade do Módulo** |
hubName | Nome do Hub IoT |
móduloId | ID do módulo |
operaçãoTimestamp | Carimbo de tempo ISO8601 de operação |
iothub-mensagem-schema | móduloLifecycleNotification |

Corpo: Esta secção encontra-se em formato JSON e representa o gémeo da identidade do módulo criado. Por exemplo,

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

| Propriedade | Opções | Descrição |
| --- | --- | --- |
| deviceId |necessário, ler apenas em atualizações |Uma cadeia sensível a casos (até 128 caracteres de comprimento) de caracteres alfanuméricos de 7 bits ASCII mais certos caracteres especiais: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| geraçãoId |necessário, apenas leitura |Uma cadeia ioT gerada por hub, sensível a casos até 128 caracteres de comprimento. Este valor é utilizado para distinguir dispositivos com o mesmo **dispositivoId,** quando foram eliminados e recriados. |
| etag |necessário, apenas leitura |Uma cadeia que representa um ETag fraco para a identidade do dispositivo, de acordo com [o RFC7232](https://tools.ietf.org/html/rfc7232). |
| auth |opcional |Um objeto composto contendo informações de autenticação e materiais de segurança. |
| auth.symkey |opcional |Um objeto composto contendo uma chave primária e secundária, armazenado no formato base64. |
| status |necessário |Um indicador de acesso. Pode ser **ativado** ou **desativado**. Se **ativado,** o dispositivo pode ligar-se. Se **desativado,** este dispositivo não pode aceder a qualquer ponto final virado para o dispositivo. |
| estadoReason |opcional |Uma corda de 128 caracteres que armazena a razão para o estado de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime |leitura-apenas |Um indicador temporal, mostrando a data e a hora da última atualização de estado. |
| conexãoEstado |leitura-apenas |Um estado de ligação indicativo: **ligado** ou **desligado**. Este campo representa a visão do Hub IoT do estado de ligação do dispositivo. **Importante**: Este campo deve ser utilizado apenas para fins de desenvolvimento/depuração. O estado de ligação é atualizado apenas para dispositivos que utilizem MQTT ou AMQP. Além disso, baseia-se em pings de nível protocole (pings MQTT, ou AMQP pings), e pode ter um atraso máximo de apenas 5 minutos. Por estas razões, pode haver falsos positivos, tais como dispositivos relatados como conectados, mas que estão desligados. |
| conexãoStateUpdatedTime |leitura-apenas |Um indicador temporal, mostrando a data e a última vez que o estado de ligação foi atualizado. |
| últimaAtividadeTempo |leitura-apenas |Um indicador temporal, mostrando a data e a última vez que o dispositivo ligou, recebeu ou enviou uma mensagem. |

> [!NOTE]
> O estado de ligação só pode representar a visão do Hub IoT do estado da ligação. As atualizações a este estado podem ser adiadas, dependendo das condições e configurações da rede.

> [!NOTE]
> Atualmente, os SDKs do `+` dispositivo `#` não suportam a utilização dos e caracteres do **dispositivoId**.

## <a name="module-identity-properties"></a>Propriedades de identidade do módulo

As identidades dos módulos são representadas como documentos JSON com as seguintes propriedades:

| Propriedade | Opções | Descrição |
| --- | --- | --- |
| deviceId |necessário, ler apenas em atualizações |Uma cadeia sensível a casos (até 128 caracteres de comprimento) de caracteres alfanuméricos de 7 bits ASCII mais certos caracteres especiais: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| móduloId |necessário, ler apenas em atualizações |Uma cadeia sensível a casos (até 128 caracteres de comprimento) de caracteres alfanuméricos de 7 bits ASCII mais certos caracteres especiais: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| geraçãoId |necessário, apenas leitura |Uma cadeia ioT gerada por hub, sensível a casos até 128 caracteres de comprimento. Este valor é utilizado para distinguir dispositivos com o mesmo **dispositivoId,** quando foram eliminados e recriados. |
| etag |necessário, apenas leitura |Uma cadeia que representa um ETag fraco para a identidade do dispositivo, de acordo com [o RFC7232](https://tools.ietf.org/html/rfc7232). |
| auth |opcional |Um objeto composto contendo informações de autenticação e materiais de segurança. |
| auth.symkey |opcional |Um objeto composto contendo uma chave primária e secundária, armazenado no formato base64. |
| status |necessário |Um indicador de acesso. Pode ser **ativado** ou **desativado**. Se **ativado,** o dispositivo pode ligar-se. Se **desativado,** este dispositivo não pode aceder a qualquer ponto final virado para o dispositivo. |
| estadoReason |opcional |Uma corda de 128 caracteres que armazena a razão para o estado de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime |leitura-apenas |Um indicador temporal, mostrando a data e a hora da última atualização de estado. |
| conexãoEstado |leitura-apenas |Um estado de ligação indicativo: **ligado** ou **desligado**. Este campo representa a visão do Hub IoT do estado de ligação do dispositivo. **Importante**: Este campo deve ser utilizado apenas para fins de desenvolvimento/depuração. O estado de ligação é atualizado apenas para dispositivos que utilizem MQTT ou AMQP. Além disso, baseia-se em pings de nível protocole (pings MQTT, ou AMQP pings), e pode ter um atraso máximo de apenas 5 minutos. Por estas razões, pode haver falsos positivos, tais como dispositivos relatados como conectados, mas que estão desligados. |
| conexãoStateUpdatedTime |leitura-apenas |Um indicador temporal, mostrando a data e a última vez que o estado de ligação foi atualizado. |
| últimaAtividadeTempo |leitura-apenas |Um indicador temporal, mostrando a data e a última vez que o dispositivo ligou, recebeu ou enviou uma mensagem. |

> [!NOTE]
> Atualmente, os SDKs do `+` dispositivo `#` não suportam a utilização do **dispositivoId** e **móduloId**.

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no guia de desenvolvimento do IoT Hub incluem:

* [Os pontos finais do IoT Hub](iot-hub-devguide-endpoints.md) descrevem os vários pontos finais que cada hub IoT expõe para operações de execução e gestão.

* [O estrangulamento e](iot-hub-devguide-quotas-throttling.md) as quotas descrevem as quotas e comportamentos de estrangulamento que se aplicam ao serviço IoT Hub.

* O [dispositivo e o serviço Azure IoT](iot-hub-devguide-sdks.md) listam os vários SDKs linguísticos que pode utilizar quando desenvolve aplicações de dispositivos e serviços que interagem com o IoT Hub.

* A linguagem de [consulta ioT Hub](iot-hub-devguide-query-language.md) descreve a linguagem de consulta que você pode usar para obter informações do IoT Hub sobre os seus gémeos e empregos do seu dispositivo.

* [O suporte IoT Hub MQTT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do IoT Hub para o protocolo MQTT.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar o registo de identidade do IoT Hub, pode estar interessado nos seguintes tópicos de guia de desenvolvimento do IoT Hub:

* [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md)

* [Use gémeos dispositivos para sincronizar estado e configurações](iot-hub-devguide-device-twins.md)

* [Invoque um método direto num dispositivo](iot-hub-devguide-direct-methods.md)

* [Programar tarefas em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte o seguinte tutorial IoT Hub:

* [Introdução ao Hub IoT do Azure](quickstart-send-telemetry-dotnet.md)

Para explorar a utilização do Serviço de Provisionamento de Dispositivos IoT Hub para permitir o fornecimento de zero toques, just-in-time, consulte: 

* [Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure](https://azure.microsoft.com/documentation/services/iot-dps)

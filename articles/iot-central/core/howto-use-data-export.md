---
title: Dados de exportação da IoT Central Microsoft Docs
description: Como utilizar os novos dados exportam para exportar os seus dados IoT para destinos em nuvem Azure e personalizados.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 737fe4b334e60f1b51e8f60f39e8821588a6841c
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010315"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>Exportar dados de IoT para destinos em nuvem usando exportação de dados (pré-visualização)

> [!Note]
> À procura da exportação de dados antigos? Pode encontrar documentos de exportação de dados [aqui.](./howto-export-data.md) Para conhecer as diferenças entre as novas exportações de dados e a exportação de dados antigos, consulte a [tabela de comparação](#comparison-of-legacy-data-export-and-new-data-export).

Este artigo descreve como utilizar as novas funcionalidades de pré-visualização da exportação de dados no Azure IoT Central. Pode utilizar esta funcionalidade para exportar continuamente os seus dados IoT filtrados e enriquecidos para os seus serviços na nuvem. Pode utilizar a exportação de dados para impulsionar alterações em tempo quase real para outras partes da sua solução de nuvem para insights de caminhos quentes, análises e armazenamento. 

 Pode, por exemplo:
-   Exportar continuamente dados de telemetria e alterações de propriedade no formato JSON em tempo quase real
-   Filtrar estes fluxos de dados para exportar capacidades específicas que correspondam às condições personalizadas
-   Enriqueça os fluxos de dados com valores personalizados e valores de propriedade do dispositivo
-   Envie estes dados para destinos como Azure Event Hubs, Azure Service Bus, Azure Blob Storage e webhook endpoints

> [!Note]
> Quando liga a exportação de dados, obtém-se apenas os dados a partir desse momento. Atualmente, os dados não podem ser recuperados por um tempo em que a exportação de dados estava desligada. Para reter mais dados históricos, ligue a exportação de dados mais cedo.

## <a name="prerequisites"></a>Pré-requisitos

Deve ser administrador na sua aplicação IoT Central ou ter permissões de exportação de Dados.

## <a name="set-up-export-destination"></a>Configurar destino de exportação

O seu destino de exportação deve existir antes de configurar a sua exportação de dados. Estes são os tipos de destinos disponíveis:
  - Azure Event Hubs
  - Fila do Azure Service Bus
  - Tópico do Azure Service Bus
  - Armazenamento de Blobs do Azure
  - Webhook

### <a name="create-an-event-hubs-destination"></a>Criar um destino de Centros de Eventos

Se você não tem um espaço de nomes de Event Hubs existente para exportar, siga estes passos:

1. Crie um [novo espaço de nomes de Centros de Eventos no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.EventHub) Você pode aprender mais em [Azure Event Hubs docs](../../event-hubs/event-hubs-create.md).

2. Crie um centro de eventos no seu espaço de nomes Event Hubs. Vá ao seu espaço de nome e selecione **+ Event Hub** no topo para criar uma instância de centro de eventos.

3. Gere uma chave que irá utilizar na IoT Central para configurar a sua exportação de dados. 
    - Clique na instância do centro de eventos que criou. 
    - Clique em **Definições/Políticas de acesso Partilhado**. 
    - Crie uma nova chave ou escolha uma chave existente que tenha permissões **enviar.** 
    - Copie a cadeia de ligação primária ou secundária. Você vai usar isto para configurar um novo destino na IoT Central.

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Crie uma fila de ônibus de serviço ou destino tópico

Se você não tem um espaço de nome de Service Bus existente para exportar para, siga estes passos:

1. Crie um [novo espaço de nomes de Service Bus no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) Pode saber mais em [docs de ônibus da Azure Service](../../service-bus-messaging/service-bus-create-namespace-portal.md).

2. Para criar uma fila ou tópico para exportar, vá ao seu espaço de nomes de Service Bus e selecione **+ Fila** ou **+ Tópico**.

3. Gere uma chave que irá utilizar na IoT Central para configurar a sua exportação de dados. 
    - Clique na fila ou tópico que criou. 
    - Clique em **Definições/Políticas de acesso Partilhado**. 
    - Crie uma nova chave ou escolha uma chave existente que tenha permissões **enviar.** 
    - Copie a cadeia de ligação primária ou secundária. Você vai usar isto para configurar um novo destino na IoT Central.

### <a name="create-an-azure-blob-storage-destination"></a>Criar um destino de armazenamento Azure Blob

Se não tiver uma conta de armazenamento Azure existente para exportar, siga estes passos:

1. Criar uma [nova conta de armazenamento no portal Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Pode saber mais sobre a criação de novas [contas de armazenamento Azure Blob](https://aka.ms/blobdocscreatestorageaccount) ou [contas de armazenamento V2 do Azure Data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md). A exportação de dados só pode escrever dados para contas de armazenamento que suportam bolhas de blocos. A lista que se segue mostra os tipos de conta de armazenamento compatíveis conhecidos:

    |Nível de desempenho|Tipo de Conta|
    |-|-|
    |Standard|Final Geral V2|
    |Standard|Final geral V1|
    |Standard|Armazenamento de blobs|
    |Premium|Armazenamento Blob de bloco|

2. Crie um recipiente na sua conta de armazenamento. Vá para a sua conta de armazenamento. No **serviço Blob**, **selecione Browse Blobs**. Selecione **+ Recipiente** na parte superior para criar um novo recipiente.

3. Gere uma cadeia de ligação para a sua conta de armazenamento indo para **as teclas Definições/Acesso**. Copie uma das duas cordas de ligação.

### <a name="create-a-webhook-endpoint"></a>Criar um ponto final webhook
Pode fornecer um ponto final HTTP disponível ao público para os dados a exportar. Pode criar um ponto final de teste webhook utilizando o RequestBin. Tenha em mente que o RequestBin tem um limite de pedido definido antes de os pedidos serem estrangulados.

1. Open [RequestBin](https://requestbin.net/).
2. Crie um novo RequestBin e copie o URL do Bin.

## <a name="set-up-data-export"></a>Configurar exportação de dados

Agora que tem um destino para exportar dados, siga estes passos para configurar a exportação de dados.

1. Inscreva-se na sua aplicação IoT Central.

2. No painel esquerdo, selecione **Data export**.

    > [!Tip]
    > Se não vir **a exportação de Dados** no painel esquerdo, então não tem permissões para configurar a exportação de dados na sua app. Fale com um administrador para configurar a exportação de dados.

3. Selecione o **botão + Novo botão de exportação.** 

4. Introduza um nome de exibição para a sua nova exportação e certifique-se de que o toggle **Ativado** está ligado para que os dados fluam.

## <a name="1-choose-the-type-of-data-to-export"></a>1. Escolha o tipo de dados para exportar
Pode escolher entre exportar continuamente diferentes tipos de dados. Aqui estão os tipos de dados suportados:

| Tipo de dados | Descrição | Formato de dados |
| :------------- | :---------- | :----------- |
|  Telemetria | Exporte mensagens de telemetria de dispositivos em tempo quase real. Cada mensagem exportada conterá todo o conteúdo da mensagem original do dispositivo, normalizada.   |  [Formato de mensagem de telemetria](#telemetry-format)   |
| Alterações de propriedade | Exportar alterações para propriedades de dispositivos e nuvem em tempo quase real. Para as propriedades do dispositivo apenas de leitura, serão exportadas alterações aos valores reportados. Para propriedades de leitura-escrita, os valores relatados e desejados serão exportados. | [Formato de mensagem de alteração de propriedade](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2. (Opcional) Adicionar filtros
Adicione filtros para reduzir a quantidade de dados exportados com base nas condições do filtro. Existem diferentes tipos de filtros disponíveis para cada tipo de dados para exportar.

### <a name="telemetry-filters"></a>Filtros de telemetria
  - **Filtro de capacidade**: Se escolher um item de telemetria no dropdown, o fluxo exportado só conterá telemetria que satisfaça o estado do filtro. Se escolher um item de propriedade de dispositivo ou nuvem no dropdown, o fluxo exportado apenas conterá telemetria de dispositivos que tenham propriedades que correspondam à condição do filtro.
  - **Filtro de propriedade de mensagem**: Os dispositivos que utilizam o dispositivo SDKs são autorizados a enviar propriedades de *mensagens* ou propriedades de *aplicação* em cada mensagem de telemetria, que é um saco de pares de valores-chave geralmente usados para marcar a mensagem com identificadores personalizados. Pode criar um filtro de propriedade de mensagem digitando a chave de propriedade de mensagem que procura e especificando uma condição. Apenas serão exportadas mensagens de telemetria que tenham propriedades de mensagens que correspondam à condição de filtro especificada. Apenas os operadores de comparação de cordas são apoiados (iguais, não é igual, contém, não contém, existe, não existe). [Saiba mais sobre as propriedades da aplicação a partir de docs IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

### <a name="property-changes-filters"></a>Filtros de mudança de propriedade
**Filtro de propriedade**: Escolha um item de propriedade no dropdown, e o fluxo exportado só conterá alterações na propriedade selecionada que satisfaça o estado do filtro.

## <a name="3-optional-add-enrichments"></a>3. (Opcional) Adicionar enriquecimentos
Adicione enriquecimentos para enriquecer mensagens exportadas com metadados adicionais em pares de valor-chave. Estes são os enriquecimentos disponíveis para telemetria e alterações de propriedade tipos de dados:
  - **Cadeia personalizada**: Adiciona uma cadeia estática personalizada a cada mensagem. Introduza qualquer chave e introduza qualquer valor de cadeia.
  - **Propriedade**: Adiciona o valor de propriedade ou propriedade reportada ao dispositivo atual a cada mensagem. Introduza qualquer chave e escolha um dispositivo ou propriedade na nuvem. Se a mensagem exportada for de um dispositivo que não tenha o dispositivo especificado ou a propriedade na nuvem, a mensagem exportada não terá esse enriquecimento.

## <a name="4-add-destinations"></a>4. Adicionar destinos
Crie um novo destino ou adicione um destino que já criou. 
  
1. Clique na **criação de um novo** link de destino. Preencha as seguintes informações:
    - **Nome do destino**: o nome de exposição do destino no IoT Central
    - **Tipo de destino**: escolha o tipo de destino. Se ainda não o fez, [crie o seu destino de exportação](#set-up-export-destination)
    - Para Azure Event Hubs, fila ou tópico do Azure Service Bus, cole a cadeia de ligação para o seu recurso. 
    - Para o armazenamento Azure Blob, cole o fio de ligação para o seu recurso e introduza o nome do recipiente (sensível à caixa).
    - Para webhook, cole o URL de retorno para o seu ponto final webhook. 
    - Clique em **Criar**

2. Clique **em + Destino** e escolha um destino a partir do dropdown. Soma-se a 5 destinos para uma única exportação.

3. Depois de ter terminado a configuração da sua exportação, clique em **Guardar**. Após alguns minutos, os seus dados aparecerão nos seus destinos.

## <a name="export-contents-and-format"></a>Conteúdos de exportação e formato

Para os destinos de Event Hubs e Service Bus, os dados são exportados em tempo quase real. Os dados estão no corpo da mensagem e estão em formato JSON codificados como UTF-8. Veja abaixo, por exemplo.

Para o armazenamento blob, os dados são exportados uma vez por minuto, com cada ficheiro contendo o lote de alterações desde o último ficheiro exportado. Os dados exportados são colocados em três pastas no formato JSON. Os caminhos predefinidos na sua conta de armazenamento são:

- Telemetria: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Alterações de propriedade: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{filename}_

Para navegar nos ficheiros exportados no portal Azure, navegue para o ficheiro e selecione o **separador 'Edição' blob.**

Para os destinos webhooks, os dados também são exportados em tempo quase real. Os dados estão no corpo da mensagem no mesmo formato que para Os Centros de Eventos e Autocarro de Serviço.


## <a name="telemetry-format"></a>Formato de telemetria
Cada mensagem exportada contém uma forma normalizada da mensagem completa que o dispositivo enviou no corpo da mensagem no formato JSON codificado como UTF-8. Informações adicionais incluídas em cada mensagem incluem:

- `applicationId`da aplicação IoT Central
- `messageSource`que é *telemetria* para exportação de dados de telemetria
- `deviceId`do dispositivo que enviou a mensagem de telemetria
- `schema`é o nome e versão do esquema de carga útil
- `templateId`é o Id do modelo de dispositivo associado ao dispositivo
- `enrichments`são quaisquer enriquecimentos que foram criados na exportação
- `messageProperties`são os dados adicionais que o dispositivo enviou ao lado da mensagem. Isto também é conhecido como *propriedades de aplicação,* [saiba mais com docs IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

Para o Event Hubs e Service Bus, a IoT Central exporta uma nova mensagem rapidamente depois de receber a mensagem de um dispositivo.

Para o armazenamento blob, as mensagens são em lotadas e exportadas uma vez por minuto.

O exemplo a seguir mostra uma mensagem de telemetria exportada:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>Formato de alterações de propriedade

Cada mensagem ou registo representa uma alteração para um dispositivo ou propriedade na nuvem. Para as propriedades do dispositivo, apenas alterações no valor reportado são exportadas como uma mensagem separada. Informações adicionais incluídas na mensagem exportada incluem:

- `applicationId`da aplicação IoT Central
- `messageSource`que são *propriedades* para exportação de dados de alterações de propriedade
- `messageType`que é *cloudPropertyChange* ou *dispositivoPropertyDesiredChange,* *dispositivoPropertyReportedChange*
- `deviceId`do dispositivo cujas propriedades mudaram
- `schema`é o nome e versão do esquema de carga útil
- `templateId`é o Id do modelo de dispositivo associado ao dispositivo
- `enrichments`são quaisquer enriquecimentos que foram criados na exportação

Para o Event Hubs e Service Bus, a IoT Central exporta novos dados de mensagens para o seu centro de eventos ou fila de autocarros de serviço ou tópico em tempo real.

Para o armazenamento blob, as mensagens são em lotadas e exportadas uma vez por minuto.

O exemplo a seguir mostra uma mensagem de mudança de propriedade exportada recebida no Azure Blob Storage.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>Comparação da exportação de dados antigos e da exportação de novos dados
Trata-se de um quadro que destaca as diferenças entre a exportação de dados antigos e a exportação de novos dados. Pode conhecer [aqui](howto-export-data.md)os dados antigos.

| Capacidades  | Exportação de dados antigos | Exportação de novos dados |
| :------------- | :---------- | :----------- |
| Tipos de dados disponíveis | Telemetria, Dispositivos, Modelos de Dispositivos | Telemetria, Alterações de Propriedade |
| Filtragem | Nenhum | Depende do tipo de dados exportado. Para telemetria, filtragem por telemetria, propriedades de mensagens, valores de propriedade |
| Enriquecimentos | Nenhum | Enriqueça com uma corda personalizada ou um valor de propriedade no dispositivo |
| Destinos | Azure Event Hubs, filas e tópicos de autocarro da Azure Service, Azure Blob Storage | O mesmo que para exportação de dados antigos e webhooks| 
| Limites notáveis | 5 exportações por app, 1 destino por exportação | 10 ligações exportações-destino por app | 

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como usar a nova exportação de dados, continue para o próximo passo:

> [!div class="nextstepaction"]
> [Como usar a análise no IoT Central](./howto-create-analytics.md)

---
title: Serviço de Aplicações Azure como fonte da Grelha de Eventos
description: Este artigo descreve como usar o Serviço de Aplicações Azure como uma fonte de evento sinuoso. Fornece o esquema e ligações ao tutorial e aos artigos de como fazer.
services: event-grid
author: jasonfreeberg
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: jafreebe
ms.openlocfilehash: 0a24e8ba84739dbc1b5de5e0546a8fe0d2e826f1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650703"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Serviço de Aplicações Azure como fonte da Rede de Eventos

Este artigo fornece as propriedades e esquemas para eventos do Serviço de Aplicações Azure. Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md). Também lhe dá uma lista de arranques rápidos e tutoriais para usar o Azure App Service como fonte de evento.

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="available-event-types"></a>Tipos de eventos disponíveis

Azure App Service emite os seguintes tipos de eventos

|    Tipo de Evento                                             |    Descrição                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites. BackupOperationIniciado             |    Desencadeado quando um backup começou                             |
|    Microsoft.Web/sites. BackupOperationCompleted           |    Desencadeado quando uma cópia de segurança tiver sido concluída                           |
|    Microsoft.Web/sites. BackupOperationFailed              |    Desencadeado quando um backup falhou                              |
|    Microsoft.Web/sites. RestauroOperação Iniciada            |    Desencadeado quando uma restauração de uma cópia de segurança começou        |
|    Microsoft.Web/sites. Restaurar Operação Concluída          |    Desencadeado quando uma restauração de uma cópia de segurança tiver concluído      |
|    Microsoft.Web/sites. Restaurar Operação Falhada             |    Desencadeado quando uma restauração de uma cópia de segurança falhou         |
|    Microsoft.Web/sites. SlotSwapStarted                    |    Desencadeado quando uma troca de slot começou                          |
|    Microsoft.Web/sites. SlotSwapCompleted                  |    Desencadeado quando uma troca de slot tiver concluído                      |
|    Microsoft.Web/sites. SlotSwapFailed                     |    Desencadeado quando uma troca de slot falhou                           |
|    Microsoft.Web/sites. SlotswapWithPreviewIniciado         |    Desencadeado quando uma troca de slot com pré-visualização começou           |
|    Microsoft.Web/sites. SlotswapWithPreviewcancelado       |    Desencadeado quando uma troca de slot com pré-visualização foi cancelada    |
|    Microsoft.Web/sites. AppUpdated.Restarted               |    Desencadeado quando um site foi reiniciado                      |
|    Microsoft.Web/sites. AppUpdated.Parou                 |    Desencadeado quando um site foi parado                          |
|    Microsoft.Web/sites. AppUpdated.ChangedAppSettings      |    Desencadeado quando as definições de aplicativos de um site mudaram             |
|    Microsoft.Web/serverfarms. AppServicePlanAtualizado        |    Desencadeado quando um Plano de Serviço de Aplicações é atualizado                 |

### <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta de evento

Quando um evento é desencadeado, o serviço De Event Grid envia dados sobre esse evento para subscrever endpoint.
Esta secção contém um exemplo do que esses dados seriam para cada evento. Cada evento tem os seguintes dados de alto nível:

|     Propriedade          |     Tipo     |     Descrição                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    tópico              |    string    |    Caminho de recursos completos para a fonte do evento. Este campo não é repreensível. O Event Grid fornece este valor.                                      |
|    Assunto            |    string    |    Caminho definido pela editora para o assunto do evento.                                                                                              |
|    eventType          |    string    |    Um dos tipos de eventos registados para esta fonte do evento.                                                                                  |
|    eventTime          |    string    |    O tempo que o evento é gerado com base no tempo UTC do fornecedor.                                                                         |
|    ID                 |    string    |    Identificador único para o evento.                                                                                                            |
|    data               |    objeto    |    Dados do evento de armazenamento blob.                                                                                                                    |
|    dataVersion        |    string    |    A versão esquema do objeto de dados. O publicador define a versão do esquema.                                                          |
|    metadataVersion    |    string    |    A versão de esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. A Grelha de Eventos fornece este valor.    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.BackupOperationStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { "action": "Started" },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Tipo      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objeto    |    Detalhe de ação na app                                                                                       |
|    action                  |    string    |    Tipo de ação da operação                                                                                   |
|    name                    |    string    |    nome do site que tinha este evento                                                                          |
|    clientRequestId         |    string    |    O id de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    correlaçãoRequestId    |    string    |    O id de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|    requestId               |    string    |    O id de pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    address                 |    string    |    HTTP solicitar URL desta operação                                                                                |
|    verbo                    |    string    |    Verbo HTTP desta operação                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.RestoreOperationStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: { 
            action: "Started" 
        },
        siteName: "<site-name>",
        clientRequestId: "None",
        correlationRequestId: "None",
        requestId: "292f499d-04ee-4066-994d-c2df57b99198",
        address: "None",
        verb: "POST"
    }
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Tipo      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objeto    |    Detalhe de ação na app                                                                                       |
|    action                  |    string    |    Tipo de ação da operação                                                                                   |
|    name                    |    string    |    nome do site que tinha este evento                                                                          |
|    clientRequestId         |    string    |    O id de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    correlaçãoRequestId    |    string    |    O id de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|    requestId               |    string    |    O id de pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    address                 |    string    |    HTTP solicitar URL desta operação                                                                                |
|    verbo                    |    string    |    Verbo HTTP desta operação                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Tipo      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objeto    |    Detalhe de ação na app                                                                                       |
|    action                  |    string    |    Tipo de ação da operação                                                                                   |
|    name                    |    string    |    nome do site que tinha este evento                                                                          |
|    clientRequestId         |    string    |    O id de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    correlaçãoRequestId    |    string    |    O id de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|    requestId               |    string    |    O id de pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    address                 |    string    |    HTTP solicitar URL desta operação                                                                                |
|    verbo                    |    string    |    Verbo HTTP desta operação                                                                                       |
|    fonteSlot              |    string    |    A ranhura de origem da troca                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotswapWithPreviewStarted, SlotswapWithPreviewCancelado

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

O objeto de dados contém as seguintes propriedades:

|    Propriedade                |    Tipo      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objeto    |    Detalhe de ação na app                                                                                       |
|    action                  |    string    |    Tipo de ação da operação                                                                                   |
|    name                    |    string    |    nome do site que tinha este evento                                                                          |
|    clientRequestId         |    string    |    O id de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    correlaçãoRequestId    |    string    |    O id de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|    requestId               |    string    |    O id de pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    address                 |    string    |    HTTP solicitar URL desta operação                                                                                |
|    verbo                    |    string    |    Verbo HTTP desta operação                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted, AppUpdated.Stopped, AppUpdated.ChangedAppSettings

```js
{
    id: 'b74ea56b-2a3f-4de5-a5d7-38e60c81cf23',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.AppUpdated',
    eventTime: '2020-01-28T18:22:30.2760952Z',
    data: {
        appEventTypeDetail: {
            action: 'Stopped'
        },
        siteName: '<site-name>',
        clientRequestId: '64a5e0aa-7cee-4ff1-9093-b9197b820014',
        correlationRequestId: '25bb36a5-8f6c-4f04-b615-e9a0ee045756',
        requestId: 'f2e8eb3f-b190-42de-b99e-6acefe587374',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

O objeto de dados tem as seguintes propriedades:

|    Propriedade                |    Tipo      |    Descrição                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    objeto    |    Detalhe de ação na app                                                                                       |
|    action                  |    string    |    Tipo de ação da operação                                                                                   |
|    name                    |    string    |    nome do site que tinha este evento                                                                          |
|    clientRequestId         |    string    |    O id de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    correlaçãoRequestId    |    string    |    O id de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|    requestId               |    string    |    O id de pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    address                 |    string    |    HTTP solicitar URL desta operação                                                                                |
|    verbo                    |    string    |    Verbo HTTP desta operação                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanAtualizado

```js
{
   id: "56501672-9150-40e1-893a-18420c7fdbf7",
   subject: "/Microsoft.Web/serverfarms/<plan-name>",
   eventType: "Microsoft.Web.AppServicePlanUpdated",
   eventTime: "2020-01-28T18:22:23.5516004Z",
   data: {
        serverFarmEventTypeDetail: {
            stampKind: "Public",
            action: "Updated",
            status: "Started"
        },
        serverFarmId: "0",
        sku: {
            name: "P1v2",
            tier: "PremiumV2",
            size: "P1v2",
            family: "Pv2",
            capacity: 1
        },
        clientRequestId: "8f880321-a991-45c7-b743-6ff63fe4c004",
        correlationRequestId: "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        requestId: "b973a8e6-6949-4783-b44c-ac778be831bb",
        address: "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        verb: "PUT"
   },
   topic: "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
   dataVersion: "1",
   metaDataVersion: "1"
}
```

O objeto de dados tem as seguintes propriedades:

|    Propriedade                         |    Tipo      |    Descrição                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appServicePlanEventTypeDetail    |    objeto    |    Detalhe de ação no plano de serviço de aplicações                                                                          |
|    carimboKind                        |    string    |    Tipo de ambiente onde o plano de serviço de aplicações é                                                                     |
|    action                           |    string    |    Tipo de ação no plano de serviço de aplicações                                                                            |
|    status                           |    string    |    Estado da operação no plano de serviço de aplicações                                                                   |
|    sku                              |    objeto    |    sku do plano de serviço de aplicações                                                                                       |
|    name                             |    string    |    nome do plano de serviço de aplicações                                                                                      |
|    Escalão                             |    string    |    nível do plano de serviço de aplicações                                                                                      |
|    Tamanho                             |    string    |    tamanho do plano de serviço de aplicações                                                                                      |
|    Família                           |    string    |    família de app plano de serviço                                                                                        |
|    Capacidade                         |    string    |    capacidade do plano de serviço de aplicações                                                                                      |
|    action                           |    string    |    Tipo de ação da operação                                                                                   |
|    name                             |    string    |    nome do site que tinha este evento                                                                          |
|    clientRequestId                  |    string    |    O id de pedido do cliente gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento         |
|    correlaçãoRequestId             |    string    |    O id de pedido de correlação gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento    |
|    requestId                        |    string    |    O id de pedido gerado pelo serviço de aplicações para a operação API do site que desencadeou este evento                |
|    address                          |    string    |    HTTP solicitar URL desta operação                                                                                |
|    verbo                             |    string    |    Verbo HTTP desta operação                                                                                       |

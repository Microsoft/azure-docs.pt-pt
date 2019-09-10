---
title: Visão geral da integração de mensagens do Azure Blockchain Workbench
description: Visão geral do uso de mensagens na visualização do Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: f0a9e90f1208d690c2423196be7f59dce71eb78b
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844076"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integração de mensagens do Azure Blockchain Workbench

Além de fornecer uma API REST, o Azure Blockchain Workbench também fornece integração baseada em mensagens. O Workbench publica eventos centrados no razão por meio da grade de eventos do Azure, permitindo que os consumidores de downstream ingerirem dados ou tomem medidas com base nesses eventos. Para os clientes que exigem mensagens confiáveis, o Azure Blockchain Workbench também entrega mensagens para um ponto de extremidade do barramento de serviço do Azure.

## <a name="input-apis"></a>APIs de entrada

Se você quiser iniciar transações de sistemas externos para criar usuários, criar contratos e atualizar contratos, poderá usar APIs de entrada de mensagens para executar transações em um razão. Consulte [exemplos de integração de mensagens](https://aka.ms/blockchain-workbench-integration-sample) para obter um exemplo que demonstra APIs de entrada.

A seguir estão as APIs de entrada disponíveis no momento.

### <a name="create-user"></a>Criar utilizador

Cria um novo usuário.

A solicitação requer os seguintes campos:

| **Name**             | **Descrição**                                      |
|----------------------|------------------------------------------------------|
| requestId            | GUID fornecido pelo cliente                                |
| firstName            | Nome do usuário                              |
| lastName             | Sobrenome do usuário                               |
| emailAddress         | Endereço de email do usuário                           |
| externalId           | ID de objeto do Azure AD do usuário                      |
| connectionId         | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion | Versão do esquema de mensagens                            |
| messageName          | **CreateUserRequest**                               |

Exemplo:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

O Blockchain Workbench retorna uma resposta com os seguintes campos:

| **Name**              | **Descrição**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | GUID fornecido pelo cliente |
| userId                | ID do usuário que foi criado |
| userChainIdentifier   | Endereço do usuário que foi criado na rede blockchain. No Ethereum, o endereço é o endereço **na cadeia** do usuário. |
| connectionId          | Identificador exclusivo para a conexão blockchain|
| messageSchemaVersion  | Versão do esquema de mensagens |
| messageName           | **CreateUserUpdate** |
| status                | Status da solicitação de criação de usuário.  Se tiver êxito, o valor é **Success**. Em caso de falha, o valor é **Failure**.     |
| additionalInformation | Informações adicionais fornecidas com base no status |

Exemplo de êxito ao criar resposta do **usuário** do Blockchain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Se a solicitação não tiver sido bem-sucedida, os detalhes sobre a falha serão incluídos em informações adicionais.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Criar contrato

Cria um novo contrato.

A solicitação requer os seguintes campos:

| **Name**             | **Descrição**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | GUID fornecido pelo cliente |
| userChainIdentifier  | Endereço do usuário que foi criado na rede blockchain. No Ethereum, esse endereço é o endereço do usuário **na cadeia** . |
| applicationName      | Nome do aplicativo |
| version              | Versão da aplicação. Necessário se você tiver várias versões do aplicativo habilitadas. Caso contrário, a versão será opcional. Para obter mais informações sobre o controle de versão do aplicativo, consulte [controle de versão do aplicativo Azure Blockchain Workbench](version-app.md). |
| workflowName         | Nome do fluxo de trabalho |
| parameters           | Entrada de parâmetros para criação de contrato |
| connectionId         | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName          | **CreateContractRequest** |

Exemplo:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

O Blockchain Workbench retorna uma resposta com os seguintes campos:

| **Name**                 | **Descrição**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | GUID fornecido pelo cliente                                                             |
| contractId               | Identificador exclusivo para o contrato dentro do Azure Blockchain Workbench |
| contractLedgerIdentifier | Endereço do contrato no razão                                            |
| connectionId             | Identificador exclusivo para a conexão blockchain                               |
| messageSchemaVersion     | Versão do esquema de mensagens                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Status da solicitação de criação de contrato.  Valores possíveis: **Enviado**, **confirmado**, **falha**.  |
| additionalInformation    | Informações adicionais fornecidas com base no status                              |

Exemplo de uma resposta de **criação de contrato** enviada do Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Exemplo de uma resposta de **criação de contrato** confirmada do Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Se a solicitação não tiver sido bem-sucedida, os detalhes sobre a falha serão incluídos em informações adicionais.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Criar ação de contrato

Cria uma nova ação de contrato.

A solicitação requer os seguintes campos:

| **Name**                 | **Descrição**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | GUID fornecido pelo cliente |
| userChainIdentifier      | Endereço do usuário que foi criado na rede blockchain. No Ethereum, esse endereço é o endereço do usuário **na cadeia** . |
| contractLedgerIdentifier | Endereço do contrato no razão |
| version                  | Versão da aplicação. Necessário se você tiver várias versões do aplicativo habilitadas. Caso contrário, a versão será opcional. Para obter mais informações sobre o controle de versão do aplicativo, consulte [controle de versão do aplicativo Azure Blockchain Workbench](version-app.md). |
| workflowFunctionName     | Nome da função de fluxo de trabalho |
| parameters               | Entrada de parâmetros para criação de contrato |
| connectionId             | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion     | Versão do esquema de mensagens |
| messageName              | **CreateContractActionRequest** |

Exemplo:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

O Blockchain Workbench retorna uma resposta com os seguintes campos:

| **Name**              | **Descrição**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | GUID fornecido pelo cliente|
| contractId            | Identificador exclusivo para o contrato dentro do Azure Blockchain Workbench |
| connectionId          | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion  | Versão do esquema de mensagens |
| messageName           | **CreateContractActionUpdate** |
| status                | Status da solicitação de ação de contrato. Valores possíveis: **Enviado**, **confirmado**, **falha**.                         |
| additionalInformation | Informações adicionais fornecidas com base no status |

Exemplo de uma resposta de **ação criar contrato** enviada do Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Exemplo de uma resposta de **ação de criação de contrato** confirmada do Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Se a solicitação não tiver sido bem-sucedida, os detalhes sobre a falha serão incluídos em informações adicionais.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Mensagens e códigos de erro da API de entrada

**Código de erro 4000: Erro de solicitação inadequado**
- ConnectionID inválida
- Falha na desserialização de CreateUserRequest
- Falha na desserialização de CreateContractRequest
- Falha na desserialização de CreateContractActionRequest
- O aplicativo {identificado pelo nome do aplicativo} não existe
- O aplicativo {identificado pelo nome do aplicativo} não tem fluxo de trabalho
- UserChainIdentifier não existe
- O contrato {identificado pelo identificador do razão} não existe
- O contrato {identificado pelo identificador do razão} não tem a função {nome da função de fluxo de trabalho}
- UserChainIdentifier não existe

**Código de erro 4090: Erro de conflito**
- O usuário já existe
- O contrato já existe
- A ação de contrato já existe

**Código de erro 5000: Erro interno do servidor**
- Mensagens de exceção

## <a name="event-notifications"></a>Notificações de eventos

As notificações de eventos podem ser usadas para notificar os usuários e sistemas downstream de eventos que ocorrem no Blockchain Workbench e na rede Blockchain à qual ele está conectado. As notificações de eventos podem ser consumidas diretamente no código ou usadas com ferramentas como aplicativos lógicos e fluxo para disparar o fluxo de dados para sistemas downstream.

Consulte [referência de mensagem de notificação](#notification-message-reference) para obter detalhes de várias mensagens que podem ser recebidas.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Consumindo eventos de grade de eventos com Azure Functions

Se um usuário quiser usar a grade de eventos para ser notificado sobre eventos que acontecem no Blockchain Workbench, você poderá consumir eventos da grade de eventos usando Azure Functions.

1. Crie uma **aplicativo de funções do Azure** no portal do Azure.
2. Crie uma nova função.
3. Localize o modelo para a grade de eventos. O código de modelo básico para ler a mensagem é mostrado. Modifique o código conforme necessário.
4. Salve a função. 
5. Selecione a grade de eventos do grupo de recursos do Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Consumindo eventos de grade de eventos com aplicativos lógicos

1. Crie um novo **aplicativo lógico do Azure** no portal do Azure.
2. Ao abrir o aplicativo lógico do Azure no portal, você será solicitado a selecionar um gatilho. Selecione **grade de eventos do Azure--quando ocorre um evento de recurso**.
3. Quando o designer de fluxo de trabalho for exibido, você será solicitado a entrar.
4. Selecione a assinatura. Recurso como **Microsoft. EventGrid. topics**. Selecione o **nome do recurso** do nome do recurso do grupo de recursos do Azure Blockchain Workbench.
5. Selecione a grade de eventos do grupo de recursos do Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Usando os tópicos do barramento de serviço para notificações

Os tópicos do barramento de serviço podem ser usados para notificar os usuários sobre eventos que ocorrem no Blockchain Workbench. 

1. Navegue até o barramento de serviço no grupo de recursos do Workbench.
2. Selecione **Tópicos**.
3. Selecione **egresso-tópico**.
4. Crie uma nova assinatura para este tópico. Obtenha uma chave para ele.
5. Crie um programa, que assina eventos dessa assinatura.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Consumindo mensagens do barramento de serviço com aplicativos lógicos

1. Crie um novo **aplicativo lógico do Azure** no portal do Azure.
2. Ao abrir o aplicativo lógico do Azure no portal, você será solicitado a selecionar um gatilho. Digite **barramento de serviço** na caixa de pesquisa e selecione o gatilho apropriado para o tipo de interação que você deseja ter com o barramento de serviço. Por exemplo, **barramento de serviço – quando uma mensagem é recebida em uma assinatura de tópico (preenchimento automático)** .
3. Quando o designer de fluxo de trabalho for exibido, especifique as informações de conexão para o barramento de serviço.
4. Selecione sua assinatura e especifique o tópico do **Workbench-external**.
5. Desenvolva a lógica para seu aplicativo que utilize a mensagem desse gatilho.

## <a name="notification-message-reference"></a>Referência de mensagem de notificação

Dependendo do **, as**mensagens de notificação têm um dos tipos de mensagem a seguir.

### <a name="block-message"></a>Bloquear mensagem

Contém informações sobre blocos individuais. O *BlockMessage* inclui uma seção com informações de nível de bloco e uma seção com informações de transação.

| Name | Descrição |
|------|-------------|
| bloquear | Contém [informações de bloco](#block-information) |
| transações | Contém informações de uma [transação](#transaction-information) de coleção para o bloco |
| connectionId | Identificador exclusivo para a conexão |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName | **BlockMessage** |
| additionalInformation | Informações adicionais fornecidas |

#### <a name="block-information"></a>Informações de bloco

| Name              | Descrição |
|-------------------|-------------|
| blockId           | Identificador exclusivo do bloco dentro do Azure Blockchain Workbench |
| blockNumber       | Identificador exclusivo de um bloco no razão |
| blockHash         | O hash do bloco |
| previousBlockHash | O hash do bloco anterior |
| blockTimestamp    | O carimbo de data/hora do bloco |

#### <a name="transaction-information"></a>Informações da transação

| Name               | Descrição |
|--------------------|-------------|
| transactionId      | Identificador exclusivo para a transação dentro do Azure Blockchain Workbench |
| transactionHash    | O hash da transação no razão |
| from               | Identificador exclusivo no razão para a origem da transação |
| para                 | Identificador exclusivo no razão para o destino da transação |
| provisioningStatus | Identifica o status atual do processo de provisionamento para a transação. Os valores possíveis são: </br>0 – a transação foi criada pela API no banco de dados</br>1 – a transação foi enviada para o razão</br>2 – a transação foi confirmada com êxito no razão</br>3 ou 4-a transação não pôde ser confirmada no razão</br>5-a transação foi confirmada com êxito no razão |

Exemplo de um *BlockMessage* do Blockchain Workbench:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Mensagem de contrato

Contém informações sobre um contrato. A mensagem inclui uma seção com propriedades de contrato e uma seção com informações de transação. Todas as transações que modificaram o contrato para o bloco específico são incluídas na seção transação.

| Name | Descrição |
|------|-------------|
| blockId | Identificador exclusivo do bloco dentro do Azure Blockchain Workbench |
| blockHash | Hash do bloco |
| modifyingTransactions | [Transações que modificaram](#modifying-transaction-information) o contrato |
| contractId | Identificador exclusivo para o contrato dentro do Azure Blockchain Workbench |
| contractLedgerIdentifier | Identificador exclusivo do contrato no razão |
| contractproperties | [Propriedades do contrato](#contract-properties) |
| isNewContract | Indica se este contrato foi criado recentemente ou não. Os valores possíveis são: true: este contrato era um novo contrato criado. false: este contrato é uma atualização de contrato. |
| connectionId | Identificador exclusivo para a conexão |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName | **ContractMessage** |
| additionalInformation | Informações adicionais fornecidas |

#### <a name="modifying-transaction-information"></a>Modificando informações da transação

| Name               | Descrição |
|--------------------|-------------|
| transactionId | Identificador exclusivo para a transação dentro do Azure Blockchain Workbench |
| transactionHash | O hash da transação no razão |
| from | Identificador exclusivo no razão para a origem da transação |
| para | Identificador exclusivo no razão para o destino da transação |

#### <a name="contract-properties"></a>Propriedades do contrato

| Name               | Descrição |
|--------------------|-------------|
| workflowPropertyId | Identificador exclusivo da propriedade de fluxo de trabalho dentro do Azure Blockchain Workbench |
| name | Nome da propriedade de fluxo de trabalho |
| value | Valor da propriedade de fluxo de trabalho |

Exemplo de um *ContractMessage* do Blockchain Workbench:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Mensagem do evento: Invocação de função de contrato

Contém informações quando uma função de contrato é invocada, como o nome da função, entrada de parâmetros e o chamador da função.

| Name | Descrição |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| chamador                      | [Informações do chamador](#caller-information) |
| contractId                  | Identificador exclusivo para o contrato dentro do Azure Blockchain Workbench |
| contractLedgerIdentifier    | Identificador exclusivo do contrato no razão |
| functionName                | Nome da função |
| parameters                  | [Informações de parâmetro](#parameter-information) |
| Aciona                 | Informações da transação |
| inTransactionSequenceNumber | O número de sequência da transação no bloco |
| connectionId                | Identificador exclusivo para a conexão |
| messageSchemaVersion        | Versão do esquema de mensagens |
| messageName                 | **EventMessage** |
| additionalInformation       | Informações adicionais fornecidas |

#### <a name="caller-information"></a>Informações do chamador

| Name | Descrição |
|------|-------------|
| type | Tipo do chamador, como um usuário ou um contrato |
| id | Identificador exclusivo para o chamador no Azure Blockchain Workbench |
| ledgerIdentifier | Identificador exclusivo do chamador no razão |

#### <a name="parameter-information"></a>Informações de parâmetro

| Name | Descrição |
|------|-------------|
| name | Nome do parâmetro |
| value | Valor do parâmetro |

#### <a name="event-message-transaction-information"></a>Informações de transação de mensagem de evento

| Name               | Descrição |
|--------------------|-------------|
| transactionId      | Identificador exclusivo para a transação dentro do Azure Blockchain Workbench |
| transactionHash    | O hash da transação no razão |
| from               | Identificador exclusivo no razão para a origem da transação |
| para                 | Identificador exclusivo no razão para o destino da transação |

Exemplo de um *eventMessage ContractFunctionInvocation* da Blockchain Workbench:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Mensagem do evento: Ingestão de aplicativos

Contém informações quando um aplicativo é carregado no Workbench, como o nome e a versão do aplicativo carregado.

| Name | Descrição |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Identificador exclusivo para o aplicativo no Azure Blockchain Workbench |
| applicationName | Nome da aplicação |
| applicationDisplayName | Nome a apresentar da aplicação |
| applicationVersion | Versão da aplicação |
| applicationDefinitionLocation | URL em que o arquivo de configuração do aplicativo está localizado |
| contractCodes | Coleção de [códigos de contrato](#contract-code-information) para o aplicativo |
| applicationRoles | Coleção de [funções de aplicativo](#application-role-information) para o aplicativo |
| applicationWorkflows | Coleção de [fluxos de trabalho do aplicativo](#application-workflow-information) para o aplicativo |
| connectionId | Identificador exclusivo para a conexão |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName | **EventMessage** |
| additionalInformation | As informações adicionais fornecidas aqui incluem os Estados de fluxo de trabalho do aplicativo e as informações de transição. |

#### <a name="contract-code-information"></a>Informações de código do contrato

| Name | Descrição |
|------|-------------|
| id | Identificador exclusivo do arquivo de código de contrato dentro do Azure Blockchain Workbench |
| ledgerId | Identificador exclusivo para o razão dentro do Azure Blockchain Workbench |
| location | URL em que o arquivo de código de contrato está localizado |

#### <a name="application-role-information"></a>Informações de função do aplicativo

| Name | Descrição |
|------|-------------|
| id | Identificador exclusivo para a função de aplicativo dentro do Azure Blockchain Workbench |
| name | Nome da função de aplicativo |

#### <a name="application-workflow-information"></a>Informações do fluxo de trabalho do aplicativo

| Name | Descrição |
|------|-------------|
| id | Identificador exclusivo do fluxo de trabalho do aplicativo dentro do Azure Blockchain Workbench |
| name | Nome do fluxo de trabalho do aplicativo |
| displayName | Nome de exibição do fluxo de trabalho do aplicativo |
| funções | Coleção de [funções para o fluxo de trabalho do aplicativo](#workflow-function-information)|
| Européia | Coleção de [Estados para o fluxo de trabalho do aplicativo](#workflow-state-information) |
| properties | [Informações de propriedades do fluxo de trabalho](#workflow-property-information) do aplicativo |

##### <a name="workflow-function-information"></a>Informações da função de fluxo de trabalho

| Name | Descrição |
|------|-------------|
| id | Identificador exclusivo para a função de fluxo de trabalho do aplicativo dentro do Azure Blockchain Workbench |
| name | Nome da função |
| parameters | Parâmetros para a função |

##### <a name="workflow-state-information"></a>Informações de estado do fluxo de trabalho

| Name | Descrição |
|------|-------------|
| name | Nome do estado |
| displayName | Nome de exibição do estado |
| style | Estilo de estado (êxito ou falha) |

##### <a name="workflow-property-information"></a>Informações de Propriedade do fluxo de trabalho

| Name | Descrição |
|------|-------------|
| id | Identificador exclusivo para a propriedade de fluxo de trabalho do aplicativo dentro do Azure Blockchain Workbench |
| name | Nome da propriedade |
| type | Tipo de propriedade |

Exemplo de um *eventMessage ApplicationIngestion* da Blockchain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                                "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                                "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Mensagem do evento: Atribuição de função

Contém informações quando um usuário recebe uma função no Workbench, como quem realizou a atribuição de função e o nome da função e o aplicativo correspondente.

| Name | Descrição |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Identificador exclusivo para o aplicativo no Azure Blockchain Workbench |
| applicationName | Nome da aplicação |
| applicationDisplayName | Nome a apresentar da aplicação |
| applicationVersion | Versão da aplicação |
| applicationRole        | Informações sobre a [função de aplicativo](#roleassignment-application-role) |
| atribuidor               | Informações sobre o [atribuínte](#roleassignment-assigner) |
| destinatário               | Informações sobre o [destinatário](#roleassignment-assignee) |
| connectionId           | Identificador exclusivo para a conexão |
| messageSchemaVersion   | Versão do esquema de mensagens |
| messageName            | **EventMessage** |
| additionalInformation  | Informações adicionais fornecidas |

#### <a name="roleassignment-application-role"></a>Função de aplicativo RoleAssignment

| Name | Descrição |
|------|-------------|
| id | Identificador exclusivo para a função de aplicativo dentro do Azure Blockchain Workbench |
| name | Nome da função de aplicativo |

#### <a name="roleassignment-assigner"></a>RoleAssignment atribuínte

| Name | Descrição |
|------|-------------|
| id | Identificador exclusivo do usuário no Azure Blockchain Workbench |
| type | Tipo de atribuínte |
| chainIdentifier | Identificador exclusivo do usuário no razão |

#### <a name="roleassignment-assignee"></a>RoleAssignment destinatário

| Name | Descrição |
|------|-------------|
| id | Identificador exclusivo do usuário no Azure Blockchain Workbench |
| type | Tipo do destinatário |
| chainIdentifier | Identificador exclusivo do usuário no razão |

Exemplo de um *eventMessage RoleAssignment* da Blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Passos Seguintes

- [Padrões de integração do contrato inteligente](integration-patterns.md)

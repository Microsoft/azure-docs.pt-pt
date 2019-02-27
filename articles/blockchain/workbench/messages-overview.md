---
title: Descrição geral da integração de mensagens do Azure Blockchain Workbench
description: Descrição geral da utilização de mensagens no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 02/21/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 860c00b876427af7395e3c04e0626131c27aca67
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56878086"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>O Azure Blockchain Workbench, integração de mensagens

Além de fornecer uma API REST, o Azure Blockchain Workbench também fornece integração baseados em mensagens. Workbench publica eventos centrada em razão através do Azure Event Grid, permitindo que os consumidores de downstream para ingestão de dados ou tomar medidas com base nestes eventos. Para os clientes que necessitam de sistema de mensagens confiável, o Azure Blockchain Workbench entregam mensagens para um ponto final do Azure Service Bus também.

## <a name="input-apis"></a>APIs de entrada

Se quiser iniciar transações de sistemas externos para criar utilizadores, criar contratos e atualizar contratos, pode utilizar as APIs de entrada de mensagens para executar transações num livro razão. Ver [exemplos de integração de mensagens](https://aka.ms/blockchain-workbench-integration-sample) para obter um exemplo que demonstra as APIs de entrada.

Seguem-se a APIs de entrada está disponível.

### <a name="create-user"></a>Criar utilizador

Cria um novo utilizador.

O pedido requer os seguintes campos:

| **Nome**             | **Descrição**                                      |
|----------------------|------------------------------------------------------|
| requestId            | GUID fornecido pelo cliente                                |
| firstName            | Nome próprio do utilizador                              |
| lastName             | Apelido do utilizador                               |
| emailAddress         | Endereço de e-mail do utilizador                           |
| externalId           | ID de objeto do Azure AD do utilizador                      |
| connectionId         | Identificador exclusivo para a ligação de blockchain |
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

Blockchain Workbench devolve uma resposta com os seguintes campos:

| **Nome**              | **Descrição**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | GUID fornecido pelo cliente |
| userId                | ID do utilizador que foi criado |
| userChainIdentifier   | Endereço do utilizador que foi criado na rede de blockchain. Em Ethereum, o endereço é o usuário **na cadeia** endereço. |
| connectionId          | Identificador exclusivo para a ligação de blockchain|
| messageSchemaVersion  | Versão do esquema de mensagens |
| messageName           | **CreateUserUpdate** |
| status                | Estado do pedido de criação do utilizador.  Se tiver êxito, o valor é **êxito**. Em caso de falha, o valor é **falha**.     |
| additionalInformation | Fornecidas informações adicionais com base no status |

Exemplo com êxito **criar utilizador** resposta do Blockchain Workbench:

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

Se o pedido não teve êxito, os detalhes sobre a falha estão incluídos nas informações adicionais.

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

### <a name="create-contract"></a>Criar o contrato

Cria um novo contrato.

O pedido requer os seguintes campos:

| **Nome**             | **Descrição**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | GUID fornecido pelo cliente |
| userChainIdentifier  | Endereço do utilizador que foi criado na rede de blockchain. Em Ethereum, este endereço é o usuário **na cadeia** endereço. |
| applicationName      | Nome da aplicação |
| versão              | Versão da aplicação. Necessário se tiver várias versões da aplicação ativada. Caso contrário, a versão é opcional. Para obter mais informações sobre o controlo de versões do aplicativo, consulte [controle de versão de aplicação de Azure Blockchain Workbench](version-app.md). |
| workflowName         | Nome do fluxo de trabalho |
| parâmetros           | Entrada de parâmetros para a criação do contrato |
| connectionId         | Identificador exclusivo para a ligação de blockchain |
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

Blockchain Workbench devolve uma resposta com os seguintes campos:

| **Nome**                 | **Descrição**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | GUID fornecido pelo cliente                                                             |
| contractId               | Identificador exclusivo para o contrato de dentro do Azure Blockchain Workbench |
| contractLedgerIdentifier | Endereço do contrato no razão                                            |
| connectionId             | Identificador exclusivo para a ligação de blockchain                               |
| messageSchemaVersion     | Versão do esquema de mensagens                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Estado do pedido de criação do contrato.  Valores possíveis: **Submetido**, **consolidadas**, **falha**.  |
| additionalInformation    | Fornecidas informações adicionais com base no status                              |

Exemplo de um submetido **criar contrato** resposta do Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

Exemplo de um compromisso **criar contrato** resposta do Blockchain Workbench:

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

Se o pedido não teve êxito, os detalhes sobre a falha estão incluídos nas informações adicionais.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Criar uma ação de contrato

Cria uma nova ação de contrato.

O pedido requer os seguintes campos:

| **Nome**                 | **Descrição**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | GUID fornecido pelo cliente |
| userChainIdentifier      | Endereço do utilizador que foi criado na rede de blockchain. Em Ethereum, este endereço é o usuário **na cadeia** endereço. |
| contractLedgerIdentifier | Endereço do contrato no razão |
| versão                  | Versão da aplicação. Necessário se tiver várias versões da aplicação ativada. Caso contrário, a versão é opcional. Para obter mais informações sobre o controlo de versões do aplicativo, consulte [controle de versão de aplicação de Azure Blockchain Workbench](version-app.md). |
| workflowFunctionName     | Nome da função de fluxo de trabalho |
| parâmetros               | Entrada de parâmetros para a criação do contrato |
| connectionId             | Identificador exclusivo para a ligação de blockchain |
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

Blockchain Workbench devolve uma resposta com os seguintes campos:

| **Nome**              | **Descrição**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | GUID fornecido pelo cliente|
| contractId            | Identificador exclusivo para o contrato de dentro do Azure Blockchain Workbench |
| connectionId          | Identificador exclusivo para a ligação de blockchain |
| messageSchemaVersion  | Versão do esquema de mensagens |
| messageName           | **CreateContractActionUpdate** |
| status                | Estado do pedido de ação de contrato. Valores possíveis: **Submetido**, **consolidadas**, **falha**.                         |
| additionalInformation | Fornecidas informações adicionais com base no status |

Exemplo de um submetido **criar uma ação de contrato** resposta do Blockchain Workbench:

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

Exemplo de um compromisso **criar uma ação de contrato** resposta do Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

Se o pedido não teve êxito, os detalhes sobre a falha estão incluídos nas informações adicionais.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Códigos de erro de API entrados e de mensagens

**Código de erro 4000: Erro de pedido incorreto**
- ConnectionId inválido
- Falhada de desserialização de CreateUserRequest
- Falhada de desserialização de CreateContractRequest
- Falhada de desserialização de CreateContractActionRequest
- Aplicação {identificada pelo nome da aplicação} não existe
- Aplicação {identificada pelo nome da aplicação} não tem o fluxo de trabalho
- UserChainIdentifier não existe
- Contrato {identificado pelo identificador de contabilidade} não existe
- Contrato {identificado pelo identificador de contabilidade} não tem a função {nome da função de fluxo de trabalho}
- UserChainIdentifier não existe

**Código de erro 4090: Erro de conflito**
- O utilizador já existe
- Contrato já existe
- Ação de contrato já existe

**Código de erro 5000: Erro de servidor interno**
- Mensagens de exceção

## <a name="event-notifications"></a>Notificações de eventos

Notificações de eventos podem ser utilizadas para notificar os utilizadores e a jusante sistemas de eventos que ocorrem no Blockchain Workbench e a rede de blockchain que está ligado a. Notificações de eventos podem ser consumidas diretamente no código ou podem ser utilizadas com ferramentas como o Logic Apps e Flow para acionar o fluxo de dados para sistemas a jusante.

Ver [referência de mensagem de notificação](#notification-message-reference) para obter detalhes de várias mensagens que podem ser recebidas.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Consumo de eventos do Event Grid com as funções do Azure

Se um utilizador quiser utilizar o Event Grid para ser notificado sobre eventos que ocorrem na bancada de trabalho de Blockchain, pode consumir eventos do Event Grid, utilizando as funções do Azure.

1. Criar uma **aplicação de funções do Azure** no portal do Azure.
2. Crie uma nova função.
3. Localize o modelo para o Event Grid. Código de modelo básico para ler a mensagem é mostrado. Modificar o código conforme necessário.
4. Guarde a função. 
5. Selecione o Event Grid do grupo de recursos de Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Consumo de eventos do Event Grid com o Logic Apps

1. Criar uma nova **aplicação lógica do Azure** no portal do Azure.
2. Ao abrir a aplicação de lógica do Azure no portal, será solicitado a selecionar um acionador. Selecione **Azure Event Grid – quando ocorre um evento de recurso**.
3. Quando o designer de fluxo de trabalho é exibido, será solicitado para iniciar sessão.
4. Selecione a subscrição. Recursos como **Microsoft.EventGrid.Topics**. Selecione o **nome do recurso** do nome do recurso do grupo de recursos do Azure Blockchain Workbench.
5. Selecione o Event Grid do grupo de recursos de Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Utilizar tópicos do Service Bus para notificações

Tópicos do Service Bus pode ser utilizados para notificar os utilizadores sobre eventos que ocorrem na bancada de trabalho de Blockchain. 

1. Navegue para o Service Bus dentro do grupo de recursos a Bancada de trabalho.
2. Selecione **tópicos**.
3. Selecione **saída tópico**.
4. Crie uma nova subscrição a esse tópico. Obter uma chave para o mesmo.
5. Crie um programa, que subscreve eventos desta subscrição.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Consumir mensagens do Service Bus com o Logic Apps

1. Criar uma nova **aplicação lógica do Azure** no portal do Azure.
2. Ao abrir a aplicação de lógica do Azure no portal, será solicitado a selecionar um acionador. Tipo **do Service Bus** na caixa de pesquisa e selecione o acionador apropriado para o tipo de interação que pretende ter com o Service Bus. Por exemplo, **do Service Bus - quando uma mensagem é recebida numa subscrição de tópico (concluir automaticamente)**.
3. Quando o designer de fluxo de trabalho é exibido, especifique as informações de ligação para o Service Bus.
4. Selecione a sua subscrição e especifique o tópico da **Bancada de trabalho externo**.
5. Desenvolva a lógica para a sua aplicação que utiliza a mensagem deste acionador.

## <a name="notification-message-reference"></a>Referência de mensagem de notificação

Consoante a **messageName**, as mensagens de notificação de ter um dos seguintes tipos de mensagem.

### <a name="block-message"></a>Mensagem de bloqueio

Contém informações sobre blocos individuais. O *BlockMessage* inclui uma seção com informações de nível de bloco e uma seção com informações de transações.

| Name | Descrição |
|------|-------------|
| bloquear | Contém [bloquear informações](#block-information) |
| transações | Contém uma coleção [informações sobre transações](#transaction-information) para o bloco |
| connectionId | Identificador exclusivo para a ligação |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName | **BlockMessage** |
| additionalInformation | Informações adicionais fornecidas |

#### <a name="block-information"></a>Informações de bloco

| Name              | Descrição |
|-------------------|-------------|
| blockId           | Identificador exclusivo para o bloco de dentro do Azure Blockchain Workbench |
| blockNumber       | Identificador exclusivo para um bloco no razão |
| blockHash         | O hash do bloco |
| previousBlockHash | O hash do bloco anterior |
| blockTimestamp    | O carimbo de hora do bloco |

#### <a name="transaction-information"></a>Informações de transações

| Name               | Descrição |
|--------------------|-------------|
| transactionId      | Identificador exclusivo para a transação dentro do Azure Blockchain Workbench |
| transactionHash    | O hash da transação no razão |
| de               | Identificador exclusivo no razão para a origem de transação |
| para                 | Identificador exclusivo no razão para o destino de transação |
| provisioningStatus | Identifica o estado atual do processo de aprovisionamento para a transação. Os valores possíveis são: </br>0 – a transação foi criada pela API na base de dados</br>1 – a transação foi enviada ao razão</br>2 – a transação foi com êxito confirmada ao razão</br>3 ou 4 - a transação falhou ser confirmada ao razão</br>5 - a transação foi com êxito consolidada ao razão |

Exemplo de um *BlockMessage* da bancada de trabalho de Blockchain:

``` json
{
    "block": {
        "blockId": 123
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235
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

Contém informações sobre um contrato. A mensagem inclui uma seção com propriedades de contrato e uma seção com informações de transações. Todas as transações que modificaram o contrato para o bloco específico são incluídas na secção transação.

| Name | Descrição |
|------|-------------|
| blockId | Identificador exclusivo para o bloco de dentro do Azure Blockchain Workbench |
| blockHash | Hash do bloco |
| modifyingTransactions | [Transações que modificou](#modifying-transaction-information) o contrato |
| contractId | Identificador exclusivo para o contrato de dentro do Azure Blockchain Workbench |
| contractLedgerIdentifier | Identificador exclusivo para o contrato no razão |
| contractProperties | [Propriedades do contrato](#contract-properties) |
| isNewContract | Indica se é ou não esse contrato foi recentemente criado. Os valores possíveis são: true: este contrato foi um novo contrato criado. FALSE: esse contrato é uma atualização de contrato. |
| connectionId | Identificador exclusivo para a ligação |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName | **ContractMessage** |
| additionalInformation | Informações adicionais fornecidas |

#### <a name="modifying-transaction-information"></a>Modificar informações de transações

| Name               | Descrição |
|--------------------|-------------|
| transactionId | Identificador exclusivo para a transação dentro do Azure Blockchain Workbench |
| transactionHash | O hash da transação no razão |
| de | Identificador exclusivo no razão para a origem de transação |
| para | Identificador exclusivo no razão para o destino de transação |

#### <a name="contract-properties"></a>Propriedades do contrato

| Name               | Descrição |
|--------------------|-------------|
| workflowPropertyId | Identificador exclusivo para a propriedade de fluxo de trabalho dentro do Azure Blockchain Workbench |
| nome | Nome da propriedade de fluxo de trabalho |
| valor | Valor da propriedade de fluxo de trabalho |

Exemplo de um *ContractMessage* da bancada de trabalho de Blockchain:

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

### <a name="event-message-contract-function-invocation"></a>Mensagem de evento: Invocação de função do contrato

Contém informações quando uma função de contrato é invocada, como o nome da função, entrada de parâmetros e o autor da chamada da função.

| Name | Descrição |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| chamador                      | [Informações do chamador](#caller-information) |
| contractId                  | Identificador exclusivo para o contrato de dentro do Azure Blockchain Workbench |
| contractLedgerIdentifier    | Identificador exclusivo para o contrato no razão |
| functionName                | Nome da função |
| parâmetros                  | [Informações de parâmetro](#parameter-information) |
| Transação                 | Informações de transações |
| inTransactionSequenceNumber | O número de sequência da transação no bloco |
| connectionId                | Identificador exclusivo para a ligação |
| messageSchemaVersion        | Versão do esquema de mensagens |
| messageName                 | **EventMessage** |
| additionalInformation       | Informações adicionais fornecidas |

#### <a name="caller-information"></a>Informações do chamador

| Name | Descrição |
|------|-------------|
| tipo | Tipo do chamador, como um utilizador ou um contrato |
| ID | Identificador exclusivo para o chamador dentro do Azure Blockchain Workbench |
| ledgerIdentifier | Identificador exclusivo para o autor da chamada no razão |

#### <a name="parameter-information"></a>Informações de parâmetro

| Name | Descrição |
|------|-------------|
| nome | Nome do parâmetro |
| valor | Valor do parâmetro |

#### <a name="event-message-transaction-information"></a>Informações de transações de mensagem de evento

| Name               | Descrição |
|--------------------|-------------|
| transactionId      | Identificador exclusivo para a transação dentro do Azure Blockchain Workbench |
| transactionHash    | O hash da transação no razão |
| de               | Identificador exclusivo no razão para a origem de transação |
| para                 | Identificador exclusivo no razão para o destino de transação |

Exemplo de uma *EventMessage ContractFunctionInvocation* da bancada de trabalho de Blockchain:

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

### <a name="event-message-application-ingestion"></a>Mensagem de evento: Ingestão de aplicação

Contém informações quando um aplicativo é carregado para a Bancada de trabalho, como o nome e versão da aplicação carregada.

| Name | Descrição |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Identificador exclusivo para a aplicação dentro do Azure Blockchain Workbench |
| applicationName | Nome da aplicação |
| applicationDisplayName | Nome a apresentar da aplicação |
| applicationVersion | Versão da aplicação |
| applicationDefinitionLocation | URL onde está localizado o ficheiro de configuração de aplicação |
| contractCodes | Coleção de [códigos de contrato](#contract-code-information) para a aplicação |
| applicationRoles | Coleção de [funções de aplicação](#application-role-information) para a aplicação |
| applicationWorkflows | Coleção de [fluxos de trabalho de aplicação](#application-workflow-information) para a aplicação |
| connectionId | Identificador exclusivo para a ligação |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName | **EventMessage** |
| additionalInformation | Informações adicionais fornecidas aqui incluem os Estados de fluxo de trabalho do aplicativo e as informações de transição. |

#### <a name="contract-code-information"></a>Informações de código do contrato

| Name | Descrição |
|------|-------------|
| ID | Identificador exclusivo para o arquivo de código de contrato dentro do Azure Blockchain Workbench |
| ledgerId | Identificador exclusivo para a razão de dentro do Azure Blockchain Workbench |
| localização | URL onde está localizado o ficheiro de código do contrato |

#### <a name="application-role-information"></a>Informações de função de aplicação

| Name | Descrição |
|------|-------------|
| ID | Identificador exclusivo para a função de aplicação dentro do Azure Blockchain Workbench |
| nome | Nome da função de aplicação |

#### <a name="application-workflow-information"></a>Informações de fluxo de trabalho de aplicação

| Name | Descrição |
|------|-------------|
| ID | Identificador exclusivo para o fluxo de trabalho de aplicação dentro do Azure Blockchain Workbench |
| nome | Nome do fluxo de trabalho de aplicação |
| displayName | Nome de exibição do fluxo de trabalho de aplicação |
| functions | Coleção de [funções para o fluxo de trabalho de aplicação](#workflow-function-information)|
| Estados | Coleção de [Estados para o fluxo de trabalho de aplicação](#workflow-state-information) |
| propriedades | Aplicação [informações de propriedades do fluxo de trabalho](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Informações de função de fluxo de trabalho

| Name | Descrição |
|------|-------------|
| ID | Identificador exclusivo para a função de fluxo de trabalho de aplicação dentro do Azure Blockchain Workbench |
| nome | Nome da função |
| parâmetros | Parâmetros da função |

##### <a name="workflow-state-information"></a>Informações de estado do fluxo de trabalho

| Name | Descrição |
|------|-------------|
| nome | Nome do estado |
| displayName | Nome a apresentar de estado |
| Estilo | Estilo de estado (êxito ou falha) |

##### <a name="workflow-property-information"></a>Informações de propriedade do fluxo de trabalho

| Name | Descrição |
|------|-------------|
| ID | Identificador exclusivo para a propriedade de fluxo de trabalho de aplicação dentro do Azure Blockchain Workbench |
| nome | Nome da propriedade |
| tipo | Tipo de propriedade |

Exemplo de uma *EventMessage ApplicationIngestion* da bancada de trabalho de Blockchain:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationDefinitionLocation": "http://url"
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
    ]
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
                            "DisplayName": "Accept"
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

### <a name="event-message-role-assignment"></a>Mensagem de evento: Atribuição de função

Contém informações quando é atribuída uma função na bancada de trabalho, tais como quem realizou a atribuição de função e o nome da função e da aplicação correspondente um utilizador.

| Name | Descrição |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Identificador exclusivo para a aplicação dentro do Azure Blockchain Workbench |
| applicationName | Nome da aplicação |
| applicationDisplayName | Nome a apresentar da aplicação |
| applicationVersion | Versão da aplicação |
| applicationRole        | Informações sobre o [função de aplicação](#roleassignment-application-role) |
| mandatário               | Informações sobre o [mandatário](#roleassignment-assigner) |
| detentor               | Informações sobre o [detentor](#roleassignment-assignee) |
| connectionId           | Identificador exclusivo para a ligação |
| messageSchemaVersion   | Versão do esquema de mensagens |
| messageName            | **EventMessage** |
| additionalInformation  | Informações adicionais fornecidas |

#### <a name="roleassignment-application-role"></a>Função de aplicação de RoleAssignment

| Name | Descrição |
|------|-------------|
| ID | Identificador exclusivo para a função de aplicação dentro do Azure Blockchain Workbench |
| nome | Nome da função de aplicação |

#### <a name="roleassignment-assigner"></a>RoleAssignment mandatário

| Name | Descrição |
|------|-------------|
| ID | Identificador exclusivo do utilizador dentro do Azure Blockchain Workbench |
| tipo | Tipo do mandatário |
| chainIdentifier | Identificador exclusivo do utilizador no razão |

#### <a name="roleassignment-assignee"></a>Detentor de RoleAssignment

| Name | Descrição |
|------|-------------|
| ID | Identificador exclusivo do utilizador dentro do Azure Blockchain Workbench |
| tipo | Tipo do detentor |
| chainIdentifier | Identificador exclusivo do utilizador no razão |

Exemplo de uma *EventMessage RoleAssignment* da bancada de trabalho de Blockchain:

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

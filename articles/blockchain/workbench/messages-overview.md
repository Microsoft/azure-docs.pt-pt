---
title: Use mensagens para integrar com a Azure Blockchain Workbench
description: Visão geral da utilização de mensagens para integrar a pré-visualização do workbench Azure Blockchain com outros sistemas.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "74324498"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integração de mensagens Azure Blockchain Workbench

Além de fornecer uma API REST, a Azure Blockchain Workbench também fornece integração baseada em mensagens. Workbench publica eventos centrados em livros através da Azure Event Grid, permitindo aos consumidores a jusante ingerir dados ou tomar medidas com base nestes eventos. Para os clientes que necessitam de mensagens fiáveis, a Azure Blockchain Workbench também entrega mensagens a um ponto final do Azure Service Bus.

## <a name="input-apis"></a>APIs de entrada

Se pretender iniciar transações de sistemas externos para criar utilizadores, criar contratos e atualizar contratos, pode utilizar apis de entrada de mensagens para realizar transações num livro-razão. Consulte [amostras de integração de mensagens](https://aka.ms/blockchain-workbench-integration-sample) para uma amostra que demonstre APIs de entrada.

Seguem-se as APIs de entrada atualmente disponíveis.

### <a name="create-user"></a>Criar utilizador

Cria um novo utilizador.

O pedido requer os seguintes campos:

| **Nome**             | **Descrição**                                      |
|----------------------|------------------------------------------------------|
| requestId            | Cliente fornecido GUID                                |
| nomePróprio            | Primeiro nome do utilizador                              |
| apelido             | Último nome do utilizador                               |
| e-mailAddress         | Endereço de e-mail do utilizador                           |
| externalId           | ID do objeto AD Azure do utilizador                      |
| connectionId         | Identificador único para a ligação blockchain |
| mensagemSchemaVersão | Versão de esquema de mensagens                            |
| mensagemName          | **CreateUserRequest**                               |

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
| requestId             | Cliente fornecido GUID |
| userId                | ID do utilizador que foi criado |
| utilizadorChainIdentifier   | Endereço do utilizador que foi criado na rede blockchain. No Ethereum, o endereço é o endereço **em cadeia** do utilizador. |
| connectionId          | Identificador único para a ligação blockchain|
| mensagemSchemaVersão  | Versão de esquema de mensagens |
| mensagemName           | **CreateUserUpdate** |
| status                | Estado do pedido de criação de utilizadores.  Se for bem sucedido, o valor é **o Sucesso.** No que diz sobre a falha, o valor é **falha.**     |
| informação adicional | Informações adicionais fornecidas com base no estado |

Exemplo bem sucedido criar resposta do **utilizador** a partir de Blockchain Workbench:

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

Se o pedido não tiver sido bem sucedido, os detalhes sobre a falha estão incluídos em informações adicionais.

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

O pedido requer os seguintes campos:

| **Nome**             | **Descrição**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | Cliente fornecido GUID |
| utilizadorChainIdentifier  | Endereço do utilizador que foi criado na rede blockchain. No Ethereum, este endereço é do utilizador no endereço **em cadeia.** |
| applicationName      | Nome do pedido |
| versão              | Versão da aplicação. Necessário se tiver várias versões da aplicação ativadas. Caso contrário, a versão é opcional. Para obter mais informações sobre a versão da aplicação, consulte [a versão da aplicação Azure Blockchain Workbench](version-app.md). |
| fluxo de trabalhoName         | Nome do fluxo de trabalho |
| parâmetros           | Entrada de parâmetros para a criação de contratos |
| connectionId         | Identificador único para a ligação blockchain |
| mensagemSchemaVersão | Versão de esquema de mensagens |
| mensagemName          | **CriarRequest DeConact** |

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
| requestId                | Cliente fornecido GUID                                                             |
| contractId               | Identificador único para o contrato dentro da Azure Blockchain Workbench |
| contractLedgerIdentifier | Endereço do contrato no livro-razão                                            |
| connectionId             | Identificador único para a ligação blockchain                               |
| mensagemSchemaVersão     | Versão de esquema de mensagens                                                         |
| mensagemName              | **Criar EncontroUpdate**                                                      |
| status                   | Estado do pedido de criação de contrato.  Valores possíveis: **Submetido,** **Comprometido,** **Falha**.  |
| informação adicional    | Informações adicionais fornecidas com base no estado                              |

Exemplo de uma resposta de contrato de **criação** submetida da Blockchain Workbench:

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

Exemplo de uma resposta **contratual** empenhada da Blockchain Workbench:

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

Se o pedido não tiver sido bem sucedido, os detalhes sobre a falha estão incluídos em informações adicionais.

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

### <a name="create-contract-action"></a>Criar ação contratual

Cria uma nova ação contratual.

O pedido requer os seguintes campos:

| **Nome**                 | **Descrição**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | Cliente fornecido GUID |
| utilizadorChainIdentifier      | Endereço do utilizador que foi criado na rede blockchain. No Ethereum, este endereço é do utilizador no endereço **em cadeia.** |
| contractLedgerIdentifier | Endereço do contrato no livro-razão |
| versão                  | Versão da aplicação. Necessário se tiver várias versões da aplicação ativadas. Caso contrário, a versão é opcional. Para obter mais informações sobre a versão da aplicação, consulte [a versão da aplicação Azure Blockchain Workbench](version-app.md). |
| workflowSucaçãoName     | Nome da função de fluxo de trabalho |
| parâmetros               | Entrada de parâmetros para a criação de contratos |
| connectionId             | Identificador único para a ligação blockchain |
| mensagemSchemaVersão     | Versão de esquema de mensagens |
| mensagemName              | **Criar ContraactActionRequest** |

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
| requestId             | Cliente fornecido GUID|
| contractId            | Identificador único para o contrato dentro da Azure Blockchain Workbench |
| connectionId          | Identificador único para a ligação blockchain |
| mensagemSchemaVersão  | Versão de esquema de mensagens |
| mensagemName           | **CreateContractActionUpdate** |
| status                | Estado do pedido de ação contratual. Valores possíveis: **Submetido,** **Comprometido,** **Falha**.                         |
| informação adicional | Informações adicionais fornecidas com base no estado |

Exemplo de uma resposta de **ação contratual** submetida da Blockchain Workbench:

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

Exemplo de uma resposta de **ação contratual** empenhada da Blockchain Workbench:

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

Se o pedido não tiver sido bem sucedido, os detalhes sobre a falha estão incluídos em informações adicionais.

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

### <a name="input-api-error-codes-and-messages"></a>Inserir códigos e mensagens de erro da API

**Código de erro 4000: Erro de mau pedido**
- Conexão inválidaId
- CreateUserRequest deserialização falhou
- CreateContractRequest deserialização falhou
- CreateContractActionRequest deserialização falhou
- Aplicação {identificada pelo nome de aplicação} não existe
- Aplicação {identificada pelo nome da aplicação} não tem fluxo de trabalho
- UserChainIdentifier não existe
- Contrato {identificado por identificador de contabilidade} não existe
- Contrato {identificado pelo identificador de contabilidade} não tem função {nome de função de fluxo de trabalho}
- UserChainIdentifier não existe

**Error code 4090: Erro de conflito**
- O utilizador já existe
- Contrato já existe
- Ação contratual já existe

**Código de erro 5000: Erro interno do servidor**
- Mensagens de exceção

## <a name="event-notifications"></a>Notificações de eventos

As notificações de eventos podem ser usadas para notificar os utilizadores e sistemas a jusante de eventos que ocorrem na Blockchain Workbench e na rede blockchain a que está ligada. As notificações de eventos podem ser consumidas diretamente em código ou usadas com ferramentas como As Aplicações Lógicas e o Flow para desencadear o fluxo de dados para sistemas a jusante.

Consulte [a referência de mensagem](#notification-message-reference) de Notificação para obter mais detalhes de várias mensagens que podem ser recebidas.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Eventos de grelha de eventos consumistas com funções Azure

Se um utilizador quiser utilizar a Grade de Eventos para ser notificado sobre eventos que ocorram na Bancada Blockchain, pode consumir eventos a partir da Grade de Eventos utilizando funções Azure.

1. Crie uma **App de Função Azure** no portal Azure.
2. Criar uma nova função.
3. Localize o modelo para a grelha de eventos. O código de modelo básico para a leitura da mensagem é mostrado. Modifique o código conforme necessário.
4. Salve a Função. 
5. Selecione a Grelha de Eventos do grupo de recursos blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Eventos de grelha de eventos consumistas com apps lógicas

1. Crie uma nova **App Azure Logic** no portal Azure.
2. Ao abrir a App Azure Logic no portal, será solicitado que selecione um gatilho. Selecione **Azure Event Grid -- Quando ocorrer um evento de recurso**.
3. Quando o designer de fluxo de trabalho for apresentado, ser-lhe-á solicitado que faça o seu sposição.
4. Selecione a Subscrição. Recurso como **Microsoft.EventGrid.Topics**. Selecione o Nome de **Recurso** a partir do nome do recurso do grupo de recursos Azure Blockchain Workbench.
5. Selecione a Grelha de Eventos do grupo de recursos blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Utilização de tópicos de autocarro de serviço para notificações

Os tópicos de autocarro de serviço podem ser usados para notificar os utilizadores sobre eventos que ocorrem em Blockchain Workbench. 

1. Navegue pelo Autocarro de Serviço dentro do grupo de recursos da Workbench.
2. Selecione **Tópicos**.
3. Selecione **o tópico de** saída.
4. Crie uma nova subscrição deste tópico. Obter uma chave para isto.
5. Crie um programa que subscreva eventos desta subscrição.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Mensagens de autocarro de serviço consumindo com aplicações lógicas

1. Crie uma nova **App Azure Logic** no portal Azure.
2. Ao abrir a App Azure Logic no portal, será solicitado que selecione um gatilho. Digite **o Service Bus** na caixa de pesquisa e selecione o gatilho apropriado para o tipo de interação que pretende ter com o Service Bus. Por exemplo, **Service Bus -- Quando uma mensagem é recebida numa subscrição de tópico (completada automaticamente)**.
3. Quando o designer de fluxo de trabalho for apresentado, especifique as informações de ligação para o Service Bus.
4. Selecione a sua subscrição e especifique o tópico do **workbench-external**.
5. Desenvolva a lógica para a sua aplicação que utiliza a mensagem a partir deste gatilho.

## <a name="notification-message-reference"></a>Referência de mensagem de notificação

Dependendo da **mensagem No nome,** as mensagens de notificação têm um dos seguintes tipos de mensagens.

### <a name="block-message"></a>Mensagem de bloqueio

Contém informações sobre blocos individuais. O *BlockMessage* inclui uma secção com informações de nível de bloco e uma secção com informações de transação.

| Nome | Descrição |
|------|-------------|
| bloquear | Contém [informações de blocos](#block-information) |
| transações | Contém uma [informação de transação](#transaction-information) de recolha para o bloco |
| connectionId | Identificador único para a ligação |
| mensagemSchemaVersão | Versão de esquema de mensagens |
| mensagemName | **BlockMessage** |
| informação adicional | Informações adicionais fornecidas |

#### <a name="block-information"></a>Informação de blocos

| Nome              | Descrição |
|-------------------|-------------|
| blockId           | Identificador único para o bloco dentro da bancada Azure Blockchain |
| blockNumber       | Identificador único para um bloco no livro-razão |
| blockHash         | O haxixe do bloco |
| anteriorBlockHash | O haxixe do bloco anterior |
| blockTimestamp    | A hora do bloco |

#### <a name="transaction-information"></a>Informações sobre transações

| Nome               | Descrição |
|--------------------|-------------|
| transactionId      | Identificador único para a transação dentro da Azure Blockchain Workbench |
| transactionHash    | O haxixe da transação no livro-razão |
| De               | Identificador único no livro-razão para a origem da transação |
| para                 | Identificador único no livro-razão para o destino de transação |
| provisionamentoStatus | Identifica o estado atual do processo de provisionamento para a transação. Os valores possíveis são: </br>0 – A transação foi criada pela API na base de dados</br>1 - A transação foi enviada para o livro-razão</br>2 - A transação foi comprometida com sucesso ao livro-razão</br>3 ou 4 - A transação não foi comprometida com o livro-razão</br>5 - A transação foi comprometida com sucesso com o livro-razão |

Exemplo de um *BlockMessage* da Blockchain Workbench:

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

### <a name="contract-message"></a>Mensagem do contrato

Contém informações sobre um contrato. A mensagem inclui uma secção com propriedades contratuais e uma secção com informações de transação. Todas as transações que tenham modificado o contrato para o bloco em particular estão incluídas na secção de transações.

| Nome | Descrição |
|------|-------------|
| blockId | Identificador único para o bloco dentro da bancada Azure Blockchain |
| blockHash | Haxixe do quarteirão |
| modificação das transferências | [Transações que modificaram](#modifying-transaction-information) o contrato |
| contractId | Identificador único para o contrato dentro da Azure Blockchain Workbench |
| contractLedgerIdentifier | Identificador único para o contrato no livro de contabilidade |
| contratoProperties | [Propriedades do contrato](#contract-properties) |
| isNewContract | Indica se este contrato foi ou não criado recentemente. Os valores possíveis são: verdadeiros: este contrato foi um novo contrato criado. falso: este contrato é uma atualização do contrato. |
| connectionId | Identificador único para a ligação |
| mensagemSchemaVersão | Versão de esquema de mensagens |
| mensagemName | **ContractMessage** |
| informação adicional | Informações adicionais fornecidas |

#### <a name="modifying-transaction-information"></a>Modificar informações de transações

| Nome               | Descrição |
|--------------------|-------------|
| transactionId | Identificador único para a transação dentro da Azure Blockchain Workbench |
| transactionHash | O haxixe da transação no livro-razão |
| De | Identificador único no livro-razão para a origem da transação |
| para | Identificador único no livro-razão para o destino de transação |

#### <a name="contract-properties"></a>Propriedades contratuais

| Nome               | Descrição |
|--------------------|-------------|
| fluxo de trabalhoPropertyId | Identificador único para a propriedade workflow dentro da bancada Azure Blockchain |
| name | Nome da propriedade do fluxo de trabalho |
| valor | Valor da propriedade de fluxo de trabalho |

Exemplo de um *ContractMessage* da Blockchain Workbench:

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

### <a name="event-message-contract-function-invocation"></a>Mensagem do evento: Invocação da função do contrato

Contém informações quando uma função contratual é invocada, como o nome da função, a entrada de parâmetros e o chamador da função.

| Nome | Descrição |
|------|-------------|
| nome de evento                   | **ContractFunctionInvocation** |
| chamador                      | [Informações de chamadas](#caller-information) |
| contractId                  | Identificador único para o contrato dentro da Azure Blockchain Workbench |
| contractLedgerIdentifier    | Identificador único para o contrato no livro de contabilidade |
| funçãoName                | Nome da função |
| parâmetros                  | [Informações de parâmetros](#parameter-information) |
| transação                 | Informações sobre transações |
| inTransactionSequenceNumber | O número de sequência da transação no bloco |
| connectionId                | Identificador único para a ligação |
| mensagemSchemaVersão        | Versão de esquema de mensagens |
| mensagemName                 | **EventoSMessage** |
| informação adicional       | Informações adicionais fornecidas |

#### <a name="caller-information"></a>Informações de chamadas

| Nome | Descrição |
|------|-------------|
| tipo | Tipo de chamador, como um utilizador ou um contrato |
| ID | Identificador único para o chamador dentro da bancada Azure Blockchain Workbench |
| ledgerIdentifier | Identificador único para quem ligou no livro-razão |

#### <a name="parameter-information"></a>Informações de parâmetros

| Nome | Descrição |
|------|-------------|
| name | Nome do parâmetro |
| valor | Valor do parâmetro |

#### <a name="event-message-transaction-information"></a>Informações sobre transações de mensagens de evento

| Nome               | Descrição |
|--------------------|-------------|
| transactionId      | Identificador único para a transação dentro da Azure Blockchain Workbench |
| transactionHash    | O haxixe da transação no livro-razão |
| De               | Identificador único no livro-razão para a origem da transação |
| para                 | Identificador único no livro-razão para o destino de transação |

Exemplo de um *EventMessage ContractFunctionInvocation* da Blockchain Workbench:

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

### <a name="event-message-application-ingestion"></a>Mensagem do evento: Ingestão de aplicações

Contém informações quando uma aplicação é enviada para workbench, como o nome e a versão da aplicação carregada.

| Nome | Descrição |
|------|-------------|
| nome de evento | **Integração de Aplicações** |
| applicationId | Identificador único para a aplicação dentro da Azure Blockchain Workbench |
| applicationName | Nome da aplicação |
| aplicaçãoDisplayName | Nome do visor da aplicação |
| aplicaçãoVersão | Versão de aplicação |
| aplicaçãoDefinitionLocation | URL onde o ficheiro de configuração da aplicação está localizado |
| contractCodes | Recolha de [códigos contratuais](#contract-code-information) para a aplicação |
| aplicaçõesRoles | Recolha de [funções](#application-role-information) de candidatura para a aplicação |
| fluxos de trabalho de aplicação | Recolha de fluxos de trabalho de [aplicação](#application-workflow-information) para a aplicação |
| connectionId | Identificador único para a ligação |
| mensagemSchemaVersão | Versão de esquema de mensagens |
| mensagemName | **EventoSMessage** |
| informação adicional | As informações adicionais aqui fornecidas incluem os estados de trabalho da aplicação e as informações de transição. |

#### <a name="contract-code-information"></a>Informações sobre códigos contratuais

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para o ficheiro de código do contrato dentro da Azure Blockchain Workbench |
| ledgerId | Identificador único para o livro-razão dentro da Azure Blockchain Workbench |
| localização | URL onde o ficheiro de código do contrato está localizado |

#### <a name="application-role-information"></a>Informação sobre funções de candidatura

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para o papel de aplicação dentro da Azure Blockchain Workbench |
| name | Nome da função de candidatura |

#### <a name="application-workflow-information"></a>Informações sobre fluxos de trabalho de aplicações

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para o fluxo de trabalho da aplicação dentro da bancada Azure Blockchain |
| name | Nome do fluxo de trabalho da aplicação |
| displayName | Nome de exibição do fluxo de trabalho da aplicação |
| funções | Recolha de [funções para o fluxo de trabalho da aplicação](#workflow-function-information)|
| estados | Recolha de [estados para o fluxo de trabalho de aplicação](#workflow-state-information) |
| propriedades | Informações sobre propriedades de fluxo de trabalho de [aplicações](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Informação da função de fluxo de trabalho

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para a função de fluxo de trabalho da aplicação dentro da bancada Azure Blockchain Workbench |
| name | Nome da função |
| parâmetros | Parâmetros para a função |

##### <a name="workflow-state-information"></a>Informações do estado do fluxo de trabalho

| Nome | Descrição |
|------|-------------|
| name | Nome do Estado |
| displayName | Nome de exposição do estado |
| estilo | Estilo de Estado (sucesso ou fracasso) |

##### <a name="workflow-property-information"></a>Informações sobre propriedade de fluxo de trabalho

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para a propriedade de fluxo de trabalho de aplicação dentro da Bancada Azure Blockchain |
| name | Nome da propriedade |
| tipo | Tipo de propriedade |

Exemplo de uma *Inscrição de Aplicação EventMessage* da Bancada Blockchain:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
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

### <a name="event-message-role-assignment"></a>Mensagem do evento: Atribuição de funções

Contém informações quando um utilizador é atribuído a uma função no Workbench, como quem executou a atribuição de funções e o nome da função e da aplicação correspondente.

| Nome | Descrição |
|------|-------------|
| nome de evento | **Assinatura de Papéis** |
| applicationId | Identificador único para a aplicação dentro da Azure Blockchain Workbench |
| applicationName | Nome da aplicação |
| aplicaçãoDisplayName | Nome do visor da aplicação |
| aplicaçãoVersão | Versão de aplicação |
| applicationRole        | Informação sobre a [função de candidatura](#roleassignment-application-role) |
| assigner               | Informação sobre o [cedente](#roleassignment-assigner) |
| destinatário               | Informação sobre o [cessionário](#roleassignment-assignee) |
| connectionId           | Identificador único para a ligação |
| mensagemSchemaVersão   | Versão de esquema de mensagens |
| mensagemName            | **EventoSMessage** |
| informação adicional  | Informações adicionais fornecidas |

#### <a name="roleassignment-application-role"></a>FunÇão PapelAtribuição de candidatura

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para o papel de aplicação dentro da Azure Blockchain Workbench |
| name | Nome da função de candidatura |

#### <a name="roleassignment-assigner"></a>Atribuição de funções

| Nome | Descrição |
|------|-------------|
| ID | Identificador único do utilizador dentro da bancada Azure Blockchain |
| tipo | Tipo do cedente |
| chainIdentifier | Identificador único do utilizador no livro-razão |

#### <a name="roleassignment-assignee"></a>Atribuição de funções

| Nome | Descrição |
|------|-------------|
| ID | Identificador único do utilizador dentro da bancada Azure Blockchain |
| tipo | Tipo do cessionário |
| chainIdentifier | Identificador único do utilizador no livro-razão |

Exemplo de um *EventMessage RoleAssignment* da Blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
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

## <a name="next-steps"></a>Passos seguintes

- [Padrões de integração de contratos inteligentes](integration-patterns.md)

---
title: Use mensagens para integrar com a Bancada de Trabalho Azure Blockchain
description: Visão geral da utilização de mensagens para integrar a Pré-visualização da bancada azure Blockchain com outros sistemas.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324498"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integração de mensagens azure Blockchain Workbench

Além de fornecer uma API REST, a Azure Blockchain Workbench também fornece integração baseada em mensagens. A Workbench publica eventos centrados em livros através da Rede de Eventos Azure, permitindo aos consumidores a jusante ingerir dados ou tomar medidas com base nestes eventos. Para os clientes que necessitam de mensagens fiáveis, a Azure Blockchain Workbench também entrega mensagens a um ponto final do Azure Service Bus.

## <a name="input-apis"></a>ApIs de entrada

Se pretender iniciar transações de sistemas externos para criar utilizadores, criar contratos e atualizar contratos, pode utilizar APIs de entrada de mensagens para efetuar transações num livro-razão. Consulte amostras de [integração de mensagens](https://aka.ms/blockchain-workbench-integration-sample) para obter uma amostra que demonstre apis de entrada.

Seguem-se as APIs de entrada atualmente disponíveis.

### <a name="create-user"></a>Criar utilizador

Cria um novo utilizador.

O pedido requer os seguintes campos:

| **Nome**             | **Descrição**                                      |
|----------------------|------------------------------------------------------|
| requestId            | Cliente fornecido GUID                                |
| nomePróprio            | Primeiro nome do utilizador                              |
| apelido             | Apelido do utilizador                               |
| e-mailAddress         | Endereço de e-mail do utilizador                           |
| id externo           | Id de objeto azure AD do utilizador                      |
| conexãoId         | Identificador único para a ligação blockchain |
| mensagemSchemaVersion | Versão de esquema de mensagens                            |
| nome da mensagem          | **CreateUserRequest**                               |

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
| userChainIdentifier   | Endereço do utilizador que foi criado na rede blockchain. No Ethereum, o endereço é o endereço do utilizador **em cadeia.** |
| conexãoId          | Identificador único para a ligação blockchain|
| mensagemSchemaVersion  | Versão de esquema de mensagens |
| nome da mensagem           | **CreateUserUpdate** |
| status                | Estado do pedido de criação do utilizador.  Se for bem sucedido, o valor é **sucesso.** No fracasso, o valor é **fracasso.**     |
| informação adicional | Informações adicionais fornecidas com base no estado |

Exemplo bem sucedido criar resposta **do utilizador** a partir da Bancada de Trabalho blockchain:

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

Se o pedido não foi bem sucedido, os detalhes sobre a falha são incluídos em informações adicionais.

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
| userChainIdentifier  | Endereço do utilizador que foi criado na rede blockchain. No Ethereum, este endereço é do utilizador no endereço **da corrente.** |
| applicationName      | Nome do pedido |
| versão              | Versão da aplicação. Necessário se tiver várias versões da aplicação ativadas. Caso contrário, a versão é opcional. Para obter mais informações sobre a versão da aplicação, consulte a versão da [aplicação Azure Blockchain Workbench](version-app.md). |
| workflowName         | Nome do fluxo de trabalho |
| parâmetros           | Entrada de parâmetros para criação de contrato |
| conexãoId         | Identificador único para a ligação blockchain |
| mensagemSchemaVersion | Versão de esquema de mensagens |
| nome da mensagem          | **CreateContractRequest** |

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
| contractId               | Identificador único para o contrato dentro da bancada Azure Blockchain |
| contractLedgerIdentifier | Endereço do contrato no livro-razão                                            |
| conexãoId             | Identificador único para a ligação blockchain                               |
| mensagemSchemaVersion     | Versão de esquema de mensagens                                                         |
| nome da mensagem              | **CreateContractUpdate**                                                      |
| status                   | Estado do pedido de criação de contrato.  Valores possíveis: **Submetido,** **Comprometido,** **Falha**.  |
| informação adicional    | Informações adicionais fornecidas com base no estado                              |

Exemplo de uma resposta **de contrato** apresentada a partir da Bancada de Trabalho blockchain:

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

Exemplo de uma **resposta contratual** comprometida da Blockchain Workbench:

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

Se o pedido não foi bem sucedido, os detalhes sobre a falha são incluídos em informações adicionais.

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
| userChainIdentifier      | Endereço do utilizador que foi criado na rede blockchain. No Ethereum, este endereço é do utilizador no endereço **da corrente.** |
| contractLedgerIdentifier | Endereço do contrato no livro-razão |
| versão                  | Versão da aplicação. Necessário se tiver várias versões da aplicação ativadas. Caso contrário, a versão é opcional. Para obter mais informações sobre a versão da aplicação, consulte a versão da [aplicação Azure Blockchain Workbench](version-app.md). |
| workflowFunctionName     | Nome da função de fluxo de trabalho |
| parâmetros               | Entrada de parâmetros para criação de contrato |
| conexãoId             | Identificador único para a ligação blockchain |
| mensagemSchemaVersion     | Versão de esquema de mensagens |
| nome da mensagem              | **CreateContractActionRequest** |

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
| contractId            | Identificador único para o contrato dentro da bancada Azure Blockchain |
| conexãoId          | Identificador único para a ligação blockchain |
| mensagemSchemaVersion  | Versão de esquema de mensagens |
| nome da mensagem           | **CreateContractActionUpdate** |
| status                | Estado do pedido de ação contratual. Valores possíveis: **Submetido,** **Comprometido,** **Falha**.                         |
| informação adicional | Informações adicionais fornecidas com base no estado |

Exemplo de uma resposta de **ação contratual** apresentada da Blockchain Workbench:

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

Exemplo de uma resposta de **ação contratual** comprometida da Blockchain Workbench:

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

Se o pedido não foi bem sucedido, os detalhes sobre a falha são incluídos em informações adicionais.

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

### <a name="input-api-error-codes-and-messages"></a>Input Códigos de erro da API e mensagens

**Error code 4000: Erro de pedido grave**
- Conexão inválidaId
- CreateUserRequest desserialização falhou
- CreateContractRequest desserialização falhou
- CreateContractActionSolicit desserialização falhou
- Aplicação {identificada pelo nome da aplicação} não existe
- Aplicação {identificada pelo nome da aplicação} não tem fluxo de trabalho
- UserChainIdentifier não existe
- Contrato {identificado por identificador de livros} não existe
- Contrato {identificado por identificador de livros} não tem função {nome da função workflow}
- UserChainIdentifier não existe

**Error code 4090: Erro de conflito**
- Utilizador já existe
- Contrato já existe
- Já existe uma ação contratual

**Error code 5000: Erro interno do servidor**
- Mensagens de exceção

## <a name="event-notifications"></a>Notificações de eventos

As notificações de eventos podem ser usadas para notificar utilizadores e sistemas a jusante de eventos que ocorrem na Bancada de Trabalho blockchain e na rede blockchain a que está ligado. As notificações de eventos podem ser consumidas diretamente em código ou usadas com ferramentas como Apps Lógicas e Flow para desencadear o fluxo de dados para sistemas a jusante.

Consulte a referência da [mensagem de notificação](#notification-message-reference) para obter detalhes de várias mensagens que possam ser recebidas.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Consumir eventos da Grelha de Eventos com Funções Azure

Se um utilizador quiser utilizar a Rede de Eventos para ser notificado sobre os eventos que ocorrem na Bancada de Trabalho blockchain, pode consumir eventos a partir da Grelha de Eventos utilizando funções Azure.

1. Crie uma **App de Função Azure** no portal Azure.
2. Criar uma nova função.
3. Localize o modelo para a Grelha de Eventos. É mostrado o código de modelo básico para a leitura da mensagem. Modifique o código conforme necessário.
4. Salve a Função. 
5. Selecione a Grelha de Eventos do grupo de recursos da Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Consumir eventos da Grelha de Eventos com Aplicações Lógicas

1. Crie uma nova **App Azure Logic** no portal Azure.
2. Ao abrir a App Lógica Azure no portal, será solicitado a selecionar um gatilho. Selecione **Grelha de Eventos Azure -- Quando ocorrer um evento**de recursos .
3. Quando o designer de fluxo de trabalho for apresentado, será solicitado a iniciar sessão.
4. Selecione a Subscrição. Recurso como **Microsoft.EventGrid.Topics**. Selecione o Nome de **Recurso** a partir do nome do recurso do grupo de recursos azure Blockchain Workbench.
5. Selecione a Grelha de Eventos do grupo de recursos da Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Utilização de tópicos de ônibus de serviço para notificações

Os Tópicos de Ônibus de serviço podem ser usados para notificar os utilizadores sobre os eventos que ocorrem na Bancada de Trabalho blockchain. 

1. Navegue no Ônibus de Serviço dentro do grupo de recursos da Bancada de Trabalho.
2. Selecione **Tópicos**.
3. Selecione **tópico de saída**.
4. Crie uma nova subscrição para este tópico. Obtenha uma chave para isto.
5. Crie um programa, que subscreve eventos a partir desta subscrição.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Consumir mensagens de ônibus de serviço com aplicações lógicas

1. Crie uma nova **App Azure Logic** no portal Azure.
2. Ao abrir a App Lógica Azure no portal, será solicitado a selecionar um gatilho. Tipo **Service Bus** na caixa de pesquisa e selecione o gatilho apropriado para o tipo de interação que pretende ter com o Ônibus de Serviço. Por exemplo, **Service Bus -- Quando uma mensagem é recebida numa subscrição de tópicos (auto-completa)**.
3. Quando o designer de fluxos de trabalho for apresentado, especifique as informações de ligação para o Ônibus de Serviço.
4. Selecione a sua subscrição e especifique o tópico da **bancada externa**.
5. Desenvolva a lógica para a sua aplicação que utiliza a mensagem a partir deste gatilho.

## <a name="notification-message-reference"></a>Referência de mensagem de notificação

Dependendo do nome da **mensagem,** as mensagens de notificação têm um dos seguintes tipos de mensagens.

### <a name="block-message"></a>Mensagem de bloco

Contém informações sobre blocos individuais. O *BlockMessage* inclui uma secção com informações de nível de bloco e uma secção com informações de transação.

| Nome | Descrição |
|------|-------------|
| bloquear | Contém [informações de blocos](#block-information) |
| transações | Contém uma informação de [transação](#transaction-information) de recolha para o bloco |
| conexãoId | Identificador único para a ligação |
| mensagemSchemaVersion | Versão de esquema de mensagens |
| nome da mensagem | **Mensagem de bloqueio** |
| informação adicional | Informações adicionais fornecidas |

#### <a name="block-information"></a>Bloquear informação

| Nome              | Descrição |
|-------------------|-------------|
| blockId           | Identificador único para o bloco dentro da bancada Azure Blockchain |
| blocoNúmero       | Identificador único para um bloco no livro de registo |
| blockHash         | O hash do quarteirão |
| anteriorBlockHash | O hash do bloco anterior |
| blocoTimestamp    | O carimbo de tempo do bloco |

#### <a name="transaction-information"></a>Informações sobre transações

| Nome               | Descrição |
|--------------------|-------------|
| transaçãoId      | Identificador único para a transação dentro da bancada Azure Blockchain |
| transaçõesHash    | O hash da transação no livro de contabilidade |
| De               | Identificador único no livro de registos para a origem da transação |
| para                 | Identificador único no livro de contabilidade para o destino de transação |
| estatuto de provisionamento | Identifica o estado atual do processo de provisionamento para a operação. Os valores possíveis são: </br>0 – A transação foi criada pela API na base de dados</br>1 - A transação foi enviada para o livro-razão</br>2 – A transação foi comprometida com sucesso com o livro-razão</br>3 ou 4 - A transação não foi comprometida com o livro-razão</br>5 - A transação foi comprometida com sucesso com o livro-razão |

Exemplo de um *BlockMessage* da bancada blockchain:

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

### <a name="contract-message"></a>Mensagem contratual

Contém informações sobre um contrato. A mensagem inclui uma secção com propriedades contratuais e uma secção com informações de transação. Todas as transações que modificaram o contrato para o bloco em particular estão incluídas na secção de transações.

| Nome | Descrição |
|------|-------------|
| blockId | Identificador único para o bloco dentro da bancada Azure Blockchain |
| blockHash | Hash do quarteirão |
| modificação de Transações | [Transações que modificaram](#modifying-transaction-information) o contrato |
| contractId | Identificador único para o contrato dentro da bancada Azure Blockchain |
| contractLedgerIdentifier | Identificador único para o contrato no livro de contabilidade |
| contratoPropriedades | [Imóveis do contrato](#contract-properties) |
| isNewContract | Indica se este contrato foi ou não recentemente criado. Os valores possíveis são: verdadeiros: este contrato foi um novo contrato criado. falso: este contrato é uma atualização do contrato. |
| conexãoId | Identificador único para a ligação |
| mensagemSchemaVersion | Versão de esquema de mensagens |
| nome da mensagem | **Mensagem de Contrato** |
| informação adicional | Informações adicionais fornecidas |

#### <a name="modifying-transaction-information"></a>Modificação de informações sobre transações

| Nome               | Descrição |
|--------------------|-------------|
| transaçãoId | Identificador único para a transação dentro da bancada Azure Blockchain |
| transaçõesHash | O hash da transação no livro de contabilidade |
| De | Identificador único no livro de registos para a origem da transação |
| para | Identificador único no livro de contabilidade para o destino de transação |

#### <a name="contract-properties"></a>Imóveis contratuais

| Nome               | Descrição |
|--------------------|-------------|
| workflowPropertyId | Identificador único para a propriedade workflow dentro da bancada de trabalho Azure Blockchain |
| nome | Nome da propriedade workflow |
| valor | Valor da propriedade workflow |

Exemplo de uma *Mensagem contratual* da bancada blockchain:

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

### <a name="event-message-contract-function-invocation"></a>Mensagem do evento: Invocação da função contratual

Contém informações quando uma função contratual é invocada, como o nome da função, a entrada de parâmetros e o chamador da função.

| Nome | Descrição |
|------|-------------|
| nome de evento                   | **Função contratualInvocação** |
| chamador                      | [Informações de chamada](#caller-information) |
| contractId                  | Identificador único para o contrato dentro da bancada Azure Blockchain |
| contractLedgerIdentifier    | Identificador único para o contrato no livro de contabilidade |
| nome de função                | Nome da função |
| parâmetros                  | [Informação do parâmetro](#parameter-information) |
| transação                 | Informações sobre transações |
| inTransactionSequenceNumber | O número de sequência da transação no bloco |
| conexãoId                | Identificador único para a ligação |
| mensagemSchemaVersion        | Versão de esquema de mensagens |
| nome da mensagem                 | **Mensagem de Evento** |
| informação adicional       | Informações adicionais fornecidas |

#### <a name="caller-information"></a>Informações de chamada

| Nome | Descrição |
|------|-------------|
| tipo | Tipo de chamada, como um utilizador ou um contrato |
| ID | Identificador único para quem liga dentro da bancada azure blockchain |
| identificador de livros | Identificador único para o chamador no livro de registo |

#### <a name="parameter-information"></a>Informação do parâmetro

| Nome | Descrição |
|------|-------------|
| nome | Nome do parâmetro |
| valor | Valor do parâmetro |

#### <a name="event-message-transaction-information"></a>Informações sobre transações de mensagens de eventos

| Nome               | Descrição |
|--------------------|-------------|
| transaçãoId      | Identificador único para a transação dentro da bancada Azure Blockchain |
| transaçõesHash    | O hash da transação no livro de contabilidade |
| De               | Identificador único no livro de registos para a origem da transação |
| para                 | Identificador único no livro de contabilidade para o destino de transação |

Exemplo de um Contrato de Contração de Mensagem de *EventoSInvocação* da bancada de trabalho blockchain:

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

### <a name="event-message-application-ingestion"></a>Mensagem do evento: Ingestão de aplicação

Contém informações quando uma aplicação é enviada para a Workbench, como o nome e a versão da aplicação enviada.

| Nome | Descrição |
|------|-------------|
| nome de evento | **Aplicação** |
| applicationId | Identificador único para a aplicação dentro da bancada Azure Blockchain |
| applicationName | Nome da aplicação |
| aplicaçãoDisplayName | Nome do display da aplicação |
| aplicaçãoVersãoVersão | Versão de aplicação |
| aplicaçãoDefiniÇãoLocalização | URL onde o ficheiro de configuração da aplicação está localizado |
| contratoCódigos | Recolha de [códigos contratuais](#contract-code-information) para a aplicação |
| aplicaçõesPapéroles | Recolha de [funções](#application-role-information) de candidatura para a aplicação |
| aplicaçõesFluxos de trabalho | Recolha de fluxos de trabalho de [aplicação](#application-workflow-information) para a aplicação |
| conexãoId | Identificador único para a ligação |
| mensagemSchemaVersion | Versão de esquema de mensagens |
| nome da mensagem | **Mensagem de Evento** |
| informação adicional | Informações adicionais aqui fornecidas incluem os estados de fluxo de aplicação e informações de transição. |

#### <a name="contract-code-information"></a>Informação sobre código contratual

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para o ficheiro de código de contrato dentro da bancada azure Blockchain Workbench |
| ledgerId | Identificador único para o livro-razão dentro da bancada azure blockchain |
| localização | URL onde o ficheiro de código de contrato está localizado |

#### <a name="application-role-information"></a>Informação sobre a função de aplicação

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para o papel de candidatura dentro da bancada azure blockchain |
| nome | Nome da função de candidatura |

#### <a name="application-workflow-information"></a>Informação sobre fluxo de trabalho de aplicação

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para o fluxo de trabalho de aplicação dentro da bancada azure blockchain |
| nome | Nome do fluxo de trabalho da aplicação |
| displayName | Nome de exibição de fluxo de trabalho de aplicação |
| funções | Recolha de [funções para o fluxo](#workflow-function-information) de trabalho da aplicação|
| estados | Recolha de [Estados para o fluxo](#workflow-state-information) de trabalho de aplicação |
| propriedades | Informação sobre propriedades de [fluxo de trabalho](#workflow-property-information) de aplicação |

##### <a name="workflow-function-information"></a>Informação sobre função de fluxo de trabalho

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para a função de fluxo de aplicação dentro da bancada de trabalho Azure Blockchain |
| nome | Nome da função |
| parâmetros | Parâmetros para a função |

##### <a name="workflow-state-information"></a>Informação do Estado do fluxo de trabalho

| Nome | Descrição |
|------|-------------|
| nome | Nome do Estado |
| displayName | Nome da exibição do Estado |
| estilo | Estilo de Estado (sucesso ou fracasso) |

##### <a name="workflow-property-information"></a>Informação sobre propriedades de fluxo de trabalho

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para a propriedade de fluxo de trabalho de aplicação dentro da bancada de trabalho Azure Blockchain |
| nome | Nome da propriedade |
| tipo | Tipo de propriedade |

Exemplo de uma aplicação de *mensagens* de evento da bancada de trabalho blockchain:

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

### <a name="event-message-role-assignment"></a>Mensagem do evento: Atribuição de papéis

Contém informações quando um utilizador é atribuído a um papel na Bancada de Trabalho, como quem desempenhou a atribuição de funções e o nome da função e da aplicação correspondente.

| Nome | Descrição |
|------|-------------|
| nome de evento | **Atribuição de Funções** |
| applicationId | Identificador único para a aplicação dentro da bancada Azure Blockchain |
| applicationName | Nome da aplicação |
| aplicaçãoDisplayName | Nome do display da aplicação |
| aplicaçãoVersãoVersão | Versão de aplicação |
| aplicaçãoRole        | Informação sobre o papel da [candidatura](#roleassignment-application-role) |
| designador               | Informações sobre o [designador](#roleassignment-assigner) |
| designado               | Informações sobre o [designado](#roleassignment-assignee) |
| conexãoId           | Identificador único para a ligação |
| mensagemSchemaVersion   | Versão de esquema de mensagens |
| nome da mensagem            | **Mensagem de Evento** |
| informação adicional  | Informações adicionais fornecidas |

#### <a name="roleassignment-application-role"></a>Função de aplicação roleAssignment

| Nome | Descrição |
|------|-------------|
| ID | Identificador único para o papel de candidatura dentro da bancada azure blockchain |
| nome | Nome da função de candidatura |

#### <a name="roleassignment-assigner"></a>Designador de Atribuição de Funções

| Nome | Descrição |
|------|-------------|
| ID | Identificador único do utilizador dentro da bancada azure blockchain |
| tipo | Tipo do designador |
| chainIdentifier | Identificador único do utilizador no livro-razão |

#### <a name="roleassignment-assignee"></a>Designado de RoleAssignment

| Nome | Descrição |
|------|-------------|
| ID | Identificador único do utilizador dentro da bancada azure blockchain |
| tipo | Tipo de designado |
| chainIdentifier | Identificador único do utilizador no livro-razão |

Exemplo de uma *RoleAssignment* de Tarefa de Mensagem de Evento da bancada de trabalho blockchain:

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

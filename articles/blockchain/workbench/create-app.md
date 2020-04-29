---
title: Criar uma aplicação blockchain - Azure Blockchain Workbench
description: Tutorial sobre como criar uma aplicação blockchain para A Pré-visualização da bancada de trabalho Azure Blockchain.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 33a9e9c10c07d0808626353a7edfd505e0f60bc9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74324812"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>Tutorial: Criar uma aplicação blockchain para a bancada azure blockchain

Pode utilizar a Bancada de Trabalho Azure Blockchain para criar aplicações blockchain que representam fluxos de trabalho multipartidários definidos pela configuração e código de contrato inteligente.

Vai aprender a:

> [!div class="checklist"]
> * Configurar uma aplicação blockchain
> * Criar um ficheiro de código de contrato inteligente
> * Adicione uma aplicação blockchain à bancada de trabalho blockchain
> * Adicione membros à aplicação blockchain

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implantação da bancada blockchain. Para mais informações, consulte a implementação da [bancada azure Blockchain](deploy.md) para obter mais detalhes sobre a implementação.
* Utilizadores de Diretório Sonante Azure no inquilino associado à Bancada de Trabalho blockchain. Para mais informações, consulte [adicionar utilizadores de AD Azure na bancada de trabalho Azure Blockchain](manage-users.md#add-azure-ad-users).
* Uma conta de administrador da Bancada de Trabalho blockchain. Para mais informações, consulte adicionar [administradores da bancada de trabalho blockchain na bancada de trabalho azure Blockchain](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Olá, Blockchain!

Vamos construir um pedido básico no qual um solicitador envia um pedido e um respondente envia uma resposta ao pedido.
Por exemplo, um pedido pode ser: "Olá, como estás?", e a resposta pode ser: "Estou ótimo!". Tanto o pedido como a resposta são registados na blockchain subjacente.

Siga os passos para criar os ficheiros da aplicação ou pode [descarregar a amostra do GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Ficheiro de configuração

Os metadados de configuração definem os fluxos de trabalho de alto nível e o modelo de interação da aplicação blockchain. Os metadados de configuração representam as fases de fluxo de trabalho e o modelo de interação da aplicação blockchain.

1. No seu editor favorito, `HelloBlockchain.json`crie um ficheiro chamado .
2. Adicione o seguinte JSON para definir a configuração da aplicação blockchain.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Guarde o ficheiro `HelloBlockchain.json`.

O ficheiro de configuração tem várias secções. Os detalhes sobre cada secção são os seguintes:

### <a name="application-metadata"></a>Metadados de aplicação

O início do ficheiro de configuração contém informações sobre a aplicação, incluindo o nome e descrição da aplicação.

### <a name="application-roles"></a>Funções da aplicação

A secção de funções de aplicação define as funções do utilizador que podem atuar ou participar dentro da aplicação blockchain. Define um conjunto de funções distintas com base na funcionalidade. No cenário de resposta a pedidos, existe uma distinção entre a funcionalidade de um solicitor como entidade que produz pedidos e um respondente como entidade que produz respostas.

### <a name="workflows"></a>Fluxos de trabalho

Os fluxos de trabalho definem uma ou mais fases e ações do contrato. No cenário de resposta a pedido, a primeira fase (estado) do fluxo de trabalho é um solicitor (função) que toma uma ação (transição) para enviar um pedido (função). A próxima fase (estado) é um respondente (papel) que toma uma ação (transição) para enviar uma resposta (função). O fluxo de trabalho de uma aplicação pode envolver propriedades, funções e estados necessários para descrever o fluxo de um contrato.

Para obter mais informações sobre o conteúdo dos ficheiros de configuração, consulte a referência de configuração do Fluxo de [Trabalho do Azure Blockchain](configuration.md).

## <a name="smart-contract-code-file"></a>Ficheiro de código de contrato inteligente

Os contratos inteligentes representam a lógica empresarial da aplicação blockchain. Atualmente, blockchain Workbench suporta Ethereum para o livro de blockchain. O Ethereum usa a [Solidity](https://solidity.readthedocs.io) como a sua linguagem de programação para escrever a autoaplicação da lógica empresarial para contratos inteligentes.

Os contratos inteligentes em Solididade são semelhantes às classes em línguas orientadas para objetos. Cada contrato contém estado e funções para implementar fases e ações do contrato inteligente.

No seu editor favorito, `HelloBlockchain.sol`crie um ficheiro chamado .

### <a name="version-pragma"></a>Versão pragma

Como uma boa prática, indique a versão da Solidez que está a visar. Especificar a versão ajuda a evitar incompatibilidades com futuras versões solidez.

Adicione a seguinte versão pragma `HelloBlockchain.sol` no topo do ficheiro de código de contrato inteligente.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Configuração e relação de código de contrato inteligente

Blockchain Workbench usa o ficheiro de configuração e o ficheiro de código de contrato inteligente para criar uma aplicação blockchain. Existe uma relação entre o que é definido na configuração e o código no contrato inteligente. Os detalhes do contrato, funções, parâmetros e tipos são necessários para combinar para criar a aplicação. A Bancada de Trabalho blockchain verifica os ficheiros antes da criação da aplicação.

### <a name="contract"></a>Contrato

Adicione **contract** o cabeçalho `HelloBlockchain.sol` do contrato ao seu ficheiro de código de contrato inteligente.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Variáveis do Estado

As variáveis estatais armazenam valores do Estado para cada caso de contrato. As variáveis estatais do seu contrato devem corresponder às propriedades de fluxo de trabalho definidas no ficheiro de configuração.

Adicione as variáveis estatais `HelloBlockchain.sol` ao seu contrato no seu ficheiro de código de contrato inteligente.

``` solidity
    //Set of States
    enum StateType { Request, Respond}

    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;

    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Construtor

O construtor define parâmetros de entrada para uma nova instância de contrato inteligente de um fluxo de trabalho. Os parâmetros necessários para o construtor são definidos como parâmetros de construção no ficheiro de configuração. O número, a ordem e o tipo de parâmetros devem coincidir em ambos os ficheiros.

Na função de construtor, escreva qualquer lógica de negócio que queira executar antes de criar o contrato. Por exemplo, rubricar as variáveis do Estado com valores iniciais.

Adicione a função de construtor `HelloBlockchain.sol` ao seu contrato no seu ficheiro de código de contrato inteligente.

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>Funções

As funções são as unidades executáveis da lógica empresarial dentro de um contrato. Os parâmetros necessários para a função são definidos como parâmetros de função no ficheiro de configuração. O número, a ordem e o tipo de parâmetros devem coincidir em ambos os ficheiros. As funções estão associadas a transições num fluxo de trabalho da bancada blockchain no ficheiro de configuração. Uma transição é uma ação realizada para passar à fase seguinte do fluxo de trabalho de uma aplicação, determinada pelo contrato.

Escreva qualquer lógica de negócio que queira desempenhar na função. Por exemplo, modificar o valor de uma variável estatal.

1. Adicione as seguintes funções `HelloBlockchain.sol` ao seu contrato no seu ficheiro de código de contrato inteligente.

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. Guarde `HelloBlockchain.sol` o seu ficheiro de código de contrato inteligente.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Adicione a aplicação blockchain à bancada de trabalho blockchain

Para adicionar uma aplicação blockchain à Bancada de Trabalho blockchain, você carrega a configuração e ficheiros de contrato seletivas para definir a aplicação.

1. Num navegador web, navegue para o endereço web blockchain Workbench. Por exemplo, `https://{workbench URL}.azurewebsites.net/` a aplicação web é criada quando implementa a bancada blockchain Workbench. Para obter informações sobre como encontrar o seu endereço web blockchain Workbench, consulte [Blockchain Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. Inscreva-se como administrador da [bancada blockchain.](manage-users.md#manage-blockchain-workbench-administrators)
3. Selecione **Aplicações** > **Novas**. O novo painel de **aplicações** é apresentado.
4. Selecione **Carregar a configuração** > do contrato**Navegue** para localizar o ficheiro de configuração **HelloBlockchain.json** que criou. O ficheiro de configuração é validado automaticamente. Selecione o link **Mostrar** para visualizar erros de validação. Corrija os erros de validação antes de implementar a aplicação.
5. **Selecione Carregar o código** > de contrato**Navegar** para localizar o ficheiro de código de contrato inteligente **HelloBlockchain.sol.** O ficheiro de código é validado automaticamente. Selecione o link **Mostrar** para visualizar erros de validação. Corrija os erros de validação antes de implementar a aplicação.
6. Selecione **Implementar** para criar a aplicação blockchain com base na configuração e nos ficheiros de contratos inteligentes.

A implementação da aplicação blockchain demora alguns minutos. Quando a implementação estiver concluída, a nova aplicação é apresentada nas **Aplicações**. 

> [!NOTE]
> Também pode criar aplicações blockchain utilizando a [API REST Rest API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)do Banco de Trabalho de Blockchain.

## <a name="add-blockchain-application-members"></a>Adicionar membros da aplicação blockchain

Adicione os membros da aplicação à sua aplicação para iniciar e tomar ações sobre contratos. Para adicionar membros da aplicação, precisa de ser administrador da [Bancada de Trabalho blockchain.](manage-users.md#manage-blockchain-workbench-administrators)

1. Selecione **Aplicações** > **Olá, Blockchain!**
2. O número de membros associados à aplicação é apresentado no canto superior direito da página. Para uma nova candidatura, o número de membros será zero.
3. Selecione o link dos **membros** no canto superior direito da página. É apresentada uma lista atual de membros para a aplicação.
4. Na lista de membros, selecione **Adicionar membros**.
5. Selecione ou introduza o nome do membro que pretende adicionar. Apenas os utilizadores de Anúncios Azure que existem no inquilino da bancada blockchain estão listados. Se o utilizador não for encontrado, tem de [adicionar utilizadores de Anúncios Azure](manage-users.md#add-azure-ad-users).
6. Selecione o **Papel** para o membro. Para o primeiro membro, selecione **Requestor** como função.
7. Selecione **Adicionar** para adicionar o membro com a função associada à aplicação.
8. Adicione outro membro à aplicação com a função **Responder.**

Para obter mais informações sobre a gestão de utilizadores na Bancada de Trabalho blockchain, consulte [a gestão de utilizadores na Bancada de Trabalho Azure Blockchain](manage-users.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, criou um pedido básico de pedido e resposta. Para aprender a usar a aplicação, continue para o próximo artigo como fazer.

> [!div class="nextstepaction"]
> [Usando uma aplicação blockchain](use.md)

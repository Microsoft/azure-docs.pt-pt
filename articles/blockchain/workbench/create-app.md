---
title: Criar uma aplicação blockchain - Azure Blockchain Workbench
description: Tutorial sobre como criar uma aplicação blockchain para pré-visualização do workbench Azure Blockchain.
ms.date: 08/24/2020
ms.topic: tutorial
ms.reviewer: ravastra
ms.openlocfilehash: add790a069d2e0ea66d84bbd632825cf9331fd38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88784033"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>Tutorial: Criar uma aplicação blockchain para a Azure Blockchain Workbench

Pode utilizar o Azure Blockchain Workbench para criar aplicações blockchain que representam fluxos de trabalho multi-partidários definidos por configuração e código de contrato inteligente.

Vai aprender a:

> [!div class="checklist"]
> * Configure uma aplicação blockchain
> * Criar um ficheiro de código de contrato inteligente
> * Adicione uma aplicação blockchain à Blockchain Workbench
> * Adicione membros à aplicação blockchain

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implantação da bancada blockchain Workbench. Para obter mais informações, consulte [a implementação da bancada de workbench Azure Blockchain](deploy.md) para obter mais detalhes sobre a implementação.
* Utilizadores do Azure Ative Directory no inquilino associado à Blockchain Workbench. Para obter mais informações, consulte [adicionar utilizadores Azure AD na bancada Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* Uma conta de administrador da Blockchain Workbench. Para obter mais informações, consulte adicionar [administradores blockchain Workbench na Azure Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Olá, Blockchain!

Vamos construir um pedido básico no qual um solicitador envia um pedido e um socorrista envia uma resposta ao pedido.
Por exemplo, um pedido pode ser: "Olá, como estás?", e a resposta pode ser: "Sou ótimo!". Tanto o pedido como a resposta são registados na blockchain subjacente.

Siga os passos para criar os ficheiros de aplicações ou pode [descarregar a amostra do GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Ficheiro de configuração

Os metadados de configuração definem os fluxos de trabalho de alto nível e o modelo de interação da aplicação blockchain. Os metadados de configuração representam as fases de fluxo de trabalho e o modelo de interação da aplicação blockchain. Para obter mais informações sobre o conteúdo dos ficheiros de configuração, consulte [a referência de configuração do fluxo de trabalho Azure Blockchain](configuration.md).

1. No seu editor favorito, crie um ficheiro chamado `HelloBlockchain.json` .
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

O início do ficheiro de configuração contém informações sobre a aplicação, incluindo o nome da aplicação e a descrição.

### <a name="application-roles"></a>Funções da aplicação

A secção de funções de aplicação define as funções de utilizador que podem agir ou participar na aplicação blockchain. Define-se um conjunto de papéis distintos baseados na funcionalidade. No cenário de resposta a pedidos, existe uma distinção entre a funcionalidade de um solicitador como entidade que produz pedidos e uma resposta como entidade que produz respostas.

### <a name="workflows"></a>Fluxos de trabalho

Os fluxos de trabalho definem uma ou mais fases e ações do contrato. No cenário de resposta a pedidos, a primeira fase (estado) do fluxo de trabalho é um solicitador (papel) toma uma ação (transição) para enviar um pedido (função). A fase seguinte (estado) é uma resposta (papel) toma uma ação (transição) para enviar uma resposta (função). O fluxo de trabalho de uma aplicação pode envolver propriedades, funções e estados necessários para descrever o fluxo de um contrato.

## <a name="smart-contract-code-file"></a>Arquivo de código de contrato inteligente

Os contratos inteligentes representam a lógica de negócio da aplicação blockchain. Atualmente, blockchain Workbench suporta Ethereum para o livro de blockchain. O Ethereum usa [a Solidity](https://solidity.readthedocs.io) como sua linguagem de programação para escrever lógica de negócio auto-executiva para contratos inteligentes.

Os contratos inteligentes na Solidity são semelhantes às classes em línguas orientadas a objetos. Cada contrato contém estado e funções para implementar etapas e ações do contrato inteligente.

No seu editor favorito, crie um ficheiro chamado `HelloBlockchain.sol` .

### <a name="version-pragma"></a>Pragma de versão

Como uma boa prática, indique a versão da Solidity que está a ser alvo. Especificar a versão ajuda a evitar incompatibilidades com futuras versões solidity.

Adicione o pragma de versão seguinte no topo do `HelloBlockchain.sol` ficheiro de código de contrato inteligente.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Configuração e relação código de contrato inteligente

Blockchain Workbench utiliza o ficheiro de configuração e o ficheiro de código do contrato inteligente para criar uma aplicação blockchain. Existe uma relação entre o que é definido na configuração e o código no contrato inteligente. Os detalhes do contrato, funções, parâmetros e tipos são necessários para combinar para criar a aplicação. Blockchain Workbench verifica os ficheiros antes da criação da aplicação.

### <a name="contract"></a>Contrato

Adicione o cabeçalho do **contrato** ao seu `HelloBlockchain.sol` ficheiro de código de contrato inteligente.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Variáveis de estado

Variáveis estatais armazenam valores do estado para cada instância do contrato. As variáveis estatais no seu contrato devem corresponder às propriedades de fluxo de trabalho definidas no ficheiro de configuração.

Adicione as variáveis estatais ao seu contrato no seu `HelloBlockchain.sol` ficheiro de código de contrato inteligente.

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

Na função de construtor, escreva qualquer lógica de negócio que pretenda realizar antes de criar o contrato. Por exemplo, inicialize as variáveis do estado com valores iniciais.

Adicione a função de construtor ao seu contrato no seu `HelloBlockchain.sol` ficheiro de código de contrato inteligente.

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

As funções são as unidades executáveis da lógica de negócio dentro de um contrato. Os parâmetros necessários para a função são definidos como parâmetros de função no ficheiro de configuração. O número, a ordem e o tipo de parâmetros devem coincidir em ambos os ficheiros. As funções estão associadas a transições num fluxo de trabalho blockchain Workbench no ficheiro de configuração. Uma transição é uma ação executada para passar à fase seguinte do fluxo de trabalho de uma aplicação, conforme determinado pelo contrato.

Escreva qualquer lógica de negócio que queira desempenhar na função. Por exemplo, modificar o valor de uma variável de estado.

1. Adicione as seguintes funções ao seu contrato no seu `HelloBlockchain.sol` ficheiro de código de contrato inteligente.

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

2. Guarde o seu `HelloBlockchain.sol` ficheiro de código de contrato inteligente.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Adicione a aplicação blockchain à Bancada Blockchain

Para adicionar uma aplicação blockchain ao Blockchain Workbench, você carrega a configuração e os ficheiros de contrato inteligente para definir a aplicação.

1. Num navegador web, navegue para o endereço web blockchain Workbench. Por exemplo, `https://{workbench URL}.azurewebsites.net/` a aplicação web é criada quando implementa o Blockchain Workbench. Para obter informações sobre como encontrar o seu endereço web Blockchain Workbench, consulte [o URL web da Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Inscreva-se como [administrador da Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecione **Aplicações**  >  **Novas**. É apresentado o painel de **aplicação Novo.**
4. Selecione **Carregar a configuração do contrato**  >  **Navegue** para localizar oHelloBlockchain.js **no** ficheiro de configuração que criou. O ficheiro de configuração é automaticamente validado. Selecione o link **'Mostrar'** para apresentar erros de validação. Corrija erros de validação antes de implementar a aplicação.
5. **Selecione Carregar o código do contrato**  >  **Navegue** para localizar o ficheiro de código de contrato inteligente **HelloBlockchain.sol.** O ficheiro de código é automaticamente validado. Selecione o link **'Mostrar'** para apresentar erros de validação. Corrija erros de validação antes de implementar a aplicação.
6. Selecione **Implementar** para criar a aplicação blockchain com base na configuração e nos ficheiros de contratos inteligentes.

A implementação da aplicação blockchain demora alguns minutos. Quando a implementação estiver concluída, a nova aplicação é apresentada em **Aplicações**. 

> [!NOTE]
> Também pode criar aplicações blockchain utilizando a API de [workbench Azure Blockchain.](/rest/api/azure-blockchain-workbench)

## <a name="add-blockchain-application-members"></a>Adicionar membros da aplicação blockchain

Adicione os membros da aplicação à sua candidatura para iniciar e tomar ações em contratos. Para adicionar membros de aplicação, precisa de ser um [administrador da Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

1. Selecione **Aplicações**  >  **Olá, Blockchain!**
2. O número de membros associados à aplicação é apresentado no canto superior direito da página. Para uma nova aplicação, o número de membros será zero.
3. Selecione o link dos **membros** no canto superior direito da página. É apresentada uma lista atual de membros para a aplicação.
4. Na lista de membros, selecione **Adicionar membros**.
5. Selecione ou introduza o nome do membro que pretende adicionar. Apenas os utilizadores de AD Azure que existem no blockchain Workbench inquilino estão listados. Se o utilizador não for encontrado, tem de [adicionar utilizadores AD do Azure](manage-users.md#add-azure-ad-users).
6. Selecione o **Papel** para o membro. Para o primeiro membro, selecione **Requestor** como o papel.
7. **Selecione Adicionar** o membro com a função associada à aplicação.
8. Adicione outro membro à aplicação com o papel **de Responder.**

Para obter mais informações sobre a gestão de utilizadores em Blockchain Workbench, consulte [a gestão de utilizadores na Azure Blockchain Workbench](manage-users.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, criou um pedido básico de pedido e resposta. Para aprender a usar a aplicação, continue para o próximo artigo como fazer.

> [!div class="nextstepaction"]
> [Usando uma aplicação blockchain](use.md)

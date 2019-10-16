---
title: Criar um aplicativo blockchain – Azure Blockchain Workbench
description: Tutorial sobre como criar um aplicativo blockchain na visualização do Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: a7d7c68840999772461655090d0213ada0997a7a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329098"
---
# <a name="tutorial-create-a-blockchain-application-in-azure-blockchain-workbench"></a>Tutorial: criar um aplicativo blockchain no Azure Blockchain Workbench

Você pode usar o Azure Blockchain Workbench para criar aplicativos Blockchain que representam fluxos de trabalho de várias partes definidos pelo código do contrato inteligente e de configuração.

Vai aprender a:

> [!div class="checklist"]
> * Configurar um aplicativo blockchain
> * Criar um arquivo de código de contrato inteligente
> * Adicionar um aplicativo blockchain ao Blockchain Workbench
> * Adicionar membros ao aplicativo blockchain

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implantação do Blockchain Workbench. Para obter mais informações, consulte [implantação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implantação.
* Azure Active Directory usuários no locatário associado ao Blockchain Workbench. Para obter mais informações, consulte [Adicionar usuários do Azure AD no Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* Uma conta de administrador do Blockchain Workbench. Para obter mais informações, consulte Adicionar [Administradores do Blockchain Workbench no Azure Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Olá, Blockchain!

Vamos criar um aplicativo básico no qual um solicitante envia uma solicitação e um Respondente envia uma resposta para a solicitação.
Por exemplo, uma solicitação pode ser "Olá, como você?" e a resposta pode ser "Eu sou ótimo!". A solicitação e a resposta são registradas no blockchain subjacente.

Siga as etapas para criar os arquivos de aplicativo ou você pode [baixar o exemplo do GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Arquivo de configuração

Os metadados de configuração definem os fluxos de trabalho de alto nível e o modelo de interação do aplicativo blockchain. Metadados de configuração representam os estágios de fluxo de trabalho e o modelo de interação do aplicativo blockchain.

1. Em seu editor favorito, crie um arquivo chamado `HelloBlockchain.json`.
2. Adicione o JSON a seguir para definir a configuração do aplicativo blockchain.

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

O arquivo de configuração tem várias seções. Os detalhes sobre cada seção são os seguintes:

### <a name="application-metadata"></a>Metadados do aplicativo

O início do arquivo de configuração contém informações sobre o aplicativo, incluindo o nome do aplicativo e a descrição.

### <a name="application-roles"></a>Funções da aplicação

A seção funções de aplicativo define as funções de usuário que podem agir ou participar dentro do aplicativo blockchain. Você define um conjunto de funções distintas com base na funcionalidade. No cenário de solicitação-resposta, há uma distinção entre a funcionalidade de um solicitante como uma entidade que produz solicitações e um Respondente como uma entidade que produz respostas.

### <a name="workflows"></a>Fluxos de trabalho

Os fluxos de trabalho definem um ou mais estágios e ações do contrato. No cenário de solicitação-resposta, o primeiro estágio (estado) do fluxo de trabalho é um solicitante (função) realiza uma ação (transição) para enviar uma solicitação (função). O próximo estágio (estado) é um Respondente (função) realiza uma ação (transição) para enviar uma resposta (função). O fluxo de trabalho de um aplicativo pode envolver Propriedades, funções e Estados necessários para descrever o fluxo de um contrato.

Para obter mais informações sobre o conteúdo dos arquivos de configuração, consulte [referência de configuração do fluxo de trabalho Blockchain do Azure](configuration.md).

## <a name="smart-contract-code-file"></a>Arquivo de código do contrato inteligente

Os contratos inteligentes representam a lógica de negócios do aplicativo blockchain. Atualmente, o Blockchain Workbench dá suporte a Ethereum para a contabilidade Blockchain. O Ethereum usa a [solidez](https://solidity.readthedocs.io) como linguagem de programação para escrever a lógica de negócios autoimport para contratos inteligentes.

Os contratos inteligentes em solidez são semelhantes às classes em linguagens orientadas a objeto. Cada contrato contém estado e funções para implementar estágios e ações do contrato inteligente.

Em seu editor favorito, crie um arquivo chamado `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Pragma da versão

Como prática recomendada, indique a versão da solidez que você está direcionando. A especificação da versão ajuda a evitar incompatibilidades com futuras versões de solidez.

Adicione o seguinte pragma de versão na parte superior do arquivo de código do contrato inteligente `HelloBlockchain.sol`.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Relacionamento de configuração e de código de contrato inteligente

O Blockchain Workbench usa o arquivo de configuração e o arquivo de código de contrato inteligente para criar um aplicativo Blockchain. Há uma relação entre o que é definido na configuração e o código no contrato inteligente. Os detalhes do contrato, as funções, os parâmetros e os tipos são necessários para corresponder ao criar o aplicativo. O Blockchain Workbench verifica os arquivos antes da criação do aplicativo.

### <a name="contract"></a>Contrato

Adicione o cabeçalho do **contrato** ao seu arquivo de código do contrato inteligente `HelloBlockchain.sol`.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Variáveis de estado

Variáveis de estado armazenam valores do estado para cada instância de contrato. As variáveis de estado em seu contrato devem corresponder às propriedades de fluxo de trabalho definidas no arquivo de configuração.

Adicione as variáveis de estado ao seu contrato no arquivo de código do contrato inteligente `HelloBlockchain.sol`.

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

### <a name="constructor"></a>Qu

O construtor define os parâmetros de entrada para uma nova instância de contrato inteligente de um fluxo de trabalho. Os parâmetros necessários para o Construtor são definidos como parâmetros do Construtor no arquivo de configuração. O número, a ordem e o tipo de parâmetros devem corresponder em ambos os arquivos.

Na função de construtor, escreva qualquer lógica de negócios que você queira executar antes de criar o contrato. Por exemplo, inicialize as variáveis de estado com valores iniciais.

Adicione a função de construtor ao seu contrato em seu arquivo de código de contrato inteligente `HelloBlockchain.sol`.

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

As funções são as unidades executáveis da lógica de negócios em um contrato. Os parâmetros necessários para a função são definidos como parâmetros de função no arquivo de configuração. O número, a ordem e o tipo de parâmetros devem corresponder em ambos os arquivos. As funções são associadas a transições em um fluxo de trabalho do Blockchain Workbench no arquivo de configuração. Uma transição é uma ação executada para mover para o próximo estágio do fluxo de trabalho de um aplicativo, conforme determinado pelo contrato.

Escreva qualquer lógica de negócios que você deseja executar na função. Por exemplo, modificar o valor de uma variável de estado.

1. Adicione as seguintes funções ao seu contrato no arquivo de código do contrato do `HelloBlockchain.sol`.

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

2. Salve seu arquivo de código de contrato inteligente `HelloBlockchain.sol`.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Adicionar aplicativo blockchain ao Blockchain Workbench

Para adicionar um aplicativo blockchain ao Blockchain Workbench, você carrega os arquivos de configuração e de contrato inteligente para definir o aplicativo.

1. Em um navegador da Web, navegue até o endereço Web do Blockchain Workbench. Por exemplo, `https://{workbench URL}.azurewebsites.net/` o aplicativo Web é criado quando você implanta o Blockchain Workbench. Para obter informações sobre como encontrar seu endereço Web do Blockchain Workbench, consulte [URL da Web do Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Entre como administrador do [Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecione **aplicativos** > **novo**. O painel **novo aplicativo** é exibido.
4. Selecione **carregar a configuração do contrato** > **procurar** para localizar o arquivo de configuração **HelloBlockchain. JSON** que você criou. O arquivo de configuração é validado automaticamente. Selecione o link **Mostrar** para exibir erros de validação. Corrija os erros de validação antes de implantar o aplicativo.
5. Selecione **carregar o código de contrato** > **navegue** para localizar o arquivo de código do contrato inteligente **HelloBlockchain. sol** . O arquivo de código é validado automaticamente. Selecione o link **Mostrar** para exibir erros de validação. Corrija os erros de validação antes de implantar o aplicativo.
6. Selecione **implantar** para criar o aplicativo blockchain com base nos arquivos de configuração e de contrato inteligente.

A implantação do aplicativo blockchain leva alguns minutos. Quando a implantação é concluída, o novo aplicativo é exibido em **aplicativos**. 

> [!NOTE]
> Você também pode criar aplicativos blockchain usando a [API REST do Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench).

## <a name="add-blockchain-application-members"></a>Adicionar membros do aplicativo blockchain

Adicione membros do aplicativo ao seu aplicativo para iniciar e executar ações em contratos. Para adicionar membros do aplicativo, você precisa ser um [administrador do Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

1. Selecione **aplicativos** > **Olá, Blockchain!** .
2. O número de membros associados ao aplicativo é exibido no canto superior direito da página. Para um novo aplicativo, o número de membros será zero.
3. Selecione o link **Membros** no canto superior direito da página. Uma lista atual de membros para o aplicativo é exibida.
4. Na lista Associação, selecione **adicionar membros**.
5. Selecione ou insira o nome do membro que você deseja adicionar. Somente usuários do Azure AD que existem no locatário do Blockchain Workbench são listados. Se o usuário não for encontrado, você precisará [Adicionar usuários do Azure ad](manage-users.md#add-azure-ad-users).
6. Selecione a **função** para o membro. Para o primeiro membro, selecione **solicitante** como a função.
7. Selecione **Adicionar** para adicionar o membro com a função associada ao aplicativo.
8. Adicione outro membro ao aplicativo com a função de **Respondente** .

Para obter mais informações sobre como gerenciar usuários no Blockchain Workbench, consulte [Managing Users in Azure Blockchain Workbench](manage-users.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo de instruções, você criou um aplicativo básico de solicitação e resposta. Para saber como usar o aplicativo, prossiga para o próximo artigo de instruções.

> [!div class="nextstepaction"]
> [Usando um aplicativo blockchain](use.md)

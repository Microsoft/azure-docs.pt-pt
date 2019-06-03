---
title: Criar uma aplicação de blockchain no Azure Blockchain Workbench
description: Tutorial sobre como criar uma aplicação de blockchain no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/30/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: b444ad799eaa356d654952c32ac58188de8d7131
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417381"
---
# <a name="tutorial-create-a-blockchain-application-in-azure-blockchain-workbench"></a>Tutorial: Criar uma aplicação de blockchain no Azure Blockchain Workbench

Pode utilizar o Azure Blockchain Workbench para criar aplicações de blockchain que representam os fluxos de trabalho de múltiplos intervenientes definidos pela configuração e o código de contrato inteligente.

Vai aprender a:

> [!div class="checklist"]
> * Configurar uma aplicação de blockchain
> * Criar um arquivo de código de contrato inteligente
> * Adicionar uma aplicação de blockchain ao Blockchain Workbench
> * Adicionar membros para a aplicação de blockchain

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implementação de Blockchain Workbench. Para obter mais informações, consulte [implementação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implementação.
* Utilizadores do Active Directory do Azure no inquilino associado com o Blockchain Workbench. Para obter mais informações, consulte [adicionar utilizadores do Azure AD no Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* Uma conta de administrador do Blockchain Workbench. Para obter mais informações, consulte Adicionar [os administradores da bancada de trabalho de Blockchain no Azure Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Olá, Blockchain!

Vamos criar uma aplicação básica no qual um requerente envia um pedido e um dispositivo de resposta, enviar uma resposta ao pedido.
Por exemplo, pode ser um pedido, "Olá, como estás?" e a lata de resposta ser, "Eu sou grande!". O pedido e resposta são registadas no blockchain subjacente.

Siga os passos para criar os ficheiros da aplicação ou pode [transferir o exemplo a partir do GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Ficheiro de configuração

Metadados de configuração definem os fluxos de trabalho de alto nível e o modelo de interação da aplicação de blockchain. Metadados de configuração representam as fases de fluxo de trabalho e o modelo de interação da aplicação de blockchain.

1. No seu editor favorito, crie um ficheiro denominado `HelloBlockchain.json`.
2. Adicione o seguinte JSON para definir a configuração da aplicação de blockchain.

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

O ficheiro de configuração tem várias seções. Detalhes sobre cada seção são os seguintes:

### <a name="application-metadata"></a>Metadados de aplicação

O início do ficheiro de configuração contém informações sobre a aplicação, incluindo o nome da aplicação e a descrição.

### <a name="application-roles"></a>Funções da aplicação

A seção de funções de aplicação define as funções de utilizador que podem agir ou tem participação na aplicação de blockchain. Definir um conjunto de funções distintas com base na funcionalidade. No cenário de solicitação-resposta, há uma distinção entre a funcionalidade de um requerente como uma entidade que produz pedidos e um dispositivo de resposta como uma entidade que produz respostas.

### <a name="workflows"></a>Fluxos de trabalho

Fluxos de trabalho definem um ou mais fases e ações do contrato. No cenário de solicitação-resposta, a primeira fase (estado) do fluxo de trabalho é que um requerente (função) efetua uma ação (transição) para enviar um pedido (função). O próximo estágio (estado) é que um dispositivo de resposta (função) efetua uma ação (transição) para enviar uma resposta (função). Fluxo de trabalho de um aplicativo pode envolver a propriedades, funções, e os Estados necessários descrevem o fluxo de um contrato.

Para obter mais informações sobre o conteúdo dos ficheiros de configuração, consulte [referência de configuração do fluxo de trabalho do Azure Blockchain](configuration.md).

## <a name="smart-contract-code-file"></a>Arquivo de código de contrato inteligente

Contratos inteligentes representam a lógica de negócio da aplicação de blockchain. Atualmente, o Blockchain Workbench suporta Ethereum para a razão de blockchain. Utiliza Ethereum [solidez](https://solidity.readthedocs.io) como sua linguagem de programação para escrever a lógica de negócio Self-a imposição de contratos inteligentes.

Contratos inteligentes em solidez são semelhantes a classes em linguagens orientadas a objeto. Cada contrato contém o estado e funções para implementar as fases e ações do contrato inteligente.

No seu editor favorito, crie um ficheiro chamado `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Versão pragma

Como melhor prática, indica a versão do solidez de destino. Especificar a versão ajuda a evitar incompatibilidades com futuras versões do solidez.

Adicionar o pragma de versão seguintes na parte superior do `HelloBlockchain.sol` arquivo de código de contrato inteligente.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Configuração e a relação de código de contrato inteligente

Blockchain Workbench utiliza o ficheiro de configuração e o arquivo de código de contrato inteligente para criar uma aplicação de blockchain. Existe uma relação entre o que é definido na configuração e o código no contrato de inteligente. Detalhes do contrato, funções, parâmetros e tipos são necessários para corresponder ao criar a aplicação. Blockchain Workbench verifica os ficheiros antes da criação da aplicação.

### <a name="contract"></a>contrato

Adicionar a **contrato** cabeçalho para sua `HelloBlockchain.sol` arquivo de código de contrato inteligente.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Variáveis de estado

Variáveis de estado armazenam valores de estado para cada instância de contrato. As variáveis de estado no seu contrato têm de corresponder as propriedades de fluxo de trabalho definidas no ficheiro de configuração.

Adicione as variáveis de estado para o contrato em seu `HelloBlockchain.sol` arquivo de código de contrato inteligente.

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

O construtor define parâmetros de entrada para uma nova instância de contrato inteligente de um fluxo de trabalho. Parâmetros necessários para o construtor são definidos como parâmetros do construtor no ficheiro de configuração. O número, a ordem e o tipo de parâmetros devem corresponder em ambos os ficheiros.

A função do construtor, escreva qualquer lógica de negócios que pretende executar antes de criar o contrato. Por exemplo, inicialize as variáveis de estado com a partir de valores.

Adicionar a função do construtor para o contrato em seu `HelloBlockchain.sol` arquivo de código de contrato inteligente.

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

As funções são as unidades executáveis de lógica de negócios dentro de um contrato. Parâmetros necessários para a função são definidos como parâmetros de função no arquivo de configuração. O número, a ordem e o tipo de parâmetros devem corresponder em ambos os ficheiros. As funções são associadas a transições num fluxo de trabalho de Blockchain Workbench no ficheiro de configuração. Uma transição é uma ação executada para mudar para o próximo estágio de fluxo de trabalho de um aplicativo conforme determinado pelo contrato.

Escreva qualquer lógica de negócio que pretende executar na função. Por exemplo, modificar o valor da variável de estado.

1. Adicione as seguintes funções para o contrato em seu `HelloBlockchain.sol` arquivo de código de contrato inteligente.

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

2. Guardar sua `HelloBlockchain.sol` arquivo de código de contrato inteligente.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Adicionar a aplicação de blockchain ao Blockchain Workbench

Para adicionar uma aplicação de blockchain, Blockchain Workbench, vai carregar a configuração e os ficheiros de contrato inteligente para definir a aplicação.

1. Num browser, navegue para o endereço de web de Blockchain Workbench. Por exemplo, `https://{workbench URL}.azurewebsites.net/` o aplicativo web é criado quando implementou o Blockchain Workbench. Para obter informações sobre como encontrar seu Blockchain Workbench endereço da web, consulte [URL de Web do Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Inicie sessão como um [administrador Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecione **aplicativos** > **novo**. O **nova aplicação** é apresentado o painel.
4. Selecione **carregar a configuração de contrato** > **procurar** para localizar o **HelloBlockchain.json** ficheiro de configuração que criou. O ficheiro de configuração é validado automaticamente. Selecione o **mostrar** link para exibir erros de validação. Corrigi erros de validação antes de implementar a aplicação.
5. Selecione **carregar o código de contrato** > **procurar** para localizar o **HelloBlockchain.sol** arquivo de código de contrato inteligente. O arquivo de código é validado automaticamente. Selecione o **mostrar** link para exibir erros de validação. Corrigi erros de validação antes de implementar a aplicação.
6. Selecione **Deploy** para criar a aplicação de blockchain com base na configuração e os ficheiros de contrato inteligente.

Implementação da aplicação de blockchain demora alguns minutos. Quando a implementação estiver concluída, a nova aplicação é apresentada na **aplicativos**. 

> [!NOTE]
> Também pode criar aplicações de blockchain ao utilizar o [API de REST do Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench).

## <a name="add-blockchain-application-members"></a>Adicionar membros de aplicação de blockchain

Adicione membros de aplicação à sua aplicação para iniciar e executar ações sobre contratos. Para adicionar membros de aplicação, tem de ser um [administrador Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

1. Selecione **aplicativos** > **Olá, Blockchain!** .
2. O número de membros associados à aplicação é apresentado no canto superior direito da página. Para um novo aplicativo, o número de membros serão zero.
3. Selecione o **membros** ligação no canto superior direito da página. É apresentada uma lista atual de membros para a aplicação.
4. Na lista de associação, selecione **adicionar membros**.
5. Selecione ou introduza o nome do membro que pretende adicionar. Apenas são listados os utilizadores do Azure AD que existe no inquilino do Blockchain Workbench. Se o utilizador não for encontrado, precisará [adicionar utilizadores do Azure AD](manage-users.md#add-azure-ad-users).
6. Selecione o **função** do membro. Para o primeiro membro, selecione **requerente** que a função.
7. Selecione **adicionar** para adicionar o membro com a função associada à aplicação.
8. Adicionar outro membro para a aplicação com o **respondente** função.

Para obter mais informações sobre a gestão de utilizadores no Blockchain Workbench, consulte [gestão de utilizadores no Azure Blockchain Workbench](manage-users.md)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo de procedimentos, acabou de criar uma aplicação básica de solicitação e resposta. Para saber como utilizar a aplicação, avance para o artigo seguinte que mostra como.

> [!div class="nextstepaction"]
> [Através de uma aplicação de blockchain](use.md)

---
title: Use Visual Studio Code para criar, compilar e implantar contratos inteligentes usando o serviço Blockchain do Azure
description: Tutorial sobre como usar a extensão do kit de desenvolvimento do Azure Blockchain para Ethereum no Visual Studio Code para criar, compilar e implantar um contrato inteligente no serviço Blockchain do Azure.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 09/10/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 96fe4d77efdd1fda309d7da021bcc208edd2dfe9
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935048"
---
# <a name="tutorial-usevisual-studio-code-to-create-buildanddeploysmartcontracts"></a>Tutorial: Use Visual Studio Code para criar, compilar e implantar contratos inteligentes

Neste tutorial, use a extensão do kit de desenvolvimento do Azure Blockchain para Ethereum no Visual Studio Code para criar, compilar e implantar um contrato inteligente no serviço Blockchain do Azure. Você também usa Truffle para executar uma função de contrato inteligente por meio de uma transação.

Você usa o kit de desenvolvimento do Azure Blockchain para Ethereum para:

> [!div class="checklist"]
> * Criar um contrato inteligente
> * Implantar um contrato inteligente
> * Executar uma função de contrato inteligente por meio de uma transação
> * Estado do contrato de consulta

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Início [rápido completo: Usar Visual Studio Code para se conectar a uma rede do Azure Blockchain Service Consortium](connect-vscode.md)

## <a name="create-a-smart-contract"></a>Criar um contrato inteligente

O kit de desenvolvimento do Azure Blockchain para Ethereum usa modelos de projeto e ferramentas de Truffle para ajudar a Scaffold, criar e implantar contratos. Antes de começar, conclua o [guia de início rápido de pré-requisito: Use Visual Studio Code para se conectar a uma rede](connect-vscode.md)do Azure Blockchain Service Consortium. O guia de início rápido orienta você pela instalação e configuração do kit de desenvolvimento do Azure Blockchain para Ethereum.

1. Na paleta de comandos do vs Code, **escolha Azure Blockchain: Novo projeto**de solidez.
1. Escolha **criar projeto básico**.
1. Crie uma nova pasta chamada `HelloBlockchain` e **Selecione novo caminho de projeto**.

O kit de desenvolvimento do Azure Blockchain cria e inicializa um novo projeto de solidez para você. O projeto básico inclui um contrato inteligente **HelloBlockchain** de exemplo e todos os arquivos necessários para compilar e implantar em seu membro do consórcio no serviço Blockchain do Azure. Pode levar vários minutos para que o projeto seja criado. Você pode monitorar o progresso no painel do Terminal VS Code selecionando a saída para o Azure Blockchain.

A estrutura do projeto é semelhante ao exemplo a seguir:

   ![Projeto de solidez](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Criar um contrato inteligente

Os contratos inteligentes estão localizados no diretório de **contratos** do projeto. Você compila contratos inteligentes antes de implantá-los em um blockchain. Use o comando **criar contratos** para compilar todos os contratos inteligentes em seu projeto.

1. Na barra lateral do VS Code Explorer, expanda a pasta **contratos** em seu projeto.
1. Clique com o botão direito do mouse em **HelloBlockchain. sol** e escolha **criar contratos** no menu.

    ![Criar contratos](./media/send-transaction/build-contracts.png)

O kit de desenvolvimento Blockchain do Azure usa o Truffle para compilar os contratos inteligentes.

![Compilar saída](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Implantar um contrato inteligente

O Truffle usa scripts de migração para implantar seus contratos em uma rede Ethereum. As migrações são arquivos JavaScript localizados no diretório de **migrações** do projeto.

1. Para implantar seu contrato inteligente, clique com o botão direito do mouse em **HelloBlockchain. sol** e escolha **implantar contratos** no menu.
1. Escolha sua rede do Azure Blockchain Consortium na paleta de comandos. A rede consórcio blockchain foi adicionada ao arquivo de configuração Truffle do projeto quando você criou o projeto.
1. Escolha **gerar mnemônico**. Escolha um nome de arquivo e salve o arquivo mnemônico na pasta do projeto. Por exemplo, `myblockchainmember.env`. O arquivo mnemônico é usado para gerar uma chave privada Ethereum para seu membro blockchain.

O kit de desenvolvimento do Azure Blockchain usa o Truffle para executar o script de migração para implantar os contratos no Blockchain.

![Contrato implantado com êxito](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Chamar uma função de contrato

A função **SendRequest** do contrato **HelloBlockchain** altera a variável de estado **RequestMessage** . A alteração do estado de uma rede blockchain é feita por meio de uma transação. Você pode criar um script para executar a função **SendRequest** por meio de uma transação.

1. Crie um novo arquivo na raiz do seu projeto Truffle e nomeie- `sendrequest.js`o. Adicione o seguinte código JavaScript Web3 ao arquivo.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Quando o kit de desenvolvimento do Azure Blockchain cria um projeto, o arquivo de configuração Truffle é gerado com os detalhes do ponto de extremidade de rede Blockchain do consórcio. Abra **Truffle-config. js** em seu projeto. O arquivo de configuração lista duas redes: uma denominada desenvolvimento e outra com o mesmo nome que o consórcio.
1. No painel de terminal do VS Code, use Truffle para executar o script em sua rede do consórcio blockchain. Na barra de menus do painel terminal, selecione a guia **terminal** e o **PowerShell** na lista suspensa.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Substitua \<blockchain Network\> pelo nome da rede blockchain definida no **Truffle-config. js**.

Truffle executa o script em sua rede blockchain.

![Saída de scripts](./media/send-transaction/execute-transaction.png)

Quando você executa a função de um contrato por meio de uma transação, a transação não é processada até que um bloco seja criado. As funções destinadas a serem executadas por meio de uma transação retornam uma ID de transação em vez de um valor de retorno.

## <a name="query-contract-state"></a>Estado do contrato de consulta

As funções de contrato inteligente podem retornar o valor atual das variáveis de estado. Vamos adicionar uma função para retornar o valor de uma variável de estado.

1. Em **HelloBlockchain. sol**, adicione uma função **GetMessage** ao contrato inteligente **HelloBlockchain** .

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    A função retorna a mensagem armazenada em uma variável de estado com base no estado atual do contrato.

1. Clique com o botão direito do mouse em **HelloBlockchain. sol** e escolha **criar contratos** no menu para compilar as alterações no contrato inteligente.
1. Para implantar, clique com o botão direito do mouse em **HelloBlockchain. sol** e escolha **implantar contratos** no menu. Quando solicitado, escolha sua rede do Azure Blockchain Consortium na paleta de comandos.
1. Em seguida, crie um script usando para chamar a função **GetMessage** . Crie um novo arquivo na raiz do seu projeto Truffle e nomeie- `getmessage.js`o. Adicione o seguinte código JavaScript Web3 ao arquivo.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. No painel de terminal do VS Code, use Truffle para executar o script em sua rede do blockchain. Na barra de menus do painel terminal, selecione a guia **terminal** e o **PowerShell** na lista suspensa.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Substitua \<blockchain Network\> pelo nome da rede blockchain definida no **Truffle-config. js**.

O script consulta o contrato inteligente chamando a função getMessage. O valor atual da variável de estado **RequestMessage** é retornado.

![Saída de scripts](./media/send-transaction/execute-get.png)

Observe que o valor não é **Olá, blockchain!** . Em vez disso, o valor retornado é um espaço reservado. Quando você altera e implanta o contrato, o contrato Obtém um novo endereço de contrato e as variáveis de estado são valores atribuídos no construtor do contrato inteligente. O script de migração de exemplo Truffle **2_deploy_contracts. js** implanta o contrato inteligente e passa um valor de espaço reservado como um argumento. O construtor define a variável de estado **RequestMessage** para o valor de espaço reservado e é o que é retornado.

1. Para definir a variável de estado **RequestMessage** e consultar o valor, execute os scripts **SendRequest. js** e **GetMessage. js** novamente.

    ![Saída de scripts](./media/send-transaction/execute-set-get.png)

    **SendRequest. js** define a variável de estado **RequestMessage** como **Hello, blockchain!** e **GetMessage. js** consulta o contrato quanto ao valor da variável de estado **RequestMessage** e retorna **Hello, blockchain!** .

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá excluir os recursos excluindo `myResourceGroup` o grupo de recursos criado no início rápido de pré-requisito de *um membro blockchain* .

Para excluir o grupo de recursos:

1. No portal do Azure, navegue até o **grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que deseja excluir.
1. Selecione **Eliminar grupo de recursos**. Verifique a exclusão inserindo o nome do grupo de recursos e selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você criou um projeto de solidez de exemplo usando o kit de desenvolvimento Blockchain do Azure. Você criou e implantou um contrato inteligente e, em seguida, chamou uma função por meio de uma transação em uma rede do blockchain Consortium hospedada no serviço Blockchain do Azure.

> [!div class="nextstepaction"]
> [Desenvolvendo aplicativos blockchain usando o serviço Blockchain do Azure](develop.md)

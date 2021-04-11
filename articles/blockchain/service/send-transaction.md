---
title: Criar, construir, & implementar contratos inteligentes tutorial - Azure Blockchain Service
description: Tutorial sobre como usar o Azure Blockchain Development Kit para extensão Ethereum no Código do Estúdio Visual para criar, construir e implementar um contrato inteligente no Azure Blockchain Service.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: 4c2df952480d2c30de10838c3d0f7714fc7e6126
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628650"
---
# <a name="tutorial-create-build-and-deploy-smart-contracts-on-azure-blockchain-service"></a>Tutorial: Criar, construir e implementar contratos inteligentes no Azure Blockchain Service

Neste tutorial, use o Kit de Desenvolvimento Azure Blockchain para extensão Ethereum no Código do Estúdio Visual para criar, construir e implementar um contrato inteligente no Azure Blockchain Service. Também usa o kit de desenvolvimento para executar uma função de contrato inteligente através de uma transação.

Você usa o Kit de Desenvolvimento Azure Blockchain para: Ethereum para:

> [!div class="checklist"]
> * Criar um contrato inteligente
> * Implementar um contrato inteligente
> * Execute uma função de contrato inteligente através de uma transação

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Quickstart completo: Use o Código do Estúdio Visual para ligar a uma rede de consórcios do Azure Blockchain Service](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Kit de desenvolvimento Azure Blockchain para extensão Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x ou superior](https://nodejs.org/download)
* [Git 2.10.x ou superior](https://git-scm.com)
* [Trufa 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

No Windows, é necessário um compilador C++ instalado para o módulo nó-giro. Pode utilizar as ferramentas MSBuild:

* Se o Visual Studio 2017 for instalado, configuure npm para usar as ferramentas MSBuild com o comando `npm config set msvs_version 2017 -g`
* Se o Visual Studio 2019 estiver instalado, desacorda o caminho das ferramentas de construção ms para npm. Por exemplo, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Caso contrário, instale as ferramentas vs build autónomas utilizando `npm install --global windows-build-tools` numa execução elevada como invólucro de comando *do administrador.*

Para obter mais informações sobre nó-gyp, consulte o [repositório nó-gyp no GitHub.](https://github.com/nodejs/node-gyp)

## <a name="create-a-smart-contract"></a>Criar um contrato inteligente

O Azure Blockchain Development Kit para Ethereum usa modelos de projeto e ferramentas truffle para ajudar andaimes, construir e implementar contratos. Antes de começar, preencha o [quickstart pré-requisito: Use o Código do Estúdio Visual para ligar a uma rede de consórcios do Azure Blockchain Service](connect-vscode.md). O quickstart guia-o através da instalação e configuração do Kit de Desenvolvimento blockchain Azure para o Ethereum.

1. A partir da paleta de comandos VS Code, escolha **Blockchain: Novo Projeto solidity**.
1. Escolha **Criar projeto básico.**
1. Crie uma nova pasta nomeada `HelloBlockchain` e **selecione um novo caminho de projeto.**

O Azure Blockchain Development Kit cria e iniciaisiza um novo projeto solidity para si. O projeto básico inclui uma amostra de contrato inteligente **HelloBlockchain** e todos os ficheiros necessários para construir e implementar para o seu membro do consórcio no Azure Blockchain Service. Pode levar vários minutos para que o projeto seja criado. Pode monitorizar o progresso no painel terminal do VS Code selecionando a saída para Azure Blockchain.

A estrutura do projeto parece o seguinte exemplo:

   ![Projeto solidez](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Construir um contrato inteligente

Os contratos inteligentes estão **localizados** no diretório de contratos do projeto. Compilas contratos inteligentes antes de os colocares numa blockchain. Utilize o comando **Build Contracts** para compilar todos os contratos inteligentes do seu projeto.

1. Na barra lateral do explorador do código VS, expanda a pasta **de contratos** no seu projeto.
1. Clique com o botão direito **HelloBlockchain.sol** e escolha **Build Contracts** no menu.

    ![Escolha o menu de contratos de construção ](./media/send-transaction/build-contracts.png)

O Azure Blockchain Development Kit usa a Truffle para compilar os contratos inteligentes.

![Saída do compilador de trufas](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Implementar um contrato inteligente

A Truffle usa scripts de migração para implementar os seus contratos numa rede Ethereum. As migrações são ficheiros JavaScript **localizados** no diretório de migrações do projeto.

1. Para implementar o seu contrato inteligente, clique com o botão direito **HelloBlockchain.sol** e escolha Contratos de **Implementação** no menu.
1. Escolha a sua rede de consórcio Azure Blockchain na paleta de comando. A rede blockchain do consórcio foi adicionada ao ficheiro de configuração truffle do projeto quando criou o projeto.
1. Escolha **Gerar mnemônico**. Escolha um nome de ficheiro e guarde o ficheiro mnemónico na pasta do projeto. Por exemplo, `myblockchainmember.env`. O ficheiro mnemónico é usado para gerar uma chave privada Ethereum para o seu membro blockchain.

O Azure Blockchain Development Kit usa a Truffle para executar o script de migração para implementar os contratos na blockchain.

![Contrato implementado com sucesso](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function&quot;></a>Ligue para uma função contratual
A função **SendRequest** do contrato **HelloBlockchain** altera a variável de estado **RequestMessage.** Mudar o estado de uma rede blockchain é feito através de uma transação. Pode criar um script para executar a função **SendRequest** através de uma transação.

1. Crie um novo ficheiro na raiz do seu projeto Truffle e nomeie-o. `sendrequest.js` Adicione o seguinte código JavaScript Web3 ao ficheiro.

    ```javascript
    var HelloBlockchain = artifacts.require(&quot;HelloBlockchain");
        
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

1. Quando o Azure Blockchain Development Kit cria um projeto, o ficheiro de configuração truffle é gerado com os detalhes do ponto final da rede blockchain do seu consórcio. Abra **truffle-config.js** no seu projeto. O ficheiro de configuração lista duas redes: uma com o nome de desenvolvimento e outra com o mesmo nome do consórcio.
1. No painel de terminais do VS Code, use a Truffle para executar o script na sua rede de blockchain do consórcio. Na barra de menu do painel de terminais, selecione o **separador Terminal** e o **PowerShell** no dropdown.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    \<blockchain network\>Substitua-o pelo nome da rede blockchain definida no **truffle-config.js**.

A Truffle executa o guião na tua rede blockchain.

![A produção que mostra a transação foi enviada](./media/send-transaction/execute-transaction.png)

Quando executa a função de um contrato através de uma transação, a transação não é processada até que um bloco seja criado. As funções destinadas a serem executadas através de uma transação devolvem um ID de transação em vez de um valor de retorno.

## <a name="query-contract-state"></a>Estado de consulta

As funções de contrato inteligentes podem devolver o valor atual das variáveis do Estado. Vamos adicionar uma função para devolver o valor de uma variável do estado.

1. Em **HelloBlockchain.sol,** adicione uma função **getMessage** ao contrato inteligente **HelloBlockchain.**

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    A função devolve a mensagem armazenada numa variável do estado com base no estado atual do contrato.

1. Clique com o botão direito **HelloBlockchain.sol** e escolha Contratos de **Construção** no menu para compilar as alterações ao contrato inteligente.
1. Para implementar, clique com o botão direito **HelloBlockchain.sol** e escolha Contratos de **Implementação** no menu. Quando solicitado, escolha a sua rede de consórcio Azure Blockchain na paleta de comando.
1. Em seguida, crie um script que utilize para chamar a função **getMessage.** Crie um novo ficheiro na raiz do seu projeto Truffle e nomeie-o. `getmessage.js` Adicione o seguinte código JavaScript Web3 ao ficheiro.

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

1. No painel de terminais do VS Code, utilize a Truffle para executar o script na sua rede blockchain. Na barra de menu do painel de terminais, selecione o **separador Terminal** e o **PowerShell** no dropdown.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    \<blockchain network\>Substitua-o pelo nome da rede blockchain definida no **truffle-config.js**.

O script consulta o contrato inteligente chamando a função getMessage. O valor atual da variável do estado **de RequestMessage** é devolvido.

![Saída da consulta getmessage mostrando o valor atual da variável do estado requestMessage](./media/send-transaction/execute-get.png)

Note que o valor não é **Olá, blockchain!** Em vez disso, o valor devolvido é um espaço reservado. Quando muda e implementa o contrato, o contrato alterado é implantado numa nova morada e as variáveis do Estado são atribuídas valores no construtor de contratos inteligentes. A amostra trufa **2_deploy_contracts.js** script de migração implementa o contrato inteligente e passa um valor reservado como argumento. O construtor define a variável do estado **requestMessage** para o valor do espaço reservado e é isso que é devolvido.

1. Para definir a variável do estado **de RequestMessage** e consultar o valor, executar novamente os **scriptssendrequest.js** e **getmessage.js.**

    ![A saída de scripts de sendrequest e getmessage mostrando RequestMessage foi definida](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** define a variável de estado **RequestMessage** para **Hello, blockchain!** e **getmessage.js** questiona o contrato de valor da variável estatal **RequestMessage** e devolve **Olá, blockchain!**
## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode eliminar os recursos eliminando o `myResourceGroup` grupo de recursos que criou no Create a blockchain *member* pré-requisito quickstart.

Para eliminar o grupo de recursos:

1. No portal Azure, navegue para o **grupo De recursos** no painel de navegação esquerdo e selecione o grupo de recursos que pretende eliminar.
1. Selecione **Eliminar grupo de recursos**. Verifique a eliminação introduzindo o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um projeto de solididade de amostra usando o Azure Blockchain Development Kit. Você construiu e implementou um contrato inteligente então chamado de função através de uma transação em uma rede de consórcio blockchain hospedado no Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Desenvolvimento de aplicações blockchain usando o Serviço Azure Blockchain](develop.md)

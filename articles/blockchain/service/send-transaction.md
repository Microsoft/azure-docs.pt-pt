---
title: Criar, construir, & implementar contratos inteligentes tutorial - Azure Blockchain Service
description: Tutorial sobre como usar o Azure Blockchain Development Kit para extensão Ethereum no Código do Estúdio Visual para criar, construir e implementar um contrato inteligente no Azure Blockchain Service.
ms.date: 04/22/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: dc23c680dfb2ed33cae2a251af16e1b1f25c6ac7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82086662"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Tutorial: Criar, construir e implementar contratos inteligentes no Azure Blockchain Service

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
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Adicione python.exe ao seu caminho. A versão python 2.7.15 no seu caminho é necessária para o Azure Blockchain Development Kit.
* [Trufa 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

No Windows, é necessário um compilador C++ instalado para o módulo nó-giro. Pode utilizar as ferramentas MSBuild:

* Se o Visual Studio 2017 for instalado, configuure npm para usar as ferramentas MSBuild com o comando `npm config set msvs_version 2017 -g`
* Se o Visual Studio 2019 estiver instalado, desacorda o caminho das ferramentas de construção ms para npm. Por exemplo, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Caso contrário, instale as ferramentas vs build autónomas utilizando `npm install --global windows-build-tools` numa execução elevada como invólucro de comando *do administrador.*

Para obter mais informações sobre nó-gyp, consulte o [repositório nó-gyp no GitHub.](https://github.com/nodejs/node-gyp)

## <a name="create-a-smart-contract"></a>Criar um contrato inteligente

O Azure Blockchain Development Kit para Ethereum usa modelos de projeto e ferramentas truffle para ajudar andaimes, construir e implementar contratos. Antes de começar, preencha o [quickstart pré-requisito: Use o Código do Estúdio Visual para ligar a uma rede de consórcios do Azure Blockchain Service](connect-vscode.md). O quickstart guia-o através da instalação e configuração do Kit de Desenvolvimento blockchain Azure para o Ethereum.

1. A partir da paleta de comandos VS Code, escolha **Azure Blockchain: Novo Projeto solidity**.
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

## <a name="call-a-contract-function"></a>Ligue para uma função contratual

A função **SendRequest** do contrato **HelloBlockchain** altera a variável de estado **RequestMessage.** Mudar o estado de uma rede blockchain é feito através de uma transação. Pode utilizar a página de interação inteligente do Kit de Desenvolvimento blockchain Azure para ligar para a função **SendRequest** através de uma transação.

1. Para interagir com o seu contrato inteligente, clique com o botão direito **HelloBlockchain.sol** e escolha mostrar página de **interação com contrato inteligente** a partir do menu.

    ![Escolha mostrar página de interação de contrato inteligente do menu](./media/send-transaction/contract-interaction.png)

1. A página de interação permite-lhe escolher uma versão contratual implementada, funções de chamada, ver o estado atual e ver metadados.

    ![Página de interação de contrato inteligente exemplo](./media/send-transaction/interaction-page.png)

1. Para convocar a função de contrato inteligente, selecione a ação contratual e passe os seus argumentos. Escolha ação contratual **SendRequest** e insira **Hello, Blockchain!** para o **parâmetro de mesagens de pedido.** Selecione **Executar** para ligar para a função **SendRequest** através de uma transação.

    ![Execute a ação SendRequest](./media/send-transaction/sendrequest-action.png)

Uma vez que a transação é processada, a secção de interação reflete as alterações do estado.

![Alterações do Estado contratual](./media/send-transaction/contract-state.png)

A função SendRequest define os campos **RequestMessage** e **State.** O estado atual **de RequestMessage** é o argumento que passou **Hello, Blockchain.** O valor do campo **do Estado** continua a ser **Pedido.**

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode eliminar os recursos eliminando o `myResourceGroup` grupo de recursos que criou no Create a blockchain *member* pré-requisito quickstart.

Para eliminar o grupo de recursos:

1. No portal Azure, navegue para o **grupo De recursos** no painel de navegação esquerdo e selecione o grupo de recursos que pretende eliminar.
1. Selecione **Eliminar grupo de recursos**. Verifique a eliminação introduzindo o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um projeto de solididade de amostra usando o Azure Blockchain Development Kit. Você construiu e implementou um contrato inteligente então chamado de função através de uma transação em uma rede de consórcio blockchain hospedado no Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Desenvolvimento de aplicações blockchain usando o Serviço Azure Blockchain](develop.md)

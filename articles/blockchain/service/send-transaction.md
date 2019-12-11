---
title: Tutorial de criar, compilar, & implantar contratos inteligentes – serviço Blockchain do Azure
description: Tutorial sobre como usar a extensão do kit de desenvolvimento do Azure Blockchain para Ethereum no Visual Studio Code para criar, compilar e implantar um contrato inteligente no serviço Blockchain do Azure.
ms.date: 12/06/2019
ms.topic: tutorial
ms.reviewer: chrisseg
ms.openlocfilehash: 5b901ab904425a22d2fe9643ffa75a4e978efa88
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972883"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Tutorial: criar, compilar e implantar contratos inteligentes no serviço Blockchain do Azure

Neste tutorial, use a extensão do kit de desenvolvimento do Azure Blockchain para Ethereum no Visual Studio Code para criar, compilar e implantar um contrato inteligente no serviço Blockchain do Azure. Você também usa o kit de desenvolvimento para executar uma função de contrato inteligente por meio de uma transação.

Você usa o kit de desenvolvimento do Azure Blockchain para Ethereum para:

> [!div class="checklist"]
> * Criar um contrato inteligente
> * Implantar um contrato inteligente
> * Executar uma função de contrato inteligente por meio de uma transação

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Concluir [o início rápido: usar Visual Studio Code para se conectar a uma rede do Azure Blockchain Service Consortium](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit para extensão Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node. js 10.15. x ou superior](https://nodejs.org/download)
* [Git 2.10. x ou superior](https://git-scm.com)
* [2.7.15 Python](https://www.python.org/downloads/release/python-2715/) Adicione Python. exe ao seu caminho. O Python versão 2.7.15 em seu caminho é necessário para o kit de desenvolvimento do Azure Blockchain.
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

No Windows, um compilador C++ instalado é necessário para o módulo node-gyp. Você pode usar as ferramentas do MSBuild:

* Se o Visual Studio 2017 estiver instalado, configure o NPM para usar as ferramentas do MSBuild com o comando `npm config set msvs_version 2017 -g`
* Se o Visual Studio 2019 estiver instalado, defina o caminho das ferramentas de Build do MS para NPM. Por exemplo, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Caso contrário, instale as ferramentas autônomas do VS Build usando `npm install --global windows-build-tools` em um shell de comando *Executar como administrador* com privilégios elevados.

Para obter mais informações sobre o node-gyp, consulte o [repositório node-gyp no GitHub](https://github.com/node-gyp).

## <a name="create-a-smart-contract"></a>Criar um contrato inteligente

O kit de desenvolvimento do Azure Blockchain para Ethereum usa modelos de projeto e ferramentas de Truffle para ajudar a Scaffold, criar e implantar contratos. Antes de começar, conclua o guia de [início rápido de pré-requisito: Use Visual Studio Code para se conectar a uma rede do Azure Blockchain Service Consortium](connect-vscode.md). O guia de início rápido orienta você pela instalação e configuração do kit de desenvolvimento do Azure Blockchain para Ethereum.

1. Na paleta de comandos do VS Code, escolha **Azure Blockchain: novo projeto de solidez**.
1. Escolha **criar projeto básico**.
1. Crie uma nova pasta chamada `HelloBlockchain` e **Selecione novo caminho de projeto**.

O kit de desenvolvimento do Azure Blockchain cria e inicializa um novo projeto de solidez para você. O projeto básico inclui um contrato inteligente **HelloBlockchain** de exemplo e todos os arquivos necessários para compilar e implantar em seu membro do consórcio no serviço Blockchain do Azure. Pode levar vários minutos para que o projeto seja criado. Você pode monitorar o progresso no painel do Terminal VS Code selecionando a saída para o Azure Blockchain.

A estrutura do projeto é semelhante ao exemplo a seguir:

   ![Projeto de solidez](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Criar um contrato inteligente

Os contratos inteligentes estão localizados no diretório de **contratos** do projeto. Você compila contratos inteligentes antes de implantá-los em um blockchain. Use o comando **criar contratos** para compilar todos os contratos inteligentes em seu projeto.

1. Na barra lateral do VS Code Explorer, expanda a pasta **contratos** em seu projeto.
1. Clique com o botão direito do mouse em **HelloBlockchain. sol** e escolha **criar contratos** no menu.

    ![Menu escolher contratos de compilação ](./media/send-transaction/build-contracts.png)

O kit de desenvolvimento Blockchain do Azure usa o Truffle para compilar os contratos inteligentes.

![Saída do compilador Truffle](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Implantar um contrato inteligente

O Truffle usa scripts de migração para implantar seus contratos em uma rede Ethereum. As migrações são arquivos JavaScript localizados no diretório de **migrações** do projeto.

1. Para implantar seu contrato inteligente, clique com o botão direito do mouse em **HelloBlockchain. sol** e escolha **implantar contratos** no menu.
1. Escolha sua rede do Azure Blockchain Consortium na paleta de comandos. A rede consórcio blockchain foi adicionada ao arquivo de configuração Truffle do projeto quando você criou o projeto.
1. Escolha **gerar mnemônico**. Escolha um nome de arquivo e salve o arquivo mnemônico na pasta do projeto. Por exemplo, `myblockchainmember.env`. O arquivo mnemônico é usado para gerar uma chave privada Ethereum para seu membro blockchain.

O kit de desenvolvimento do Azure Blockchain usa o Truffle para executar o script de migração para implantar os contratos no Blockchain.

![Contrato implantado com êxito](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Chamar uma função de contrato

A função **SendRequest** do contrato **HelloBlockchain** altera a variável de estado **RequestMessage** . A alteração do estado de uma rede blockchain é feita por meio de uma transação. Você pode usar a página de interação do contrato inteligente do kit de desenvolvimento do Azure Blockchain para chamar a função **SendRequest** por meio de uma transação.

1. Para interagir com seu contrato inteligente, clique com o botão direito do mouse em **HelloBlockchain. sol** e escolha **mostrar página de interação do contrato inteligente** no menu.

    ![Escolha mostrar página de interação do contrato inteligente no menu](./media/send-transaction/contract-interaction.png)

1. A página interação permite que você escolha uma versão de contrato implantada, chame funções, exiba o estado atual e exiba os metadados.

    ![Página de interação do contrato inteligente de exemplo](./media/send-transaction/interaction-page.png)

1. Para chamar a função de contrato inteligente, selecione a ação de contrato e passe seus argumentos. Escolha a ação de contrato **SendRequest** e insira **Olá, Blockchain!** para o parâmetro **requestMessage** . Selecione **executar** para chamar a função **SendRequest** por meio de uma transação.

    ![Executar ação SendRequest](./media/send-transaction/sendrequest-action.png)

Depois que a transação é processada, a seção de interação reflete as alterações de estado.

![Alterações de estado do contrato](./media/send-transaction/contract-state.png)

A função SendRequest define os campos **RequestMessage** e **State** . O estado atual de **RequestMessage** é o argumento que você passou **Olá, Blockchain**. O valor do campo de **estado** permanece **solicitação**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá excluir os recursos excluindo o grupo de recursos `myResourceGroup` criado no início rápido de pré-requisito de *um membro blockchain* .

Para eliminar o grupo de recursos:

1. No portal do Azure, navegue até o **grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que deseja excluir.
1. Selecione **Eliminar grupo de recursos**. Verifique a exclusão inserindo o nome do grupo de recursos e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você criou um projeto de solidez de exemplo usando o kit de desenvolvimento Blockchain do Azure. Você criou e implantou um contrato inteligente e, em seguida, chamou uma função por meio de uma transação em uma rede do blockchain Consortium hospedada no serviço Blockchain do Azure.

> [!div class="nextstepaction"]
> [Desenvolvendo aplicativos blockchain usando o serviço Blockchain do Azure](develop.md)

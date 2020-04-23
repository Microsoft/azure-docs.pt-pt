---
title: Criar, construir, & implementar contratos inteligentes tutorial - Azure Blockchain Service
description: Tutorial sobre como usar o Kit de Desenvolvimento Azure Blockchain para extensão Ethereum em Visual Studio Code para criar, construir e implementar um contrato inteligente no Serviço Azure Blockchain.
ms.date: 04/22/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: dc23c680dfb2ed33cae2a251af16e1b1f25c6ac7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086662"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Tutorial: Criar, construir e implementar contratos inteligentes no Serviço Azure Blockchain

Neste tutorial, utilize o Kit de Desenvolvimento Azure Blockchain para extensão Ethereum no Visual Studio Code para criar, construir e implementar um contrato inteligente no Serviço Azure Blockchain. Também usa o kit de desenvolvimento para executar uma função de contrato inteligente através de uma transação.

Usa o Kit de Desenvolvimento Azure Blockchain para o Ethereum para:

> [!div class="checklist"]
> * Criar um contrato inteligente
> * Implementar um contrato inteligente
> * Executar uma função de contrato inteligente através de uma transação

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Complete [Quickstart: Use visual studio code para ligar a uma rede](connect-vscode.md) de consórcio azure Blockchain Service
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Kit de desenvolvimento azure blockchain para extensão Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Nó.js 10.15.x ou superior](https://nodejs.org/download)
* [Git 2.10.x ou superior](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Adicione python.exe ao seu caminho. A versão 2.7.15 da Python no seu percurso é necessária para o Kit de Desenvolvimento azure Blockchain.
* [Trufa 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

No Windows, é necessário um compilador C++ instalado para o módulo de giro do nó. Pode utilizar as ferramentas MSBuild:

* Se o Visual Studio 2017 estiver instalado, configure as npm para utilizar as ferramentas MSBuild com o comando`npm config set msvs_version 2017 -g`
* Se o Visual Studio 2019 estiver instalado, defina o caminho de ferramentas de construção de MS para as npm. Por exemplo, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Caso contrário, instale as ferramentas `npm install --global windows-build-tools` vs build autónomas utilizando num Run elevado como concha de comando *administrador.*

Para mais informações sobre o nó-gyp, consulte o [repositório de nó-gyp no GitHub](https://github.com/nodejs/node-gyp).

## <a name="create-a-smart-contract"></a>Criar um contrato inteligente

O Kit de Desenvolvimento Azure Blockchain para o Ethereum utiliza modelos de projeto e ferramentas truffle para ajudar andaimes, construir e implementar contratos. Antes de começar, complete o pré-requisito [Quickstart: Use](connect-vscode.md)o Código do Estúdio Visual para se ligar a uma rede de consórcio seletiva Azure Blockchain Service . O quickstart guia-o através da instalação e configuração do Kit de Desenvolvimento Azure Blockchain para o Ethereum.

1. A partir da paleta de comando vs Código, escolha **Azure Blockchain: New Solidity Project**.
1. Escolha **Criar projeto básico.**
1. Crie uma `HelloBlockchain` nova pasta nomeada e **Selecione o novo caminho**do projeto .

O Kit de Desenvolvimento azure blockchain cria e inicializa um novo projeto solidity para si. O projeto básico inclui uma amostra de contrato inteligente **HelloBlockchain** e todos os ficheiros necessários para construir e implantar para o seu membro do consórcio no Azure Blockchain Service. Pode levar vários minutos para que o projeto seja criado. Pode monitorizar o progresso no painel de terminais do Código VS selecionando a saída para O Blockchain Azure.

A estrutura do projeto parece ser o seguinte exemplo:

   ![Projeto de solidez](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Construir um contrato inteligente

Os contratos inteligentes estão localizados no diretório de **contratos** do projeto. Compila-se contratos inteligentes antes de os implantar num blockchain. Utilize o comando **Build Contracts** para compilar todos os contratos inteligentes do seu projeto.

1. Na barra lateral do explorador do Código VS, expanda a pasta de **contratos** no seu projeto.
1. Clique no **HelloBlockchain.sol** e escolha **Build Contracts** a partir do menu.

    ![Escolha construir o menu de contratos ](./media/send-transaction/build-contracts.png)

O Kit de Desenvolvimento Azure Blockchain usa a Truffle para compilar os contratos inteligentes.

![Saída de compilador de trufas](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Implementar um contrato inteligente

A Truffle usa scripts de migração para implantar os seus contratos numa rede Ethereum. As migrações são ficheiros JavaScript localizados no diretório de **migrações** do projeto.

1. Para implementar o seu contrato inteligente, clique no **HelloBlockchain.sol** e escolha **Contratos de Implementação** do menu.
1. Escolha a sua rede de consórcio Azure Blockchain na paleta de comando. A rede blockchain do consórcio foi adicionada ao ficheiro de configuração Truffle do projeto quando criou o projeto.
1. Escolha **Gerar mnemônico.** Escolha um nome de ficheiro e guarde o ficheiro mnemónico na pasta do projeto. Por exemplo, `myblockchainmember.env`. O ficheiro mnemónico é usado para gerar uma chave privada Ethereum para o seu membro blockchain.

O Kit de Desenvolvimento Azure Blockchain usa a Truffle para executar o guião de migração para implementar os contratos para a blockchain.

![Contrato implementado com sucesso](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Ligue para uma função contratual

A função **SendRequest** do contrato **HelloBlockchain** altera a variável do estado **RequestMessage.** A alteração do estado de uma rede blockchain é feita através de uma transação. Pode utilizar a página de interação inteligente do Kit de Desenvolvimento de Blockchain Azure para ligar para a função **SendRequest** através de uma transação.

1. Para interagir com o seu contrato inteligente, clique no **HelloBlockchain.sol** e escolha página de **interação de contrato inteligente** do menu.

    ![Escolha mostrar página de interação de contrato inteligente a partir do menu](./media/send-transaction/contract-interaction.png)

1. A página de interação permite-lhe escolher uma versão de contrato implementada, funções de chamada, ver o estado atual e ver metadados.

    ![Página de interação de contrato inteligente de exemplo](./media/send-transaction/interaction-page.png)

1. Para ligar para a função de contrato inteligente, selecione a ação do contrato e aprove os seus argumentos. Escolha a ação do contrato **SendRequest** e insira **Hello, Blockchain!** para o **parâmetro de mensagemMensagem.** Selecione **Executar** para ligar para a função **Enviar Solicitar** através de uma transação.

    ![Executar ação SendRequest](./media/send-transaction/sendrequest-action.png)

Uma vez que a transação é processada, a secção de interação reflete as alterações do Estado.

![Alterações do Estado contratual](./media/send-transaction/contract-state.png)

A função SendRequest define os campos **RequestMessage** e **State.** O estado atual do **RequestMessage** é o argumento que passou **olá, Blockchain.** O valor do campo **do Estado** continua a ser **pedido.**

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode eliminar `myResourceGroup` os recursos eliminando o grupo de recursos que criou no *Create a blockchain membro* pré-requisito quickstart.

Para eliminar o grupo de recursos:

1. No portal Azure, navegue para o **grupo Recurso** no painel de navegação à esquerda e selecione o grupo de recursos que pretende eliminar.
1. Selecione **Eliminar grupo de recursos**. Verifique a eliminação inserindo o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um projeto de solidez de amostra utilizando o Kit de Desenvolvimento azure blockchain. Você construiu e implementou um contrato inteligente então chamado uma função através de uma transação em uma rede de consórcio blockchain hospedada no Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Desenvolvimento de aplicações blockchain utilizando o Serviço Blockchain Azure](develop.md)

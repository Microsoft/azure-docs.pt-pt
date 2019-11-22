---
title: Usar Visual Studio Code para se conectar ao serviço Blockchain do Azure
description: Conectar-se a uma rede do Azure Blockchain Service Consortium usando a extensão do kit de desenvolvimento do Blockchain do Azure para Ethereum no Visual Studio Code
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/19/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: c5880282ada9a2789c0a583568ba7e77624ebfe3
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286700"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Início rápido: usar Visual Studio Code para se conectar a uma rede do Azure Blockchain Service Consortium

Neste guia de início rápido, você instala e usa o kit de desenvolvimento do Azure Blockchain para Ethereum Visual Studio Code extensão para anexar a um consórcio no serviço Blockchain do Azure. O kit de desenvolvimento do Azure Blockchain simplifica a maneira como você cria, conecta, cria e implanta contratos inteligentes em razões Blockchain Ethereum.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Conclua [o início rápido: criar um membro do blockchain usando o portal do Azure](create-member.md) ou [início rápido: criar um membro Blockchain do serviço blockchain do Azure usando o CLI do Azure](create-member-cli.md)
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

### <a name="verify-azure-blockchain-development-kit-environment"></a>Verificar o ambiente do kit de desenvolvimento do Azure Blockchain

O kit de desenvolvimento do Azure Blockchain verifica se os pré-requisitos do ambiente de desenvolvimento foram atendidos. Para verificar seu ambiente de desenvolvimento:

Na paleta de comandos do VS Code, escolha **Azure Blockchain: mostrar página inicial**.

O kit de desenvolvimento do Azure Blockchain executa um script de validação que leva cerca de um minuto para ser concluído. Você pode exibir a saída selecionando **terminal > novo terminal**. Na barra de menus do terminal, selecione a guia **saída** e **Blockchain do Azure** na lista suspensa. A validação bem-sucedida é semelhante à imagem a seguir:

![Ambiente de desenvolvimento válido](./media/connect-vscode/valid-environment.png)

 Se você não tiver uma ferramenta necessária, uma nova guia chamada **Kit de desenvolvimento do Azure Blockchain-Preview** listará as ferramentas necessárias com links de download.

![Aplicativos necessários do kit de desenvolvimento](./media/connect-vscode/required-apps.png)

Instale os pré-requisitos ausentes antes de continuar com o início rápido.

## <a name="connect-to-consortium-member"></a>Conectar ao membro do consórcio

Você pode se conectar a membros do Consortium usando a extensão de VS Code do kit de desenvolvimento do Azure Blockchain. Uma vez conectado a um consórcio, você pode compilar, compilar e implantar contratos inteligentes em um membro do Azure Blockchain Service Consortium.

Se você não tiver acesso a um membro do Azure Blockchain Service Consortium, conclua o guia de [início rápido de pré-requisito: criar um membro do Blockchain usando o portal do Azure](create-member.md) ou [início rápido: criar um membro do Azure Blockchain Service Blockchain usando o CLI do Azure](create-member-cli.md).

1. No painel Visual Studio Code (VS Code) Explorer, expanda a extensão **Blockchain do Azure** .
1. Selecione **conectar ao consórcio**.

   ![Conectar ao consórcio](./media/connect-vscode/connect-consortium.png)

    Se a autenticação do Azure for solicitada, siga os prompts para autenticar usando um navegador.
1. Escolha **conectar ao Azure Blockchain Service Consortium** no menu suspenso da paleta de comandos.
1. Escolha a assinatura e o grupo de recursos associados ao seu membro do Azure Blockchain Service Consortium.
1. Escolha seu Consórcio na lista.

Os membros do consórcio e do blockchain são listados na barra lateral do Visual Studio Explorer.

![Consórcio exibido no Explorer](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você usou o kit de desenvolvimento do Azure Blockchain para Ethereum Visual Studio Code extensão para anexar a um consórcio no serviço Blockchain do Azure. Experimente o próximo tutorial para usar o kit de desenvolvimento do Azure Blockchain para Ethereum para criar, compilar, implantar e executar uma função de contrato inteligente por meio de uma transação.

> [!div class="nextstepaction"]
> [Use Visual Studio Code para criar, compilar e implantar contratos inteligentes](send-transaction.md)
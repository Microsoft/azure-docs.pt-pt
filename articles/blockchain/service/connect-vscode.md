---
title: Use o Código do Estúdio Visual para ligar ao Serviço Azure Blockchain
description: Ligue-se a uma rede de consórcio seleções Azure Blockchain utilizando o Kit de Desenvolvimento Azure Blockchain para extensão Ethereum em Código de Estúdio Visual
ms.date: 04/22/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 8b502966317c5d07e89de4ae70ff72b899e963e6
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084843"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Quickstart: Use o Código do Estúdio Visual para se conectar a uma rede de consórcio seleções Azure Blockchain Service

Neste arranque rápido, instala e utiliza o Kit de Desenvolvimento Azure Blockchain para a extensão do Código do Estúdio Visual Ethereum (CÓDIGO VS) para anexar a um consórcio no Serviço Azure Blockchain. O Kit de Desenvolvimento Azure Blockchain simplifica a forma como cria, conecta, constrói e implementa contratos inteligentes em livros blockchain Ethereum.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Complete [Quickstart: Criar um membro blockchain usando o portal Azure](create-member.md) ou [Quickstart: Criar um membro blockchain Azure Blockchain Service usando o Azure CLI](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Kit de desenvolvimento azure blockchain para extensão Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Nó.js 10.15.x ou superior](https://nodejs.org)
* [Git 2.10.x ou superior](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Adicione python.exe ao seu caminho. Ter a versão Python 2.7.15 no seu caminho é necessário para o Kit de Desenvolvimento Azure Blockchain.
* [Trufa 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

No Windows, é necessário um compilador C++ instalado para o módulo de giro do nó. Pode utilizar as ferramentas MSBuild:

* Se o Visual Studio 2017 estiver instalado, configure as npm para utilizar as ferramentas MSBuild com o comando`npm config set msvs_version 2017 -g`
* Se o Visual Studio 2019 estiver instalado, defina o caminho de ferramentas de construção de MS para as npm. Por exemplo, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Caso contrário, instale as ferramentas `npm install --global windows-build-tools` vs build autónomas utilizando num Run elevado como concha de comando *administrador.*

Para mais informações sobre o nó-gyp, consulte o [repositório de nó-gyp no GitHub](https://github.com/nodejs/node-gyp).

### <a name="verify-azure-blockchain-development-kit-environment"></a>Verifique o ambiente do Kit de Desenvolvimento azure blockchain

O Kit de Desenvolvimento azure Blockchain verifica que os pré-requisitos do ambiente de desenvolvimento foram cumpridos. Para verificar o seu ambiente de desenvolvimento:

A partir da paleta de comando vs Código, escolha **Azure Blockchain: Mostrar página de boas-vindas**.

O Kit de Desenvolvimento Azure Blockchain executa um script de validação que demora cerca de um minuto a ser concluído. Pode visualizar a saída selecionando **terminal > Novo Terminal**. Na barra de menu seletiva, selecione o separador **Output** e **o Azure Blockchain** no dropdown. Validação bem sucedida parece a seguinte imagem:

![Ambiente de desenvolvimento válido](./media/connect-vscode/valid-environment.png)

 Se faltar uma ferramenta necessária, um novo separador chamado **Kit de Desenvolvimento Azure Blockchain - Preview** lista as ferramentas necessárias com links de descarregamento.

![Dev kit aplicativos necessários](./media/connect-vscode/required-apps.png)

Instale quaisquer pré-requisitos em falta antes de continuar com o arranque rápido.

## <a name="connect-to-consortium-member"></a>Ligar-se ao membro do consórcio

Pode ligar-se aos membros do consórcio utilizando a extensão do Código VS do Kit de Desenvolvimento azure Blockchain. Uma vez ligado a um consórcio, pode compilar, construir e implementar contratos inteligentes para um membro do consórcio Azure Blockchain Service.

Se não tiver acesso a um membro do consórcio Azure Blockchain Service, complete o pré-requisito [Quickstart: Crie um membro blockchain utilizando o portal Azure](create-member.md) ou [Quickstart: Crie um membro blockchain do Serviço Blockchain Azure utilizando o Azure CLI](create-member-cli.md).

1. No painel de explorador estoque do Código VS, expanda a extensão **Azure Blockchain.**
1. Selecione **Ligar à rede**.

   ![Ligar à rede](./media/connect-vscode/connect-consortium.png)

    Se solicitado para autenticação Azure, siga as instruções para autenticar usando um browser.
1. Escolha o **Serviço Blockchain Azure** na queda da paleta de comando.
1. Escolha o grupo de subscrição e recursos associado ao seu membro do consórcio Azure Blockchain Service.
1. Escolha o seu consórcio da lista.

Os membros do consórcio e blockchain estão listados na barra lateral do explorador do Código VS.

![Consórcio exibido em explorador](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, usou o Kit de Desenvolvimento Azure Blockchain para a extensão do Código Ethereum VS para anexar a um consórcio no Serviço Blockchain Azure. Experimente o próximo tutorial para usar o Kit de Desenvolvimento Azure Blockchain para o Ethereum para criar, construir, implementar e executar uma função de contrato inteligente através de uma transação.

> [!div class="nextstepaction"]
> [Criar, construir e implementar contratos inteligentes no Serviço Azure Blockchain](send-transaction.md)
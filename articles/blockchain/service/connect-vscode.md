---
title: Use código de estúdio visual para ligar ao Serviço Azure Blockchain
description: Ligue-se a uma rede de consórcio do Azure Blockchain Service utilizando o Kit de Desenvolvimento Azure Blockchain para extensão Ethereum no Código do Estúdio Visual
ms.date: 12/04/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 6e94d93d91f25c15743c4c467e31de49fd9da41d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763337"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Quickstart: Use o Código do Estúdio Visual para ligar a uma rede de consórcios do Azure Blockchain Service

Neste arranque rápido, instala e utiliza o Kit de Desenvolvimento Azure Blockchain para a extensão do Código de Estúdio Visual Ethereum (Código VS) para anexar a um consórcio no Serviço Azure Blockchain. O Azure Blockchain Development Kit simplifica a forma como cria, conecta, constrói e implementa contratos inteligentes nos livros de blockchain do Ethereum.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Quickstart Completo: Criar um membro blockchain utilizando o portal Azure](create-member.md) ou [Quickstart: Criar um membro blockchain do Azure Blockchain Service utilizando o Azure CLI](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Kit de desenvolvimento Azure Blockchain para extensão Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x ou superior](https://nodejs.org)
* [Git 2.10.x ou superior](https://git-scm.com)
* [Trufa 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

No Windows, é necessário um compilador C++ instalado para o módulo nó-giro. Pode utilizar as ferramentas MSBuild:

* Se o Visual Studio 2017 for instalado, configuure npm para usar as ferramentas MSBuild com o comando `npm config set msvs_version 2017 -g`
* Se o Visual Studio 2019 estiver instalado, desacorda o caminho das ferramentas de construção ms para npm. Por exemplo, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Caso contrário, instale as ferramentas vs build autónomas utilizando `npm install --global windows-build-tools` numa execução elevada como invólucro de comando *do administrador.*

Para obter mais informações sobre nó-gyp, consulte o [repositório nó-gyp no GitHub.](https://github.com/nodejs/node-gyp)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Verifique o ambiente do Kit de Desenvolvimento da Blockchain Azure

O Azure Blockchain Development Kit verifica que os seus pré-requisitos para o ambiente de desenvolvimento foram cumpridos. Para verificar o seu ambiente de desenvolvimento:

A partir da paleta de comandos VS Code, escolha **Blockchain: Mostrar Página de boas-vindas**.

O Azure Blockchain Development Kit executa um script de validação que demora cerca de um minuto a ser concluído. Pode visualizar a saída selecionando **Terminal > Novo Terminal.** Na barra de menu terminal, selecione o **separador Saída** e **o Azure Blockchain** no dropdown. A validação bem sucedida parece a seguinte imagem:

![Ambiente de desenvolvimento válido](./media/connect-vscode/valid-environment.png)

 Se faltar uma ferramenta necessária, um novo separador chamado **Azure Blockchain Development Kit - Pré-visualização** lista as ferramentas necessárias com links de descarregamento.

![Dev kit exigido apps](./media/connect-vscode/required-apps.png)

Instale os pré-requisitos em falta antes de continuar com o arranque rápido.

## <a name="connect-to-consortium-member"></a>Conecte-se ao membro do consórcio

Pode ligar-se aos membros do consórcio utilizando a extensão do Código VS do Kit de Desenvolvimento da Blockchain Azure Blockchain. Uma vez ligado a um consórcio, você pode compilar, construir e implementar contratos inteligentes para um membro do consórcio Azure Blockchain Service.

Se não tiver acesso a um membro do consórcio Azure Blockchain Service, preencha o [pré-requisito Quickstart: Criar um membro blockchain utilizando o portal Azure](create-member.md) ou [Quickstart: Criar um membro blockchain do Azure Blockchain Service utilizando o Azure CLI](create-member-cli.md).

1. No painel de exploradores do código VS, expanda a extensão **Azure Blockchain.**
1. Selecione **Ligar à rede**.

   ![Ligar à rede](./media/connect-vscode/connect-consortium.png)

    Se solicitado para a autenticação do Azure, siga as instruções para autenticar usando um browser.
1. Escolha **o Serviço Azure Blockchain** no dropdown da paleta de comando.
1. Escolha o grupo de subscrição e recursos associado ao seu membro do consórcio Azure Blockchain Service.
1. Escolha o seu consórcio na lista.

Os membros do consórcio e blockchain estão listados na barra lateral do explorador do código VS.

![Consórcio exibido no explorador](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você usou o Kit de Desenvolvimento Azure Blockchain para a extensão do Código Ethereum VS para anexar a um consórcio no Azure Blockchain Service. Experimente o próximo tutorial para usar o Azure Blockchain Development Kit para o Ethereum para criar, construir, implementar e executar uma função de contrato inteligente através de uma transação.

> [!div class="nextstepaction"]
> [Criar, construir e implementar contratos inteligentes no Azure Blockchain Service](send-transaction.md)
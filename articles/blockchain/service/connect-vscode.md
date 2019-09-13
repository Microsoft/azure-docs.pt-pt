---
title: Usar o kit de desenvolvimento do Azure Blockchain para Ethereum para se conectar ao serviço Blockchain do Azure
description: Conectar-se a uma rede do Azure Blockchain Service Consortium usando a extensão do kit de desenvolvimento do Blockchain do Azure para Ethereum no Visual Studio Code
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 82b71a9d837ed3cb1d9461c0de2559178685b4b4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935818"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Início rápido: Usar Visual Studio Code para se conectar a uma rede do Azure Blockchain Service Consortium

Neste guia de início rápido, você instala e usa o kit de desenvolvimento do Azure Blockchain para Ethereum Visual Studio Code extensão para anexar a um consórcio no serviço Blockchain do Azure. O kit de desenvolvimento do Azure Blockchain simplifica a maneira como você cria, conecta, cria e implanta contratos inteligentes em razões Ethereums. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Início [rápido completo: Crie um membro do blockchain usando o](create-member.md) portal do Azure [ou o início rápido: Criar um membro Blockchain do serviço Blockchain do Azure usando CLI do Azure](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit para extensão Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Adicione Python. exe ao seu caminho. O Python no seu caminho é necessário para o kit de desenvolvimento do Azure Blockchain.
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [CLI do ganache](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Verificar o ambiente do kit de desenvolvimento do Azure Blockchain

O kit de desenvolvimento do Azure Blockchain verifica se os pré-requisitos do ambiente de desenvolvimento foram atendidos. Para verificar seu ambiente de desenvolvimento:

Na paleta de comandos do vs Code, **escolha Azure Blockchain: Mostrar página**de boas-vindas.

O kit de desenvolvimento do Azure Blockchain executa um script de validação que leva cerca de um minuto para ser concluído. Você pode exibir a saída selecionando **terminal > novo terminal**. Na barra de menus do terminal, selecione a guia **saída** e **Blockchain do Azure** na lista suspensa. A validação bem-sucedida é semelhante à imagem a seguir:

![Ambiente de desenvolvimento válido](./media/connect-vscode/valid-environment.png)

 Se você não tiver uma ferramenta necessária, uma nova guia chamada **Kit de desenvolvimento do Azure Blockchain-Preview** listará os aplicativos necessários para instalar e os links para baixar as ferramentas.

![Aplicativos necessários do kit de desenvolvimento](./media/connect-vscode/required-apps.png)

Instale os pré-requisitos ausentes antes de continuar com o início rápido.

## <a name="connect-to-consortium-member"></a>Conectar ao membro do consórcio

Você pode se conectar a membros do Consortium usando a extensão de VS Code do kit de desenvolvimento do Azure Blockchain. Uma vez conectado a um consórcio, você pode compilar, compilar e implantar contratos inteligentes em um membro do Azure Blockchain Service Consortium.

Se você não tiver acesso a um membro do Azure Blockchain Service Consortium, conclua [o guia de início rápido do pré-requisito: Crie um membro do blockchain usando o](create-member.md) portal do Azure [ou o início rápido: Crie um membro Blockchain do serviço Blockchain do Azure](create-member-cli.md)usando CLI do Azure.

1. No painel Visual Studio Code (VS Code) Explorer, expanda a extensão **Blockchain do Azure** .
1. Selecione **conectar ao consórcio**.

   ![Conectar ao consórcio](./media/connect-vscode/connect-consortium.png)

    Se a autenticação do Azure for solicitada, siga os prompts para autenticar usando um navegador.
1. Escolha **conectar ao Azure Blockchain Service Consortium** no menu suspenso da paleta de comandos.
1. Escolha a assinatura e o grupo de recursos associados ao seu membro do Azure Blockchain Service Consortium.
1. Escolha seu Consórcio na lista.

Os membros do consórcio e do blockchain são listados na barra lateral do Visual Studio Explorer.

![Consórcio exibido no Explorer](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você usou o kit de desenvolvimento do Azure Blockchain para Ethereum Visual Studio Code extensão para anexar a um consórcio no serviço Blockchain do Azure. Experimente o próximo tutorial para usar o kit de desenvolvimento do Azure Blockchain para Ethereum e Truffle para criar, compilar, implantar e executar uma função de contrato inteligente por meio de uma transação.

> [!div class="nextstepaction"]
> [Use Visual Studio Code para criar, compilar e implantar contratos inteligentes](send-transaction.md)
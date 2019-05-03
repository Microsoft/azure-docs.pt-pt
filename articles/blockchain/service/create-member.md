---
title: Criar um serviço do Azure Blockchain com o portal do Azure
description: Utilize o serviço do Azure Blockchain para criar um membro de consortium.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 51775c5534a13fb2515fafa182658beafd38c1eb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026894"
---
# <a name="quickstart-create-an-azure-blockchain-service-using-the-azure-portal"></a>Início rápido: Criar um serviço do Azure Blockchain com o portal do Azure

O serviço do Azure Blockchain é uma plataforma de blockchain que pode executar a lógica de negócios dentro de um contrato inteligente. Este guia de introdução mostra-lhe como começar ao criar um razão gerido com o portal do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-managed-ledger"></a>Criar um livro razão gerido

O serviço do Azure Blockchain é criado com um conjunto definido de recursos de computação e armazenamento.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Selecione **Blockchain** > **o serviço do Azure Blockchain**.
1. Conclua o modelo.

    ![Criar Serviço](./media/create-member/create-member.png)

    Definição | Descrição
    --------|------------
    Membro de Blockchain | Escolha um nome exclusivo que identifica o membro de serviço do Azure Blockchain. O nome do membro blockchain só pode conter letras minúsculas e números. O primeiro caráter tem de ser uma letra. O valor tem de ser entre 2 e 20 carateres de comprimento.
    Subscrição | Selecione a subscrição do Azure que pretende utilizar para o seu serviço. Se tiver várias subscrições, escolha a subscrição na qual o recurso é cobrado.
    Grupo de recursos | Um nome de grupo de recursos novo ou um já existente na sua subscrição.
    Região | Localização tem de ser igual para todos os membros do consortium.
    Palavra-passe da conta de membro | Forneça uma nova palavra-passe da conta de membro. A palavra-passe da conta de membro é utilizada para autenticar para ponto final público o membro de blockchain usando a autenticação básica.
    Nome de consórcio | Para um consórcio nova, introduza um nome exclusivo. Se associar um consórcio por meio de um convite, o valor é o consortium que estiver associando.
    Descrição | Descrição do consortium.
    Protocolo |  Pré-visualização suporta o protocolo de quórum.
    Preços | A configuração do nó para o seu novo serviço. Selecione **padrão**. 2 nós de validador e 1 transação nó é a predefinição.

1. Selecione **criar** para aprovisionar o serviço. O aprovisionamento demora cerca de 10 minutos.
1. Selecione **notificações** na barra de ferramentas para monitorizar o processo de implantação.
1. Após a implementação, navegue para o membro de blockchain.

Selecione **descrição geral**, pode ver as informações básicas do serviço, incluindo a conta de endereço e membro RootContract.

![Descrição geral de membro de Blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Limpar recursos

Pode usar o membro que criou para o próximo início rápido ou tutorial. Quando já não for necessário, pode eliminar os recursos a eliminar o `myResourceGroup` grupo de recursos que criou o serviço do Azure Blockchain.

Para eliminar o grupo de recursos:

1. No portal do Azure, navegue até **grupo de recursos** no painel de navegação esquerda e selecione o grupo de recursos que pretende eliminar.
2. Selecione **Eliminar grupo de recursos**. Verifique a eliminação ao introduzir o nome do grupo de recursos e selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Utilizar MetaMask para ligar e implementar um contrato inteligente](connect-metamask.md)
---
title: Criar um membro do serviço Blockchain do Azure-portal do Azure
description: Crie um membro do serviço Blockchain do Azure para um consórcio Blockchain usando o portal do Azure.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/18/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 9b08bf030d66c058ef1ec7c57f1fb2517046c522
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285111"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Início rápido: criar um membro Blockchain do serviço Blockchain do Azure usando o portal do Azure

Neste guia de início rápido, você implanta um novo membro do blockchain e o consórcio no serviço Blockchain do Azure usando o portal do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Crie um membro de blockchain

Crie um membro blockchain que execute o protocolo de razão de quorum em um consórcio novo ou existente.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Selecione **Blockchain** > **serviço Blockchain do Azure (versão prévia)** .

    ![Criar serviço](./media/create-member/create-member.png)

    Definição | Descrição
    --------|------------
    Subscrição | Selecione a assinatura do Azure que você deseja usar para o serviço. Se tiver várias subscrições, escolha a subscrição na qual o recurso é cobrado.
    Grupo de recursos | Crie um novo nome de grupo de recursos ou escolha um existente em sua assinatura.
    Região | Escolha uma região para criar o membro. Todos os membros do consórcio devem estar no mesmo local.
    Protocolo | Atualmente, a versão prévia do serviço Blockchain do Azure dá suporte ao protocolo de quorum.
    Consórcio | Para um novo consórcio, insira um nome exclusivo. Se estiver unindo um consórcio por meio de um convite, escolha o consórcio que você está unindo.
    Nome | Escolha um nome exclusivo para o membro do serviço Blockchain do Azure. O nome do membro blockchain pode conter apenas letras minúsculas e números. O primeiro caráter tem de ser uma letra. O valor deve ter entre 2 e 20 caracteres.
    Senha da conta do membro | A senha da conta de membro é usada para criptografar a chave privada para a conta Ethereum que é criada para o seu membro. Use a conta de membro e a senha da conta do membro para o gerenciamento do consórcio.
    Descrição | Descrição do consórcio.
    Preços | A configuração de nó e o custo para o novo serviço. Selecione o link **alterar** para escolher entre as camadas **Standard** e **Basic** .
    Senha do nó | A senha para o nó de transação padrão do membro. Use a senha para autenticação básica ao se conectar ao ponto de extremidade público do nó de transação padrão do membro do blockchain.

1. Selecione **examinar + criar** para validar suas configurações. Selecione **criar** para provisionar o serviço. O provisionamento leva cerca de 10 minutos.
1. Selecione **notificações** na barra de ferramentas para monitorar o processo de implantação.
1. Após a implantação, navegue até o membro blockchain.

Selecione **visão geral**, você pode exibir as informações básicas sobre seu serviço, incluindo o endereço RootContract e a conta de membro.

![Visão geral do membro do Blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o membro que criou para o próximo início rápido ou tutorial. Quando não for mais necessário, você poderá excluir os recursos excluindo o `myResourceGroup` grupo de recursos criado para o guia de início rápido.

Para excluir o grupo de recursos:

1. No portal do Azure, navegue até o **grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que deseja excluir.
2. Selecione **Eliminar grupo de recursos**. Verifique a exclusão inserindo o nome do grupo de recursos e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você implantou um membro do serviço Blockchain do Azure e um novo consórcio. Experimente o próximo início rápido para usar o kit de desenvolvimento do Azure Blockchain para Ethereum para anexar a um consórcio no serviço Blockchain do Azure.

> [!div class="nextstepaction"]
> [Usar Visual Studio Code para se conectar a uma rede do Azure Blockchain Service Consortium](connect-vscode.md)

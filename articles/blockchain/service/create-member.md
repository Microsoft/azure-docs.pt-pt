---
title: Criar um membro Blockchain do serviço Blockchain do Azure usando o portal do Azure
description: Use o serviço Blockchain do Azure para criar um membro do Blockchain.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 9f58379acd88fd93a00edc11b73b41dc3d6226a5
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241043"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Início rápido: Criar um membro Blockchain do serviço Blockchain do Azure usando o portal do Azure

O serviço Blockchain do Azure é uma plataforma Blockchain que você pode executar a lógica de negócios em um contrato inteligente. Este guia de início rápido mostra como começar criando um membro blockchain usando o portal do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Crie um membro de blockchain

Crie um membro blockchain que execute o protocolo de razão de quorum em um consórcio novo ou existente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Selecione **Blockchain** > **Azure Blockchain Service**.
1. Conclua o modelo.

    ![Criar Serviço](./media/create-member/create-member.png)

    Definição | Descrição
    --------|------------
    Membro Blockchain | Escolha um nome exclusivo que identifique o membro Blockchain do serviço Blockchain do Azure. O nome do membro blockchain pode conter apenas letras minúsculas e números. O primeiro caractere deve ser uma letra. O valor deve ter entre 2 e 20 caracteres.
    Subscription | Selecione a assinatura do Azure que você deseja usar para o serviço. Se tiver várias subscrições, escolha a subscrição na qual o recurso é cobrado.
    Resource group | Um nome de grupo de recursos novo ou um já existente na sua subscrição.
    Região | O local deve ser o mesmo para todos os membros do consórcio.
    Senha da conta do membro | A senha da conta de membro é usada para criptografar a chave privada para a conta Ethereum que é criada para o seu membro. Use a conta de membro e a senha da conta do membro para o gerenciamento do consórcio.
    Nome do consórcio | Para um novo consórcio, insira um nome exclusivo. Se estiver unindo um consórcio por meio de um convite, o valor será o consórcio que você está unindo.
    Descrição | Descrição do consórcio.
    Protocol |  A versão prévia dá suporte ao protocolo de quorum.
    Preços | A configuração de nó para o novo serviço. Selecione **padrão**. 2 nós de validador e 1 nó de transação são as configurações padrão.
    Senha do nó da transação | A senha para o nó de transação padrão do membro. Use a senha para autenticação básica ao se conectar ao ponto de extremidade público do nó de transação padrão do membro do blockchain.

1. Selecione **criar** para provisionar o serviço. O provisionamento leva cerca de 10 minutos.
1. Selecione **notificações** na barra de ferramentas para monitorar o processo de implantação.
1. Após a implantação, navegue até o membro blockchain.

Selecione **visão geral**, você pode exibir as informações básicas sobre seu serviço, incluindo o endereço RootContract e a conta de membro.

![Visão geral do membro do Blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o membro que criou para o próximo início rápido ou tutorial. Quando não for mais necessário, você poderá excluir os recursos excluindo `myResourceGroup` o grupo de recursos criado pelo serviço Blockchain do Azure.

Para excluir o grupo de recursos:

1. No portal do Azure, navegue até o **grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que deseja excluir.
2. Selecione **Eliminar grupo de recursos**. Verifique a exclusão inserindo o nome do grupo de recursos e selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Usar a metamáscara para conectar e implantar um contrato inteligente](connect-metamask.md)
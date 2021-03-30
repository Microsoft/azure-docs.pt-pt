---
title: Criar um membro do Serviço Azure Blockchain - Portal Azure
description: Crie um membro do Serviço Azure Blockchain para um consórcio blockchain utilizando o portal Azure.
ms.date: 07/16/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: 8a7f5aaea56f34e8107664ab786a14b59cd1cb7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91292733"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Quickstart: Criar um membro blockchain do Azure Blockchain Service utilizando o portal Azure

Neste quickstart, você implementa um novo membro blockchain e consórcio no Azure Blockchain Service usando o portal Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Nenhum.

## <a name="create-a-blockchain-member"></a>Criar um membro blockchain

Um membro do Azure Blockchain Service é um nó blockchain numa rede de blockchain de consórcio privado. Ao providenciar um membro, pode criar ou aderir a uma rede de consórcios. Precisa de pelo menos um membro para uma rede de consórcios. O número de membros blockchain necessários pelos participantes depende do seu cenário. Os participantes do consórcio podem ter um ou mais membros blockchain ou podem partilhar membros com outros participantes. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Selecione **Blockchain**  >  **Azure Blockchain Service (pré-visualização)**.

    ![Criar Serviço](./media/create-member/create-member.png)

    Definições | Descrição
    --------|------------
    Subscrição | Selecione a subscrição Azure que pretende utilizar para o seu serviço. Se tiver várias subscrições, escolha a subscrição na qual o recurso é cobrado.
    Grupo de recursos | Crie um novo nome de grupo de recursos ou escolha um existente da sua subscrição.
    Region | Escolha uma região para criar o membro. Todos os membros do consórcio devem estar no mesmo local. As funcionalidades podem não estar disponíveis em algumas regiões. O Azure Blockchain Data Manager está disponível nas seguintes regiões de Azure: Leste dos EUA e Europa Ocidental.
    Protocolo | Atualmente, a Pré-visualização do Serviço Azure Blockchain suporta o protocolo Quorum.
    Consórcio | Para um novo consórcio, insira um nome único. Se se juntar a um consórcio através de um convite, escolha o consórcio a que se vai juntar. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).
    Name | Escolha um nome único para o membro do Serviço Azure Blockchain. O nome do membro blockchain só pode conter letras e números minúsculos. O primeiro caráter tem de ser uma letra. O valor deve ter entre 2 e 20 caracteres de comprimento.
    Senha de conta do membro | A palavra-passe da conta do membro é usada para encriptar a chave privada da conta Ethereum que é criada para o seu membro. Utilize a conta de membro e a palavra-passe da conta do membro para a gestão do consórcio.
    Preços | A configuração do nó e o custo do seu novo serviço. Selecione o link **Alterar** para escolher entre os níveis **Standard** e **Basic.** Utilize o nível *básico* para desenvolvimento, teste e prova de conceitos. Utilize o nível *Standard* para implantações de nível de produção. Utilize também o nível *Standard* se estiver a utilizar o Blockchain Data Manager ou a enviar um grande volume de transações privadas. A alteração do nível de preços entre o básico e o padrão após a criação dos membros não é apoiada.
    Senha de nó | A palavra-passe para o nó de transação padrão do membro. Utilize a palavra-passe para autenticação básica ao ligar-se ao ponto final público do nó de transação padrão do membro blockchain.

1. Selecione **Rever + criar** para validar as suas definições. Selecione **Criar** para providenciar o serviço. O provisionamento leva cerca de 10 minutos.
1. Selecione **Notificações** na barra de ferramentas para monitorizar o processo de implantação.
1. Após a implementação, navegue para o seu membro blockchain.

Selecione **Overview**, pode ver as informações básicas sobre o seu serviço, incluindo o endereço RootContract e a conta de membro.

![Visão geral do membro blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Pode utilizar o membro que criou para o próximo quickstart ou tutorial. Quando já não é necessário, pode eliminar os recursos eliminando o `myResourceGroup` grupo de recursos que criou para o arranque rápido.

Para eliminar o grupo de recursos:

1. No portal Azure, navegue para o **grupo De recursos** no painel de navegação esquerdo e selecione o grupo de recursos que pretende eliminar.
2. Selecione **Eliminar grupo de recursos**. Verifique a eliminação introduzindo o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você lançou um membro do Azure Blockchain Service e um novo consórcio. Experimente o próximo quickstart para usar o Kit de Desenvolvimento Azure Blockchain para o Ethereum para se ligar a um membro do Serviço Azure Blockchain.

> [!div class="nextstepaction"]
> [Use código de estúdio visual para ligar ao Serviço Azure Blockchain](connect-vscode.md)

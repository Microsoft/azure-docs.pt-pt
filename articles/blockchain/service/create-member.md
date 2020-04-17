---
title: Criar um membro do Serviço Azure Blockchain - Portal Azure
description: Crie um membro do Serviço Azure Blockchain para um consórcio blockchain utilizando o portal Azure.
ms.date: 03/30/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 7d29382cdedf7e35d186c73c9d32547e10fb10d6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460278"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Quickstart: Criar um membro blockchain azure blockchain Service usando o portal Azure

Neste arranque rápido, você implementa um novo membro blockchain e consórcio no Azure Blockchain Service usando o portal Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Criar um membro blockchain

Um membro do Azure Blockchain Service é um nó blockchain numa rede privada de blockchain de consórcio. Ao fornecer um membro, pode criar ou aderir a uma rede de consórcios. Precisa de pelo menos um membro para uma rede de consórcios. O número de membros blockchain necessários pelos participantes depende do seu cenário. Os participantes do consórcio podem ter um ou mais membros blockchain ou podem partilhar membros com outros participantes. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Selecione **Blockchain** > **Azure Blockchain Service (pré-visualização)**.

    ![Criar Serviço](./media/create-member/create-member.png)

    Definição | Descrição
    --------|------------
    Subscrição | Selecione a subscrição Azure que pretende utilizar para o seu serviço. Se tiver várias subscrições, escolha a subscrição na qual o recurso é cobrado.
    Grupo de recursos | Crie um novo nome de grupo de recursos ou escolha um existente a partir da sua subscrição.
    Região | Escolha uma região para criar o membro. Todos os membros do consórcio devem estar no mesmo local.
    Protocolo | Atualmente, a Pré-Visualização do Serviço Azure Blockchain suporta o protocolo Quorum.
    Consórcio | Para um novo consórcio, insira um nome único. Se aderir a um consórcio através de um convite, escolha o consórcio a que se vai juntar. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).
    Nome | Escolha um nome único para o membro do Serviço Azure Blockchain. O nome do membro blockchain só pode conter letras minúsculas e números. O primeiro caráter tem de ser uma letra. O valor deve ter entre 2 e 20 caracteres de comprimento.
    Senha de conta de membro | A palavra-passe da conta membro é usada para encriptar a chave privada da conta Ethereum que é criada para o seu membro. Utiliza a conta membro e a palavra-passe da conta membro para a gestão do consórcio.
    Preços | A configuração do nó e o custo do seu novo serviço. Selecione o link **Alterar** para escolher entre os níveis **Standard** e **Basic.** Utilize o nível *Básico* para desenvolvimento, teste e prova de conceitos. Utilize o nível *Standard* para implementações de qualidade de produção. Também deve utilizar o nível *Standard* se estiver a utilizar o Gestor de Dados blockchain ou a enviar um elevado volume de transações privadas. A alteração do nível de preços entre a criação de base e a norma após a criação dos membros não é suportada.
    Senha do nó | A palavra-passe para o nó de transação padrão do membro. Utilize a palavra-passe para autenticação básica quando ligar ao ponto final de transação padrão do membro blockchain.

1. Selecione **Rever + criar** para validar as suas definições. Selecione **Criar** para fornecer o serviço. O fornecimento demora cerca de 10 minutos.
1. Selecione **Notificações** na barra de ferramentas para monitorizar o processo de implementação.
1. Após a implantação, navegue para o seu membro blockchain.

Selecione **Visão Geral,** pode ver as informações básicas sobre o seu serviço, incluindo o endereço RootContract e a conta membro.

![Visão geral do membro blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Limpar recursos

Pode utilizar o membro que criou para o próximo quickstart ou tutorial. Quando já não for necessário, pode eliminar `myResourceGroup` os recursos eliminando o grupo de recursos que criou para o arranque rápido.

Para eliminar o grupo de recursos:

1. No portal Azure, navegue para o **grupo Recurso** no painel de navegação à esquerda e selecione o grupo de recursos que pretende eliminar.
2. Selecione **Eliminar grupo de recursos**. Verifique a eliminação inserindo o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você implantou um membro do Serviço Azure Blockchain e um novo consórcio. Experimente o próximo quickstart a usar o Kit de Desenvolvimento Azure Blockchain para o Ethereum para ligar a um membro do Serviço Azure Blockchain.

> [!div class="nextstepaction"]
> [Use o Código do Estúdio Visual para ligar ao Serviço Azure Blockchain](connect-vscode.md)

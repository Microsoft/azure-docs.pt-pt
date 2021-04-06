---
title: Começar e criar um laboratório de Azure Lab Services dentro de equipas
description: Aprenda a começar e crie um laboratório de Azure Lab Services dentro das equipas.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: cc4ad604bdf250cc6e4ba2c50c2f7143c921e906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433976"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>Começar e criar um laboratório de serviços de laboratório dentro de equipas

Este artigo mostra como adicionar a app **Azure Lab Services** a uma Equipa e, em seguida, como criar um laboratório dentro do ambiente das Equipas DE MS.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial cria-se um laboratório com máquinas virtuais para a sua equipa. Para criar um laboratório numa conta de laboratório, deve ser membro de uma destas funções na conta do laboratório: Proprietário, Criador de Laboratório ou Colaborador. A conta que usou para criar uma conta de laboratório é automaticamente adicionada à função de proprietário. Então, pode usar a conta de utilizador que usou para criar uma conta de laboratório para criar um laboratório.

Aqui está o fluxo de trabalho típico quando se utiliza o Azure Lab Services dentro das Equipas

1. O utilizador [cria uma Conta de Laboratório](tutorial-setup-lab-account.md#create-a-lab-account) no portal Azure.
1. Um [criador de conta de laboratório adiciona outros utilizadores](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) ao papel de Criador de **Laboratório.** Por exemplo, o criador/administrador da conta de laboratório adiciona educadores ao papel de Criador de **Laboratório** para que possam criar laboratórios para as suas aulas.
1. Depois, os educadores criam laboratórios, pré-configuram o modelo VM e publicam o laboratório para a prestação de VM's a todos na equipa.
1. Uma vez publicado o laboratório, é atribuído um VM a todos os membros da equipa no seu primeiro login para a Azure Lab Services, quer clicando no separador que contém **a App de Serviços de Laboratório Azure** dentro de Equipas (SSO) quer acedendo ao site de [laboratórios.](https://labs.azure.com) Os utilizadores podem então utilizar o VM para fazer o trabalho de aula e os trabalhos de casa.

> [!IMPORTANT]
> Os Serviços Azure Lab só podem ser utilizados dentro de Equipas se as contas de laboratório forem criadas no mesmo inquilino que as Equipas.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Adicione a app Azure Lab Services como separador a uma Equipa

Você, como dono de uma equipa, pode adicionar a aplicação **Azure Lab Services** diretamente nos seus canais Teams, e então a aplicação está disponível para todos na equipa usarem. Siga os três passos abaixo:

1. Navegue para o canal Teams onde pretende adicionar a aplicação e selecione **+** para adicionar um separador. 
1. Procure **serviços Azure Lab a** partir das opções do separador e adicione esta aplicação. 

    > [!NOTE]
    > Só **os donos das** equipas poderão criar laboratórios para a equipa.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="Adicione um separador":::
1. Selecione uma conta lab services, que você gostaria de usar para criar laboratórios nesta equipa. 

    A Azure Lab Services usa um único sinal no site da [Azure Lab Services](https://labs.azure.com) e puxa todas as contas de laboratório a que tem acesso. 

    São apresentadas as contas que estão no mesmo arrendatário que as Equipas e para as quais tem **o Proprietário,** **Colaborador** ou **Creator.** 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="Bem-vindo à ALS":::
1. Pressione **Save** e a aba é adicionada ao canal.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="Criei o separador ALS":::

    Agora pode selecionar o separador **Azure Lab Services** a partir do seu canal e começar a gerir laboratórios como descrito nos seguintes artigos.

Depois da conta do laboratório ser selecionada, os donos da equipa poderão criar laboratórios para a equipa. Todo o processo de criação de laboratório e todas as tarefas ao nível do laboratório podem ser realizadas dentro das equipas. Os utilizadores terão a opção de criar vários laboratórios dentro da mesma equipa e o dono da Equipa, com acesso adequado ao nível da conta de laboratório, verá apenas os laboratórios associados à equipa específica.

## <a name="next-steps"></a>Passos seguintes

Quando um laboratório é criado dentro de Equipas, a lista de utilizadores do laboratório é automaticamente povoada e sincronizada com a associação da equipa. Todos na equipa, incluindo proprietários, membros e convidados serão automaticamente adicionados à lista de utilizadores do laboratório. Os serviços de laboratório da Azure manterão uma sincronização com a equipa e uma sincronização automática é ativada a cada 24 horas. Para obter mais detalhes, veja:

[Gerir listas de utilizadores de Serviços de Laboratório dentro de Equipas](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Ver também

Consulte também os seguintes artigos:

- [Use os serviços do Azure Lab dentro da visão geral das equipas](lab-services-within-teams-overview.md)
- [Gerir a piscina VM do laboratório dentro das equipas](how-to-manage-vm-pool-within-teams.md)
- [Criar e gerir horários de laboratório dentro de Equipas](how-to-create-schedules-within-teams.md)
- [Aceda a um VM dentro de equipas - Vista do aluno](how-to-access-vm-for-students-within-teams.md)
- [Eliminar laboratórios dentro de equipas](how-to-delete-lab-within-teams.md)

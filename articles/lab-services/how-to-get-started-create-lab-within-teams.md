---
title: Começar e criar um laboratório de Serviços Azure Lab de Equipas
description: Aprenda a começar e crie um laboratório de Serviços Azure lab a partir de equipas.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: b585196fe61a09697cfa203aaa33f08afae2b427
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946738"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Começar e criar um laboratório de serviços de laboratório de equipas

Este artigo mostra como adicionar uma aplicação Azure Lab Services às Equipas. Então, como criar um laboratório de equipas.

## <a name="add-a-lab-services-app-to-teams"></a>Adicione uma aplicação de Serviços de Laboratório às Equipas

Pode adicionar Serviços de Laboratório diretamente nos seus canais De Equipas e, em seguida, a aplicação está disponível para todos na equipa usarem. Siga estes três passos:

1. Navegue para o canal Teams onde pretende adicionar a app e selecione **+** para adicionar um separador clicando em "..." do topo da janela direita. 
1. Procure **serviços Azure Lab a** partir das opções do separador e adicione esta aplicação. 

    > [!NOTE]
    > Só **os donos das** equipas poderão criar laboratórios para a equipa.
1. Selecione uma conta lab services, que você gostaria de usar para criar laboratórios de sala de aula nesta equipa. 

    A Azure Lab Services usa um único sinal no site da [Azure Lab Services](https://labs.azure.com) e puxa todas as contas de laboratório a que tem acesso. 

    São apresentadas as contas que estão no mesmo arrendatário que as Equipas e para as quais tem **o Proprietário,** **Colaborador**ou **Creator.** 

   ![Bem-vindo à ALS](./media/integrate-with-teams/welcome.png) 
1. Pressione **Save** e a aplicação é adicionada às Equipas e o separador é adicionado ao canal. 

    Agora pode selecionar o separador **Azure Lab Services** a partir do seu canal e começar a gerir laboratórios como descrito no passo seguinte.

    Uma vez que um membro de uma equipa adiciona a conta, aparece para todos no canal. Todos os utilizadores que tenham acesso à aplicação têm acesso único com as credenciais que utilizam para as Equipas da Microsoft. Qualquer utilizadores que não tenham acesso à app podem ver o separador em Equipas, mas estão bloqueados até que lhes dê permissões para a app no local e para a versão publicada do portal Azure da aplicação.

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

Depois da conta do laboratório ser selecionada, os donos da equipa poderão criar laboratórios para a equipa. Todo o processo de criação de laboratório e todas as tarefas ao nível do laboratório podem ser realizadas dentro das equipas. Os utilizadores terão a opção de criar vários laboratórios dentro da mesma equipa e o dono da Equipa, com acesso adequado ao nível da conta de laboratório, verá apenas os laboratórios associados à equipa específica.

## <a name="giving-access-to-users-of-the-lab-account"></a>Dar acesso aos utilizadores da conta de laboratório

O acesso aos utilizadores ao nível da conta de laboratório tem de acontecer no portal [Azure.](https://ms.portal.azure.com/)

1. No portal Azure, navegue para a sua conta dos Serviços Azure Lab. 
1. Na página **Conta Lab,** selecione **Access control (IAM)**, selecione **+ Adicione** na barra de ferramentas e, em seguida, selecione + Adicione a atribuição de **função** na barra de ferramentas. 

    ![Controlo de acesso -> adicionar botão de atribuição de funções](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na página de **atribuição de funções Adicionar,** selecione **O Criador de Laboratório** para **Função**, selecione o utilizador que pretende adicionar à função Criadores de Laboratório e selecione **Save**. 

    ![Adicionar criador de laboratório](./media/tutorial-setup-lab-account/add-lab-creator.png)

### <a name="creating-classroom-labs"></a>Criação de laboratórios de salas de aula

O processo de criação de laboratórios em sala de aula é o mesmo quer esteja a criar laboratórios a partir de Equipas ou do [site lab Services](https://labs.azure.com). 

Para mais detalhes sobre a criação deste artigo delineie o processo de criação de laboratórios: [Gerir laboratórios de sala de aula nos Serviços Azure Lab](how-to-manage-classroom-labs.md).

## <a name="deleting-classroom-labs"></a>Eliminação de laboratórios de sala de aula

Um laboratório criado dentro de Equipas pode ser eliminado no site lab [Services](https://labs.azure.com) eliminando o laboratório diretamente, como descrito em [Laboratórios de Gestão em Azure Lab Services](how-to-manage-classroom-labs.md). 

A eliminação do laboratório também é desencadeada quando a equipa é eliminada. Se a equipa em que o laboratório é criado for eliminada, o laboratório será automaticamente eliminado 24 horas após o sincronizamento automático da lista de utilizadores. 

A eliminação do separador ou a desinstalação da aplicação não resultará na eliminação do laboratório. Se o separador for eliminado, os utilizadores da lista de membros da equipa ainda poderão aceder aos VMs no [site dos Lab Services,](https://labs.azure.com) a menos que a eliminação do laboratório seja explicitamente desencadeada pela eliminação do laboratório no site ou pela eliminação da equipa. 

## <a name="next-steps"></a>Próximos passos

Quando um laboratório é criado dentro de Equipas, a lista de utilizadores do laboratório é automaticamente povoada e sincronizada com a associação da equipa. Todos na equipa, incluindo proprietários, membros e convidados serão automaticamente adicionados à lista de utilizadores do laboratório. Os serviços de laboratório da Azure manterão uma sincronização com a equipa e uma sincronização automática é ativada a cada 24 horas. Para obter mais detalhes, veja:

[Gerir listas de utilizadores de Serviços de Laboratório de Equipas](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Consulte também

Consulte também os seguintes artigos:

- [Use os serviços do Azure Lab dentro da visão geral das equipas](lab-services-within-teams-overview.md)
- [Gerir uma piscina VM em Serviços de Laboratório de Equipas](how-to-manage-vm-pool-within-teams.md)
- [Criar horários de Serviços de Laboratório de Equipas](how-to-create-schedules-within-teams.md)
- [Aceda a um VM (visão do aluno) nos Serviços de Laboratório das Equipas](how-to-access-vm-for-students-within-teams.md)


---
title: Gerir laboratórios de sala de aula no Azure Lab Services | Documentos da Microsoft
description: Saiba como criar e configurar um laboratório de sala de aula, ver todos os laboratórios de sala de aula, partilhar a ligação de registo com um utilizador de laboratório ou eliminar um laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 332f899f3502f34e46b4f158a6980dc96248140e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703158"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gerir laboratórios de sala de aula no Azure Lab Services 
Este artigo descreve como criar e eliminar um laboratório de sala de aula. Ele também mostra como ver todos os laboratórios de sala de aula numa conta de laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula numa conta do laboratório, tem de ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Proprietário de um laboratório pode adicionar outros utilizadores para a função de criador do laboratório, utilizando os passos no seguinte artigo: [Adicionar um utilizador à função de criador do laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). Tenha em atenção que Internet Explorer 11 ainda não é suportado. 
2. Selecione **iniciar sessão**. Selecione ou introduza um **ID de utilizador** isto é um membro da **criador do laboratório** função no laboratório de conta e introduza a palavra-passe. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório. 
    2. Especifique o número máximo **número de máquinas virtuais** no laboratório. Pode aumentar ou diminuir o número de máquinas virtuais no laboratório mais tarde. 
    6. Selecione **Guardar**.

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na página **Selecionar especificações da máquina virtual**, execute os passos seguintes:
    1. Selecione um **tamanho** para máquinas virtuais (VM) criadas no laboratório. Atualmente, **pequeno**, **médio**, **grandes**, e **GPU** tamanhos são permitidos.
    2. Selecione a **região** na qual pretende que as VM sejam criadas. 
    3. Selecione a **imagem de VM** a ser utilizado para criar as VM no laboratório. Se selecionar uma imagem do Linux, verá uma opção para ativar a ligação de ambiente de trabalho remoto para o mesmo. Para obter detalhes, consulte [ativar a ligação de ambiente de trabalho remoto para Linux](how-to-enable-remote-desktop-linux.md).
    4. Selecione **Seguinte**.

        ![Especificar as especificações de VM](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na página **Definir credenciais** , especifique as credenciais predefinidas para todas as VM do laboratório. 
    1. Especifique o **nome de utilizador** de todas as VM do laboratório.
    2. Especifique a **palavra-passe** do utilizador. 

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Selecione **Criar**. 

        ![Definir credenciais](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na página **Configurar modelo**, verá o estado do processo de criação do laboratório. A criação do modelo no laboratório demora no máximo 20 minutos. Um modelo num laboratório é a imagem de máquina virtual de base da qual todas as máquinas virtuais dos utilizadores são criadas. Configure a máquina virtual do modelo para que seja configurado com exatamente o que pretende fornecer aos utilizadores do laboratório.  

    ![Configurar modelo](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Depois de concluída a configuração do modelo, verá a página seguinte: 

    ![Configurar a página do modelo após a conclusão](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Os passos seguintes são opcionais neste tutorial: 
    1. Inicie a VM de modelo através da seleção de **Iniciar**.
    2. Ligue-se à VM de modelo através da seleção de **Ligar**. Se for um modelo VM do Linux, escolher se pretende ligar com SSH ou RDP (se estiver ativado RDP).
    3. Instalar e configurar o software na sua VM de modelo. 
    4. **Parar** a VM.  
    5. Introduzir uma **descrição** do modelo

        ![Em seguida, na página Configurar modelo](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Selecione **Seguinte** na página do modelo. 
10. Na página **Publicar o modelo**, execute as ações seguintes. 
    1. Para publicar o modelo imediatamente, selecione a caixa de verificação *Compreendo que não é possível modificar o modelo após a publicação. Este processo pode ser feito apenas uma vez e pode demorar até uma hora*e selecione **Publicar**.  Publique o modelo para tornar as instâncias da VM de modelo disponíveis para os seus utilizadores de laboratório.

        > [!WARNING]
        > Depois de publicar, não é possível anular a publicação. 
    2. Para publicar mais tarde, selecione **Guardar para utilização posterior**. Pode publicar a VM de modelo depois de concluir o assistente. Para obter detalhes sobre como configurar e publicar depois de concluir o assistente, consulte o artigo para obter detalhes sobre como configurar e publicar depois de concluir o assistente, consulte publicar o modelo de secção a [como gerir laboratórios de sala de aula](how-to-manage-classroom-labs.md) artigo.

        ![Publicar modelo](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Verá o **progresso da publicação** do modelo. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Verá a página seguinte, quando o modelo for publicado com êxito. Selecione **Done** (Concluído).

    ![Publicar modelo – êxito](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Verá o **dashboard** para o seu laboratório. 
    
    ![Dashboard de laboratório de sala de aula](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Mude para a página **Máquinas virtuais** e confirme que vê as máquinas virtuais que estão no estado **Não atribuído**. Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="view-all-classroom-labs"></a>Ver todos os laboratórios de sala de aula
1. Navegue para [portal do Azure Lab Services](https://labs.azure.com).
2. Selecione **iniciar sessão**. Selecione ou introduza um **ID de utilizador** isto é um membro da **criador do laboratório** função no laboratório de conta e introduza a palavra-passe. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Confirme que vê todos os laboratórios na conta de laboratório selecionado. 

    ![Todos os laboratórios](../media/how-to-manage-classroom-labs/all-labs.png)
3. Utilize a lista pendente na parte superior para selecionar uma conta de laboratório diferentes. Veja laboratórios na conta de laboratório selecionado. 

## <a name="delete-a-classroom-lab"></a>Eliminar um laboratório de sala de aula
1. No mosaico para o laboratório, selecione as reticências (...) no canto. 

    ![Selecionar o laboratório](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Selecione **Eliminar**. 

    ![Botão de eliminar](../media/how-to-manage-classroom-labs/delete-button.png)
3. Sobre o **laboratório de eliminação** caixa de diálogo, selecione **eliminar**. 

    ![Eliminar a caixa de diálogo](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Mudar para outra laboratório de sala de aula
Para mudar para outra laboratório de sala de aula do atual, selecione a lista pendente de laboratórios na conta de laboratório, na parte superior.

![Selecione o laboratório de lista pendente na parte superior](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como proprietário de um laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como proprietário de um laboratório, configurar e controlar a utilização de um laboratório](how-to-configure-student-usage.md)
- [Como um utilizador de laboratório, acessar os laboratórios de sala de aula](how-to-use-classroom-lab.md)


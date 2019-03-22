---
title: Ativar ambientes de várias VMS no Azure Lab Services | Documentos da Microsoft
description: Saiba como criar um ambiente com várias máquinas virtuais dentro de uma máquina virtual do modelo num laboratório de sala de aula do Azure Lab Services.
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190598"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Criar um ambiente com várias VMs dentro de um modelo de VM de um laboratório de sala de aula
Atualmente o Azure Lab Services permite-lhe configurar a máquina virtual de um modelo num laboratório e disponibilizar uma única cópia a cada um dos seus utilizadores. Mas se for um professor que ensina uma classe IT sobre como configurar a firewalls ou servidores, poderá ter de fornecer cada um dos seus estudantes com um ambiente em que várias máquinas virtuais podem se comunicar entre si através de uma rede.

Virtualização aninhada permite-lhe criar um ambiente de várias VMS dentro de máquina de virtual do modelo de um laboratório. Publicar o modelo fornece a cada usuário no laboratório com uma máquina virtual configurado com várias VMs dentro da mesma.

## <a name="what-is-nested-virtualization"></a>O que é a virtualização aninhada?
Virtualização aninhada permite-lhe criar máquinas virtuais dentro de uma máquina virtual. Virtualização aninhada é feita por meio do Hyper-V e só está disponível em VMs do Windows.

Para obter mais informações sobre a virtualização aninhada, consulte os artigos seguintes:

- [Virtualização aninhada no Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Como ativar a virtualização aninhada na VM do Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Utilizar a virtualização aninhada no Azure Lab Services
Os passos importantes são:

1. Criar uma **grandes** tamanho **Windows** máquina de modelo para o laboratório. 
2. Ligar à mesma e [ativar a virtualização aninhada](../../virtual-machines/windows/nested-virtualization.md).


O procedimento seguinte dá-lhe os passos detalhados: 

1. Se ainda não tiver uma, crie uma conta de laboratório. Para obter instruções, consulte [Tutorial: Configurar uma conta de laboratório com o Azure Lab Services](tutorial-setup-lab-account.md).
2. Navegue para o [site do Azure Lab Services](https://labs.azure.com). 
3. Selecione **Iniciar sessão** e introduza as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
4. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório. 
    2. Especifique o número máximo **número de máquinas virtuais** no laboratório. Pode aumentar ou decreate o número de VMs, depois de criar o laboratório ou num laboratório existente. Para obter mais informações, consulte [atualizar o número de VMs num laboratório](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. Selecione **Guardar**.

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na página **Selecionar especificações da máquina virtual**, execute os passos seguintes:
    1. Selecione **grande** para o tamanho das máquinas virtuais (VMs) a ser criada no laboratório. Atualmente, apenas o tamanho grande suporta virtualização aninhada.
    2. Escolha uma imagem de máquina virtual que é um **imagem do Windows**. Virtualização aninhada só está disponível nas máquinas do Windows. 
    3. Selecione **Seguinte**.

        ![Especificar as especificações de VM](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
5. Na página **Definir credenciais** , especifique as credenciais predefinidas para todas as VM do laboratório. 
    1. Especifique o **nome de utilizador** de todas as VM do laboratório.
    2. Especifique a **palavra-passe** do utilizador. 

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Selecione **Criar**. 

        ![Definir credenciais](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na página **Configurar modelo**, verá o estado do processo de criação do laboratório. A criação do modelo no laboratório demora no máximo 20 minutos. 

    ![Configurar modelo](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Depois de concluída a configuração do modelo, verá a página seguinte: 

    ![Configurar a página do modelo após a conclusão](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Sobre o **modelo de configurar** página, selecione **Connect** para ligar ao modelo de VM para configurar a virtualização aninhada. Também pode configurar mais tarde depois de concluir os passos neste assistente. 
9. Dentro da máquina virtual de modelo, configure a virtualização aninhada e configurar uma rede virtual com várias máquinas virtuais. Para obter instruções passo a passo detalhadas, consulte [como ativar a virtualização aninhada na VM do Azure](../../virtual-machines/windows/nested-virtualization.md). Aqui está um resumo rápido das etapas: 
    1. Ative a funcionalidade de Hyper-V na máquina de virtual do modelo.
    2. Configurar uma rede virtual interna com ligação à internet para as máquinas virtuais aninhadas
    3. Criar máquinas virtuais através do Gestor de Hyper-V
    4. Atribuir um endereço IP para as máquinas virtuais
10. Selecione **Seguinte** na página do modelo. 
11. Na página **Publicar o modelo**, execute as ações seguintes. 
    1. Para publicar o modelo imediatamente e selecione **publicar**.  

        > [!WARNING]
        > Depois de publicar, não é possível anular a publicação. 
    2. Para publicar mais tarde, selecione **Guardar para utilização posterior**. Pode publicar a VM de modelo depois de concluir o assistente. Para obter detalhes sobre como configurar e publicar depois de concluir o assistente, consulte a secção [Publicar o modelo](how-to-create-manage-template.md#publish-the-template-vm)no artigo [Como gerir laboratórios de sala de aula](how-to-manage-classroom-labs.md).

        ![Publicar modelo](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. Verá o **progresso da publicação** do modelo. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Verá a página seguinte, quando o modelo for publicado com êxito. Selecione **Done** (Concluído).

    ![Publicar modelo – êxito](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Verá o **dashboard** para o seu laboratório. 
    
    ![Dashboard de laboratório de sala de aula](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>Passos Seguintes

Agora, cada usuário obtém uma única máquina virtual que inclui um ambiente de várias VMS dentro da mesma. Para saber como adicionar utilizadores ao laboratório e enviar a ligação de registo-las, consulte o artigo seguinte: [Adicionar utilizadores ao laboratório](tutorial-setup-classroom-lab.md#add-users-to-the-lab).
---
title: Gerir um modelo de um laboratório de sala de aula em Azure Lab Services | Microsoft Docs
description: Aprenda a criar e gerir um modelo de laboratório em Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be84a05fd8b278f5176cd768191d10a20fc719e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434282"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Criar e gerir um modelo de sala de aula em Azure Lab Services
Um modelo num laboratório é a imagem de máquina virtual de base da qual todas as máquinas virtuais dos utilizadores são criadas. Configure a máquina virtual do modelo para que seja configurado com exatamente o que pretende fornecer aos utilizadores do laboratório. Pode fornecer um nome e descrição do modelo que os utilizadores do laboratório vão ver. Em seguida, publica o modelo para disponibilizar os casos do modelo VM aos utilizadores do laboratório. Ao publicar um modelo, o Azure Lab Services cria VMs no laboratório através do modelo. O número de VMs criadas neste processo é igual ao número máximo de utilizadores permitidos no laboratório, o que pode configurar na política de utilização do laboratório. Todas as máquinas virtuais têm a mesma configuração do modelo.

Este artigo descreve como criar e gerir uma máquina virtual de modelo em um laboratório de sala de aula da Azure Lab Services. 

> [!NOTE]
> Quando se cria um laboratório, o modelo VM é criado, mas ainda não começou. Pode ligá-lo, ligar-se a ele e instalar qualquer software pré-requisito para o laboratório e depois publicá-lo. Quando publica o modelo VM, é automaticamente desligado para si se não o fez. 
> 
> Os VMs do modelo incorrem em **custos** ao correr, por isso certifique-se de que o modelo VM é desligado quando não precisa que esteja a funcionar. 


## <a name="set-or-update-template-title-and-description"></a>Definir ou atualizar título e descrição do modelo
Use os seguintes passos para definir o título e a descrição pela primeira vez e atualizá-los mais tarde. 

1. Na página **modelo,** insira o novo **título** para o laboratório.  
2. Introduza a nova **descrição** para o modelo. Quando se tira o foco da caixa de texto, é automaticamente guardado. 

    ![Nome do modelo e descrição](./media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Atualizar um VM modelo
Utilize os seguintes passos para atualizar um VM do modelo.  

1. Aguarde até que o VM do modelo seja iniciado e, em seguida, **selecione Ligar o modelo** na barra de ferramentas para ligar ao VM do modelo e seguir as instruções. Se for uma máquina Do Windows, verá uma opção para descarregar o ficheiro RDP. 
1. Uma vez que se conecte ao modelo e faça alterações, deixará de ter a mesma configuração que as máquinas virtuais publicadas pela última vez para os seus utilizadores. As alterações de modelos só serão refletidas nas máquinas virtuais existentes dos seus utilizadores depois de publicar novamente.

    ![Ligar à VM do modelo](./media/how-to-create-manage-template/connect-template-vm.png)
    
1. Instale o software obrigatório para os estudantes fazerem o laboratório (por exemplo, o Visual Studio, o Explorador de Armazenamento do Azure, etc.). 
1. Desligue (feche a sessão em curso remota) a partir do modelo da VM. 
1. **Pare** o modelo VM selecionando **o modelo stop**. 
1. Siga os passos na secção seguinte para **publicar** o VM do modelo atualizado. 

## <a name="publish-the-template-vm"></a>Publicar a VM do modelo  
Neste passo, publica-se o modelo VM. Ao publicar o modelo VM, a Azure Lab Services cria VMs no laboratório utilizando o modelo. Todas as máquinas virtuais têm a mesma configuração do modelo.


1. Na página **Modelo,** **selecione Publicar** na barra de ferramentas. 

    ![Publicar botão de modelo](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, não é possível anular a publicação. 
2. Na página do **modelo publicar,** insira o número de máquinas virtuais que pretende criar no laboratório e, em seguida, selecione **Publicar**. 

    ![Modelo de publicação - número de VMs](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Vê o **estado de publicação** do modelo na página. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Aguarde até que a publicação esteja concluída e, em seguida, mude para a página da piscina das **máquinas Virtuais** selecionando **máquinas virtuais** no menu esquerdo ou selecionando o azulejo **das máquinas Virtuais.** Confirme que vê máquinas virtuais que estão em estado **não atribuído.** Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gere contas de laboratório](how-to-manage-lab-accounts.md)
- [Como dono de laboratório, crie e gere laboratórios](how-to-manage-classroom-labs.md)
- [Como dono de laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceder a laboratórios](how-to-use-classroom-lab.md)

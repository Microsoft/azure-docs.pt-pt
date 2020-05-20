---
title: Gerir um modelo de um laboratório de sala de aula em Serviços de Laboratório Azure [ Microsoft Docs
description: Aprenda a criar e gerir um modelo de laboratório em laboratório em Azure Lab Services.
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
ms.date: 05/19/2020
ms.author: spelluru
ms.openlocfilehash: 57005bc18d1eda42043236086ebbac69e54cc505
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655781"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Criar e gerir um modelo de sala de aula em Serviços de Laboratório Azure
Um modelo num laboratório é a imagem de máquina virtual de base da qual todas as máquinas virtuais dos utilizadores são criadas. Configure a máquina virtual do modelo para que seja configurado com exatamente o que pretende fornecer aos utilizadores do laboratório. Pode fornecer um nome e descrição do modelo que os utilizadores do laboratório vão ver. Em seguida, publica o modelo para disponibilizar casos do modelo VM aos utilizadores do laboratório. Ao publicar um modelo, o Azure Lab Services cria VMs no laboratório através do modelo. O número de VMs criadas neste processo é igual ao número máximo de utilizadores permitidos no laboratório, o que pode configurar na política de utilização do laboratório. Todas as máquinas virtuais têm a mesma configuração do modelo.

Este artigo descreve como criar e gerir uma máquina virtual modelo em um laboratório de sala de aula de Serviços de Laboratório Azure. 

> [!NOTE]
> Quando se cria um laboratório, o modelo VM é criado, mas não começou. Pode ligá-lo, ligá-lo e instalar qualquer software pré-requisito para o laboratório e depois publicá-lo. Quando publica o modelo VM, é automaticamente desligado para si se ainda não o tiver feito. 
> 
> Os VMs do modelo incorrem em **custos** durante a execução, por isso certifique-se de que o modelo VM é desligado quando não precisa que esteja em execução. 


## <a name="set-or-update-template-title-and-description"></a>Definir ou atualizar título e descrição do modelo
Utilize os seguintes passos para definir o título e a descrição pela primeira vez e atualizá-los mais tarde. 

1. Na página **Modelo,** insira o novo **título** para o laboratório.  
2. Introduza a nova **descrição** para o modelo. Quando se retira o foco da caixa de texto, é automaticamente guardado. 

    ![Nome e descrição do modelo](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Atualizar um modelo VM
Utilize os seguintes passos para atualizar um Modelo VM.  

1. Aguarde até que o modelo VM esteja iniciado e, em seguida, selecione **Ligar ao modelo** na barra de ferramentas para ligar ao modelo VM e siga as instruções. Se for uma máquina do Windows, verá uma opção para descarregar o ficheiro RDP. 
1. Assim que ligar ao modelo e fazer alterações, deixará de ter a mesma configuração que as máquinas virtuais publicadas pela última vez aos seus utilizadores. As alterações de modelo só se refletirão nas máquinas virtuais existentes dos seus utilizadores após a publicação.

    ![Ligar à VM do modelo](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. Instale o software obrigatório para os estudantes fazerem o laboratório (por exemplo, o Visual Studio, o Explorador de Armazenamento do Azure, etc.). 
1. Desligue (feche a sessão em curso remota) a partir do modelo da VM. 
1. **Pare** o modelo VM selecionando o **modelo stop**. 
1. Siga os passos na secção seguinte para **publicar** o modelo atualizado VM. 

## <a name="publish-the-template-vm"></a>Publicar a VM do modelo  
Neste passo, publica o modelo VM. Ao publicar o modelo VM, o Azure Lab Services cria VMs no laboratório utilizando o modelo. Todas as máquinas virtuais têm a mesma configuração do modelo.


1. Na página **'Modelo',** **selecione Publicar** na barra de ferramentas. 

    ![Publicar botão de modelo](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, não é possível anular a publicação. 
2. Na página do **modelo Publicar,** introduza o número de máquinas virtuais que pretende criar em laboratório e, em seguida, selecione **Publicar**. 

    ![Publicar modelo - número de VMs](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Você vê o **estado de publicar** o modelo na página. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Aguarde até que a publicação esteja completa e, em seguida, mude para a página de **piscina de máquinas virtuais** selecionando **máquinas virtuais** no menu esquerdo ou selecionando azulejos de **máquinas virtuais.** Confirme que vê máquinas virtuais que estão em estado **não atribuído.** Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gere as contas de laboratório](how-to-manage-lab-accounts.md)
- [Como dono de laboratório, crie e gere a gestão de laboratórios](how-to-manage-classroom-labs.md)
- [Como dono de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceda a laboratórios de sala de aula](how-to-use-classroom-lab.md)

---
title: Gerir um modelo de um laboratório de sala de aula em Serviços de Laboratório Azure  Microsoft Docs
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 08fbe9565356dc1b7db952fdd265770fef600ca8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989047"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Criar e gerir um modelo de sala de aula em Serviços de Laboratório Azure
Um modelo num laboratório é a imagem de máquina virtual de base da qual todas as máquinas virtuais dos utilizadores são criadas. Configure a máquina virtual do modelo para que seja configurado com exatamente o que pretende fornecer aos utilizadores do laboratório. Pode fornecer um nome e descrição do modelo que os utilizadores do laboratório vão ver. Em seguida, publica o modelo para disponibilizar casos do modelo VM aos utilizadores do laboratório. Ao publicar um modelo, o Azure Lab Services cria VMs no laboratório através do modelo. O número de VMs criadas neste processo é igual ao número máximo de utilizadores permitidos no laboratório, o que pode configurar na política de utilização do laboratório. Todas as máquinas virtuais têm a mesma configuração do modelo.

Este artigo descreve como criar e gerir uma máquina virtual modelo em um laboratório de sala de aula de Serviços de Laboratório Azure. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publique um modelo ao criar um laboratório de sala de aula
Para aprender a publicar um modelo enquanto cria um laboratório de sala de aula, consulte [Criar um laboratório de sala de aula](how-to-manage-classroom-labs.md#create-a-classroom-lab)
 
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
Se não publicar o modelo enquanto cria o laboratório, pode publicá-lo mais tarde. Antes de publicar, poderá querer ligar-se ao modelo VM e atualizá-lo com qualquer software. Ao publicar um modelo, o Azure Lab Services cria VMs no laboratório através do modelo. O número de VMs criados neste processo é o número de VMs que especificou quando o publicou pela primeira vez ou o que especificou na página de pool de máquinas Virtuais. Todas as máquinas virtuais têm a mesma configuração do modelo. 

1. Na página **'Modelo',** **selecione Publicar** na barra de ferramentas. 
1. Na **caixa de mensagens de modelo,** reveja a mensagem e selecione **Publicar**. Este processo pode demorar algum tempo dependendo de quantos VMs estão sendo criados.

    ![Botão Publicar](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > Depois de um modelo ser publicado, não pode ser anulado. Pode voltar a publicar o modelo. 
1. Pode ver o estado do processo de publicação na página 'Modelo'. Aguarde que o estado do modelo mude para **Publicado**. 

    ![Publicar estado](../media/how-to-create-manage-template/publish-status.png)
1. Mude para a página **Máquinas virtuais** e confirme que vê as máquinas virtuais que estão no estado **Não atribuído**. Estas VMs ainda não estão atribuídas a estudantes. Aguarde até que as VMs sejam criadas. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-los nesta página ou deixar os seus alunos iniciarem os VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerenciar laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de um laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
- [Como usuário do laboratório, acesse laboratórios da sala de aula](how-to-use-classroom-lab.md)

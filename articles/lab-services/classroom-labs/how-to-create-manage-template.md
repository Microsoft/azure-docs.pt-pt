---
title: Gerenciar um modelo de um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Saiba como criar e gerenciar um modelo de laboratório de sala de aula no Azure Lab Services.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: f0fc1e143ce7d271d5faaa8dda0eb40cdfc9e006
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332762"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Criar e gerenciar um modelo de sala de aula no Azure Lab Services
Um modelo num laboratório é a imagem de máquina virtual de base da qual todas as máquinas virtuais dos utilizadores são criadas. Configure a máquina virtual do modelo para que seja configurado com exatamente o que pretende fornecer aos utilizadores do laboratório. Pode fornecer um nome e descrição do modelo que os utilizadores do laboratório vão ver. Em seguida, você publica o modelo para tornar as instâncias da VM de modelo disponíveis para os usuários do laboratório. Ao publicar um modelo, o Azure Lab Services cria VMs no laboratório através do modelo. O número de VMs criadas neste processo é igual ao número máximo de utilizadores permitidos no laboratório, o que pode configurar na política de utilização do laboratório. Todas as máquinas virtuais têm a mesma configuração do modelo.

Este artigo descreve como criar e gerenciar uma máquina virtual de modelo em um laboratório de sala de aula de Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publicar um modelo ao criar um laboratório de sala de aula
Para saber como publicar um modelo durante a criação de um laboratório de sala de aula, consulte [criar um laboratório de sala de aula](how-to-manage-classroom-labs.md#create-a-classroom-lab)
 
## <a name="set-or-update-template-title-and-description"></a>Definir ou atualizar o título e a descrição do modelo
Use as etapas a seguir para definir o título e a descrição pela primeira vez e atualizá-los mais tarde. 

1. Na página **modelo** , insira o novo **título** para o laboratório.  
2. Insira a nova **Descrição** para o modelo. Quando você move o foco para fora da caixa de texto, ele é salvo automaticamente. 

    ![Nome e descrição do modelo](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Atualizar uma VM de modelo
Use as etapas a seguir para atualizar uma VM de modelo.  

1. Na página **modelo** , selecione **Personalizar modelo** na barra de ferramentas. 

    ![Botão Personalizar modelo](../media/how-to-create-manage-template/customize-template-button.png)
2. Na caixa de diálogo **Personalizar modelo** , selecione **continuar**. Depois de iniciar o modelo e fazer alterações, ele não terá mais a mesma configuração que as máquinas virtuais publicadas pela última vez para os usuários. As alterações de modelo não serão refletidas nas máquinas virtuais existentes de seus usuários até que você publique novamente.

    ![Caixa de diálogo Personalizar](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Selecione o botão **conectar ao modelo** na barra de ferramentas para se conectar à VM do modelo e siga as instruções. Se for um computador Windows, você verá uma opção para baixar o arquivo RDP. 

    ![Ligar à VM do modelo](../media/how-to-create-manage-template/connect-template-vm.png)
1. Instale o software obrigatório para os estudantes fazerem o laboratório (por exemplo, o Visual Studio, o Explorador de Armazenamento do Azure, etc.). 
2. Desligue (feche a sessão em curso remota) a partir do modelo da VM. 
3. **Pare** a VM do modelo selecionando **parar modelo**. 
4. Siga as etapas na próxima seção para **publicar** a VM de modelo atualizada. 

## <a name="publish-the-template-vm"></a>Publicar a VM do modelo  
Se você não publicar o modelo durante a criação do laboratório, poderá publicá-lo mais tarde. Antes da publicação, talvez você queira se conectar à VM do modelo e atualizá-la com qualquer software. Ao publicar um modelo, o Azure Lab Services cria VMs no laboratório através do modelo. O número de VMs criadas nesse processo são o número de VMs que você especificou quando a publicou pela primeira vez ou o que você especificou na página do pool de máquinas virtuais. Todas as máquinas virtuais têm a mesma configuração do modelo. 

1. Na página **modelo** , selecione **publicar** na barra de ferramentas. 
1. Na caixa de mensagem **publicar o modelo** , examine a mensagem e selecione **publicar**. Esse processo pode levar algum tempo, dependendo de quantas VMs estão sendo criadas.

    ![Botão Publicar](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > Depois de um modelo ser publicado, não pode ser anulado. No entanto, você pode republicar o modelo. 
4. Você pode ver o status do processo de publicação na página de modelo. Aguarde até que o status do modelo seja alterado para **publicado**. 

    ![Status da publicação](../media/how-to-create-manage-template/publish-status.png)
1. Mude para a página **Máquinas virtuais** e confirme que vê as máquinas virtuais que estão no estado **Não atribuído**. Estas VMs ainda não estão atribuídas a estudantes. Aguarde até que as VMs sejam criadas. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Você pode iniciá-los nesta página ou deixar que os alunos iniciem as VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerenciar laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de um laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
- [Como usuário do laboratório, acesse laboratórios da sala de aula](how-to-use-classroom-lab.md)

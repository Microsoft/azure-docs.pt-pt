---
title: Criar um laboratório no Azure DevTest Labs | Microsoft Docs
description: Este artigo acompanha-o através do processo de criação de um laboratório utilizando o portal Azure e o Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 5cd675823b85e975dcb8dfe152c27b2d30463c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759742"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Criar um laboratório no Azure DevTest Labs
Um laboratório no Azure DevTest Labs é a infraestrutura que abrange um grupo de recursos, como máquinas virtuais (VM), que lhe permite gerir melhor esses recursos através da especificação de limites e quotas. Este artigo serve de orientação durante o processo de criação de laboratórios com o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para criar um laboratório, precisa de:

* Uma subscrição do Azure. Para obter informações sobre opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/). Tem de ser o proprietário da subscrição para criar o laboratório.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Passos para criar um laboratório no Azure DevTest Labs
Os passos seguintes mostram como utilizar o portal do Azure para criar um laboratório no Azure DevTest Labs. 

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. No menu principal no lado esquerdo, selecione **Todos os serviços** (na parte superior da lista). Selecione * (estrela) ao lado de **DevTest Labs** na secção **DEVOPS.** Esta ação adiciona **DevTest Labs** ao menu de navegação esquerdo para que possa acessá-lo facilmente da próxima vez. 

    ![Todos os serviços - selecione DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
2. Selecione **DevTest Labs** no menu de navegação esquerdo. **Selecione Adicionar** na barra de ferramentas. 
   
    ![Adicionar um laboratório](./media/devtest-lab-create-lab/add-lab-button.png)
1. Na página **Create a DevTest Lab,** faça as seguintes ações: 
    1. Insira um **nome** para o laboratório.
    2. Selecione a **Subscrição** para associar ao laboratório.
    3. Insira um **nome para o grupo** de recursos para o laboratório. 
    4. Selecione um **local** para armazenar o laboratório.
    4. Selecione **Encerramento automático** para especificar se pretende ativar e definir os parâmetros para o encerramento automático de todas as VMs do laboratório. A funcionalidade de encerramento automático é principalmente uma funcionalidade de poupança de custos, na qual poderá especificar quando deseja que a VM seja automaticamente desligada. Pode alterar as definições de paragem automática após a criação do laboratório seguindo os passos descritos no artigo [Gerir todas as políticas para um laboratório em Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Introduza as informações de **NOME** e **VALOR** nas **Etiquetas** se quiser criar etiquetas personalizadas que são adicionadas a todos os recursos que criar no laboratório. As etiquetas são úteis para ajudar a gerir e organizar os recursos do laboratório por categoria. Para obter mais informações sobre etiquetas, incluindo como adicioná-las depois de criar o laboratório, veja [Add tags to a lab](devtest-lab-add-tag.md) (Adicionar etiquetas a um laboratório).
    6. Selecione **Opções de automatização** para obter modelos do Azure Resource Manager para a automatização de configuração. 
    7. Selecione **Criar**. Pode monitorizar o estado do processo de criação de laboratório ao observar a área de **Notificações**. 
    
        ![Criar uma secção de laboratório do DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. Uma vez concluído, selecione **Ir recorrer** na notificação. Em alternativa, refresque a página **de DevTest Labs** para ver o laboratório recém-criado na lista de laboratórios.  Selecione o laboratório na lista. Vê a página inicial do seu laboratório. 

        ![Página inicial para o laboratório](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que tiver criado o seu laboratório, seguem-se alguns passos a ter em consideração:

* [Acesso seguro a um laboratório](devtest-lab-add-devtest-user.md)
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md)
* [Criar um modelo de laboratório](devtest-lab-create-template.md)
* [Crie artefactos personalizados para os seus VMs](devtest-lab-artifact-author.md)
* [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)


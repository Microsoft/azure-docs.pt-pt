---
title: Criar um laboratório no Azure DevTest Labs | Microsoft Docs
description: Este artigo acompanha-o através do processo de criação de um laboratório usando o portal Azure e a Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 09999c5b0187f924f9cfbbc2afad8954adee0fd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481260"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Criar um laboratório no Azure DevTest Labs
Um laboratório no Azure DevTest Labs é a infraestrutura que abrange um grupo de recursos, como máquinas virtuais (VM), que lhe permite gerir melhor esses recursos através da especificação de limites e quotas. Este artigo serve de orientação durante o processo de criação de laboratórios com o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para criar um laboratório, precisa de:

* Uma subscrição do Azure. Para obter informações sobre opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/). Tem de ser o proprietário da subscrição para criar o laboratório.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Passos para criar um laboratório no Azure DevTest Labs
Os passos seguintes mostram como utilizar o portal do Azure para criar um laboratório no Azure DevTest Labs. 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. No menu principal no lado esquerdo, selecione **Todos os serviços** (na parte superior da lista). Selecione * (estrela) ao lado **de DevTest Labs** na secção **DEVOPS.** Esta ação adiciona **a DevTest Labs** ao menu de navegação à esquerda para que possa aceder facilmente ao mesmo da próxima vez. 

    ![Todos os serviços - selecione DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
2. Agora, selecione **DevTest Labs** no menu de navegação à esquerda. **Selecione Adicione** na barra de ferramentas. 
   
    ![Adicionar um laboratório](./media/devtest-lab-create-lab/add-lab-button.png)
1. Na página **Create a DevTest Lab,** faça as seguintes ações: 
    1. Insira um **nome** para o laboratório.
    2. Selecione a **Subscrição** para associar ao laboratório.
    3. Insira um **nome para o grupo de recursos** para o laboratório. 
    4. Selecione um **local** para guardar o laboratório.
    4. Selecione **Encerramento automático** para especificar se pretende ativar e definir os parâmetros para o encerramento automático de todas as VMs do laboratório. A funcionalidade de encerramento automático é principalmente uma funcionalidade de poupança de custos, na qual poderá especificar quando deseja que a VM seja automaticamente desligada. Pode alterar as definições de paragem automática após a criação do laboratório seguindo os passos descritos no artigo [Gerir todas as políticas para um laboratório em Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).
    1. Introduza as informações de **NOME** e **VALOR** nas **Etiquetas** se quiser criar etiquetas personalizadas que são adicionadas a todos os recursos que criar no laboratório. As etiquetas são úteis para ajudar a gerir e organizar os recursos do laboratório por categoria. Para obter mais informações sobre etiquetas, incluindo como adicioná-las depois de criar o laboratório, veja [Add tags to a lab](devtest-lab-add-tag.md) (Adicionar etiquetas a um laboratório).
    6. Selecione **Opções de automatização** para obter modelos do Azure Resource Manager para a automatização de configuração. 
    7. Selecione **Criar**. Pode monitorizar o estado do processo de criação de laboratório ao observar a área de **Notificações**. 
    
        ![Criar uma secção de laboratório do DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. Uma vez concluído, selecione **Ir para o recurso** na notificação. Em alternativa, refresque a página **da DevTest Labs** para ver o laboratório recém-criado na lista de laboratórios.  Selecione o laboratório da lista. Vê a página inicial do seu laboratório. 

        ![Página inicial para o laboratório](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que tiver criado o seu laboratório, seguem-se alguns passos a ter em consideração:

* [Acesso seguro a um laboratório](devtest-lab-add-devtest-user.md)
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md)
* [Criar um modelo de laboratório](devtest-lab-create-template.md)
* [Crie artefactos personalizados para os seus VMs](devtest-lab-artifact-author.md)
* [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)


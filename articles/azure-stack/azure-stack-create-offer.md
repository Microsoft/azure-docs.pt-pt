---
title: Criar uma oferta no Azure Stack | Documentos da Microsoft
description: Como um administrador da nuvem, saiba como criar uma oferta para os seus utilizadores no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 42336205726823dd04e0412f29c3e7a23d134d39
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763998"
---
# <a name="create-an-offer-in-azure-stack"></a>Criar uma oferta no Azure Stack

[Oferece](azure-stack-key-features.md) são grupos de um ou mais planos que Fornecedores apresentam aos utilizadores, o que os utilizadores podem comprar ou subscrever. Este artigo descreve como criar uma oferta que inclui a [plano que criou](azure-stack-create-plan.md). Esta oferta permite aos subscritores para configurar máquinas virtuais (VMs).

## <a name="create-an-offer-1902-and-later"></a>Criar uma oferta (1902 e posterior)

1. Entrar para o [portal de administrador do Azure Stack](https://adminportal.local.azurestack.external) e selecione **+ criar um recurso**, em seguida, **ofertas + planos**e, em seguida, **oferecem**.

   ![Criar uma oferta](media/azure-stack-create-offer/offers.png)

2. Uma interface do usuário com guias aparece que permite-lhe definir o nome de oferta e adicionar existente ou criar novos planos bases e planos de suplementos. Mais importante, pode rever os detalhes da oferta que cria, antes de decidir para criá-lo.

   Na **Noções básicas** separador, em **nova oferta**, introduza um **nome a apresentar** e uma **nome do recurso**e, em **recursos Grupo**, selecione **criar novo** ou **utilizar existente**. O nome a apresentar é o nome amigável para a oferta. Este nome amigável é a única informação sobre a oferta que os utilizadores veem quando eles subscrevem uma oferta no portal de utilizador. Utilize um nome intuitivo que ajuda os utilizadores a compreender o que vem com a oferta. Apenas o administrador pode ver o nome do recurso. Trata-se do nome que o administrador utiliza para trabalhar com a oferta como um recurso do Azure Resource Manager. Neste separador, pode também optar por tornar esta oferta pública ou mantenha-a privada, o que é a predefinição. Pode [alterar o estado de público ou privado da oferta](#change-the-state-of-an-offer) mais tarde, bem.

   ![Nova oferta](media/azure-stack-create-offer/new-offer.png)
  
3. Selecione o **Planos Base** separador. Selecione o plano (s) que pretende incluir na oferta.

   ![Selecionar plano](media/azure-stack-create-offer/select-plan.png)

4. Neste momento pode criar um plano de suplemento para modificar o plano de base, mas isto é opcional. Vamos criar um plano de suplemento no próximo artigo, [planos de suplementos do Azure Stack](create-add-on-plan.md).

5. Selecione o **rever + criar** separador. Reveja o resumo de oferta para garantir que todos os valores estão corretos. A interface permite-lhe expandir as quotas dos planos escolhidos, uma de cada vez, para ver os detalhes de cada quota num plano e voltar para efetue as edições necessárias.

6. Selecione **criar** para criar a oferta.

   ![Rever e criar](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Alterar o estado de uma oferta

Depois de criar a oferta, pode alterar seu estado. Ofertas têm de ser feitas **público** para os utilizadores obter a vista completa quando eles subscrevem. Ofertas podem ser:

   - **Público**: Visível para os utilizadores.
   - **Privada**: Só é visível para os administradores da cloud. Esta definição é útil durante a elaboração de plano ou à oferta, ou se o administrador da nuvem deseja [criar cada subscrição para os utilizadores](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Desativado**: Fechados a novos subscritores. O administrador da nuvem pode desativar a ofertas para evitar futuras subscrições, mas deixe os assinantes atuais afetada.

   > [!TIP]  
   > As alterações para a oferta não são imediatamente visíveis ao usuário. Para ver as alterações, os utilizadores poderão ter de terminar e iniciar sessão novamente para o portal de utilizador para ver a nova oferta.

Existem duas formas de alterar o estado de uma oferta:

1. Na **todos os recursos**, selecione o nome da oferta. Sobre o **descrição geral** ecrã para a oferta, selecione **alterar o estado**. Escolha o estado de que pretende utilizar (por exemplo, **público**).

   ![Escolha o Estado](media/azure-stack-create-offer/change-state.png)

2. Na **todos os recursos**, selecione o nome da oferta. Em seguida, selecione **definições da oferta**. Escolha o estado de que pretende utilizar (por exemplo, **pública**), em seguida, selecione **guardar**.

   ![Selecione o estado de acessibilidade](media/azure-stack-create-offer/offer-settings.png)

## <a name="create-an-offer-1901-and-earlier"></a>Criar uma oferta (1901 e anterior)

1. Entrar para o [portal de administrador do Azure Stack](https://adminportal.local.azurestack.external) e selecione **+ criar um recurso**, em seguida, **inquilino ofertas + planos**e, em seguida, **oferecem**.

   ![Criar uma oferta](media/azure-stack-create-offer/image01.png)
  
2. Sob **nova oferta**, introduza um **nome a apresentar** e um **nome do recurso**e, em **grupo de recursos**, selecione **Create novos** ou **utilizar existente**. O nome a apresentar é o nome amigável para a oferta. Este nome amigável é a única informação sobre a oferta que os utilizadores veem quando eles subscrevem uma oferta. Utilize um nome intuitivo que ajuda os utilizadores a compreender o que vem com a oferta. Apenas o administrador pode ver o nome do recurso. Trata-se do nome que o administrador utiliza para trabalhar com a oferta como um recurso do Azure Resource Manager.

   ![Nova oferta](media/azure-stack-create-offer/image01a.png)
  
3. Selecione **Planos Base** para abrir o **planear**. Selecione os planos que pretende incluir na oferta e, em seguida, escolha **selecione**. Para criar a oferta, selecione **criar**.

   ![Selecionar plano](media/azure-stack-create-offer/image02.png)
  
4. Depois de criar a oferta, pode alterar seu estado. Ofertas têm de ser feitas **público** para os utilizadores obter a vista completa quando eles subscrevem. Ofertas podem ser:

   - **Público**: Visível para os utilizadores.
   - **Privada**: Só é visível para os administradores da cloud. Esta definição é útil durante a elaboração de plano ou à oferta, ou se o administrador da nuvem deseja [criar cada subscrição para os utilizadores](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Desativado**: Fechados a novos subscritores. O administrador da nuvem pode desativar a ofertas para evitar futuras subscrições, mas deixe os assinantes atuais afetada.

   > [!TIP]  
   > As alterações para a oferta não são imediatamente visíveis ao usuário. Para ver as alterações, os utilizadores poderão ter de terminar e iniciar sessão novamente para o portal de utilizador para ver a nova oferta.

   No ecrã de descrição geral para a oferta, selecione **estado de acessibilidade**. Escolha o estado de que pretende utilizar (por exemplo, **pública**) e, em seguida, selecione **guardar**.

     ![Escolha o Estado](media/azure-stack-create-offer/change-stage-1807.png)

     Como alternativa, selecione **alterar estado** e, em seguida, escolha um Estado.

    ![Selecione o estado de acessibilidade](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> Também pode utilizar o PowerShell para criar o padrão de ofertas, planos e quotas. Para obter mais informações, consulte [módulo do PowerShell do Azure Stack 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).

## <a name="next-steps"></a>Passos Seguintes

- [Criar subscrições](azure-stack-subscribe-plan-provision-vm.md)
- [Aprovisionar uma máquina virtual](azure-stack-provision-vm.md)

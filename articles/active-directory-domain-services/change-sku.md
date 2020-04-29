---
title: Altere o SKU para um Azure AD Domain Services [ Serviços de Domínio AD' Azure [ Microsoft Docs
description: Saiba como obter o nível SKU para um domínio gerido pelo Azure AD Domain Services se os seus requisitos de negócio mudarem
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 32f8f157abaf5076911c3908a83be4a644e09656
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655588"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Altere o SKU para um domínio gerido pelos Serviços de Domínio Azure existentes

No Azure Ative Directory Domain Services (Azure AD DS), o desempenho e as funcionalidades disponíveis baseiam-se no tipo SKU. Estas diferenças de características incluem a frequência de backup ou o número máximo de fundos florestais de saída de ida (atualmente em pré-visualização). Selecione um SKU quando criar o domínio gerido e pode mudar as SKUs para cima ou para baixo à medida que o seu negócio precisa de ser alterado após a implementação do domínio gerido. As alterações nos requisitos empresariais podem incluir a necessidade de backups mais frequentes ou de criar fundos florestais adicionais. Para obter mais informações sobre os limites e preços das diferentes SKUs, consulte os [conceitos Azure AD DS SKU][concepts-sku] e as páginas de [preços da AD DS Azure.][pricing]

Este artigo mostra-lhe como mudar o SKU para um domínio gerido azure DS existente usando o portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, complete o tutorial para criar e configurar uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]

## <a name="sku-change-limitations"></a>Limitações de mudança sku

Pode alterar SKUs para cima ou para baixo depois de o domínio gerido pelo Azure AD DS ter sido implantado. No entanto, se utilizar uma floresta de recursos (atualmente em pré-visualização) e tiver criado fundos florestais de saída única de Azure AD DS para um ambiente AD DS no local, existem algumas limitações para a operação de mudança de SKU. O *Premium* e *as Enterprise* SKUs definem um limite no número de fundos que pode criar. Não pode mudar para um SKU com um limite máximo mais baixo do que o que configurado atualmente.

Por exemplo:

* Se criou dois fundos florestais no *Premium* SKU, não pode mudar para o *Standard* SKU. O *Standard* SKU não apoia os fundos florestais.
* Ou, se criou sete fundos no *Premium* SKU, não pode mudar para o *Enterprise* SKU. A *Enterprise* SKU suporta um máximo de cinco fundos.

Para obter mais informações sobre estes limites, consulte as [funcionalidades e limites do Azure AD DS SKU][concepts-sku].

## <a name="select-a-new-sku"></a>Selecione um novo SKU

Para alterar o SKU para um domínio gerido por Azure AD DS utilizando o portal Azure, complete os seguintes passos:

1. No topo do portal Azure, procure e selecione Serviços de **Domínio Azure AD.** Escolha o seu domínio gerido da lista, como *aaddscontoso.com*.
1. No menu do lado esquerdo da página Azure AD DS, selecione **Definições > SKU**.

    ![Selecione a opção de menu SKU para o seu domínio gerido azure AD DS no portal Azure](media/change-sku/overview-change-sku.png)

1. A partir do menu suspenso, selecione o SKU que deseja para o seu domínio gerido pelo Azure AD DS. Se tiver uma floresta de recursos, não pode selecionar *o Standard* SKU, uma vez que os fundos florestais só estão disponíveis no *Enterprise* SKU ou superior.

    Escolha o SKU que deseja a partir do menu suspenso e, em seguida, selecione **Guardar**.

    ![Escolha o SKU necessário do menu suspenso no portal Azure](media/change-sku/change-sku-selection.png)

Pode levar um minuto ou dois para alterar o tipo SKU.

## <a name="next-steps"></a>Passos seguintes

Se tiver uma floresta de recursos e quiser criar fundos adicionais após a mudança do SKU, consulte [Criar uma confiança florestal de saída para um domínio no local em Azure AD DS (pré-visualização)][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/

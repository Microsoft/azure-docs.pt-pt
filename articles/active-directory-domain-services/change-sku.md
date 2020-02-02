---
title: Altere o SKU para um Azure AD Domain Services  Serviços de Domínio AD' Azure  Microsoft Docs
description: Saiba como obter o nível SKU para um domínio gerido pelo Azure AD Domain Services se os seus requisitos de negócio mudarem
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 46557d802222190c0ed82f6243dd2a9b997ecaa5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960571"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Altere o SKU para um domínio gerido pelos Serviços de Domínio Azure existentes

No Azure Ative Directory Domain Services (Azure AD DS), o desempenho e as funcionalidades disponíveis baseiam-se no tipo SKU. Estas diferenças de características incluem a frequência de backup ou o número máximo de fundos florestais de saída de ida (atualmente em pré-visualização). Selecione um SKU quando criar o domínio gerido e pode mudar as SKUs à medida que o seu negócio precisa de ser alterado após a implementação do domínio gerido. As alterações nos requisitos empresariais podem incluir a necessidade de backups mais frequentes ou de criar fundos florestais adicionais. Para obter mais informações sobre os limites e preços das diferentes SKUs, consulte os [conceitos Azure AD DS SKU][concepts-sku] e as páginas de [preços da AD DS Azure.][pricing]

Este artigo mostra-lhe como mudar o SKU para um domínio gerido azure DS existente usando o portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Limitações de mudança sku

Existem algumas limitações para a operação de mudança de SKU se você usar uma floresta de recursos (atualmente em pré-visualização) e criou fundos florestais de saída única de Azure AD DS para um ambiente AD DS no local. O *Premium* e *as Enterprise* SKUs definem um limite no número de fundos que pode criar. Não pode mudar para um SKU com um limite máximo mais baixo do que o que configurado atualmente.

Por exemplo, se criou dois fundos florestais no *Premium* SKU, não pode mudar para o *SKU Padrão.* O *Standard* SKU não apoia os fundos florestais. Ou, se criou sete fundos no *Premium* SKU, não pode mudar para o *Enterprise* SKU. A *Enterprise* SKU suporta um máximo de cinco fundos.

Para obter mais informações sobre estes limites, consulte as [funcionalidades e limites do Azure AD DS SKU][concepts-sku].

## <a name="select-a-new-sku"></a>Selecione um novo SKU

Para alterar o SKU para um domínio gerido por Azure AD DS utilizando o portal Azure, complete os seguintes passos:

1. Na parte superior da portal do Azure, procure e selecione **Azure AD Domain Services**. Escolha o seu domínio gerido da lista, como *aadds.contoso.com*.
1. No menu do lado esquerdo da página Azure AD DS, **selecione Definições > SKU**.

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

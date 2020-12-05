---
title: Mude o SKU para um Azure AD Domain Services / Microsoft Docs
description: Saiba como chegar ao nível SKU para um domínio gerido por Serviços de Domínio Azure AD se os seus requisitos de negócio mudarem
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 320bd87aa78d26cee44c48f27365febd1dd426ff
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620295"
---
# <a name="change-the-sku-for-an-existing-azure-active-directory-domain-services-managed-domain"></a>Alterar o SKU para um domínio gerido pelos Serviços de Domínio do Diretório Ativo Azure existentes

Nos Serviços de Domínio do Diretório Ativo Azure (Azure AD DS), o desempenho e funcionalidades disponíveis são baseados no tipo SKU. Estas diferenças incluem a frequência de backup ou o número máximo de fundos florestais de saída de ida.

Selecione um SKU quando cria o domínio gerido e pode mudar os SKUs para cima ou para baixo à medida que o seu negócio necessita de ser alterado após a implementação do domínio gerido. As alterações nos requisitos empresariais podem incluir a necessidade de cópias de segurança mais frequentes ou de criar fundos florestais adicionais. Para obter mais informações sobre os limites e preços dos diferentes SKUs, consulte os [conceitos AZure AD DS SKU][concepts-sku] e as páginas [de preços da Azure AD DS.][pricing]

Este artigo mostra-lhe como mudar o SKU para um domínio gerido Azure AD DS existente usando o portal Azure.

## <a name="before-you-begin"></a>Before you begin

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, complete o tutorial para [criar e configurar um domínio gerido][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Limitações de alteração de SKU

Pode alterar SKUs para cima ou para baixo depois de o domínio gerido ter sido implantado. No entanto, se você usar uma floresta de recursos e criar fundos florestais de ida e saída de Azure AD DS para um ambiente AD DS no local, existem algumas limitações para a operação de mudança SKU. Os SKUs *Premium* e *Enterprise* definem um limite no número de fidedignidades que pode criar. Não pode mudar para um SKU com um limite máximo inferior ao que tem configurado atualmente.

Por exemplo:

* Se criou dois fundos florestais no *SKU Premium,* não pode mudar para o *SKU Standard.* O *Standard* SKU não apoia os fundos florestais.
* Ou, se criaste sete fidedignidades no SKU *Premium,* não podes mudar para o SKU da *Enterprise.* A *Enterprise* SKU suporta um máximo de cinco fidedignidades.

Para obter mais informações sobre estes limites, consulte [as funcionalidades e limites da Azure AD DS SKU][concepts-sku].

## <a name="select-a-new-sku"></a>Selecione um novo SKU

Para alterar o SKU para um domínio gerido utilizando o portal Azure, complete os seguintes passos:

1. No topo do portal Azure, procure e selecione **serviços de domínio Azure AD**. Escolha o seu domínio gerido na lista, como *aaddscontoso.com*.
1. No menu no lado esquerdo da página Azure AD DS, selecione **Definições > SKU**.

    ![Selecione a opção de menu SKU para o seu domínio gerido Azure AD DS no portal Azure](media/change-sku/overview-change-sku.png)

1. A partir do menu suspenso, selecione o SKU que deseja para o seu domínio gerido. Se tiver uma floresta de recursos, não pode selecionar *o Standard* SKU, uma vez que os fundos florestais só estão disponíveis no *SKU da Enterprise* ou superior.

    Escolha o SKU que deseja no menu suspenso e, em seguida, **selecione Guardar**.

    ![Escolha o SKU necessário do menu suspenso no portal Azure](media/change-sku/change-sku-selection.png)

Pode levar um minuto ou dois para mudar o tipo SKU.

## <a name="next-steps"></a>Passos seguintes

Se tiver uma floresta de recursos e quiser criar fundos adicionais após a mudança do SKU, consulte [criar uma confiança florestal de saída para um domínio no local em Azure AD DS][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/

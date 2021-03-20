---
title: Modelo de faturação MAU para identidades externas Azure AD
description: Saiba mais sobre O modelo de faturação mensal de utilizadores ativos (MAU) para colaboração de utilizadores convidados (B2B) em Azure AD. Saiba como ligar o seu inquilino Azure AD a uma subscrição da Azure.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983a803245467145a0b1161a4495e8045759e7ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92442070"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Modelo de faturação para identidades externas Azure AD

O preço das identidades externas do Azure Ative (Azure AD) baseia-se nos utilizadores ativos mensais (MAU), que é a contagem de utilizadores únicos com atividade de autenticação dentro de um mês civil. Este modelo de faturação aplica-se tanto aos inquilinos convidados da Azure AD (B2B) como [aos inquilinos AZURE AD B2C.](../../active-directory-b2c/billing.md) A faturação mau ajuda a reduzir custos oferecendo um nível livre e preços flexíveis e previsíveis. Neste artigo, conheça a faturação da MAU e ligue os seus inquilinos AD AZure a uma subscrição.

> [!IMPORTANT]
> Este artigo não contém detalhes sobre os preços. Para obter as informações mais recentes sobre a faturação e preços de utilização, consulte [os preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-i-need-to-do"></a>O que preciso fazer?

Para tirar partido da faturação mau, o seu inquilino AZure AD deve estar ligado a uma subscrição da Azure.

|Se o seu inquilino for:  |Tem de:  |
|---------|---------|
| Um inquilino da AD AZure já ligado a uma subscrição     | Não fazer nada. Quando utilizar funcionalidades de Identidades Externas para colaborar com os utilizadores convidados, será automaticamente faturado utilizando o modelo MAU.        |
| Um inquilino da AD AZure ainda não está ligado a uma subscrição     | [Ligue o seu inquilino Azure AD a uma subscrição](#link-your-azure-ad-tenant-to-a-subscription) para ativar a faturação MAU.        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>Sobre a faturação mensal dos utilizadores ativos (MAU)

No seu inquilino Azure AD, o uso da colaboração do utilizador convidado é faturado com base na contagem de utilizadores convidados únicos com atividade de autenticação dentro de um mês civil. Este modelo substitui o modelo de faturação de rácio de 1:5, que permitiu até cinco utilizadores convidados por cada licença Azure AD Premium no seu inquilino. Quando o seu inquilino estiver ligado a uma subscrição e utilizar funcionalidades de Identidades Externas para colaborar com os utilizadores convidados, será automaticamente faturado utilizando o modelo de faturação baseado na MAU.
  
O nível de preços que se aplica aos seus utilizadores convidados baseia-se no nível de preços mais elevado atribuído ao seu inquilino AZure AD. Por exemplo, se o nível de preços mais elevado do seu inquilino for O Azure AD Premium P1, o nível de preços Premium P1 também se aplica aos seus utilizadores convidados. Se o preço mais alto for Azure AD Free, será solicitado que faça upgrade para um nível de preços premium quando tentar usar funcionalidades premium para os utilizadores convidados.

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>Ligue o seu inquilino Azure AD a uma subscrição

Um inquilino Azure AD deve estar ligado a uma subscrição da Azure para faturação adequada e acesso a funcionalidades. Se o diretório já não tiver uma subscrição a que possa ligar, terá a oportunidade de adicionar uma durante este processo.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com uma conta Azure que foi atribuída pelo menos a função [de Contribuinte](../../role-based-access-control/built-in-roles.md) dentro da subscrição ou um grupo de recursos dentro da subscrição.

2. Selecione o diretório que pretende ligar: Na barra de ferramentas do portal Azure, selecione o ícone **Do Diretório + Subscrição** e, em seguida, selecione o diretório.

    ![Selecione o ícone de direção + subscrição](media/external-identities-pricing/portal-mau-pick-directory.png)

3. No âmbito **dos Serviços Azure,** selecione **Azure Ative Directory**.

4. No menu esquerdo, selecione **Identidades Externas.**

5. Em **Subscrições**, selecione **subscrições ligadas**.

6. Na lista de inquilinos, selecione a caixa de verificação ao lado do inquilino e, em seguida, selecione **a subscrição link**.

    ![Selecione o inquilino e ligue uma subscrição](media/external-identities-pricing/linked-subscriptions.png)

7. No painel de subscrição Link, selecione uma **Subscrição** e um **grupo de Recursos.** Em seguida, selecione **Aplicar**.

   > [!NOTE]
   > Se não houver subscrições listadas, pode [associar uma subscrição ao seu inquilino.](../fundamentals/active-directory-how-subscriptions-associated-directory.md) Ou, pode adicionar uma nova subscrição selecionando o link **se ainda não tiver uma subscrição, pode criar uma aqui**.

    ![Selecione um grupo de subscrição e recursos](media/external-identities-pricing/link-subscription-resource.png)

Depois de completar estes passos, a sua subscrição Azure é faturada com base nos detalhes do Acordo Azure Direct ou Enterprise, se aplicável.

## <a name="next-steps"></a>Passos seguintes

Para obter as informações mais recentes sobre preços, consulte [os preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/).
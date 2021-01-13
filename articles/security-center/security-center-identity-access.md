---
title: Monitorizar a identidade e o acesso no Centro de Segurança do Azure | Microsoft Docs
description: Saiba como utilizar a capacidade de identidade e acesso no Centro de Segurança do Azure para monitorizar a atividade de acesso dos seus utilizadores e os problemas relacionados com identidades.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: 3c0dd2b4e7e48eeb76d82c26eb52b89b61e9f668
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134114"
---
# <a name="monitor-identity-and-access"></a>Monitorizar a identidade e o acesso

O perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. Com este desenvolvimento, a segurança é menos sobre a defesa da sua rede, e mais sobre a gestão da segurança das suas apps, dados e utilizadores.

Ao monitorizar as atividades e configurações relacionadas com a identidade, pode tomar ações proativas antes de um incidente ocorrer, ou ações reativas para parar tentativas de ataques.

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>Que garantias de identidade e acesso o Centro de Segurança fornece? 

O Azure Security Center dispõe de dois controlos de segurança dedicados para garantir que está a cumprir os requisitos de identidade e segurança da sua organização: 

 - **Gerir acessos e permissões** - Encorajamo-lo a adotar o [modelo de acesso menos privilegiado](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e garantir que concede aos seus utilizadores apenas o acesso necessário para que eles façam o seu trabalho. Este controlo também inclui recomendações para implementar o [controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) para controlar o acesso aos seus recursos.
 
 - **Ativar MFA** - Com [O MFA](https://www.microsoft.com/security/business/identity/mfa) ativado, as suas contas são mais seguras, e os utilizadores ainda podem autenticar para quase qualquer aplicação com um único sent-on.

### <a name="example-recommendations-for-identity-and-access"></a>Exemplo de recomendações para identidade e acesso

Exemplos de recomendações que pode ver nestes dois controlos na página de **Recomendações** do Centro de Segurança:

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- As contas preprecadas devem ser removidas da sua subscrição (as contas preprecadas são contas que já não são necessárias e impedidas de assinar pelo Azure Ative Directory)

> [!TIP]
> Para mais informações sobre estas recomendações e as outras que poderá ver nestes [controlos, consulte recomendações de Identidade e Acesso.](recommendations-reference.md#recs-identityandaccess)

### <a name="limitations"></a>Limitações

Existem algumas limitações às proteções de identidade e acesso do Centro de Segurança:

- As recomendações de identidade não estão disponíveis para subscrições com mais de 600 contas. Nestes casos, estas recomendações serão enumeradas em "avaliações indisponíveis".
- As recomendações de identidade não estão disponíveis para os agentes administrativos do Cloud Solution Provider (CSP).
- As recomendações de identidade não identificam contas que são geridas com um sistema privilegiado de gestão de identidade (PIM). Se estiver a utilizar uma ferramenta PIM, poderá ver resultados imprecisos no controlo **de acessos e permissões de Gestão.**

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>Autenticação multi-factor (MFA) e Diretório Ativo Azure 

Habilitar o MFA requer [permissões de inquilinos do Azure Ative Directory (AD).](../active-directory/roles/permissions-reference.md)

- Se tiver uma edição premium de AD, ative o MFA utilizando [o Acesso Condicional.](../active-directory/conditional-access/concept-conditional-access-policy-common.md)
- Se estiver a utilizar a edição gratuita da AD, ative os **incumprimentos de segurança descritos** na [documentação do Azure Ative Directory](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identificar contas sem autenticação de vários fatores (MFA) habilitados

Para ver quais as contas que não têm MFA ativada, utilize a seguinte consulta de Gráfico de Recursos Azure. A consulta devolve todos os recursos insalubres - contas - da recomendação "MFA deve ser ativado em contas com permissões do proprietário na sua subscrição". 

1. Abrir **o Explorador de Gráficos de Recurso Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Página de recomendação do Azure Resource Graph Explorer**" :::

1. Introduza a seguinte consulta e selecione **consulta de execução**.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. A `additionalData` propriedade revela a lista de iDs de objeto de conta para contas que não têm MFA aplicada. 

    > [!NOTE]
    > As contas são apresentadas como IDs de objeto em vez de nomes de conta para proteger a privacidade dos titulares da conta.

> [!TIP]
> Alternativamente, pode utilizar o método de API REST do Centro de Segurança [Avaliações - Obter](/rest/api/securitycenter/assessments/get).


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre recomendações aplicáveis a outros tipos de recursos Azure, consulte o seguinte artigo:

- [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
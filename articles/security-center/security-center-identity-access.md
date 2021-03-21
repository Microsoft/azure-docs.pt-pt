---
title: Recomendações de segurança do Centro de Segurança Azure para mFA
description: Saiba como impor a autenticação de vários fatores para as suas subscrições Azure utilizando o Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 9af4f225b1b9ca5e8023a8d5b4bb7607762e4447
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631902"
---
# <a name="manage-multi-factor-authentication-mfa-enforcement-on-your-subscriptions"></a>Gerir a aplicação da autenticação de vários fatores (MFA) nas suas subscrições

Se estiver apenas a usar senhas para autenticar os seus utilizadores, está a deixar um vetor de ataque aberto. Os utilizadores usam frequentemente palavras-passe fracas ou reutilizam-nas para vários serviços. Com [o MFA](https://www.microsoft.com/security/business/identity/mfa) ativado, as suas contas são mais seguras e os utilizadores ainda podem autenticar para quase todas as aplicações com sso único.s.autônea.

Existem várias formas de ativar o MFA para os seus utilizadores do Azure Ative Directory (AD) com base nas licenças que a sua organização possui. Esta página fornece os detalhes para cada um no contexto do Centro de Segurança Azure.


## <a name="mfa-and-security-center"></a>MFA e Centro de Segurança 

O Centro de Segurança coloca um valor elevado no MFA. O controlo de segurança que mais contribui para a sua pontuação segura é **Enable MFA**. 

As recomendações no controlo Enable MFA garantem que está a cumprir as práticas recomendadas para os utilizadores das suas subscrições:

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- O MFA deve ser ativado em contas com permissões de escrita na sua subscrição

Existem três formas de ativar o MFA e estar em conformidade com as duas recomendações no Centro de Segurança: incumprimentos de segurança, atribuição por utilizador, política de acesso condicional (CA). Cada uma destas opções é explicada abaixo.

### <a name="free-option---security-defaults"></a>Opção gratuita - incumprimentos de segurança
Se estiver a utilizar a edição gratuita do Azure AD, utilize [padrão de segurança](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) para permitir a autenticação de vários fatores no seu inquilino.

### <a name="mfa-for-microsoft-365-business-e3-or-e5-customers"></a>MFA para clientes Microsoft 365 Business, E3 ou E5
Os clientes com Microsoft 365 podem utilizar **a atribuição Por utilizador.** Neste cenário, o Azure AD MFA está ativado ou desativado para todos os utilizadores, para todos os eventos de inscrição. Não existe capacidade de ativar a autenticação de vários fatores para um subconjunto de utilizadores, ou sob determinados cenários, e a gestão é através do portal do Office 365.

### <a name="mfa-for-azure-ad-premium-customers"></a>MFA para clientes Azure AD Premium
Para uma melhor experiência do utilizador, atualize para Azure AD Premium P1 ou P2 para opções **de política de acesso condicional (CA).** Para configurar uma política de CA, você precisará de permissões de [inquilinos do Azure Ative Directory (AD).](../active-directory/roles/permissions-reference.md)

A sua política de CA deve:
- impor MFA
- incluem a aplicação microsoft Azure Management ID (797f4846-ba00-4fd7-ba43-dac1f8f63013) ou todas as aplicações
- não excluir o ID da aplicação Microsoft Azure Management

Os clientes **Azure AD Premium P1** podem utilizar o Azure AD CA para solicitar aos utilizadores a autenticação de vários fatores durante determinados cenários ou eventos que se adaptem aos requisitos do seu negócio. Outras licenças que incluem esta funcionalidade: Enterprise Mobility + Security E3, Microsoft 365 F1 e Microsoft 365 E3.

**O Azure AD Premium P2** fornece as funcionalidades de segurança mais fortes e uma experiência melhorada do utilizador. Esta licença adiciona [acesso condicional baseado no risco](../active-directory/conditional-access/howto-conditional-access-policy-risk.md) às funcionalidades Azure AD Premium P1. A AC baseada no risco adapta-se aos padrões dos seus utilizadores e minimiza as indicações de autenticação de vários fatores. Outras licenças que incluem esta funcionalidade: Enterprise Mobility + Security E5 ou Microsoft 365 E5.

Saiba mais na [documentação de Acesso Condicionado Azure.](../active-directory/conditional-access/overview.md)

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identificar contas sem autenticação de vários fatores (MFA) habilitados

Pode ver a lista de contas de utilizador sem MFA ativada a partir da página de detalhes de recomendações do Centro de Segurança ou usando o Gráfico de Recursos Azure.

### <a name="view-the-accounts-without-mfa-enabled-in-the-azure-portal"></a>Ver as contas sem MFA ativadas no portal Azure
Na página de detalhes da recomendação, selecione uma subscrição da lista de **recursos insalubres** ou selecione **Ação** e a lista será exibida.

### <a name="view-the-accounts-without-mfa-enabled-using-azure-resource-graph"></a>Ver as contas sem MFA ativadas através do Gráfico de Recursos Azure
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


## <a name="faq---mfa-in-security-center"></a>FAQ - MFA no Centro de Segurança

- [Já estamos a usar a política da AC para impor a MFA. Por que ainda recebemos as recomendações do Centro de Segurança?](#were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Estamos a usar uma ferramenta de MFA de terceiros para impor a MFA. Por que ainda recebemos as recomendações do Centro de Segurança?](#were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations)
- [Porque é que o Security Center mostra as contas dos utilizadores sem permissões na subscrição como "requerendo MFA"?](#why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa)
- [Estamos a impor a MFA com a PIM. Porque é que as contas pim são apresentadas como incompatíveis?](#were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant)
- [Posso isentar ou dispensar algumas das contas?](#can-i-exempt-or-dismiss-some-of-the-accounts)
- [Existem limitações às proteções de identidade e acesso do Centro de Segurança?](#are-there-any-limitations-to-security-centers-identity-and-access-protections)

### <a name="were-already-using-ca-policy-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Já estamos a usar a política da AC para impor a MFA. Por que ainda recebemos as recomendações do Centro de Segurança?
Para investigar por que as recomendações ainda estão sendo geradas, verifique as seguintes opções de configuração na sua política de MFA CA:

- Incluiu as contas na secção **Utilizadores** da sua política de MFA CA (ou um dos grupos na secção **Grupos)**
- A aplicação Azure Management ID (797f4846-ba00-4fd7-ba43-dac1f8f63013), ou todas as aplicações, estão incluídas na secção **apps** da sua política de MFA CA
- O ID da app Azure Management não está excluído na secção **apps** da sua política de MFA CA

### <a name="were-using-a-third-party-mfa-tool-to-enforce-mfa-why-do-we-still-get-the-security-center-recommendations"></a>Estamos a usar uma ferramenta de MFA de terceiros para impor a MFA. Por que ainda recebemos as recomendações do Centro de Segurança?
As recomendações do MFA do Security Center não suportam ferramentas de MFA de terceiros (por exemplo, DUO).

Se as recomendações forem irrelevantes para a sua organização, considere a sua marcação como "atenuada", conforme descrito na [isenção de recursos e recomendações da sua pontuação segura](exempt-resource.md). Também pode [desativar uma recomendação.](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)

### <a name="why-does-security-center-show-user-accounts-without-permissions-on-the-subscription-as-requiring-mfa"></a>Porque é que o Security Center mostra as contas dos utilizadores sem permissões na subscrição como "requerendo MFA"?
As recomendações do MFA do Security Center referem-se às funções [do Azure RBAC](../role-based-access-control/role-definitions-list.md) e ao papel [de administradores de subscrição clássicos do Azure.](../role-based-access-control/classic-administrators.md) Confirme que nenhuma das contas tem estas funções.

### <a name="were-enforcing-mfa-with-pim-why-are-pim-accounts-shown-as-noncompliant"></a>Estamos a impor a MFA com a PIM. Porque é que as contas pim são apresentadas como incompatíveis?
As recomendações do MFA do Security Center não suportam contas PIM. Pode adicionar estas contas a uma política de AC na secção Utilizadores/Grupo.

### <a name="can-i-exempt-or-dismiss-some-of-the-accounts"></a>Posso isentar ou dispensar algumas das contas?
A capacidade de isentar algumas contas que não utilizam MFA não é suportada atualmente.  

### <a name="are-there-any-limitations-to-security-centers-identity-and-access-protections"></a>Existem limitações às proteções de identidade e acesso do Centro de Segurança?
Existem algumas limitações às proteções de identidade e acesso do Centro de Segurança:

- As recomendações de identidade não estão disponíveis para subscrições com mais de 600 contas. Nestes casos, estas recomendações serão enumeradas em "avaliações indisponíveis".
- As recomendações de identidade não estão disponíveis para os agentes administrativos do Cloud Solution Provider (CSP).
- As recomendações de identidade não identificam contas que são geridas com um sistema privilegiado de gestão de identidade (PIM). Se estiver a utilizar uma ferramenta PIM, poderá ver resultados imprecisos no controlo **de acessos e permissões de Gestão.**


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre recomendações aplicáveis a outros tipos de recursos Azure, consulte o seguinte artigo:

- [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
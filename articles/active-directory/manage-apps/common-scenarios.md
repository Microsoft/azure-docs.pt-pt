---
title: Cenários comuns de gestão de aplicações para o Diretório Ativo do Azure Microsoft Docs
description: Centralizar a gestão de aplicações com a Azure AD
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1874a2f2cf96aaa905616bddcc6cb83c60c1d279
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115613"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralizar a gestão de aplicações com a Azure AD

Palavras-passe, tanto um pesadelo de TI como uma dor para os empregados de todo o mundo. É por isso que cada vez mais empresas estão a recorrer ao Azure Ative Directory, à solução de Gestão de Identidade e Acesso da Microsoft para a nuvem e a todos os outros recursos. Salte de aplicação para aplicação sem ter que introduzir uma senha para cada um. Salte do Outlook, para o Workday, para a ADP o mais rápido possível, de forma rápida e segura. Em seguida, colabore com parceiros e até mesmo outros fora da sua organização, todos sem ter que chamar IT. Além disso, o Azure AD ajuda a gerir o risco, garantindo as aplicações que utiliza com coisas como a autenticação de vários fatores para verificar quem é, utilizando machine learning e inteligência de segurança continuamente adaptativas para detetar inscrições suspeitas que lhe dêem acesso seguro às aplicações de que necessita, onde quer que esteja. Não só é ótimo para os utilizadores, mas também para TI. Com avaliações de acesso just-in-time e uma suíte de governação em larga escala, a Azure AD ajuda-o a manter-se em conformidade e a aplicar políticas também. E obtém isto, pode até automatizar as contas dos utilizadores, tornando a gestão de acesso sacana. confira alguns dos cenários comuns para os casos em que o cliente utiliza as capacidades de gestão de aplicações da Azure Ative Directory.

**Cenários comuns**


> [!div class="checklist"]
> * SSO para todas as suas aplicações
> * Automatizar o provisionamento e a desprovisionamento 
> * Proteja as suas aplicações
> * Regule o acesso às suas aplicações
> * Acesso híbrido seguro

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Cenário 1: Configurar SSO para todas as suas aplicações

Chega de gerir a senha. Aceda de forma segura a todos os recursos necessários com as suas credenciais corporativas. 

|Funcionalidade  | Descrição | Recomendação |
|---------|---------|---------|
|SSO|SSO federado baseado em padrões usando padrões de confiança da indústria.|Utilize sempre [o SAML / OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) para ativar o SSO quando a sua aplicação o suporta.|
|Painel de acesso|Ofereça aos seus utilizadores um simples hub para descobrir e aceder a todas as suas aplicações. Permitir-lhes ser mais produtivos com capacidades de self-service, como solicitar acesso a apps e grupos, ou gerir o acesso aos recursos em nome de outros.| Implemente o painel de [acesso](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) na sua organização assim que tiver integrado as suas aplicações com a Azure AD para SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Cenário 2: Automatizar o provisionamento e a desprovisionamento 


A maioria das aplicações exige que um utilizador seja aprovisionado na aplicação antes de aceder aos recursos de que necessita. A utilização de ficheiros CSV ou scripts complexos pode ser dispendiosa e difícil de gerir. Além disso, os clientes precisam de garantir que as contas são removidas quando alguém não deve ter mais acesso. Aproveite as ferramentas abaixo para automatizar o fornecimento e o deprovisionamento. 


|Funcionalidade  |Descrição|Recomendação |
|---------|---------|---------|
|Provisionamento SCIM|[A SCIM](https://aka.ms/SCIMOverview) é uma excelente prática da indústria para automatizar o fornecimento de utilizadores. Qualquer aplicação compatível com o SCIM pode ser integrada com a Azure AD. Criar, atualizar e eliminar automaticamente as contas de utilizador sem ter de manter ficheiros CSV, scripts personalizados ou soluções on-prem.|Confira a crescente lista de aplicações [pré-integradas](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) na galeria de aplicações Azure AD|
|Microsoft Graph|Aproveite a respiração e profundidade dos dados que a Azure AD tem para enriquecer a sua aplicação com os dados de que necessita.|Aproveite o gráfico da [Microsoft](https://developer.microsoft.com/graph/) para obter dados de todo o ecossistema da Microsoft. |


## <a name="scenario-3-secure-your-applications"></a>Cenário 3: Proteja as suas aplicações
A identidade é o eixo da segurança. Se uma identidade fica comprometida, é incrivelmente difícil parar o efeito dominó antes que seja tarde demais. Em média, passam mais de 100 dias até que as organizações descubram que houve um compromisso. Utilize as ferramentas fornecidas pela Azure AD para melhorar a postura de segurança das suas aplicações. 

|Funcionalidade  |Descrição| Recomendação |
|---------|---------| ---------|
|Azure MFA|A Azure Multi-Factor Authentication (MFA) é uma solução de verificação em dois passos da Microsoft. Utilizando métodos de autenticação aprovados pela administração, o Azure MFA ajuda a salvaguardar o acesso aos seus dados e aplicações, ao mesmo tempo que satisfaz a exigência de um processo de entrada simples.| [Ative o MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) para os seus utilizadores.  |
|Acesso Condicional|Com acesso condicional, pode implementar decisões automatizadas de controlo de acesso para quem pode aceder às suas aplicações na nuvem, com base em condições.| Reveja os [incumprimentos](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) de segurança e [as políticas comuns](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) que os clientes estão a usar. | 
|Identity Protection|A Identity Protection utiliza as aprendizagens que a Microsoft adquiriu a partir da sua posição em organizações com a Azure AD, o espaço de consumo com as Contas Microsoft e em jogos com a Xbox para proteger os seus utilizadores. A Microsoft analisa 6,5 biliões de sinais por dia para identificar e proteger os clientes de ameaças.|Ativar as [políticas de proteção de identidade predefinidas](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) fornecidas pelo nosso serviço. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Cenário 4: Reger o acesso às suas candidaturas
A Governação de Identidade ajuda as organizações a alcançar um equilíbrio entre a produtividade - Quão rapidamente uma pessoa pode ter acesso às aplicações de que precisa, como quando se juntam à minha organização? E segurança - Como deve o seu acesso mudar ao longo do tempo, como devido a alterações ao estatuto laboral dessa pessoa? 

|Funcionalidade  |Descrição|Recomendação |
|---------|---------| ---------|
|OLMO|A gestão de direitos da Azure AD pode ajudar os utilizadores dentro e fora da sua organização a gerir de forma mais eficiente o acesso às suas aplicações.| Permitir que os não administradores gerem o acesso às suas aplicações com pacotes de [acesso.](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)|
|Revisões de Acesso|O acesso do utilizador às aplicações pode ser revisto regularmente para garantir que apenas as pessoas certas tenham acesso continuado.| [Reveja o acesso](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) às suas aplicações mais sensíveis. |
|Log Analytics|Gere relatórios sobre quem acede a quais aplicações e armazene-as na sua ferramenta SIEM de eleição para correlacionar dados entre fontes de dados e ao longo do tempo.| Ative a análise de [registoe](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) configurar alertas para eventos críticos relacionados com as suas aplicações. |


## <a name="scenario-5-hybrid-secure-access"></a>Cenário 5: Acesso Híbrido Seguro
A identidade só pode ser o seu plano de controlo se conseguir ligar tudo através de aplicações cloud e no local. Aproveite as ferramentas fornecidas pela Azure AD e seus parceiros para garantir o acesso a aplicações baseadas em legados.

|Funcionalidade  |Descrição|Recomendação |
|---------|---------|---------|
|Proxy de Aplicações|Os colaboradores dos nossos dias querem ser produtivos em qualquer sítio, em qualquer altura e em qualquer dispositivo. Precisam de aceder a aplicações SaaS na nuvem e aplicações corporativas no local. O representante da Aplicação Azure AD permite este acesso robusto sem redes privadas virtuais dispendiosas e complexas (VPNs) ou zonas desmilitarizadas (DMZs).|Configurar [o acesso remoto](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) para as suas aplicações on-prem. |
|F5, Akamai, Zscaler|Utilizando o seu controlador de rede e entrega existente, pode facilmente proteger aplicações antigas que ainda são cruciais para os seus processos de negócio, mas que não conseguiu proteger antes com a Azure AD. É provável que já tenha tudo o que precisa para começar a proteger estas aplicações.| Usando Akamai, Citrix, F5 ou Zscaler? Confira as [nossas soluções pré-construídas.](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access) | 

## <a name="related-articles"></a>Artigos relacionados

- [Gestão de aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Aprovisionamento de aplicações](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Acesso híbrido seguro](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [Governação da identidade](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Segurança de identidade](https://docs.microsoft.com/azure/active-directory/conditional-access/index)

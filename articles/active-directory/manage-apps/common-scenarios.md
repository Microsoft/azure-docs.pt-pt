---
title: Cenários comuns de gestão de aplicações para o Azure Ative Directory | Microsoft Docs
description: Centralizar a gestão de aplicações com a Azure AD
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/02/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6372407a5deebefab60e09a9d0c01d977157357b
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259575"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralizar a gestão de aplicações com a Azure AD

Palavras-passe, tanto um pesadelo de TI como uma dor para os empregados em todo o mundo. É por isso que cada vez mais empresas estão a recorrer ao Azure Ative Directory, solução de Gestão de Identidade e Acesso da Microsoft para a nuvem e todos os seus outros recursos. Salte de aplicação para aplicação sem ter que inserir uma senha para cada um. Salte de Outlook, para Workday, para ADP o mais rápido possível abri-los, de forma rápida e segura. Em seguida, colabore com parceiros e até outros fora da sua organização, todos sem ter que chamar IT. Além disso, o Azure AD ajuda a gerir o risco, assegurando as aplicações que utiliza com coisas como autenticação multi-fator para verificar quem é, utilizando aprendizagem automática e inteligência de segurança continuamente adaptativas para detetar insusões suspeitas que lhe dão acesso seguro às aplicações de que necessita, onde quer que esteja. Não só é ótimo para os utilizadores, mas também para ti. Com revisões de acesso just-in-time e uma suíte de governação em larga escala, a Azure AD ajuda-o a manter-se em conformidade e a impor políticas também. E obtenha isto, pode até automatizar o fornecimento de contas de utilizadores, tornando a gestão de acessos uma brisa. confira alguns dos cenários comuns para os que o cliente utiliza as capacidades de gestão de aplicações do Azure Ative Directory.

**Cenários comuns**


> [!div class="checklist"]
> * SSO para todas as suas aplicações
> * Automatizar o provisionamento e a desprovisionamento 
> * Proteja as suas aplicações
> * Regule o acesso às suas aplicações
> * Acesso híbrido seguro

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Cenário 1: Configurar SSO para todas as suas aplicações

Chega de gerir a senha. Aceda de forma segura todos os recursos necessários com as suas credenciais corporativas. 

|Funcionalidade  | Descrição | Recomendação |
|---------|---------|---------|
|SSO|SSO federado baseado em padrões usando padrões de indústria fidedignas.|Utilize sempre [o SAML /OIDC](../develop/v2-howto-app-gallery-listing.md) para ativar o SSO quando a sua aplicação o suporta.|
|As Minhas Aplicações|Ofereça aos seus utilizadores um simples hub para descobrir e aceder a todas as suas aplicações. Capacitá-los a serem mais produtivos com capacidades de self-service, como solicitar acesso a apps e grupos, ou gerir o acesso a recursos em nome de outros.| Implemente [as Minhas Apps](my-apps-deployment-plan.md) na sua organização assim que tiver integrado as suas aplicações com Azure AD para SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Cenário 2: Automatizar o provisionamento e a desprovisionamento 


A maioria das aplicações requer que um utilizador seja a provisionado na aplicação antes de aceder aos recursos de que necessita. A utilização de ficheiros CSV ou scripts complexos pode ser dispendiosa e difícil de gerir. Além disso, os clientes precisam de garantir que as contas são removidas quando alguém não deve ter mais acesso. Aproveite as ferramentas abaixo para automatizar o fornecimento e a desprovisionamento. 


|Funcionalidade  |Descrição|Recomendação |
|---------|---------|---------|
|Provisão SCIM|[A SCIM](https://aka.ms/SCIMOverview) é uma das melhores práticas da indústria para automatizar o fornecimento de utilizadores. Qualquer aplicação compatível com SCIM pode ser integrada com Azure AD. Crie, atualize e elimine automaticamente as contas dos utilizadores sem ter de manter ficheiros CSV, scripts personalizados ou soluções on-prem.|Confira a crescente lista de aplicações [pré-integradas](../saas-apps/tutorial-list.md) na galeria de aplicações AD AZure|
|Microsoft Graph|Aproveite a respiração e profundidade de dados que a Azure AD tem para enriquecer a sua aplicação com os dados de que necessita.|Aproveite o gráfico da [Microsoft](https://developer.microsoft.com/graph/) para obter dados de todo o ecossistema da Microsoft. |


## <a name="scenario-3-secure-your-applications"></a>Cenário 3: Proteja as suas aplicações
A identidade é o eixo da segurança. Se uma identidade fica comprometida, é incrivelmente difícil parar o efeito dominó antes que seja tarde demais. Em média, passam mais de 100 dias antes que as organizações descubram que houve um compromisso. Utilize as ferramentas fornecidas pela Azure AD para melhorar a postura de segurança das suas aplicações. 

|Funcionalidade  |Descrição| Recomendação |
|---------|---------| ---------|
|Azure AD MFA|A Azure AD Multi-Factor Authentication (MFA) é a solução de verificação em duas etapas da Microsoft. Utilizando métodos de autenticação aprovados pela administração, o Azure AD MFA ajuda a salvaguardar o acesso aos seus dados e aplicações, satisfazendo a procura de um processo simples de inscrição.| [Ativar o MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) para os seus utilizadores.  |
|Acesso Condicional|Com o Acesso Condicional, pode implementar decisões automatizadas de controlo de acesso para quem pode aceder às suas aplicações na nuvem, com base nas condições.| Reveja os [incumprimentos de segurança](../fundamentals/concept-fundamentals-security-defaults.md) e [as políticas comuns que](../conditional-access/concept-conditional-access-policy-common.md) os clientes estão a usar. | 
|Identity Protection|A Identity Protection utiliza as aprendizagens que a Microsoft adquiriu da sua posição em organizações com a Azure AD, o espaço de consumo com contas da Microsoft e em jogos com a Xbox para proteger os seus utilizadores. A Microsoft analisa 6,5 biliões de sinais por dia para identificar e proteger os clientes de ameaças.|Ativar as [políticas de proteção de identidade padrão](../identity-protection/concept-identity-protection-policies.md) fornecidas pelo nosso serviço. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Cenário 4: Regule o acesso às suas aplicações
A Governação da Identidade ajuda as organizações a alcançar um equilíbrio entre produtividade - Quão rapidamente uma pessoa pode ter acesso às aplicações de que precisa, como quando se junta à minha organização? E segurança - Como deve o seu acesso mudar ao longo do tempo, como devido a alterações ao estatuto laboral dessa pessoa? 

|Funcionalidade  |Descrição|Recomendação |
|---------|---------| ---------|
|OLMO|A gestão de direitos AD AZure pode ajudar os utilizadores dentro e fora da sua organização a gerir de forma mais eficiente o acesso às suas aplicações.| Permitir que os administradores não administrativos consigam aceder às suas aplicações com [pacotes de acesso.](../governance/entitlement-management-access-package-first.md)|
|Revisões de Acesso|O acesso dos utilizadores às apps pode ser revisto regularmente para garantir que apenas as pessoas certas tenham acesso continuado.| [Reveja o acesso](../governance/access-reviews-overview.md) às suas aplicações mais sensíveis. |
|Log Analytics|Gere relatos sobre quem está a aceder às aplicações e armazene-as na sua ferramenta siem de eleição para correlacionar dados entre fontes de dados e ao longo do tempo.| Ative [a análise de registos](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) e configurar alertas para eventos críticos relacionados com as suas aplicações. |


## <a name="scenario-5-hybrid-secure-access"></a>Cenário 5: Acesso Híbrido Seguro
A identidade só pode ser o seu plano de controlo se conseguir ligar tudo através de aplicações em nuvem e no local. Aproveite as ferramentas fornecidas pela Azure AD e seus parceiros para garantir o acesso a aplicações baseadas em legados.

|Funcionalidade  |Descrição|Recomendação |
|---------|---------|---------|
|Proxy da aplicação|Os colaboradores dos nossos dias querem ser produtivos em qualquer sítio, em qualquer altura e em qualquer dispositivo. Precisam de aceder a aplicações SaaS na nuvem e aplicações corporativas no local. O proxy da Azure AD Application permite este acesso robusto sem redes privadas virtuais (VPNs) ou zonas desmilitarizadas (DMZs).|Configurar [o acesso remoto](./application-proxy.md) para as suas aplicações on-prem. |
|F5, Akamai, Zscaler|Utilizando o seu controlador de rede e entrega existente, pode proteger facilmente aplicações antigas que ainda são fundamentais para os seus processos de negócio, mas que não conseguiu proteger antes com a Azure AD. É provável que já tenhas tudo o que precisas para começar a proteger estas aplicações.| Usando Akamai, Citrix, F5 ou Zscaler? Confira as [nossas soluções pré-construídas.](./secure-hybrid-access.md) | 

## <a name="related-articles"></a>Artigos relacionados

- [Gestão de aplicações](./index.yml)
- [Aprovisionamento de aplicações](../app-provisioning/user-provisioning.md)
- [Acesso híbrido seguro](./secure-hybrid-access.md)
- [Governação de identidade](../governance/identity-governance-overview.md)
- [Plataforma de identidades da Microsoft](../develop/v2-overview.md)
- [Segurança de identidade](../conditional-access/index.yml)
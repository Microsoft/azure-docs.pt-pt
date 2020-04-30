---
title: Responda rapidamente a identidades seguras com o Diretório Ativo Azure
description: Responda rapidamente a ameaças com identidades baseadas em nuvem azure
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 713afb7b277fba65dc4c860e8bdd6b62b4e0147d
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204952"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Responda rapidamente a identidades seguras com a AD Azure

Pode parecer assustador tentar proteger os seus trabalhadores no mundo de hoje, especialmente quando você tem que responder rapidamente e fornecer acesso a muitos serviços rapidamente. Este artigo destina-se a fornecer uma lista concisa de todas as ações a tomar, ajudando-o a identificar e priorizar qual a ordem de implementação das funcionalidades azure AD com base no tipo de licença que possui. A Azure AD oferece muitas funcionalidades e fornece muitas camadas de segurança para as suas Identidades, navegar qual a funcionalidade relevante pode por vezes ser esmagadora. Muitas organizações já estão na nuvem ou movem-se rapidamente para a nuvem, este documento destina-se a permitir-lhe implementar serviços rapidamente, garantindo as suas identidades como principal consideração. 

Cada tabela fornece uma recomendação de segurança consistente, protegendo as identidades do Administrador e do Utilizador dos principais ataques de segurança (repetição de violações, phishing e spray de palavra-passe) minimizando o impacto do utilizador e melhorando a experiência do utilizador. 

A orientação também permitirá que os administradores configurem o acesso às aplicações SaaS e no local de forma segura e protegida e é aplicável a identidades cloud ou híbridas (sincronizadas) e aplica-se aos utilizadores que trabalham remotamente ou no escritório.

Esta lista de verificação irá ajudá-lo a implementar rapidamente ações recomendadas críticas para proteger a sua organização imediatamente, explicando como:

- Fortaleça as suas credenciais.
- Reduza a sua área de superfície de ataque.
- Automatizar a resposta à ameaça.
- Utilize a inteligência na nuvem.
- Ativar o self-service do utilizador final.

## <a name="prerequisites"></a>Pré-requisitos

Este guia pressupõe que as suas identidades apenas em nuvem ou híbrida saem já estabelecidas em Azure AD. Para ajudar na escolha do seu tipo de identidade consulte o artigo, [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](../hybrid/choose-ad-authn.md) 

## <a name="summary"></a>Resumo

Existem muitos aspetos para uma infraestrutura de identidade segura, mas esta lista de verificação centra-se numa infraestrutura de identidade segura e segura que permite aos utilizadores trabalhar remotamente. Garantir a sua identidade é apenas uma parte da sua história de segurança, proteger dados, aplicações e dispositivos também deve ser considerado.

### <a name="guidance-for-azure-ad-free-or-office-365-customers"></a>Orientação para clientes Azure AD Free ou Office 365.

Existem várias recomendações que os clientes da app Azure AD Free ou Office 365 devem tomar para proteger as suas identidades de utilizador, o quadro seguinte destina-se a destacar as ações-chave para as seguintes assinaturas de licença:

- Escritório 365 (O365 E1, E3, E5, F1, A1, A3, A5)
- Escritório 365 Negócios (Essencial, Negócios, Business Premium)
- Microsoft 365 (M365 Business, A1)
- Azure AD Free (incluído com Azure, Dynamics 365, Intune e Power Platform)

| Ação recomendada | Detalhe |
| --- | --- |
| [Ativar falhas de segurança](concept-fundamentals-security-defaults.md) | Proteja todas as identidades e aplicações do utilizador, permitindo o MFA e bloqueando a autenticação do legado |
| [Ativar o Password Hash Sync](../hybrid/how-to-connect-password-hash-synchronization.md) (se utilizar identidades híbridas) | Fornecer redundância para autenticação e melhorar a segurança (incluindo Smart Lockout, IP Lockout, e a capacidade de descobrir credenciais vazadas.) |
| Ativar o [bloqueio inteligente ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (se aplicável) | Protege os seus utilizadores de experimentarem o bloqueio da conta extranet de atividades maliciosas. |
| Ativar o bloqueio inteligente do [Diretório Ativo Azure](../authentication/howto-password-smart-lockout.md) (se utilizar identidades geridas) | Smart lockout ajuda a bloquear maus atores que estão a tentar adivinhar as palavras-passe dos seus utilizadores ou usar métodos de força bruta para entrar. |
| [Desativar o consentimento do utilizador final para as aplicações](../manage-apps/configure-user-consent.md) | O fluxo de trabalho de consentimento do administrador dá aos administradores uma forma segura de conceder acesso a aplicações que requerem aprovação administrativa para que os utilizadores finais não exponham os dados corporativos. A Microsoft recomenda que desaperte futuras operações de consentimento dos utilizadores para ajudar a reduzir a sua área de superfície e mitigar este risco. |
| [Integrar aplicações saaS suportadas da galeria para a AD Azure e permitir a inscrição única](../manage-apps/add-application-portal.md) | A Azure AD tem uma galeria que contém milhares de aplicações pré-integradas. Algumas das aplicações que a sua organização utiliza estão provavelmente na galeria acessível diretamente do portal Azure. Fornecer acesso a aplicações saaS corporativas remotamente e de forma segura com uma melhor experiência do utilizador (SSO) |
| [Automatizar o fornecimento e o desprovisionamento dos utilizadores a partir de aplicações SaaS](../app-provisioning/user-provisioning.md) (se aplicável) | Criar automaticamente identidades e funções de utilizador nas aplicações cloud (SaaS) a que os utilizadores precisam de ter acesso. Além de criar identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como situação ou mudança de funções, aumentando a segurança da sua organização. |
| [Permitir o acesso híbrido Seguro: Proteja as aplicações antigas com controladores e redes de entrega de aplicações existentes](../manage-apps/secure-hybrid-access.md) (se aplicável) | Publique e proteja as suas aplicações de autenticação no local e na nuvem, ligando-as à AD Azure com o seu controlador ou rede de entrega de aplicações existente. |
| [Ativar o reset de palavra-passe self-service](../authentication/tutorial-enable-sspr.md) (aplicável apenas às contas em nuvem) | Esta capacidade reduz as chamadas de secretária e a perda de produtividade quando um utilizador não pode assinar no seu dispositivo ou numa aplicação. |
| [Utilize, sempre que possível, funções administrativas não globais](../users-groups-roles/directory-assign-admin-roles.md) | Dê aos seus administradores apenas o acesso de que necessitam apenas para as áreas a que necessitam acesso. Nem todos os administradores precisam de ser administradores globais. |
| [Ativar a orientação de palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os utilizadores alterem a sua palavra-passe num horário definido, desativem os requisitos de complexidade e os seus utilizadores estão mais aptos a lembrar-se das suas palavras-passe e a mantê-las algo seguro. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Orientação para clientes do Plano Premium Azure AD 1.

O quadro seguinte destina-se a destacar as acções-chave para as seguintes assinaturas de licença:

- Azure Ative Directory Premium P1 (Azure AD P1)
- Mobilidade Empresarial + Segurança (EMS E3)
- Microsoft 365 (M365 E3, A3, F1, F3)

| Ação recomendada | Detalhe |
| --- | --- |
| [Permitir a experiência de registo combinado para o Azure MFA e sSPR para simplificar a experiência de registo do utilizador](../authentication/howto-registration-mfa-sspr-combined.md) | Permita que os seus utilizadores se registem a partir de uma experiência comum para a autenticação de multi-factores Azure e para o reset de senha de autosserviço. |
| [Configure as definições de MFA para a sua organização](../authentication/howto-mfa-getstarted.md) | Garantir que as contas estão protegidas de serem comprometidas com a autenticação de vários fatores |
| [Ativar a reposição de palavras-passe self-service](../authentication/tutorial-enable-sspr.md) | Esta capacidade reduz as chamadas de secretária e a perda de produtividade quando um utilizador não pode assinar no seu dispositivo ou numa aplicação |
| [Implementar A Writeback password](../authentication/tutorial-enable-sspr-writeback.md) (se utilizar identidades híbridas) | Permita que as alterações de palavra-passe na nuvem sejam reescritas para um ambiente de Diretório Ativo do Windows Server no local. |
| Criar e ativar políticas de acesso condicional | [MFA para administradores para proteger contas que são atribuídas direitos administrativos.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Bloqueie protocolos de autenticação legado devido ao risco acrescido associado aos protocolos de autenticação do legado.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [MFA para todos os utilizadores e aplicações para criar uma política de MFA equilibrada para o seu ambiente, garantindo aos seus utilizadores e aplicações.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Exija mFA para a Azure Management proteger os seus recursos privilegiados, exigindo a autenticação de vários fatores para qualquer utilizador que aceda aos recursos do Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Ativar o Password Hash Sync](../hybrid/how-to-connect-password-hash-synchronization.md) (se utilizar identidades híbridas) | Fornecer redundância para autenticação e melhorar a segurança (incluindo Smart Lockout, IP Lockout, e a capacidade de descobrir credenciais vazadas.) |
| Ativar o [bloqueio inteligente ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (se aplicável) | Protege os seus utilizadores de experimentarem o bloqueio da conta extranet de atividades maliciosas. |
| Ativar o bloqueio inteligente do [Diretório Ativo Azure](../authentication/howto-password-smart-lockout.md) (se utilizar identidades geridas) | Smart lockout ajuda a bloquear maus atores que estão a tentar adivinhar as palavras-passe dos seus utilizadores ou usar métodos de força bruta para entrar. |
| [Desativar o consentimento do utilizador final para as aplicações](../manage-apps/configure-user-consent.md) | O fluxo de trabalho de consentimento do administrador dá aos administradores uma forma segura de conceder acesso a aplicações que requerem aprovação administrativa para que os utilizadores finais não exponham os dados corporativos. A Microsoft recomenda que desaperte futuras operações de consentimento dos utilizadores para ajudar a reduzir a sua área de superfície e mitigar este risco. |
| [Permitir o acesso remoto a aplicações antigas no local com procuração de aplicações](../manage-apps/application-proxy-add-on-premises-application.md) | Ative o Proxy de Aplicação aD Azure e integre-se com aplicações antigas para que os utilizadores acedam de forma segura às aplicações no local, inserindo-se com a sua conta Azure AD. |
| [Ativar o acesso híbrido Secure: Proteja as aplicações antigas com controladores e redes de entrega de aplicações existentes](../manage-apps/secure-hybrid-access.md) (se aplicável). | Publique e proteja as suas aplicações de autenticação no local e na nuvem, ligando-as à AD Azure com o seu controlador ou rede de entrega de aplicações existente. |
| [Integrar aplicações saaS suportadas da galeria para a AD Azure e permitir a inscrição única](../manage-apps/add-application-portal.md) | A Azure AD tem uma galeria que contém milhares de aplicações pré-integradas. Algumas das aplicações que a sua organização utiliza estão provavelmente na galeria acessível diretamente do portal Azure. Fornecer acesso a aplicações saaS corporativas remotamente e de forma segura com uma melhor experiência do utilizador (SSO). |
| [Automatizar o fornecimento e o desprovisionamento dos utilizadores a partir de aplicações SaaS](../app-provisioning/user-provisioning.md) (se aplicável) | Criar automaticamente identidades e funções de utilizador nas aplicações cloud (SaaS) a que os utilizadores precisam de ter acesso. Além de criar identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como situação ou mudança de funções, aumentando a segurança da sua organização. |
| [Ativar o acesso condicional – Baseado em Dispositivos](../conditional-access/require-managed-devices.md) | Melhorar as experiências de segurança e de utilização com acesso condicional baseado no dispositivo. Este passo garante que os utilizadores só podem aceder a partir de dispositivos que cumpram os seus padrões de segurança e conformidade. Estes dispositivos também são conhecidos como dispositivos geridos. Os dispositivos geridos podem ser dispositivos intune compatíveis ou Hybrid Azure AD. |
| [Ativar a proteção de passwords](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Proteja os utilizadores de usar palavras-passe fracas e fáceis de adivinhar. |
| [Designar mais do que um administrador global](../users-groups-roles/directory-emergency-access.md) | Atribuir pelo menos duas contas de administrador global permanente permanente sem nuvens para uso em caso de emergência. Estas contas não são utilizadas diariamente e devem ter senhas longas e complexas. Break Glass Accounts certifique-se de que pode aceder ao serviço em caso de emergência. |
| [Utilize, sempre que possível, funções administrativas não globais](../users-groups-roles/directory-assign-admin-roles.md) | Dê aos seus administradores apenas o acesso de que necessitam apenas para as áreas a que necessitam acesso. Nem todos os administradores precisam de ser administradores globais. |
| [Ativar a orientação de palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os utilizadores alterem a sua palavra-passe num horário definido, desativem os requisitos de complexidade e os seus utilizadores estão mais aptos a lembrar-se das suas palavras-passe e a mantê-las algo seguro. |
| [Criar um plano para o acesso ao utilizador convidado](../b2b/what-is-b2b.md) | Colabore com os utilizadores convidados, permitindo-lhes assinar nas suas apps e serviços com o seu próprio trabalho, escola ou identidades sociais. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Orientação para clientes do Plano Premium Azure AD 2.

O quadro seguinte destina-se a destacar as acções-chave para as seguintes assinaturas de licença:

- Diretório Ativo Azure Premium P2 (Azure AD P2)
- Mobilidade Empresarial + Segurança (EMS E5)
- Microsoft 365 (M365 E5, A5)

| Ação recomendada | Detalhe |
| --- | --- |
| [Permitir a experiência de registo combinado para o Azure MFA e sSPR para simplificar a experiência de registo do utilizador](../authentication/howto-registration-mfa-sspr-combined.md) | Permita que os seus utilizadores se registem a partir de uma experiência comum para a autenticação de multi-factores Azure e para o reset de senha de autosserviço. |
| [Configure as definições de MFA para a sua organização](../authentication/howto-mfa-getstarted.md) | Garantir que as contas estão protegidas de serem comprometidas com a autenticação de vários fatores |
| [Ativar a reposição de palavras-passe self-service](../authentication/tutorial-enable-sspr.md) | Esta capacidade reduz as chamadas de secretária e a perda de produtividade quando um utilizador não pode assinar no seu dispositivo ou numa aplicação |
| [Implementar A Writeback password](../authentication/tutorial-enable-sspr-writeback.md) (se utilizar identidades híbridas) | Permita que as alterações de palavra-passe na nuvem sejam reescritas para um ambiente de Diretório Ativo do Windows Server no local. |
| [Ativar políticas de proteção de identidade para impor o registo de MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Gerir o lançamento da Autenticação Multi-Factor Azure (MFA). |
| [Ativar políticas de risco de utilizador e de risco de proteção de identidade](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Ativar as políticas de utilizador e de sessão de proteção de identidade. A política de inscrição recomendada destina-se a direcionar os sign-ins de risco médio e exigir MFA. Para as políticas do Utilizador, deve visar utilizadores de alto risco que exijam a alteração da palavra-passe. |
| Criar e ativar políticas de acesso condicional | [MFA para administradores para proteger contas que são atribuídas direitos administrativos.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Bloqueie protocolos de autenticação legado devido ao risco acrescido associado aos protocolos de autenticação do legado.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Exija mFA para a Azure Management proteger os seus recursos privilegiados, exigindo a autenticação de vários fatores para qualquer utilizador que aceda aos recursos do Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Ativar o Password Hash Sync](../hybrid/how-to-connect-password-hash-synchronization.md) (se utilizar identidades híbridas) | Fornecer redundância para autenticação e melhorar a segurança (incluindo Smart Lockout, IP Lockout, e a capacidade de descobrir credenciais vazadas.) |
| Ativar o [bloqueio inteligente ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (se aplicável) | Protege os seus utilizadores de experimentarem o bloqueio da conta extranet de atividades maliciosas. |
| Ativar o bloqueio inteligente do [Diretório Ativo Azure](../authentication/howto-password-smart-lockout.md) (se utilizar identidades geridas) | Smart lockout ajuda a bloquear maus atores que estão a tentar adivinhar as palavras-passe dos seus utilizadores ou usar métodos de força bruta para entrar. |
| [Desativar o consentimento do utilizador final para as aplicações](../manage-apps/configure-user-consent.md) | O fluxo de trabalho de consentimento do administrador dá aos administradores uma forma segura de conceder acesso a aplicações que requerem aprovação administrativa para que os utilizadores finais não exponham os dados corporativos. A Microsoft recomenda que desaperte futuras operações de consentimento dos utilizadores para ajudar a reduzir a sua área de superfície e mitigar este risco. |
| [Permitir o acesso remoto a aplicações antigas no local com procuração de aplicações](../manage-apps/application-proxy-add-on-premises-application.md) | Ative o Proxy de Aplicação aD Azure e integre-se com aplicações antigas para que os utilizadores acedam de forma segura às aplicações no local, inserindo-se com a sua conta Azure AD. |
| [Ativar o acesso híbrido Secure: Proteja as aplicações antigas com controladores e redes de entrega de aplicações existentes](../manage-apps/secure-hybrid-access.md) (se aplicável). | Publique e proteja as suas aplicações de autenticação no local e na nuvem, ligando-as à AD Azure com o seu controlador ou rede de entrega de aplicações existente. |
| [Integrar aplicações saaS suportadas da galeria para a AD Azure e permitir a inscrição única](../manage-apps/add-application-portal.md) | A Azure AD tem uma galeria que contém milhares de aplicações pré-integradas. Algumas das aplicações que a sua organização utiliza estão provavelmente na galeria acessível diretamente do portal Azure. Fornecer acesso a aplicações saaS corporativas remotamente e de forma segura com uma melhor experiência do utilizador (SSO). |
| [Automatizar o fornecimento e o desprovisionamento dos utilizadores a partir de aplicações SaaS](../app-provisioning/user-provisioning.md) (se aplicável) | Criar automaticamente identidades e funções de utilizador nas aplicações cloud (SaaS) a que os utilizadores precisam de ter acesso. Além de criar identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como situação ou mudança de funções, aumentando a segurança da sua organização. |
| [Ativar o acesso condicional – Baseado em Dispositivos](../conditional-access/require-managed-devices.md) | Melhorar as experiências de segurança e de utilização com acesso condicional baseado no dispositivo. Este passo garante que os utilizadores só podem aceder a partir de dispositivos que cumpram os seus padrões de segurança e conformidade. Estes dispositivos também são conhecidos como dispositivos geridos. Os dispositivos geridos podem ser dispositivos intune compatíveis ou Hybrid Azure AD. |
| [Ativar a proteção de passwords](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Proteja os utilizadores de usar palavras-passe fracas e fáceis de adivinhar. |
| [Designar mais do que um administrador global](../users-groups-roles/directory-emergency-access.md) | Atribuir pelo menos duas contas de administrador global permanente permanente sem nuvens para uso em caso de emergência. Estas contas não são utilizadas diariamente e devem ter senhas longas e complexas. Break Glass Accounts certifique-se de que pode aceder ao serviço em caso de emergência. |
| [Utilize, sempre que possível, funções administrativas não globais](../users-groups-roles/directory-assign-admin-roles.md) | Dê aos seus administradores apenas o acesso de que necessitam apenas para as áreas a que necessitam acesso. Nem todos os administradores precisam de ser administradores globais. |
| [Ativar a orientação de palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os utilizadores alterem a sua palavra-passe num horário definido, desativem os requisitos de complexidade e os seus utilizadores estão mais aptos a lembrar-se das suas palavras-passe e a mantê-las algo seguro. |
| [Criar um plano para o acesso ao utilizador convidado](../b2b/what-is-b2b.md) | Colabore com os utilizadores convidados, permitindo-lhes assinar nas suas apps e serviços com o seu próprio trabalho, escola ou identidades sociais. |
| [Ativar a Gestão de Identidade Privilegiada](../privileged-identity-management/pim-configure.md) | Permite-lhe gerir, controlar e monitorizar o acesso a recursos importantes na sua organização, garantindo que os administradores só têm acesso quando necessário e com aprovação |

## <a name="next-steps"></a>Passos seguintes

- Para obter orientações detalhadas de implementação para características individuais da AD Azure, reveja os planos de implementação do [projeto Azure AD](active-directory-deployment-plans.md).

- Para uma lista de verificação de implementação de admissão azure de ponta a ponta, consulte o guia de implementação de [funcionalidades azure Ative Directory](active-directory-deployment-checklist-p2.md)
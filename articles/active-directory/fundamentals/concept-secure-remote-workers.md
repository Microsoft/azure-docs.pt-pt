---
title: Responda rapidamente a identidades seguras com o Azure Ative Directory
description: Responda rapidamente a ameaças com identidades baseadas em nuvem Azure AD
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
ms.openlocfilehash: 43b883cac7b970488a30116bc06efc8663766629
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370207"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Responder rapidamente para proteger identidades com o Azure AD

Pode parecer assustador tentar proteger os seus trabalhadores no mundo de hoje, especialmente quando você tem que responder rapidamente e fornecer acesso a muitos serviços rapidamente. Este artigo destina-se a fornecer uma lista concisa de todas as ações a tomar, ajudando-o a identificar e priorizar qual a ordem para implementar as funcionalidades AZURE AD com base no tipo de licença que possui. O Azure AD oferece muitas funcionalidades e fornece muitas camadas de segurança para as suas identidades, navegar qual a característica relevante pode por vezes ser esmagadora. Muitas organizações já estão na nuvem ou movendo-se rapidamente para a nuvem, este documento destina-se a permitir que você implemente serviços rapidamente, garantindo as suas identidades como a principal consideração. 

Cada tabela fornece uma recomendação de segurança consistente, protegendo as identidades do Administrador e do Utilizador dos principais ataques de segurança (repetição de violação, phishing e spray de palavra-passe) ao mesmo tempo que minimiza o impacto do utilizador e melhora a experiência do utilizador. 

A orientação também permitirá aos administradores configurar o acesso às aplicações saaS e no local de forma segura e protegida e é aplicável a identidades em nuvem ou híbrida (sincronizada) e aplica-se aos utilizadores que trabalham remotamente ou no escritório.

Esta lista de verificação irá ajudá-lo a implementar rapidamente ações recomendadas críticas para proteger a sua organização imediatamente, explicando como:

- Fortaleça as suas credenciais.
- Reduza a área da superfície do ataque.
- Automatizar a resposta da ameaça.
- Utilize a inteligência das nuvens.
- Ativar o autosserviço do utilizador final.

## <a name="prerequisites"></a>Pré-requisitos

Este guia pressupõe que a sua nuvem apenas ou identidades híbridas já foram estabelecidas em Azure AD. Para ajudar na escolha do seu tipo de identidade consulte o artigo, [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](../hybrid/choose-ad-authn.md) 

## <a name="summary"></a>Resumo

Existem muitos aspetos para uma infraestrutura de identidade segura, mas esta lista de verificação centra-se numa infraestrutura de identidade segura e segura que permite aos utilizadores trabalhar remotamente. Garantir a sua identidade é apenas uma parte da sua história de segurança, protegendo dados, aplicações e dispositivos também devem ser considerados.

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Orientação para clientes Azure AD Free, Office 365 ou Microsoft 365.

Há uma série de recomendações que os clientes da Azure AD Free, Office 365 ou Microsoft 365 devem tomar para proteger as suas identidades de utilizador, a tabela que se segue destina-se a destacar as ações-chave para as seguintes subscrições de licença:

- Escritório 365 (Escritório 365 E1, E3, E5, F1, A1, A3, A5)
- Microsoft 365 (Business Basic, Apps for Business, Business Standard, Business Premium, A1)
- Azure AD Free (incluído com Azure, Dynamics 365, Intune e Power Platform)

| Ação recomendada | Detalhes |
| --- | --- |
| [Ativar incumprimentos de segurança](concept-fundamentals-security-defaults.md) | Proteja todas as identidades e aplicações dos utilizadores, permitindo a autenticação de legados MFA e bloqueando a autenticação de legados |
| [Ativar o Password Hash Sync](../hybrid/how-to-connect-password-hash-synchronization.md) (se utilizar identidades híbridas) | Fornecer redundância para a autenticação e melhorar a segurança (incluindo Smart Lockout, IP Lockout, e a capacidade de descobrir credenciais vazadas.) |
| [Ativar o bloqueio inteligente ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (Se aplicável) | Protege os seus utilizadores de experimentarem o bloqueio da conta extranet de atividades maliciosas. |
| [Ativar o bloqueio inteligente do Azure Ative Directory](../authentication/howto-password-smart-lockout.md) (se utilizar identidades geridas) | O bloqueio inteligente ajuda a bloquear os maus atores que estão a tentar adivinhar as palavras-passe dos seus utilizadores ou a usar métodos de força bruta para entrar. |
| [Desativar o consentimento do utilizador final para aplicações](../manage-apps/configure-user-consent.md) | O fluxo de trabalho de consentimento administrativo dá aos administradores uma forma segura de conceder acesso a aplicações que requerem aprovação de administração para que os utilizadores finais não exponham os dados corporativos. A Microsoft recomenda a desativação de futuras operações de consentimento do utilizador para ajudar a reduzir a sua área de superfície e a mitigar este risco. |
| [Integre aplicações de SaaS suportadas da galeria para Azure AD e ativar o único sinal em](../manage-apps/add-application-portal.md) | A Azure AD tem uma galeria que contém milhares de aplicações pré-integradas. Algumas das aplicações que a sua organização utiliza provavelmente estão na galeria acessível diretamente a partir do portal Azure. Fornecer acesso a aplicações Corporativas SaaS remotamente e de forma segura com uma melhor experiência do utilizador (SSO) |
| [Automatizar o fornecimento e desprovisionamento dos utilizadores a partir de aplicações saas](../app-provisioning/user-provisioning.md) (se aplicável) | Crie automaticamente identidades e funções de utilizador nas aplicações cloud (SaaS) às que os utilizadores precisam de acesso. Além de criar identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como alteração de estatuto ou de funções, aumentando a segurança da sua organização. |
| [Ativar o acesso híbrido Secure: Proteja as aplicações antigas com controladores e redes de entrega de aplicações existentes](../manage-apps/secure-hybrid-access.md) (se aplicável) | Publique e proteja as suas aplicações de autenticação de legados em nuvem, ligando-as à Azure AD com o controlador ou rede de entrega de aplicações existente. |
| [Ativar o reset da palavra-passe de autosserviço](../authentication/tutorial-enable-sspr.md) (aplicável apenas às contas na nuvem) | Esta capacidade reduz as chamadas de secretária de ajuda e a perda de produtividade quando um utilizador não pode assinar no seu dispositivo ou numa aplicação. |
| [Utilizar funções administrativas não globais sempre que possível](../roles/permissions-reference.md) | Dê aos seus administradores apenas o acesso que precisam para apenas as áreas a que precisam de acesso. Nem todos os administradores precisam de ser administradores globais. |
| [Ativar a orientação da palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os utilizadores alterem a sua palavra-passe num horário definido, desativem os requisitos de complexidade e os seus utilizadores estão mais aptos a lembrarem-se das suas palavras-passe e a mantê-las em algo seguro. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Orientação para clientes Azure AD Premium Plan 1.

O quadro a seguir destina-se a destacar as ações-chave para as seguintes subscrições de licença:

- Azure Ative Directory Premium P1 (Azure AD P1)
- Mobilidade Empresarial + Segurança (EMS E3)
- Microsoft 365 (M365 E3, A3, F1, F3)

| Ação recomendada | Detalhes |
| --- | --- |
| [Permitir experiência de registo combinado para Azure MFA e SSPR para simplificar a experiência de registo do utilizador](../authentication/howto-registration-mfa-sspr-combined.md) | Deixe que os seus utilizadores se registem a partir de uma experiência comum para a autenticação multi-factor Azure e para o reset da palavra-passe de autosserviço. |
| [Configurar configurações de MFA para a sua organização](../authentication/howto-mfa-getstarted.md) | Garantir que as contas estão protegidas contra serem comprometidas com a autenticação de vários fatores |
| [Ativar a reposição de palavras-passe self-service](../authentication/tutorial-enable-sspr.md) | Esta capacidade reduz as chamadas de secretária de ajuda e a perda de produtividade quando um utilizador não pode assinar no seu dispositivo ou numa aplicação |
| [Implementar o Writeback de Palavra-Passe](../authentication/tutorial-enable-sspr-writeback.md) (se utilizar identidades híbridas) | Permitir que as alterações de palavra-passe na nuvem sejam escritas de volta para um ambiente de Diretório Ativo do Windows Server no local. |
| Criar e ativar políticas de acesso condicional | [MFA para administradores para proteger contas que são atribuídos direitos administrativos.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Bloqueie os protocolos de autenticação de legados devido ao risco acrescido associado aos protocolos de autenticação de legados.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [MFA para todos os utilizadores e aplicações para criar uma política de MFA equilibrada para o seu ambiente, garantindo os seus utilizadores e aplicações.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Exija que a MFA for Azure Management proteja os seus recursos privilegiados, exigindo autenticação multi-factor para qualquer utilizador que aceda aos recursos do Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Ativar o Password Hash Sync](../hybrid/how-to-connect-password-hash-synchronization.md) (se utilizar identidades híbridas) | Fornecer redundância para a autenticação e melhorar a segurança (incluindo Smart Lockout, IP Lockout, e a capacidade de descobrir credenciais vazadas.) |
| [Ativar o bloqueio inteligente ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (Se aplicável) | Protege os seus utilizadores de experimentarem o bloqueio da conta extranet de atividades maliciosas. |
| [Ativar o bloqueio inteligente do Azure Ative Directory](../authentication/howto-password-smart-lockout.md) (se utilizar identidades geridas) | O bloqueio inteligente ajuda a bloquear os maus atores que estão a tentar adivinhar as palavras-passe dos seus utilizadores ou a usar métodos de força bruta para entrar. |
| [Desativar o consentimento do utilizador final para aplicações](../manage-apps/configure-user-consent.md) | O fluxo de trabalho de consentimento administrativo dá aos administradores uma forma segura de conceder acesso a aplicações que requerem aprovação de administração para que os utilizadores finais não exponham os dados corporativos. A Microsoft recomenda a desativação de futuras operações de consentimento do utilizador para ajudar a reduzir a sua área de superfície e a mitigar este risco. |
| [Permitir o acesso remoto a aplicações antigas no local com Aplicação Proxy](../manage-apps/application-proxy-add-on-premises-application.md) | Ativar o Proxy da Aplicação AD Azure e integrar-se com aplicações antigas para os utilizadores acederem de forma segura às aplicações no local, insinundo-se com a sua conta Azure AD. |
| [Ativar o acesso híbrido Secure: Proteja as aplicações antigas com controladores e redes de entrega de aplicações existentes](../manage-apps/secure-hybrid-access.md) (se aplicável). | Publique e proteja as suas aplicações de autenticação de legados em nuvem, ligando-as à Azure AD com o controlador ou rede de entrega de aplicações existente. |
| [Integre aplicações de SaaS suportadas da galeria para Azure AD e ativar o único sinal em](../manage-apps/add-application-portal.md) | A Azure AD tem uma galeria que contém milhares de aplicações pré-integradas. Algumas das aplicações que a sua organização utiliza provavelmente estão na galeria acessível diretamente a partir do portal Azure. Fornecer acesso a aplicações Corporativas SaaS remotamente e de forma segura com uma melhor experiência do utilizador (SSO). |
| [Automatizar o fornecimento e desprovisionamento dos utilizadores a partir de aplicações saas](../app-provisioning/user-provisioning.md) (se aplicável) | Crie automaticamente identidades e funções de utilizador nas aplicações cloud (SaaS) às que os utilizadores precisam de acesso. Além de criar identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como alteração de estatuto ou de funções, aumentando a segurança da sua organização. |
| [Ativar acesso condicional – Baseado em Dispositivo](../conditional-access/require-managed-devices.md) | Melhorar a segurança e as experiências do utilizador com o Acesso Condicional baseado no dispositivo. Este passo garante que os utilizadores só podem aceder a partir de dispositivos que cumpram os seus padrões de segurança e conformidade. Estes dispositivos também são conhecidos como dispositivos geridos. Os dispositivos geridos podem ser dispositivos compatíveis com o Intune ou a AD Híbrido Azure. |
| [Ativar a proteção de palavras-passe](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Proteja os utilizadores de usar palavras-passe fracas e fáceis de adivinhar. |
| [Designar mais do que um administrador global](../roles/security-emergency-access.md) | Atribua pelo menos duas contas de administrador global permanente apenas na nuvem para utilização em caso de emergência. Estas contas não são usadas diariamente e devem ter senhas longas e complexas. As Contas Break Glass garantem que pode aceder ao serviço em caso de emergência. |
| [Utilizar funções administrativas não globais sempre que possível](../roles/permissions-reference.md) | Dê aos seus administradores apenas o acesso que precisam para apenas as áreas a que precisam de acesso. Nem todos os administradores precisam de ser administradores globais. |
| [Ativar a orientação da palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os utilizadores alterem a sua palavra-passe num horário definido, desativem os requisitos de complexidade e os seus utilizadores estão mais aptos a lembrarem-se das suas palavras-passe e a mantê-las em algo seguro. |
| [Crie um plano para o acesso do utilizador convidado](../external-identities/what-is-b2b.md) | Colabore com os utilizadores convidados, permitindo-lhes assinar nas suas apps e serviços com o seu próprio trabalho, escola ou identidades sociais. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Orientação para clientes Azure AD Premium Plan 2.

O quadro a seguir destina-se a destacar as ações-chave para as seguintes subscrições de licença:

- Azure Ative Directory Premium P2 (Azure AD P2)
- Mobilidade Empresarial + Segurança (EMS E5)
- Microsoft 365 (M365 E5, A5)

| Ação recomendada | Detalhes |
| --- | --- |
| [Permitir experiência de registo combinado para Azure MFA e SSPR para simplificar a experiência de registo do utilizador](../authentication/howto-registration-mfa-sspr-combined.md) | Deixe que os seus utilizadores se registem a partir de uma experiência comum para a autenticação multi-factor Azure e para o reset da palavra-passe de autosserviço. |
| [Configurar configurações de MFA para a sua organização](../authentication/howto-mfa-getstarted.md) | Garantir que as contas estão protegidas contra serem comprometidas com a autenticação de vários fatores |
| [Ativar a reposição de palavras-passe self-service](../authentication/tutorial-enable-sspr.md) | Esta capacidade reduz as chamadas de secretária de ajuda e a perda de produtividade quando um utilizador não pode assinar no seu dispositivo ou numa aplicação |
| [Implementar o Writeback de Palavra-Passe](../authentication/tutorial-enable-sspr-writeback.md) (se utilizar identidades híbridas) | Permitir que as alterações de palavra-passe na nuvem sejam escritas de volta para um ambiente de Diretório Ativo do Windows Server no local. |
| [Permitir políticas de proteção de identidade para impor o registo de MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Gerir o roll-out da Autenticação Multi-Factor Azure (MFA). |
| [Ativar as políticas de risco de proteção de identidade e de inscrição](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Ativar as políticas de Utilização de Identidade e Desinsusitada. A política de inscrição recomendada destina-se a inscrições de risco médio e requer MFA. Para as políticas do Utilizador, deve direcionar os utilizadores de alto risco que necessitem da ação de alteração da palavra-passe. |
| Criar e ativar políticas de acesso condicional | [MFA para administradores para proteger contas que são atribuídos direitos administrativos.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Bloqueie os protocolos de autenticação de legados devido ao risco acrescido associado aos protocolos de autenticação de legados.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Exija que a MFA for Azure Management proteja os seus recursos privilegiados, exigindo autenticação multi-factor para qualquer utilizador que aceda aos recursos do Azure.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Ativar o Password Hash Sync](../hybrid/how-to-connect-password-hash-synchronization.md) (se utilizar identidades híbridas) | Fornecer redundância para a autenticação e melhorar a segurança (incluindo Smart Lockout, IP Lockout, e a capacidade de descobrir credenciais vazadas.) |
| [Ativar o bloqueio inteligente ADFS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (Se aplicável) | Protege os seus utilizadores de experimentarem o bloqueio da conta extranet de atividades maliciosas. |
| [Ativar o bloqueio inteligente do Azure Ative Directory](../authentication/howto-password-smart-lockout.md) (se utilizar identidades geridas) | O bloqueio inteligente ajuda a bloquear os maus atores que estão a tentar adivinhar as palavras-passe dos seus utilizadores ou a usar métodos de força bruta para entrar. |
| [Desativar o consentimento do utilizador final para aplicações](../manage-apps/configure-user-consent.md) | O fluxo de trabalho de consentimento administrativo dá aos administradores uma forma segura de conceder acesso a aplicações que requerem aprovação de administração para que os utilizadores finais não exponham os dados corporativos. A Microsoft recomenda a desativação de futuras operações de consentimento do utilizador para ajudar a reduzir a sua área de superfície e a mitigar este risco. |
| [Permitir o acesso remoto a aplicações antigas no local com Aplicação Proxy](../manage-apps/application-proxy-add-on-premises-application.md) | Ativar o Proxy da Aplicação AD Azure e integrar-se com aplicações antigas para os utilizadores acederem de forma segura às aplicações no local, insinundo-se com a sua conta Azure AD. |
| [Ativar o acesso híbrido Secure: Proteja as aplicações antigas com controladores e redes de entrega de aplicações existentes](../manage-apps/secure-hybrid-access.md) (se aplicável). | Publique e proteja as suas aplicações de autenticação de legados em nuvem, ligando-as à Azure AD com o controlador ou rede de entrega de aplicações existente. |
| [Integre aplicações de SaaS suportadas da galeria para Azure AD e ativar o único sinal em](../manage-apps/add-application-portal.md) | A Azure AD tem uma galeria que contém milhares de aplicações pré-integradas. Algumas das aplicações que a sua organização utiliza provavelmente estão na galeria acessível diretamente a partir do portal Azure. Fornecer acesso a aplicações Corporativas SaaS remotamente e de forma segura com uma melhor experiência do utilizador (SSO). |
| [Automatizar o fornecimento e desprovisionamento dos utilizadores a partir de aplicações saas](../app-provisioning/user-provisioning.md) (se aplicável) | Crie automaticamente identidades e funções de utilizador nas aplicações cloud (SaaS) às que os utilizadores precisam de acesso. Além de criar identidades de utilizador, o fornecimento automático inclui a manutenção e remoção das identidades dos utilizadores como alteração de estatuto ou de funções, aumentando a segurança da sua organização. |
| [Ativar acesso condicional – Baseado em Dispositivo](../conditional-access/require-managed-devices.md) | Melhorar a segurança e as experiências do utilizador com o Acesso Condicional baseado no dispositivo. Este passo garante que os utilizadores só podem aceder a partir de dispositivos que cumpram os seus padrões de segurança e conformidade. Estes dispositivos também são conhecidos como dispositivos geridos. Os dispositivos geridos podem ser dispositivos compatíveis com o Intune ou a AD Híbrido Azure. |
| [Ativar a proteção de palavras-passe](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Proteja os utilizadores de usar palavras-passe fracas e fáceis de adivinhar. |
| [Designar mais do que um administrador global](../roles/security-emergency-access.md) | Atribua pelo menos duas contas de administrador global permanente apenas na nuvem para utilização em caso de emergência. Estas contas não são usadas diariamente e devem ter senhas longas e complexas. As Contas Break Glass garantem que pode aceder ao serviço em caso de emergência. |
| [Utilizar funções administrativas não globais sempre que possível](../roles/permissions-reference.md) | Dê aos seus administradores apenas o acesso que precisam para apenas as áreas a que precisam de acesso. Nem todos os administradores precisam de ser administradores globais. |
| [Ativar a orientação da palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os utilizadores alterem a sua palavra-passe num horário definido, desativem os requisitos de complexidade e os seus utilizadores estão mais aptos a lembrarem-se das suas palavras-passe e a mantê-las em algo seguro. |
| [Crie um plano para o acesso do utilizador convidado](../external-identities/what-is-b2b.md) | Colabore com os utilizadores convidados, permitindo-lhes assinar nas suas apps e serviços com o seu próprio trabalho, escola ou identidades sociais. |
| [Ativar a Gestão de Identidade Privilegiada](../privileged-identity-management/pim-configure.md) | Permite-lhe gerir, controlar e monitorizar o acesso a recursos importantes na sua organização, garantindo que os administradores têm acesso apenas quando necessário e com aprovação |

## <a name="next-steps"></a>Passos seguintes

- Para obter orientações detalhadas de implementação para características individuais do Azure AD, reveja os [planos de implantação do projeto Azure AD](active-directory-deployment-plans.md).

- Para obter uma lista de verificação de implementação Azure AD de ponta a ponta, consulte o guia de implementação de [funcionalidades do Azure Ative Directory](active-directory-deployment-checklist-p2.md)
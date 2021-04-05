---
title: Lista de verificação de Implementação do Azure AD
description: Lista de verificação de funcionalidades do Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92d1e5b8ac6492b0b1d819431e4616d32a092cc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836925"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Guia de implementação de funcionalidades do Azure Active Directory

Pode parecer assustador implantar o Azure Ative Directory (Azure AD) para a sua organização e mantê-lo seguro. Este artigo identifica tarefas comuns que os clientes consideram úteis para completar em fases, ao longo de 30, 60, 90 dias, ou mais, para melhorar a sua postura de segurança. Mesmo as organizações que já implementaram a Azure AD podem usar este guia para garantir que estão a tirar o máximo partido do seu investimento.

Uma infraestrutura de identidade bem planeada e executada abre caminho para um acesso seguro às suas cargas de trabalho e dados de produtividade apenas por utilizadores e dispositivos conhecidos.

Além disso, os clientes podem verificar a sua [pontuação de identidade segura](identity-secure-score.md) para ver como estão alinhados com as melhores práticas da Microsoft. Verifique a sua pontuação segura antes e depois de implementar estas recomendações para ver como está bem comparado com outros da sua indústria e com outras organizações do seu tamanho.

## <a name="prerequisites"></a>Pré-requisitos

Muitas das recomendações deste guia podem ser implementadas com Azure AD Free ou nenhuma licença. Quando são necessárias licenças, indicamos qual a licença necessária no mínimo para realizar a tarefa.

Informações adicionais sobre o licenciamento podem ser encontradas nas seguintes páginas:

* [Licenciamento Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Empresarial](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Preços das identidades externas da Azure AD](../external-identities/external-identities-pricing.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fase 1: Construir uma base de segurança

Nesta fase, os administradores permitem funcionalidades de segurança de base para criar uma fundação mais segura e fácil de usar em Azure AD antes de importar ou criar contas normais de utilizador. Esta fase fundamental garante que está num estado mais seguro desde o início e que os seus utilizadores finais só têm de ser introduzidos em novos conceitos uma vez.

| Tarefa | Detalhes | Licença requerida |
| ---- | ------ | ---------------- |
| [Designar mais do que um administrador global](../roles/security-emergency-access.md) | Atribua pelo menos duas contas de administrador global permanente apenas na nuvem para utilização em caso de emergência. Estas contas não são usadas diariamente e devem ter senhas longas e complexas. | Azure AD Gratuito |
| [Utilizar funções administrativas não globais sempre que possível](../roles/permissions-reference.md) | Dê aos seus administradores apenas o acesso que precisam para apenas as áreas a que precisam de acesso. Nem todos os administradores precisam de ser administradores globais. | Azure AD Gratuito |
| [Ativar a Gestão de Identidade Privilegiada para acompanhar o uso de funções de administração](../privileged-identity-management/pim-getting-started.md) | Capacitar a Gestão de Identidade Privilegiada para começar a rastrear o uso de funções administrativas. | Azure AD Premium P2 |
| [Implementar a reposição personalizada de palavra-passe](../authentication/howto-sspr-deployment.md) | Reduza as chamadas helpdesk para resets de palavra-passe, permitindo que o pessoal repõe as suas próprias palavras-passe usando políticas que você como um controlo de administrador. | |
| [Crie uma lista de senhas personalizadas personalizadas de organização](../authentication/tutorial-configure-custom-password-protection.md) | Impedir que os utilizadores criem senhas que incluam palavras ou frases comuns da sua organização ou área. | |
| [Permitir a integração no local com a proteção de senha AZure AD](../authentication/concept-password-ban-bad-on-premises.md) | Estenda a lista de senhas proibidas para o seu diretório no local, para garantir que as palavras-passe definidas no local também estão em conformidade com as listas de senhas proibidas global e específicas do inquilino. | Azure AD Premium P1 |
| [Ativar a orientação da palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os utilizadores alterem a sua palavra-passe num horário definido, desativem os requisitos de complexidade e os seus utilizadores estão mais aptos a lembrarem-se das suas palavras-passe e a mantê-las em algo seguro. | Azure AD Gratuito |
| [Desativar resets periódicos de palavra-passe para contas de utilizador baseadas na nuvem](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | As reposições periódicas de palavra-passe incentivam os seus utilizadores a incrementar as suas palavras-passe existentes. Utilize as diretrizes no doc de orientação de passwords da Microsoft e espelhe a sua política no local para utilizadores apenas na nuvem. | Azure AD Gratuito |
| [Personalize o bloqueio inteligente do Azure Ative Directory](../authentication/howto-password-smart-lockout.md) | Impedir que os bloqueios de utilizadores baseados na nuvem sejam replicados para utilizadores do Ative Directory | |
| [Ativar o bloqueio inteligente da extranet para FS AD](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | O bloqueio da extranet AD FS protege contra ataques de adivinhação de senha de força bruta, enquanto permite que utilizadores válidos de FS AD continuem a usar as suas contas. | |
| [Bloquear a autenticação do legado para Azure AD com acesso condicional](../conditional-access/block-legacy-authentication.md) | Bloqueie protocolos de autenticação de legados como POP, SMTP, IMAP e MAPI que não podem impor a Autenticação Multi-Factor, tornando-os um ponto de entrada preferido para os adversários. | Azure AD Premium P1 |
| [Implementar autenticação multi-factor Azure AD utilizando políticas de acesso condicional](../authentication/howto-mfa-getstarted.md) | Exija que os utilizadores realizem uma verificação em duas etapas ao aceder a aplicações sensíveis utilizando políticas de Acesso Condicional. | Azure AD Premium P1 |
| [Ativar a proteção de identidade do diretório ativo Azure](../identity-protection/overview-identity-protection.md) | Ativar o rastreio de insusões de risco e credenciais comprometidas para os utilizadores da sua organização. | Azure AD Premium P2 |
| [Utilize deteções de risco para desencadear alterações na autenticação de vários fatores e nas alterações da palavra-passe](../authentication/tutorial-risk-based-sspr-mfa.md) | Ativar a automatização que pode desencadear eventos como autenticação de vários fatores, reset de palavra-passe e bloqueio de ins-ins com base no risco. | Azure AD Premium P2 |
| [Ativar o registo combinado para reset de senha de autosserviço e autenticação multi-factor Azure AD](../authentication/concept-registration-mfa-sspr-combined.md) | Deixe que os seus utilizadores se registem a partir de uma experiência comum para a autenticação multi-factor Azure AD e para o reset da palavra-passe de autosserviço. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fase 2: Importar utilizadores, permitir sincronização e gerir dispositivos

Em seguida, adicionamos à fundação estabelecida na fase 1, importando os nossos utilizadores e permitindo a sincronização, planejando o acesso dos hóspedes, e preparando-se para apoiar funcionalidades adicionais.

| Tarefa | Detalhes | Licença requerida |
| ---- | ------ | ---------------- |
| [Instalar o Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md) | Prepare-se para sincronizar os utilizadores do seu diretório existente no local até à nuvem. | Azure AD Gratuito |
| [Implementar sincronização de hash de palavra-passe](../hybrid/how-to-connect-password-hash-synchronization.md) | Sincronizar os hashes de palavra-passe para permitir que as alterações de palavras-passe sejam replicadas, a deteção e reparação de palavras-passe erradas e a fuga de relatórios de credenciais. | Azure AD Gratuito |
| [Implementar writeback de palavra-passe](../authentication/tutorial-enable-sspr-writeback.md) | Permitir que as alterações de palavra-passe na nuvem sejam escritas de volta para um ambiente de Diretório Ativo do Windows Server no local. | Azure AD Premium P1 |
| [Implementar Azure Ad Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) | Ativar a monitorização das principais estatísticas de saúde dos seus servidores AD Connect Azure, servidores AD FS e controladores de domínio. | Azure AD Premium P1 |
| [Atribuir licenças aos utilizadores por membro do grupo no Azure Ative Directory](../enterprise-users/licensing-groups-assign.md) | Economize tempo e esforço criando grupos de licenciamento que permitem ou desativam funcionalidades por grupo em vez de definir por utilizador. | |
| [Crie um plano para o acesso do utilizador convidado](../external-identities/what-is-b2b.md) | Colabore com os utilizadores convidados, deixando-os entrar nas suas apps e serviços com o seu próprio trabalho, escola ou identidades sociais. | [Preços das identidades externas da Azure AD](../external-identities/external-identities-pricing.md) |
| [Decida sobre a estratégia de gestão de dispositivos](../devices/overview.md) | Decida o que a sua organização permite em relação aos dispositivos. Registrando vs junção, Bring Your Own Device vs empresa fornecido. | |
| [Implementar o Windows Hello for Business na sua organização](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Prepare-se para autenticação sem palavras-passe usando o Windows Hello | |
| [Implementar métodos de autenticação sem palavras-passe para os seus utilizadores](../authentication/concept-authentication-passwordless.md) | Forneça aos seus utilizadores métodos convenientes de autenticação sem palavras-passe | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>Fase 3: Gerir aplicações

À medida que continuamos a construir nas fases anteriores, identificamos candidaturas candidatas para migração e integração com a AZure AD e completamos a configuração dessas aplicações.

| Tarefa | Detalhes | Licença requerida |
| ---- | ------ | ---------------- |
| Identifique as suas aplicações | Identifique aplicações em uso na sua organização: no local, aplicações SaaS na nuvem e outras aplicações de linha de negócio. Determine se estas aplicações podem e devem ser geridas com Azure AD. | Sem licença necessária |
| [Integrar candidaturas de SaaS apoiadas na galeria](../manage-apps/add-application-portal.md) | A Azure AD tem uma galeria que contém milhares de aplicações pré-integradas. Algumas das aplicações que a sua organização utiliza provavelmente estão na galeria acessível diretamente a partir do portal Azure. | Azure AD Gratuito |
| [Utilizar aplicações Proxy para integrar aplicações no local](../manage-apps/application-proxy-add-on-premises-application.md) | O Application Proxy permite que os utilizadores acedam a aplicações no local, insinundo-se na sua conta Azure AD. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fase 4: Auditar identidades privilegiadas, completar uma revisão de acesso e gerir o ciclo de vida do utilizador

A fase 4 vê os administradores a impor princípios de privilégios mínimos para a administração, a completar as suas primeiras revisões de acesso e a permitir a automatização de tarefas comuns do ciclo de vida do utilizador.

| Tarefa | Detalhes | Licença requerida |
| ---- | ------ | ---------------- |
| [Impor o uso da Gestão de Identidade Privilegiada](../privileged-identity-management/pim-security-wizard.md) | Remova as funções administrativas das contas normais do dia-a-dia do utilizador. Tornar os utilizadores administrativos elegíveis para utilizarem o seu papel após uma verificação de autenticação multi-factor, fornecendo uma justificação do negócio ou solicitando a aprovação dos aprovadores designados. | Azure AD Premium P2 |
| [Complete uma revisão de acesso para funções de diretório AZure AD em PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Trabalhe com as suas equipas de segurança e liderança para criar uma política de revisão de acesso para rever o acesso administrativo com base nas políticas da sua organização. | Azure AD Premium P2 |
| [Implementar políticas dinâmicas de adesão ao grupo](../enterprise-users/groups-dynamic-membership.md) | Utilize grupos dinâmicos para atribuir automaticamente os utilizadores a grupos baseados nos seus atributos de RH (ou na sua fonte de verdade), tais como departamento, título, região e outros atributos. |  |
| [Implementar provisão de aplicações baseadas em grupo](../manage-apps/what-is-access-management.md) | Utilize o fornecimento de gestão de acesso baseado em grupo para provisão automática de utilizadores para aplicações SaaS. |  |
| [Automatizar o fornecimento e desprovisionamento dos utilizadores](../app-provisioning/user-provisioning.md) | Remova os passos manuais do ciclo de vida da conta do seu funcionário para evitar o acesso não autorizado. Sincronizar identidades da sua fonte de verdade (Sistema HR) para Azure AD. |  |

## <a name="next-steps"></a>Passos seguintes

[Detalhes de licenciamento e preços da AZure AD](https://azure.microsoft.com/pricing/details/active-directory/)

[Configurações de acesso aos dispositivos e identidade](/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Políticas comuns de identidade recomendada e de acesso ao dispositivo](/microsoft-365/enterprise/identity-access-policies)
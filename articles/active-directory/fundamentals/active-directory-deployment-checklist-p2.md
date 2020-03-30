---
title: Lista de verificação de Implementação do Azure AD
description: Lista de verificação de funcionalidades de implementação de funcionalidades Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: f84226a631014b51338d47887fe3bafc969dc571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063650"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Guia de implantação de funcionalidades azure ative diretório

Pode parecer assustador implementar o Azure Ative Directory (Azure AD) para a sua organização e mantê-lo seguro. Este artigo identifica tarefas comuns que os clientes consideram úteis para completar em fases, ao longo de 30, 60, 90 dias, ou mais, para melhorar a sua postura de segurança. Mesmo as organizações que já implementaram a Azure AD podem usar este guia para garantir que estão a tirar o máximo partido do seu investimento.

Uma infraestrutura de identidade bem planeada e executada abre caminho para um acesso seguro às suas cargas de trabalho e dados de produtividade apenas por utilizadores e dispositivos conhecidos.

Além disso, os clientes podem verificar a sua [pontuação segura](identity-secure-score.md) de identidade para ver quão alinhados estão com as melhores práticas da Microsoft. Verifique a sua pontuação segura antes e depois de implementar estas recomendações para ver o quão bem está em comparação com outras na sua indústria e com outras organizações do seu tamanho.

## <a name="prerequisites"></a>Pré-requisitos

Muitas das recomendações deste guia podem ser implementadas com a Azure AD Free ou nenhuma licença. Quando forem necessárias licenças, afirmamos qual a licença exigida no mínimo para realizar a tarefa.

Informações adicionais sobre o licenciamento podem ser encontradas nas seguintes páginas:

* [Licenciamento Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Orientação de licenciamento Azure AD B2B](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fase 1: Construir uma base de segurança

Nesta fase, os administradores permitem que as funcionalidades de segurança de base criem uma fundação mais segura e fácil de usar em Azure AD antes de importarmos ou criarmos contas de utilizador normais. Esta fase fundacional garante que está num estado mais seguro desde o início e que os seus utilizadores finais só têm de ser introduzidos em novos conceitos uma vez.

| Tarefa | Detalhe | Licença obrigatória |
| ---- | ------ | ---------------- |
| [Designar mais do que um administrador global](../users-groups-roles/directory-emergency-access.md) | Atribuir pelo menos duas contas de administrador global permanente permanente sem nuvens para uso em caso de emergência. Estas contas não são utilizadas diariamente e devem ter senhas longas e complexas. | Azure AD Gratuito |
| [Utilize, sempre que possível, funções administrativas não globais](../users-groups-roles/directory-assign-admin-roles.md) | Dê aos seus administradores apenas o acesso de que necessitam apenas para as áreas a que necessitam acesso. Nem todos os administradores precisam de ser administradores globais. | Azure AD Gratuito |
| [Permitir a Gestão de Identidade Privilegiada para rastrear a utilização de funções de administrador](../privileged-identity-management/pim-getting-started.md) | Permitir que a Gestão de Identidade Privilegiada comece a rastrear o uso de funções administrativas. | Azure AD Premium P2 |
| [Implementar a reposição personalizada de palavra-passe](../authentication/howto-sspr-deployment.md) | Reduza as chamadas de helpdesk para resets de palavra-passe, permitindo que o pessoal redefinir as suas próprias palavras-passe usando políticas que você como um controlo de administrador. | |
| [Criar uma lista de senhas proibidas específicas da organização](../authentication/howto-password-ban-bad-configure.md) | Evite que os utilizadores criem senhas que incluam palavras ou frases comuns da sua organização ou área. | |
| [Ativar a integração no local com a proteção de senhas Azure AD](../authentication/concept-password-ban-bad-on-premises.md) | Alarga a lista de palavras-passe proibida seleções ao seu diretório no local, para garantir que as palavras-passe definidas no local também estão em conformidade com as listas de senhas proibidas específicas do global e do arrendatário. | Azure AD Premium P1 |
| [Ativar a orientação de palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Pare de exigir que os utilizadores alterem a sua palavra-passe num horário definido, desativem os requisitos de complexidade e os seus utilizadores estão mais aptos a lembrar-se das suas palavras-passe e a mantê-las algo seguro. | Azure AD Gratuito |
| [Desativar resets periódicos de palavra-passe para contas de utilizadores baseadas na nuvem](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Resets periódicos de palavra-passe incentivam os seus utilizadores a incrementar as suas senhas existentes. Utilize as diretrizes no doc de orientação de palavras-passe da Microsoft e espelhe a sua política no local para utilizadores apenas na nuvem. | Azure AD Gratuito |
| [Personalize o bloqueio inteligente do Diretório Ativo Azure](../authentication/howto-password-smart-lockout.md) | Impedir que os bloqueios de utilizadores baseados na nuvem sejam replicados para utilizadores de Directórioactivo no local | |
| [Ativar o bloqueio inteligente extranet para AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | O bloqueio extranet AD FS protege contra ataques de adivinhação de senhas de força bruta, enquanto permite que os utilizadores de AD FS válidos continuem a usar as suas contas. | |
| [Implementar autenticação multi-factor Azure AD utilizando políticas de acesso condicional](../authentication/howto-mfa-getstarted.md) | Exigir que os utilizadores realizem uma verificação em duas etapas ao aceder a aplicações sensíveis utilizando políticas de Acesso Condicional. | Azure AD Premium P1 |
| [Ativar a Proteção de Identidade de Diretório Ativo azure](../identity-protection/overview-identity-protection.md) | Ative o rastreio de inscrições arriscadas e credenciais comprometidas para os utilizadores da sua organização. | Azure AD Premium P2 |
| [Utilize deteções de risco para desencadear a autenticação de vários fatores e alterações de palavra-passe](../authentication/tutorial-risk-based-sspr-mfa.md) | Ativar a automatização que pode desencadear eventos como a autenticação de vários fatores, o reset de passwords e o bloqueio de inscrições com base no risco. | Azure AD Premium P2 |
| [Ativar o registo convergente para reset de palavra-passe autosserviço e autenticação multi-factor Azure AD (pré-visualização)](../authentication/concept-registration-mfa-sspr-converged.md) | Permita que os seus utilizadores se registem a partir de uma experiência comum para a autenticação de multi-factores Azure e para o reset de senha de autosserviço. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fase 2: Importar utilizadores, permitir sincronização e gerir dispositivos

Em seguida, adicionamos à fundação estabelecida na fase 1, importando os nossos utilizadores e permitindo a sincronização, planejando acesso ao hóspede e preparando-se para suportar funcionalidades adicionais.

| Tarefa | Detalhe | Licença obrigatória |
| ---- | ------ | ---------------- |
| [Instalar o Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | Prepare-se para sincronizar os utilizadores desde o seu diretório no local até à nuvem. | Azure AD Gratuito |
| [Implementar Sincronização hash password](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Sincronizar hashes de palavra-passe para permitir que alterações de palavra-passe sejam replicadas, deteção e reparação de palavras-passe incorretas e relatórios credenciais vazados. | Azure AD Gratuito |
| [Implementar A Danulação da Palavra-passe](../authentication/howto-sspr-writeback.md) | Permita que as alterações de palavra-passe na nuvem sejam reescritas para um ambiente de Diretório Ativo do Windows Server no local. | Azure AD Premium P1 |
| [Implementar a AD Connect Health](../connect-health/active-directory-aadconnect-health.md) | Ative a monitorização das principais estatísticas de saúde dos seus servidores Azure AD Connect, servidores AD FS e controladores de domínio. | Azure AD Premium P1 |
| [Atribuir licenças aos utilizadores por associação de grupo seleções em Diretório Ativo Azure](../users-groups-roles/licensing-groups-assign.md) | Poupe tempo e esforço criando grupos de licenciamento que ativem ou desativem funcionalidades por grupo em vez de definirem por utilizador. | |
| [Criar um plano para o acesso ao utilizador convidado](../b2b/what-is-b2b.md) | Colabore com os utilizadores convidados, permitindo-lhes iniciar sessão nas suas apps e serviços com o seu próprio trabalho, escola ou identidades sociais. | [Orientação de licenciamento Azure AD B2B](../b2b/licensing-guidance.md) |
| [Decida sobre a estratégia de gestão de dispositivos](../devices/overview.md) | Decida o que a sua organização permite em relação aos dispositivos. Registo vs aderir, Bring Your Own Device vs empresa fornecida. | |
| [Implemente o Windows Hello for Business na sua organização](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Prepare-se para autenticação sem palavras-passe usando o Windows Hello | |
| [Implemente métodos de autenticação sem palavras-passe para os seus utilizadores](../authentication/concept-authentication-passwordless.md) | Forneça aos seus utilizadores métodos de autenticação convenientes sem palavras-passe | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>Fase 3: Gerir aplicações

À medida que continuamos a basear-nos nas fases anteriores, identificamos candidaturas candidatas à migração e integração com a Azure AD e completamos a configuração dessas candidaturas.

| Tarefa | Detalhe | Licença obrigatória |
| ---- | ------ | ---------------- |
| Identifique as suas aplicações | Identifique aplicações em uso na sua organização: no local, aplicações SaaS na nuvem e outras aplicações de linha de negócio. Determine se estas aplicações podem e devem ser geridas com a AD Azure. | Não é necessária licença |
| [Integrar candidaturas apoiadas pelo SaaS na galeria](../manage-apps/add-application-portal.md) | A Azure AD tem uma galeria que contém milhares de aplicações pré-integradas. Algumas das aplicações que a sua organização utiliza estão provavelmente na galeria acessível diretamente do portal Azure. | Azure AD Gratuito |
| [Utilizar o Proxy de Aplicação para integrar aplicações no local](../manage-apps/application-proxy-add-on-premises-application.md) | Aplicação Proxy permite que os utilizadores acedam às aplicações no local através da assinatura na sua conta Azure AD. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fase 4: Auditar identidades privilegiadas, concluir uma revisão de acesso e gerir o ciclo de vida do utilizador

A fase 4 vê os administradores a aplicarem princípios de menor privilégio para a administração, completando as suas primeiras avaliações de acesso e permitindo a automatização de tarefas comuns de ciclo de vida do utilizador.

| Tarefa | Detalhe | Licença obrigatória |
| ---- | ------ | ---------------- |
| [Impor o uso da Gestão de Identidade Privilegiada](../privileged-identity-management/pim-security-wizard.md) | Remova as funções administrativas das contas normais do utilizador do dia-a-dia. Tornar os utilizadores administrativos elegíveis para o seu papel após a sumissão de um controlo de autenticação multifactor, fornecendo uma justificação de negócio, ou solicitando a aprovação dos aprovadores designados. | Azure AD Premium P2 |
| [Complete uma revisão de acesso para funções de diretório azure ad no PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Trabalhe com as suas equipas de segurança e liderança para criar uma política de revisão de acesso para rever o acesso administrativo com base nas políticas da sua organização. | Azure AD Premium P2 |
| [Implementar políticas dinâmicas de adesão ao grupo](../users-groups-roles/groups-dynamic-membership.md) | Utilize grupos dinâmicos para atribuir automaticamente utilizadores a grupos com base nos seus atributos de RH (ou a sua fonte de verdade), tais como departamento, título, região e outros atributos. |  |
| [Implementar o provisionamento de aplicações baseadas em grupos](../manage-apps/what-is-access-management.md) | Utilize o fornecimento de gestão de acesso baseado em grupo para fornecer automaticamente utilizadores para aplicações SaaS. |  |
| [Automatizar o fornecimento e a desprovisionamento dos utilizadores](../app-provisioning/user-provisioning.md) | Retire os passos manuais do ciclo de vida da sua conta de empregado para evitar o acesso não autorizado. Sincronizar identidades da sua fonte de verdade (Sistema HR) para AD Azure. |  |

## <a name="next-steps"></a>Passos seguintes

[Detalhes de licenciamento e preços da AD Azure](https://azure.microsoft.com/pricing/details/active-directory/)

[Configurações de acesso aos dispositivos e identidade](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Políticas comuns de acesso à identidade e ao dispositivo recomendadas](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)

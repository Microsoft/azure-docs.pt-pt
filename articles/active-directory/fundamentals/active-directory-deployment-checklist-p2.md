---
title: Lista de verificação de implementação do Azure AD
description: O Azure Active Directory funcionalidade implementação lista de verificação
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9ee0d6fab96c84eee8a520d01d97faddab49f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60249714"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Guia de implementação de recursos do Azure Active Directory

Pode parecer assustadora para implementar o Azure Active Directory (Azure AD) para a sua organização e mantê-lo seguro. Este artigo identifica as tarefas comuns que os clientes encontrar útil para concluir em fases, ao longo de 30, 60, 90 dias, ou postura mais, para melhorar a segurança dos mesmos. Até mesmo organizações que já implementaram do Azure AD podem utilizar este guia para garantir que estão a obter o máximo proveito do seu investimento.

Uma infraestrutura de identidade bem planejada e executada abre o caminho para um acesso seguro às suas cargas de trabalho de produtividade e a dados por usuários conhecidos e apenas a dispositivos.

Além dos clientes podem verificar suas [identidade secure pontuação](identity-secure-score.md) para ver como alinhados são para melhores práticas da Microsoft. Verifique a sua pontuação segura antes e depois de implementar estas recomendações para ver quão bem estão a fazer em comparação com outras pessoas na sua indústria e outras organizações do tamanho dos.

## <a name="prerequisites"></a>Pré-requisitos

Muitas das recomendações neste guia podem ser implementadas com o Azure AD gratuito, básico ou nenhuma licença de todo. Em que são necessárias licenças iremos do Estado de que licença é necessária no mínimo para realizar a tarefa.

Pode encontrar informações adicionais sobre o licenciamento nas seguintes páginas:

* [Licenciamento Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Orientação de licenciamento do Azure AD B2B](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fase 1: Criar uma base de segurança

Nesta fase, os administradores ativar funcionalidades de segurança de linha de base criar uma base mais segura e fácil de usar no Azure AD antes que importe ou crie contas de utilizador normal. Nesta fase fundamental garante que está num estado mais seguro desde o início e de que os utilizadores finais só tem de ser introduzida para novos conceitos de uma vez.

| Tarefa | Detalhe | Licença necessária |
| ---- | ------ | ---------------- |
| [Designar mais do que um administrador global](../users-groups-roles/directory-emergency-access.md) | Atribua, pelo menos, duas contas de administrador global permanente apenas na cloud para utilização, se houver uma emergência. Estas contas não são utilizado diariamente e devem ter palavras-passe compridas e complexas. | Azure AD Gratuito |
| [Utilize as funções administrativas não global, sempre que possível](../users-groups-roles/directory-assign-admin-roles.md) | Permitir que os administradores apenas o que precisam de acesso ao apenas as áreas que precisam de acesso a. Nem todos os administradores precisam ser os administradores globais. | Azure AD Gratuito |
| [Ativar a Privileged Identity Management para controlar a utilização da função de administrador](../privileged-identity-management/pim-getting-started.md) | Ative a Privileged Identity Management iniciar o controlo da utilização da função de administrador. | Azure AD Premium P2 |
| [Implementar a reposição personalizada de palavra-passe](../authentication/howto-sspr-deployment.md) | Reduz as chamadas de suporte técnico para reposições de palavra-passe, permitindo que a equipe de TI repor a palavra-passe através de políticas de, como um controle de administrador. | Azure AD Básico |
| [Criar uma lista de personalizado banidas palavra-passe específica da organização](../authentication/howto-password-ban-bad-configure.md) | Impedir que os utilizadores criem palavras-passe que incluem comuns palavras ou frases de sua organização ou área. | Azure AD Básico |
| [Ativar a integração no local com a proteção de palavra-passe do Azure AD](../authentication/concept-password-ban-bad-on-premises.md) | Expandir a lista de palavra-passe banidas ao seu diretório no local, para garantir que as palavras-passe conjunto locais também estão em conformidade com o global e específico de inquilino excluído listas de palavra-passe. | Azure AD Premium P1 |
| [Ativar a orientação de palavra-passe da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Parar exigir que os utilizadores para alterar a palavra-passe numa agenda definida, desativar os requisitos de complexidade e os usuários é mais apto a memorizar as palavras-passe e mantê-los algo que é seguro. | Azure AD Gratuito |
| [Desativar redefinições de senha periódicas para contas de utilizador com base na cloud](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Redefinições de senha periódicas Incentive os seus utilizadores para incrementar as palavras-passe existentes. Utilize as diretrizes num documento de orientação de palavra-passe da Microsoft e espelhar a política no local para os utilizadores apenas na cloud. | Azure AD Gratuito |
| [Personalizar o bloqueio inteligente do Azure Active Directory](../authentication/howto-password-smart-lockout.md) | Parar bloqueios dos utilizadores com base na cloud a ser replicados para utilizadores do Active Directory no local | Azure AD Básico |
| [Ativar o bloqueio de Extranet inteligente para o AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | O bloqueio de extranet do AD FS protege contra ataques, a adivinhação, permitindo que os utilizadores do AD FS válidos, continuar a utilizar as suas contas de palavra-passe de força bruta. | |
| [Implementar a autenticação multi-factor do Azure AD, através de políticas de acesso condicional](../authentication/howto-mfa-getstarted.md) | Exigir que os utilizadores efetuar a verificação de dois passos ao aceder a aplicações confidenciais através de políticas de acesso condicional. | Azure AD Premium P1 |
| [Ativar o Azure Active Directory Identity Protection](../identity-protection/enable.md) | Ative o controlo de inícios de sessão de risco e credenciais comprometidas para utilizadores na sua organização. | Azure AD Premium P2 |
| [Utilizar eventos de risco para acionar a autenticação multifator e alterações de palavra-passe](../authentication/tutorial-risk-based-sspr-mfa.md) | Ative a automatização que pode acionar eventos, tais como o multi-factor authentication, reposição de palavra-passe e bloqueio de inícios de sessão com base no risco. | Azure AD Premium P2 |
| [Ativar o registo convergido para reposição de palavra-passe self-service e do Azure AD multi-factor Authentication (pré-visualização)](../authentication/concept-registration-mfa-sspr-converged.md) | Permitir que os utilizadores registar a partir de uma experiência comum para o Azure multi-factor Authentication e reposição de palavra-passe self-service. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fase 2: Importar utilizadores, ativar a sincronização e gerir dispositivos

Em seguida, podemos adicionar a base sedimentada em fase 1, ao importar nossos usuários e ativar a sincronização, planeamento de acesso de convidado e a preparação para suportar funcionalidades adicionais.

| Tarefa | Detalhe | Licença necessária |
| ---- | ------ | ---------------- |
| [Instalar o Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | Prepare-se para sincronizar os utilizadores do seu diretório no local existente para a cloud. | Azure AD Gratuito |
| [Implementar a sincronização de Hash de palavra-passe](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Sincronize os hashes de palavra-passe para permitir alterações de palavra-passe a ser replicados, deteção de palavra-passe incorreta e remediação e relatórios de fuga de credenciais. | Azure AD Premium P1 |
| [Implementar a repetição de escrita de palavra-passe](../authentication/howto-sspr-writeback.md) | Permitir alterações de palavra-passe na cloud para a repetição de escrita para um ambiente do Windows Server Active Directory no local. | Azure AD Premium P1 |
| [Implementar o Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) | Ative a monitorização de estatísticas de estado de funcionamento de chave para os seus servidores do Azure AD Connect, servidores do AD FS e controladores de domínio. | Azure AD Premium P1 |
| [Atribuir licenças aos utilizadores pela associação de grupo no Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Poupe tempo e esforço através da criação de grupos de licenciamento que ativar ou desativar funcionalidades por grupo, em vez de definição de por utilizador. | |
| [Criar um plano para acesso do utilizador convidado](../b2b/what-is-b2b.md) | Colabore com os utilizadores convidados, permitindo que o início de sessão às suas aplicações e serviços com seus próprios profissional, escolar ou as identidades sociais-los. | [Orientação de licenciamento do Azure AD B2B](../b2b/licensing-guidance.md) |
| [Decidir qual a estratégia de gestão de dispositivos](../devices/overview.md) | Decida o que sua organização permite sobre dispositivos. Registar vs associar, Bring Your Own Device vs empresa fornecido. | |
| [Implantar o Windows Hello para empresas em sua organização](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Preparar para a autenticação sem palavra-passe com o Windows Hello | |

## <a name="phase-3-manage-applications"></a>Fase 3: Gerir aplicações

À medida que Continuamos a criação de fases anteriores, podemos identificar aplicativos candidatos à migração e integração com o Azure AD e concluir a configuração desses aplicativos.

| Tarefa | Detalhe | Licença necessária |
| ---- | ------ | ---------------- |
| Identificar as suas aplicações | Identificar as aplicações em utilização na sua organização: no local, aplicações SaaS na cloud e outras aplicações de linha de negócio. Determine se esses aplicativos podem e devem ser geridos com o Azure AD. | Nenhuma licença necessária |
| [Integrar aplicações SaaS suportadas na Galeria](../manage-apps/add-application-portal.md) | O Azure AD tem uma galeria que contém a milhares de aplicações previamente integradas. Alguns aplicativos de que sua organização utiliza provavelmente estão presentes na Galeria acessível diretamente a partir do portal do Azure. | Azure AD Gratuito |
| [Utilize o Proxy de aplicações para integrar aplicações no local](../manage-apps/application-proxy-add-on-premises-application.md) | Proxy de aplicações permite aos utilizadores aceder a aplicações no local ao iniciar sessão com a respetiva conta do Azure AD. | Azure AD Básico |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fase 4: Auditar as identidades privilegiadas, concluir uma revisão de acesso e gerir o ciclo de vida do utilizador

Fase 4 vê os administradores a imposição de princípios de privilégios mínimos para a administração, as revisões de acesso primeiro a concluir e ativar a automatização de tarefas comuns de ciclo de vida do utilizador.

| Tarefa | Detalhe | Licença necessária |
| ---- | ------ | ---------------- |
| [Impor a utilização do Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | Remova funções administrativas a contas de usuários normais do dia a dia. Os utilizadores administrativos tornar elegíveis para utilizar a respetiva função depois que os sucedem uma verificação de autenticação multifator, fornecer uma justificação de negócio ou o pedido de aprovação de aprovadores designados. | Azure AD Premium P2 |
| [Concluir uma revisão de acesso para funções de diretório do Azure AD no PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Contacte as equipas de segurança e liderança globais para criar uma política de revisão de acesso para rever o acesso administrativo com base nas políticas da sua organização. | Azure AD Premium P2 |
| [Implementar políticas de associação de grupo dinâmico](../users-groups-roles/groups-dynamic-membership.md) | Utilize grupos dinâmicos para atribuir automaticamente os utilizadores a grupos com base nos seus atributos de RH (ou sua fonte de verdade), por exemplo, departamento, título, região e outros atributos. |  |
| [Implementar o provisionamento de aplicativos de grupo com base](../manage-apps/what-is-access-management.md) | Utilize o aprovisionamento da gestão de acesso baseado em grupo para aprovisionar automaticamente os utilizadores para aplicações SaaS. |  |
| [Automatizar o aprovisionamento de utilizador e de desaprovisionamento](../manage-apps/user-provisioning.md) | Remova passos manuais do seu ciclo de vida de conta de funcionário para evitar acessos não autorizados. Sincronize as identidades da sua origem de verdade (sistema de RH) para o Azure AD. |  |

## <a name="next-steps"></a>Passos Seguintes

[Detalhes de preços e licenciamento do AD do Azure](https://azure.microsoft.com/pricing/details/active-directory/)

[Configurações de acesso de dispositivos e identidade](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Comum recomendado políticas de acesso de dispositivos e identidade](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)

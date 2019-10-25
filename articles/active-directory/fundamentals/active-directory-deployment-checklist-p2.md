---
title: Lista de verificação de Implementação do Azure AD
description: Lista de verificação de implantação de recurso Azure Active Directory
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
ms.openlocfilehash: bc06931dd36c9ecd91ec1d748b9463f47f7afafc
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880593"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Guia de implantação de recurso Azure Active Directory

Pode parecer desanimador implantar Azure Active Directory (Azure AD) para sua organização e mantê-la segura. Este artigo identifica as tarefas comuns que os clientes acham úteis para serem concluídas em fases, ao longo de 30, 60, 90 dias ou mais, para aprimorar sua postura de segurança. Mesmo as organizações que já implantaram o Azure AD podem usar este guia para garantir que estejam obtendo o máximo de seus investimentos.

Uma infraestrutura de identidade bem planejada e executada abre a maneira de proteger o acesso a seus dados e cargas de trabalho de produtividade somente por usuários e dispositivos conhecidos.

Além disso, os clientes podem verificar sua [Pontuação segura de identidade](identity-secure-score.md) para ver como eles são alinhados às práticas recomendadas da Microsoft. Verifique sua pontuação segura antes e depois de implementar essas recomendações para ver como você está fazendo comparações com outras organizações do seu setor e para outras empresas de seu tamanho.

## <a name="prerequisites"></a>Pré-requisitos

Muitas das recomendações neste guia podem ser implementadas com Azure AD Gratuito ou sem licença. Onde as licenças são exigidas, podemos declarar qual licença é necessária no mínimo para realizar a tarefa.

Informações adicionais sobre o licenciamento podem ser encontradas nas páginas a seguir:

* [Licenciamento Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Diretrizes de licenciamento B2B do Azure AD](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fase 1: criar uma base de segurança

Nesta fase, os administradores habilitam os recursos de segurança de linha de base para criar uma base mais segura e fácil de usar no Azure AD antes de importar ou criar contas de usuário normais. Essa fase fundamental garante que você esteja em um estado mais seguro desde o início e que seus usuários finais precisem ser introduzidos a novos conceitos apenas uma vez.

| Tarefa | Detalhes | Licença necessária |
| ---- | ------ | ---------------- |
| [Designar mais de um administrador global](../users-groups-roles/directory-emergency-access.md) | Atribua pelo menos duas contas de administrador global permanentes somente em nuvem para uso se houver uma emergência. Essas contas não são usadas diariamente e devem ter senhas longas e complexas. | Azure AD Gratuito |
| [Usar funções administrativas não globais sempre que possível](../users-groups-roles/directory-assign-admin-roles.md) | Dê aos seus administradores somente o acesso de que eles precisam para as áreas às quais precisam acessar. Nem todos os administradores precisam ser administradores globais. | Azure AD Gratuito |
| [Habilitar Privileged Identity Management para controlar o uso da função de administrador](../privileged-identity-management/pim-getting-started.md) | Habilite Privileged Identity Management para começar a controlar o uso de função administrativa. | Azure AD Premium P2 |
| [Implementar a reposição personalizada de palavra-passe](../authentication/howto-sspr-deployment.md) | Reduza as chamadas da assistência técnica para redefinições de senha, permitindo que a equipe redefina suas próprias senhas usando políticas que você como um controle de administrador. | |
| [Criar uma lista de senhas banidas personalizada específica da organização](../authentication/howto-password-ban-bad-configure.md) | Impedir que os usuários criem senhas que incluam palavras ou frases comuns de sua organização ou de sua área. | |
| [Habilitar a integração local com a proteção de senha do Azure AD](../authentication/concept-password-ban-bad-on-premises.md) | Estenda a lista de senhas banidas para seu diretório local, para garantir que as senhas definidas localmente também estejam em conformidade com as listas de senhas proibidas globais e específicas do locatário. | Azure AD Premium P1 |
| [Habilitar as diretrizes de senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Parar de exigir que os usuários alterem sua senha em uma programação definida, desabilitar os requisitos de complexidade e os usuários são mais aptos a lembrar suas senhas e mantê-las seguras. | Azure AD Gratuito |
| [Desabilitar redefinições de senha periódicas para contas de usuário baseadas em nuvem](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | As redefinições de senha periódicas incentivam seus usuários a incrementarem suas senhas existentes. Use as diretrizes no documento de diretrizes de senha da Microsoft e espelhe sua política local para usuários somente na nuvem. | Azure AD Gratuito |
| [Personalizar o bloqueio inteligente Azure Active Directory](../authentication/howto-password-smart-lockout.md) | Impedir que os bloqueios de usuários baseados em nuvem sejam replicados para usuários Active Directory locais | |
| [Habilitar o bloqueio inteligente de extranet para AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS bloqueio de extranet protege contra ataques de adivinhação de senha de força bruta, ao mesmo tempo que permite que usuários de AD FS válidos continuem a usar suas contas. | |
| [Implantar a autenticação multifator do Azure AD usando políticas de acesso condicional](../authentication/howto-mfa-getstarted.md) | Exigir que os usuários executem a verificação em duas etapas ao acessar aplicativos confidenciais usando políticas de acesso condicional. | Azure AD Premium P1 |
| [Habilitar Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) | Habilite o acompanhamento de entradas arriscadas e credenciais comprometidas para usuários em sua organização. | Azure AD Premium P2 |
| [Usar as detecções de risco para disparar a autenticação multifator e as alterações de senha](../authentication/tutorial-risk-based-sspr-mfa.md) | Habilite a automação que pode disparar eventos como autenticação multifator, redefinição de senha e bloqueio de entradas com base em risco. | Azure AD Premium P2 |
| [Habilitar o registro convergido para redefinição de senha de autoatendimento e autenticação multifator do Azure AD (versão prévia)](../authentication/concept-registration-mfa-sspr-converged.md) | Permita que os usuários se registrem de uma experiência comum para a autenticação multifator do Azure e a redefinição de senha de autoatendimento. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fase 2: importar usuários, habilitar a sincronização e gerenciar dispositivos

Em seguida, adicionamos à Fundação dividida na fase 1 importando nossos usuários e habilitando a sincronização, planejando o acesso de convidado e preparando-se para oferecer suporte a funcionalidades adicionais.

| Tarefa | Detalhes | Licença necessária |
| ---- | ------ | ---------------- |
| [Instalar o Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | Prepare-se para sincronizar os usuários de seu diretório local existente para a nuvem. | Azure AD Gratuito |
| [Implementar sincronização de hash de senha](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Sincronize os hashes de senha para permitir que as alterações de senha sejam replicadas, detecção de senha inadequada e correção e relatórios de credenciais vazados. | Azure AD Gratuito |
| [Implementar write-back de senha](../authentication/howto-sspr-writeback.md) | Permita que as alterações de senha na nuvem sejam gravadas em um ambiente local Active Directory do Windows Server. | Azure AD Premium P1 |
| [Implementar Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) | Habilite o monitoramento de estatísticas de integridade de chave para seus servidores Azure AD Connect, servidores AD FS e controladores de domínio. | Azure AD Premium P1 |
| [Atribuir licenças a usuários por associação de grupo no Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Economize tempo e esforço criando grupos de licenciamento que habilitam ou desabilitam recursos por grupo em vez de configuração por usuário. | |
| [Criar um plano para acesso de usuário convidado](../b2b/what-is-b2b.md) | Colabore com usuários convidados, permitindo que eles entrem em seus aplicativos e serviços com suas próprias identidades corporativas, de estudante ou sociais. | [Diretrizes de licenciamento B2B do Azure AD](../b2b/licensing-guidance.md) |
| [Decidir sobre a estratégia de gerenciamento de dispositivos](../devices/overview.md) | Decida o que a sua organização permite com relação a dispositivos. Registrando vs unindo, traga seu próprio dispositivo versus a empresa fornecida. | |
| [Implantar o Windows Hello para empresas em sua organização](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Preparar a autenticação com senha usando o Windows Hello | |
| [Implantar métodos de autenticação com senha para seus usuários](../authentication/concept-authentication-passwordless.md) | Forneça aos seus usuários métodos de autenticação convenientes sem senha | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>Fase 3: gerenciar aplicativos

À medida que continuamos a criar as fases anteriores, identificamos os aplicativos candidatos para migração e integração com o Azure AD e concluímos a configuração desses aplicativos.

| Tarefa | Detalhes | Licença necessária |
| ---- | ------ | ---------------- |
| Identificar seus aplicativos | Identifique aplicativos em uso em sua organização: aplicativos SaaS locais na nuvem e outros aplicativos de linha de negócios. Determine se esses aplicativos podem e devem ser gerenciados com o Azure AD. | Nenhuma licença necessária |
| [Integrar aplicativos SaaS com suporte na Galeria](../manage-apps/add-application-portal.md) | O Azure AD tem uma galeria que contém milhares de aplicativos previamente integrados. Alguns dos aplicativos que sua organização usa provavelmente estão na Galeria acessível diretamente do portal do Azure. | Azure AD Gratuito |
| [Usar o proxy de aplicativo para integrar aplicativos locais](../manage-apps/application-proxy-add-on-premises-application.md) | O proxy de aplicativo permite que os usuários acessem aplicativos locais ao entrar com sua conta do Azure AD. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fase 4: auditar identidades com privilégios, concluir uma revisão de acesso e gerenciar o ciclo de vida do usuário

A fase 4 vê os administradores impondo os princípios de privilégio mínimo para administração, concluindo suas primeiras revisões de acesso e habilitando a automação de tarefas comuns do ciclo de vida do usuário.

| Tarefa | Detalhes | Licença necessária |
| ---- | ------ | ---------------- |
| [Impor o uso de Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | Remova funções administrativas das contas de usuário do dia a dia normal. Torne os usuários administrativos qualificados para usar sua função depois de ter sucesso uma verificação de autenticação multifator, fornecendo uma justificativa de negócios ou solicitando aprovação de aprovadores designados. | Azure AD Premium P2 |
| [Concluir uma revisão de acesso para as funções de diretório do Azure AD no PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Trabalhe com suas equipes de segurança e de liderança para criar uma política de revisão de acesso para examinar o acesso administrativo com base nas políticas da sua organização. | Azure AD Premium P2 |
| [Implementar políticas de associação de grupo dinâmico](../users-groups-roles/groups-dynamic-membership.md) | Use grupos dinâmicos para atribuir automaticamente usuários a grupos com base em seus atributos de RH (ou sua fonte de verdade), como departamento, título, região e outros atributos. |  |
| [Implementar o provisionamento de aplicativos baseados em grupo](../manage-apps/what-is-access-management.md) | Use o provisionamento de gerenciamento de acesso baseado em grupo para provisionar automaticamente os usuários para aplicativos SaaS. |  |
| [Automatizar o provisionamento e o desprovisionamento de usuários](../manage-apps/user-provisioning.md) | Remova as etapas manuais do ciclo de vida de sua conta de funcionário para impedir o acesso não autorizado. Sincronize identidades da sua fonte de verdade (sistema de RH) para o Azure AD. |  |

## <a name="next-steps"></a>Passos seguintes

[Licenciamento do Azure AD e detalhes de preços](https://azure.microsoft.com/pricing/details/active-directory/)

[Configurações de acesso de dispositivo e identidade](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Políticas recomendadas comuns de identidade e de acesso do dispositivo](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)

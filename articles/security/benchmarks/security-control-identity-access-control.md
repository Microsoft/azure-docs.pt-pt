---
title: Controlo de Segurança Azure - Controlo de Identidade e Acesso
description: Identidade de controlo de segurança Azure e Controlo de Acesso
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8d2901e71a3c638e25899803ff9b24e20fdf1969
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412668"
---
# <a name="security-control-identity-and-access-control"></a>Controlo de Segurança: Controlo de Identidade e Acesso

As recomendações de gestão de identidade e acesso focam-se em abordar questões relacionadas com o controlo de acesso baseado na identidade, bloquear o acesso administrativo, alertar sobre eventos relacionados com identidade, comportamento anormal de conta e controlo de acesso baseado em funções.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3,1 | 4.1 | Cliente |

A Azure AD tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true)

## <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.2 | 4.2 | Cliente |

A Azure AD não tem o conceito de senhas padrão. Outros recursos Azure que requerem uma palavra-passe forçam a criação de uma palavra-passe com requisitos de complexidade e um comprimento mínimo de senha, que difere consoante o serviço. É responsável por aplicações de terceiros e serviços de marketplace que possam utilizar senhas padrão.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.3 | 4.3 | Cliente |

Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Também pode ativar um Just-In-Time / Just-Enough-Access utilizando funções privilegiadas de gestão de identidade privilegiada Azure AD para serviços microsoft e gestor de recursos Azure. 

- [Saiba mais sobre Gestão de Identidade Privilegiada](../../active-directory/privileged-identity-management/index.yml)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.4 | 4.4 | Cliente |

Sempre que possível, utilize o Azure Ative Directory SSO em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

- [Compreender SSO com Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.5 | 4.5, 11.5, 12.11, 16.3 | Cliente |

Ativar O Azure AD MFA e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como permitir o MFA em Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../../security-center/security-center-identity-access.md)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Cliente |

Utilize PAWs (estações de trabalho privilegiadas de acesso) com MFA configurados para iniciar sessão e configurar recursos Azure.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Como permitir o MFA em Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.7 | 4.8, 4.9 | Cliente |

Utilize relatórios de segurança do Azure Ative Directory para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores de AD Azure sinalizados para atividade de risco](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure](../../security-center/security-center-identity-access.md)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.8 | 11.7 | Cliente |

Utilize locais nomeados de acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

## <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Cliente |

Utilize o Azure Ative Directory como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como criar e configurar uma instância AD Azure](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.10 | 16.9, 16.10 | Cliente |

A Azure AD fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado. 

- [Compreenda a Azure AD reportando](../../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários sobre acesso à identidade do Azure](../../active-directory/governance/access-reviews-overview.md)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.11 | 16.12 | Cliente |

Tem acesso a fontes de registo de registo de registos de registos, auditoria e eventos de risco Azure, que lhe permitem integrar-se com qualquer ferramenta SIEM/Monitoring.

Pode agilizar este processo criando Definições de Diagnóstico para contas de utilizadores do Azure Ative Directory e enviando os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do log analytics workspace.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.12 | 16.13 | Cliente |

Utilize funcionalidades de Risco e Proteção de Identidade Azure AD para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver a Azure AD a entrar em risco](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.13 | 16 | Cliente |

Em cenários de suporte onde a Microsoft precisa de aceder aos dados dos clientes, o Customer Lockbox fornece uma interface para que possa rever e aprovar ou rejeitar pedidos de acesso aos dados dos clientes.

- [Compreender o bloqueio do cliente](../fundamentals/customer-lockbox-overview.md)


## <a name="next-steps"></a>Passos seguintes

- Ver o próximo Controlo de Segurança: [Proteção de Dados](security-control-data-protection.md)
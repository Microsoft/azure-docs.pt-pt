---
title: Controlo de Segurança Azure - Controlo de Identidade e Acesso
description: Controlo de Identidade e Acesso de Controlo de Segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 543573610c2ea3ab0bcd89e1b8f4ee5f5a34dbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934437"
---
# <a name="security-control-identity-and-access-control"></a>Controlo de Segurança: Controlo de Identidade e Acesso

As recomendações de gestão de identidade e acesso centram-se na abordagem de questões relacionadas com o controlo de acesso baseado na identidade, no bloqueio do acesso administrativo, alertando sobre eventos relacionados com a identidade, comportamento anormal da conta e controlo de acesso baseado em papéis.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3,1 | 4.1 | Cliente |

A Azure AD tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Como obter um papel de diretório em Azure AD com powerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório em Azure AD com PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

## <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3,2 | 4.2 | Cliente |

A Azure AD não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma palavra-passe obrigam a criar uma palavra-passe com requisitos de complexidade e um comprimento mínimo de senha, que difere consoante o serviço. Você é responsável por aplicações de terceiros e serviços de mercado que podem usar senhas padrão.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.3 | 4.3 | Cliente |

Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Identidade e Gestão de Acesso do Azure Security Center para monitorizar o número de contas administrativas.

Também pode ativar um Acesso Just-In-Time / Just-Enough utilizando funções privilegiadas de gestão de identidade privilegiada seletivas do Azure AD Para serviços da Microsoft e gestor de recursos Azure. 

Saiba mais:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.4 | 4.4 | Cliente |

Sempre que possível, utilize o Azure Ative Directory SSO em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Identidade e Gestão de Acesso do Centro de Segurança Azure.

Compreenda o SSO com a Azure AD:

https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.5 | 4.5, 11.5, 12.11, 16.3 | Cliente |

Enable Azure AD MFA e siga as recomendações de Identidade e Gestão de Acesso do Azure Security Center.

Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Cliente |

Utilize PAWs (estações de trabalho de acesso privilegiados) com MFA configurado para iniciar sessão e configurar recursos Azure.

Saiba mais sobre postos de trabalho de acesso privilegiados:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.7 | 4.8, 4.9 | Cliente |

Utilize relatórios de segurança do Diretório Ativo Azure para a geração de registos e alertas quando ocorrer atividade suspeite ou insegura no ambiente. Utilize o Azure Security Center para monitorizar a atividade de identidade e acesso.

Como identificar utilizadores de Anúncios Azure sinalizados para atividades de risco:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.8 | 11.7 | Cliente |

Utilizar locais nomeados para acesso condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões.

Como configurar localizações nomeadas em Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

## <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Cliente |

Utilize o Diretório Ativo Azure (AAD) como sistema central de autenticação e autorização. A AAD protege os dados utilizando encriptação forte para dados em repouso e em trânsito. A AAD também sais, hashes e armazena seguramente credenciais de utilizador.

Como criar e configurar uma instância AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.10 | 16.9, 16.10 | Cliente |

A Azure AD fornece registos para ajudar a descobrir contas velhas. Além disso, utilize as Análises de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado. 

Relatórios da AD Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como utilizar comentários de acesso à identidade do Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

## <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.11 | 16.12 | Cliente |

Tem acesso a fontes de registo de registo de registo de Registos de AD Azure, auditoria e risco, que lhe permitem integrar-se com qualquer ferramenta de monitorização/monitorização do SIEM/Monitor.

Pode simplificar este processo criando Definições de Diagnóstico para contas de utilizadores do Diretório Ativo Azure e enviando os registos de auditoria e registos de log-in para um espaço de trabalho de Log Analytics. Pode configurar os alertas desejados dentro do espaço de trabalho de Log Analytics.

Como integrar registos de atividade do Azure no Monitor Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.12 | 16.13 | Cliente |

Utilize funcionalidades de risco e proteção de identidade Azure AD para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Também pode ingerir dados no Azure Sentinel para mais investigação.

Como ver os sign-ins de risco da AD Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 3.13 | 16 | Cliente |

Nos cenários de suporte em que a Microsoft precisa de aceder aos dados dos clientes, o Customer Lockbox fornece uma interface para que você reveja e aprove ou rejeite pedidos de acesso a dados do cliente.

Compreender o Bloqueio do Cliente:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

## <a name="next-steps"></a>Passos seguintes

Consulte o próximo controlo de segurança: [Proteção de Dados](security-control-data-protection.md)

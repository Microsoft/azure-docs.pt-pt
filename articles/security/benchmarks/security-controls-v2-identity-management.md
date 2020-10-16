---
title: Azure Security Benchmark V2 - Gestão de Identidade
description: Gestão de Identidade V2 de referência de segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e7447d03fc231d8d96b71c7d944a380c10add0d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757941"
---
# <a name="security-control-v2-identity-management"></a>Controlo de Segurança V2: Gestão de Identidade

A Gestão de Identidade abrange controlos para estabelecer uma identidade segura e controlos de acesso utilizando o Azure Ative Directory. Isto inclui a utilização de autenticações únicas, autenticações fortes, identidades geridas (e princípios de serviço) para aplicações, acesso condicional e monitorização de anomalias de conta.

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Normalizar o Azure Ative Directory como o sistema central de identidade e autenticação

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-1 | 16.1, 16.2, 16.4, 16.5 | IA-2, IA-8, AC-2, AC-3 |

Azure Ative Directory (Azure AD) é o serviço de gestão de identidade e acesso padrão da Azure. Deve normalizar a Azure AD para governar a gestão de identidade e acesso da sua organização em:
- Recursos em nuvem da Microsoft, como o portal Azure, Azure Storage, Azure Virtual Machines (Linux e Windows), Azure Key Vault, PaaS e SaaS.

- Os recursos da sua organização, tais como aplicações no Azure ou os recursos da sua rede corporativa.

Garantir a Azure AD deve ser uma alta prioridade na prática de segurança na nuvem da sua organização. O Azure AD fornece uma pontuação segura de identidade para ajudá-lo a avaliar a sua postura de segurança de identidade em relação às recomendações de boas práticas da Microsoft. Use a pontuação para avaliar o quão perto a sua configuração corresponde às recomendações de boas práticas e para fazer melhorias na sua postura de segurança.

Nota: A Azure AD suporta fornecedores de identidade externos, que permitem aos utilizadores sem conta da Microsoft iniciar súm na sua aplicação e recursos com a sua identidade externa.

- [Arrendamento em Azure AD](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Como criar e configurar uma instância AD Azure](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Definir inquilinos da AD AZure](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Utilize fornecedores de identidade externos para uma aplicação](/azure/active-directory/b2b/identity-providers)

- [Qual é a pontuação de identidade segura em Azure AD](../../active-directory/fundamentals/identity-secure-score.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gerir as identidades da aplicação de forma segura e automática

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-2 | N/D | AC-2, AC-3, IA-2, IA-4, IA-9 |

Para contas não humanas, como serviços ou automação, utilize identidades geridas pela Azure, em vez de criar uma conta humana mais poderosa para aceder a recursos ou executar código. As identidades geridas pelo Azure podem autenticar para os serviços e recursos da Azure que suportam a autenticação AZure. A autenticação é ativada através de regras de concessão de acesso pré-definidas, evitando credenciais codificadas em código fonte ou ficheiros de configuração. 

Para serviços que não suportem identidades geridas, utilize a Azure AD para criar um principal de serviço com permissões restritas ao nível dos recursos.  Recomenda-se configurar os principais do serviço com credenciais de certificado e voltar aos segredos dos clientes. Em ambos os casos, o Azure Key Vault pode ser usado em conjunto com identidades geridas aZure, de modo que o ambiente de tempo de execução (como uma função Azure) pode recuperar a credencial do cofre chave.

- [Identidades geridas por Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Serviços que suportam identidades geridas para recursos da Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Principal de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Criar um principal de serviço com certificados](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

Utilize o Cofre da Chave Azure para registo principal de segurança: autenticação#authorize-a-security-principal-to-access-key-vault

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Use a Azure AD single sign-on (SSO) para acesso à aplicação

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-3 | 4.4 | IA-2, IA-4 |

A Azure AD fornece gestão de identidade e acesso aos recursos da Azure, aplicações em nuvem e aplicações no local. A gestão de identidade e acesso aplica-se a identidades empresariais como colaboradores, bem como identidades externas, como parceiros, fornecedores e fornecedores.

Use o único sign-on (SSO) do Azure AD para gerir e garantir o acesso aos dados e recursos da sua organização no local e na nuvem. Ligue todos os seus utilizadores, aplicações e dispositivos ao Azure AD para um acesso sem emenda, acesso seguro e maior visibilidade e controlo. 

- [Compreender a aplicação SSO com Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilize controlos de autenticação forte para todo o acesso baseado no Azure Ative Directory

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-4 | 4.2, 4.4 4.5, 11.5, 12.11, 16.3 | AC-2, AC-3, IA-2, IA-4 |

A Azure AD suporta controlos de autenticação forte através de autenticação multi-factor (MFA) e métodos fortes sem palavras-passe.  
- Autenticação multi-factor: Ative O Azure AD MFA e siga as recomendações de identidade e gestão de acesso do Azure Security Center para a sua configuração de MFA. O MFA pode ser aplicado em todos os utilizadores, utilizadores selecionados ou ao nível por utilizador com base em condições de inscrição e fatores de risco. 

- Autenticação sem palavras-passe: Estão disponíveis três opções de autenticação sem palavras-passe: Windows Hello for Business, Microsoft Authenticator e métodos de autenticação no local, como cartões inteligentes. 

Para os utilizadores de administrador e privilegiados, certifique-se de que é utilizado o mais alto nível do método de autenticação forte, seguindo-se a aplicação da política de autenticação forte adequada a outros utilizadores.

Se a autenticação baseada em passwords legados ainda for utilizada para a autenticação Azure AD, tenha em atenção que as contas apenas na nuvem (contas de utilizador criadas diretamente no Azure) têm uma política de senha de base padrão. E as contas híbridas (contas de utilizador que vêm de Ative Directory) seguem as políticas de senha no local. Ao utilizar a autenticação baseada em palavras-passe, o Azure AD fornece uma capacidade de proteção de palavras-passe que impede os utilizadores de definir palavras-passe que são fáceis de adivinhar. A Microsoft fornece uma lista global de senhas proibidas que é atualizada com base na telemetria, e os clientes podem aumentar a lista com base nas suas necessidades (por exemplo, branding, referências culturais, etc.). Esta proteção de palavra-passe pode ser utilizada para contas exclusivamente em nuvem e híbridas. 

Nota: A autenticação baseada apenas nas credenciais de senha é suscetível a métodos de ataque populares. Para uma maior segurança, utilize uma autenticação forte, como mFA e uma política de senha forte. Para aplicações de terceiros e serviços de marketplace que possam ter senhas padrão, deve alterá-las durante a configuração inicial do serviço. 

- [Como permitir o MFA em Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Introdução a opções de autenticação sem palavras-passe para O Diretório Ativo Azure](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Política de senha padrão AD AZure](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminar palavras-passe más usando a proteção de senha ad Azure](../../active-directory/authentication/concept-password-ban-bad.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitor e alerta sobre anomalias de conta

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-5 | 4.8, 4.9, 16.12, 16.13 | AC-2, AC-3, AC-7, AU-6 |

A Azure AD fornece as seguintes fontes de dados: 
-   Ins- O relatório de inscrições fornece informações sobre o uso de aplicações geridas e atividades de inscrição do utilizador.

-   Registos de auditoria - Fornece rastreabilidade através de registos para todas as alterações escoradas através de várias funcionalidades em Azure AD. Exemplos de alterações registadas nos registos de auditoria incluem adicionar ou remover utilizadores, apps, grupos, funções e políticas.

-   Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

-   Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Estas fontes de dados podem ser integradas com sistemas Azure Monitor, Azure Sentinel ou siem de terceiros.

O Azure Security Center também pode alertar para certas atividades suspeitas, como um número excessivo de tentativas de autenticação falhadas e contas precíídas na subscrição. 

A Azure Advanced Threat Protection (ATP) é uma solução de segurança que pode usar sinais de Ative Directory no local para identificar, detetar e investigar ameaças avançadas, identidades comprometidas e ações de insider maliciosas.

- [Relatórios de atividades de auditoria em Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como ver a Azure AD a entrar em risco](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como identificar utilizadores de AD Azure sinalizados para atividade de risco](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure](../../security-center/security-center-identity-access.md)

- [Alertas no módulo de proteção de ameaças do Centro de Segurança Azure](/azure/security-center/alerts-reference)

- [Como integrar os registos de atividade do Azure no Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Ligar dados da Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso a recurso Azure com base em condições

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-6 | N/D | AC-2, AC-3 |

Utilize o acesso condicional AZure AD para um controlo de acesso mais granular com base em condições definidas pelo utilizador, tais como exigir logins de utilizadores de determinadas gamas IP para utilizar MFA. Uma gestão de sessão de autenticação granular também pode ser usada através da política de acesso condicional Azure AD para diferentes casos de uso. 

- [Visão geral do acesso condicional do Azure](../../active-directory/conditional-access/overview.md)

- [Políticas de Acesso Condicional comuns](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurar a gestão de sessões de autenticação com o Acesso Condicional](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminar exposição credencial não intencional

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-7 | 18.1, 18.7 | IA-5 |

Implementar o Scanner Credencial Azure DevOps para identificar credenciais dentro do código. O Scanner Credencial também encoraja a movimentação de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Para o GitHub, pode utilizar funcionalidades de digitalização secretas nativas para identificar credenciais ou outra forma de segredo dentro do código.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Digitalização secreta de GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: Acesso seguro do utilizador a aplicações antigas

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IM-8 | 14.6 | AC-2, AC-3, SC-11 |

Certifique-se de que tem modernos controlos de acesso e monitorização de sessão para aplicações antigas e os dados que armazenam e processam. Embora as VPNs sejam geralmente usadas para aceder a aplicações antigas, muitas vezes têm apenas controlo básico de acesso e monitorização limitada da sessão.

O Azure AD Application Proxy permite-lhe publicar aplicações antigas no local para utilizadores remotos com um único sign-on (SSO) ao mesmo tempo que valida explicitamente a fiabilidade tanto dos utilizadores remotos como dos dispositivos com Acesso Condicionado AD Azure. 

Em alternativa, o Microsoft Cloud App Security é um serviço de corretor de segurança de acesso à nuvem (CASB) que pode fornecer controlos para monitorizar as sessões de aplicações de um utilizador e ações de bloqueio (tanto para aplicações antigas no local como software de nuvem como aplicações de serviço (SaaS). 

- [Azure AD Application Proxy](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Microsoft Cloud App Security as melhores práticas](/cloud-app-security/best-practices)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

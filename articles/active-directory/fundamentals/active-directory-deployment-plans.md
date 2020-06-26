---
title: Planos de implantação - Azure Ative Directory / Microsoft Docs
description: Orientação de ponta a ponta sobre como implementar muitas capacidades do Azure Ative Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c439bbded7fe55f1edd5eb1597f98b339e340956
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85386339"
---
# <a name="azure-active-directory-deployment-plans"></a>Planos de implementação do Azure Active Directory
Procura orientação de ponta a ponta sobre a implementação de capacidades do Azure Ative Directory (Azure AD) ? Os planos de implementação da AD AZure percorrem o valor do negócio, as considerações de planeamento e os procedimentos operacionais necessários para implementar com sucesso as capacidades comuns da AD Azure.

A partir de qualquer uma das páginas do plano, use a capacidade de Impressão do seu navegador para PDF para criar uma versão offline atualizada da documentação.
## <a name="include-the-right-stakeholders"></a>Incluir as partes interessadas certas

Ao iniciar o seu planeamento de implementação para uma nova capacidade, é importante incluir as principais partes interessadas em toda a sua organização. Recomendamos que identifique e documente a pessoa ou pessoas que desempenham cada uma das seguintes funções, e que trabalhe com elas para determinar o seu envolvimento no projeto.  

As funções podem incluir os seguintes 

|Função |Description |
|-|-|
|Utilizador final|Um grupo representativo de utilizadores para o qual a capacidade será implementada. Muitas vezes pré-visualiza as alterações num programa piloto.
|Gestor de Suporte de TI|Representante da organização de suporte de TI que pode fornecer informações sobre a capacidade de apoio desta mudança numa perspetiva de helpdesk.  
|Arquiteto de Identidade ou Administrador Global da Azure|Representante da equipa de gestão de identidade responsável por definir como esta mudança está alinhada com a infraestrutura de gestão de identidade central na sua organização.|
|Proprietário de Negócios de Aplicação |O proprietário geral do(s) de aplicações afetadas, que pode incluir a gestão do acesso.Pode também fornecer informações sobre a experiência do utilizador e a utilidade desta alteração do ponto de vista do utilizador final.
|Proprietário de Segurança|Um representante da equipa de segurança que pode assinar que o plano vai cumprir os requisitos de segurança da sua organização.|
|Gestor de Conformidade|A pessoa dentro da sua organização responsável por garantir o cumprimento dos requisitos corporativos, industriais ou governamentais.|

**Os níveis de envolvimento podem incluir:**

- **R**eponsível para implementação do plano de projeto e do resultado 

- **Um**pproval do plano de projeto e do resultado 

- **C**ontributor para projetar plano e resultado 

- **Eu**nformou do plano de projeto e do resultado


## <a name="best-practices-for-a-pilot"></a>Melhores práticas para um piloto
Um piloto permite-lhe testar com um pequeno grupo antes de ligar uma capacidade para todos. Certifique-se de que, como parte do seu teste, cada caso de utilização dentro da sua organização é completamente testado. É melhor direcionar um grupo específico de utilizadores piloto antes de passar isto para a sua organização como um todo.

Na sua primeira onda, direcione a TI, a usabilidade e outros utilizadores apropriados que possam testar e fornecer feedback. Este feedback deve ser utilizado para desenvolver ainda mais as comunicações e instruções que envia aos seus utilizadores e para fornecer informações sobre os tipos de problemas que o seu pessoal de suporte pode ver. 

O alargamento do lançamento a grupos maiores de utilizadores deve ser efetuado aumentando o âmbito de aplicação do grupo(s) visados. Isto pode ser feito através da [adesão dinâmica do grupo,](../users-groups-roles/groups-dynamic-membership.md)ou adicionando manualmente os utilizadores ao(s) grupo(s) alvo.


## <a name="deploy-authentication"></a>Implementar autenticação

| Funcionalidade | Description|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| A Azure Multi-Factor Authentication (MFA) é uma solução de verificação em dois passos da Microsoft. Utilizando métodos de autenticação aprovados pela administração, o Azure MFA ajuda a salvaguardar o acesso aos seus dados e aplicações, ao mesmo tempo que satisfaz a exigência de um processo de inscrição simples. |
| [Conditional Access](../conditional-access/plan-conditional-access.md)| Com o Acesso Condicional, pode implementar decisões automatizadas de controlo de acesso para quem pode aceder às suas aplicações na nuvem, com base nas condições. |
| [Reposição personalizada de palavra-passe](../authentication/howto-sspr-deployment.md)| O reset da palavra-passe de autosserviço ajuda os seus utilizadores a redefinir as suas palavras-passe sem intervenção do administrador, quando e onde precisam. |
| [Sem palavra-passe](../authentication/howto-authentication-passwordless-deployment.md) | Implementar autenticação sem palavras-passe utilizando a app Microsoft Authenticator ou as teclas de segurança FIDO2 na sua organização |

## <a name="deploy-application-and-device-management"></a>Implementar aplicação e gestão de dispositivos

| Funcionalidade | Description|
| -| - |
| [Início de sessão único](../manage-apps/plan-sso-deployment.md)| O único sessão de sessão ajuda os seus utilizadores a aceder às apps e recursos de que necessitam para fazer negócios enquanto fazem a sessão apenas uma vez. Depois de se terem assinado, podem ir do Microsoft Office para SalesForce para Box para aplicações internas sem serem obrigados a introduzir credenciais uma segunda vez. |
| [Painel de acesso](../manage-apps/access-panel-deployment-plan.md)| Ofereça aos seus utilizadores um simples hub para descobrir e aceder a todas as suas aplicações. Capacitá-los a serem mais produtivos com capacidades de self-service, como solicitar acesso a apps e grupos, ou gerir o acesso a recursos em nome de outros. |
| [Dispositivos](../devices/plan-device-deployment.md) | Este artigo ajuda-o a avaliar os métodos para integrar o seu dispositivo com a Azure AD, escolher o plano de implementação e fornecer links-chave para ferramentas de gestão de dispositivos suportados. |


## <a name="deploy-hybrid-scenarios"></a>Implementar cenários híbridos

| Funcionalidade | Description|
| -| -|
| [ADFS para Sincronização Hash de Palavras-passe](../hybrid/plan-migrate-adfs-password-hash-sync.md)| Com a sincronização de Hash Password, as hashes de senhas de utilizador são sincronizadas desde o Ative Directy no local até ao Azure AD, permitindo que o Azure AD autente os utilizadores sem qualquer interação com o Ative Directory no local |
| [ADFS para Autenticação Pass-through](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| A Azure AD AD Authentication ajuda os seus utilizadores a iniciar súmis tanto no local como em aplicações baseadas na nuvem usando as mesmas palavras-passe. Esta funcionalidade proporciona aos utilizadores uma melhor experiência - uma palavra-passe a menos para se lembrarem - e reduz os custos de IT helpdesk porque os utilizadores são menos propensos a esquecer como fazer o s. Quando as pessoas iniciam sessão com o Azure AD, esta funcionalidade valida as respetivas palavras-passe diretamente no seu Active Directory no local. |
| [Proxy de Aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) |Os colaboradores dos nossos dias querem ser produtivos em qualquer sítio, em qualquer altura e em qualquer dispositivo. Precisam de aceder a aplicações SaaS na nuvem e aplicações corporativas no local. O proxy da Azure AD Application permite este acesso robusto sem redes privadas virtuais (VPNs) ou zonas desmilitarizadas (DMZs). |
| [SSO totalmente integrado](../hybrid/how-to-connect-sso-quick-start.md)| O Início de Sessão Único Totalmente Integrado do Azure Active Directory (SSO Totalmente Integrado do Azure AD) inicia sessão automaticamente pelos utilizadores quando estão a utilizar os dispositivos da empresa ligados à sua rede empresarial. Com esta funcionalidade, os utilizadores não precisarão de digitar as suas palavras-passe para iniciar sação no AZure AD e normalmente não precisarão de introduzir os seus nomes de utilizador. Esta funcionalidade proporciona aos utilizadores autorizados um fácil acesso às suas aplicações baseadas na nuvem sem necessitar de componentes adicionais no local. |

## <a name="deploy-user-provisioning"></a>Implementar o fornecimento de utilizadores

| Funcionalidade | Description|
| -| -|
| [Aprovisionamento de utilizadores](../app-provisioning/plan-auto-user-provisioning.md)| O Azure AD ajuda-o a automatizar a criação, a manutenção e a remoção de identidades de utilizadores em aplicações da cloud (SaaS), como o Dropbox, o Salesforce, o ServiceNow, entre outras. |
| [Fornecimento de utilizadores de RH em nuvem](../app-provisioning/plan-cloud-hr-provision.md)| O fornecimento de utilizadores de HR em nuvem ao Ative Directory cria uma base para a governação de identidade em curso e melhora a qualidade dos processos de negócio que dependem de dados de identidade autoritários. Utilizando esta funcionalidade com o seu produto HR em nuvem, como Workday ou Successfactors, pode gerir perfeitamente o ciclo de vida identitário dos colaboradores e trabalhadores contingentes através da configuração de regras que mapeiam processos de Joiner-Mover-Leaver (como New Hire, Terminate, Transfer) para ações de provisionamento de TI (tais como Criar, Ativar, Desativar) |

## <a name="deploy-governance-and-reporting"></a>Implementar governação e relatórios

| Funcionalidade | Description|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| A Azure AD Privileged Identity Management (PIM) ajuda-o a gerir funções administrativas privilegiadas em Azure AD, recursos Azure e outros Serviços Online da Microsoft. A PIM fornece soluções como o acesso just-in-time, solicitar fluxos de trabalho de aprovação e avaliações de acesso totalmente integradas para que possa identificar, descobrir e prevenir atividades maliciosas de funções privilegiadas em tempo real. |
| [Reportagem e Monitorização](../reports-monitoring/plan-monitoring-and-reporting.md)| O desenho da sua solução de relatório e monitorização Azure AD depende dos seus requisitos legais, de segurança e operacionais, bem como do ambiente e processos existentes. Este artigo apresenta as várias opções de design e guia-o para a estratégia de implementação certa. |

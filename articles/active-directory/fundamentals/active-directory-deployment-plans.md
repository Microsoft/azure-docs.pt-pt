---
title: Planos de implantação - Diretório Ativo Azure / Microsoft Docs
description: Orientação de ponta a ponta sobre como implementar muitas capacidades de Diretório Ativo Azure.
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
ms.openlocfilehash: d7a596454a48a1d6fcee77634363dd38f34a4d58
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603355"
---
# <a name="azure-active-directory-deployment-plans"></a>Planos de implementação do Azure Active Directory
À procura de orientação de ponta a ponta para a implementação das capacidades do Azure Ative Directory (Azure AD) ? Os planos de implantação da Azure AD percorrem-no através do valor do negócio, das considerações de planeamento e dos procedimentos operacionais necessários para implementar com sucesso as capacidades comuns da AD Azure.

A partir de qualquer uma das páginas do plano, use a capacidade de Impressão do seu navegador para PDF para criar uma versão offline atualizada da documentação.
## <a name="include-the-right-stakeholders"></a>Incluir as partes interessadas certas

Ao iniciar o seu plano de implementação para uma nova capacidade, é importante incluir as principais partes interessadas em toda a sua organização. Recomendamos que identifique e documente a pessoa ou pessoas que cumprem cada uma das seguintes funções, e trabalhe com ela para determinar o seu envolvimento no projeto.  

As funções podem incluir o seguinte 

|Função |Descrição |
|-|-|
|Utilizador final|Um grupo representativo de utilizadores para os quais a capacidade será implementada. Muitas vezes antevê as alterações num programa piloto.
|Gestor de suporte de TI|Representante da organização de apoio de TI que pode fornecer informações sobre a capacidade de suporte desta mudança de uma perspetiva de helpdesk.  
|Arquiteto de Identidade ou Administrador Global Azure|Representante da equipa de gestão de identidade responsável pela definição de como esta mudança está alinhada com a principal infraestrutura de gestão de identidade na sua organização.|
|Proprietário de negócios de aplicação |O proprietário global das aplicações afetadas, que pode incluir a gestão do acesso.Pode também fornecer informações sobre a experiência do utilizador e a utilidade desta mudança do ponto de vista de um utilizador final.
|Proprietário de Segurança|Um representante da equipa de segurança que pode assinar que o plano irá satisfazer os requisitos de segurança da sua organização.|
|Gestor de Conformidade|A pessoa dentro da sua organização responsável por garantir o cumprimento dos requisitos corporativos, industriais ou governamentais.|

**Os níveis de envolvimento podem incluir:**

- **R**esponsível para implementar plano e resultado do projeto 

- **Um**pproval de plano de projeto e resultado 

- **C**ontributor para projeto plano e resultado 

- **Eu informei**sobre o plano do projeto e o resultado


## <a name="best-practices-for-a-pilot"></a>Boas práticas para um piloto
Um piloto permite-lhe testar com um pequeno grupo antes de ligar a capacidade para todos. Certifique-se de que, como parte dos seus testes, cada caso de utilização dentro da sua organização é cuidadosamente testado. É melhor direcionar um grupo específico de utilizadores piloto antes de passar isto para a sua organização como um todo.

Na sua primeira onda, o alvo de TI, a usabilidade e outros utilizadores apropriados que possam testar e fornecer feedback. Este feedback deve ser utilizado para desenvolver ainda mais as comunicações e instruções que envia aos seus utilizadores e para dar informações sobre os tipos de problemas que a sua equipa de suporte pode ver. 

O alargamento da implantação a grupos maiores de utilizadores deve ser efetuado através do aumento do âmbito de aplicação do grupo(s) visado. Isto pode ser feito através da [adesão dinâmica do grupo](../users-groups-roles/groups-dynamic-membership.md), ou adicionando manualmente os utilizadores ao(s) grupo(s) visado.


## <a name="deploy-authentication"></a>Implementar a autenticação

| Capacidade | Descrição|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| A Azure Multi-Factor Authentication (MFA) é uma solução de verificação em dois passos da Microsoft. Utilizando métodos de autenticação aprovados pela administração, o Azure MFA ajuda a salvaguardar o acesso aos seus dados e aplicações, ao mesmo tempo que satisfaz a exigência de um processo de entrada simples. |
| [Conditional Access](../conditional-access/plan-conditional-access.md)| Com acesso condicional, pode implementar decisões automatizadas de controlo de acesso para quem pode aceder às suas aplicações na nuvem, com base em condições. |
| [Reposição personalizada de palavra-passe](../authentication/howto-sspr-deployment.md)| O reset da palavra-passe self-service ajuda os seus utilizadores a redefinir as suas palavras-passe sem a intervenção do administrador, quando e onde precisam. |
| [Sem palavra-passe](../authentication/howto-authentication-passwordless-deployment.md) | Implemente a autenticação sem palavras-passe utilizando a aplicação Microsoft Authenticator ou as chaves de segurança FIDO2 na sua organização |

## <a name="deploy-application-management"></a>Implementar gestão de aplicações

| Capacidade | Descrição|
| -| - |
| [Início de sessão único](../manage-apps/plan-sso-deployment.md)| O único registo ajuda os seus utilizadores a aceder às aplicações e recursos de que necessitam para fazer negócios enquanto assinam apenas uma vez. Depois de terem assinado, podem ir do Microsoft Office para o SalesForce para box para aplicações internas sem serem obrigados a introduzir credenciais uma segunda vez. |
| [Painel de acesso](../manage-apps/access-panel-deployment-plan.md)| Ofereça aos seus utilizadores um simples hub para descobrir e aceder a todas as suas aplicações. Permitir-lhes ser mais produtivos com capacidades de self-service, como solicitar acesso a apps e grupos, ou gerir o acesso aos recursos em nome de outros. |


## <a name="deploy-hybrid-scenarios"></a>Implementar cenários híbridos

| Capacidade | Descrição|
| -| -|
| [ADFS para Sincronização Hash de Palavras-passe](../hybrid/plan-migrate-adfs-password-hash-sync.md)| Com a Sincronização de Password Hash, as hashes das palavras-passe dos utilizadores são sincronizadas desde o Diretório Ativo no local até à AD Azure, permitindo que a Azure AD autentique os utilizadores sem qualquer interação com o Diretório Ativo no local |
| [ADFS para Autenticação Pass-through](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| A Autenticação pass-through Azure AD ajuda os seus utilizadores a iniciarem sessão tanto no local como nas aplicações baseadas na nuvem utilizando as mesmas palavras-passe. Esta funcionalidade proporciona aos utilizadores uma melhor experiência - uma palavra-passe a menos para se lembrarem - e reduz os custos de helpdesk de TI porque os utilizadores são menos propensos a esquecer como iniciar sessão. Quando as pessoas iniciam sessão com o Azure AD, esta funcionalidade valida as respetivas palavras-passe diretamente no seu Active Directory no local. |
| [Proxy de Aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) |Os colaboradores dos nossos dias querem ser produtivos em qualquer sítio, em qualquer altura e em qualquer dispositivo. Precisam de aceder a aplicações SaaS na nuvem e aplicações corporativas no local. O representante da Aplicação Azure AD permite este acesso robusto sem redes privadas virtuais dispendiosas e complexas (VPNs) ou zonas desmilitarizadas (DMZs). |
| [SSO totalmente integrado](../hybrid/how-to-connect-sso-quick-start.md)| O Início de Sessão Único Totalmente Integrado do Azure Active Directory (SSO Totalmente Integrado do Azure AD) inicia sessão automaticamente pelos utilizadores quando estão a utilizar os dispositivos da empresa ligados à sua rede empresarial. Com esta funcionalidade, os utilizadores não precisarão de escrever as suas palavras-passe para iniciar sessão no Azure AD e normalmente não precisam de introduzir os seus nomes de utilizador. Esta funcionalidade proporciona aos utilizadores autorizados um fácil acesso às suas aplicações baseadas na nuvem sem precisar de componentes adicionais no local. |

## <a name="deploy-user-provisioning"></a>Implementar o fornecimento de utilizadores

| Capacidade | Descrição|
| -| -|
| [Aprovisionamento de utilizadores](../app-provisioning/plan-auto-user-provisioning.md)| O Azure AD ajuda-o a automatizar a criação, a manutenção e a remoção de identidades de utilizadores em aplicações da cloud (SaaS), como o Dropbox, o Salesforce, o ServiceNow, entre outras. |
| [Fornecimento de utilizadores de Cloud HR](../app-provisioning/plan-cloud-hr-provision.md)| O fornecimento de utilizadores de Cloud HR ao Ative Directory cria uma base para a governação de identidade em curso e melhora a qualidade dos processos de negócio que dependem de dados de identidade autoritários. Utilizando esta funcionalidade com o seu produto DE RH em nuvem, como Workday ou Successfactors, pode gerir sem problemas o ciclo de vida de identidade dos colaboradores e trabalhadores contingentes configurando regras que mapeiam processos Joiner-Mover-Leaver (tais como New Hire, Terminate, Transfer) para ações de fornecimento de TI (tais como Criar, Ativar, Desativar) |

## <a name="deploy-governance-and-reporting"></a>Implementar governação e relatórios

| Capacidade | Descrição|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| A Azure AD Privileged Identity Management (PIM) ajuda-o a gerir funções administrativas privilegiadas em toda a Azure AD, recursos Azure e outros Serviços Online da Microsoft. A PIM fornece soluções como acesso just-in-time, solicitação de fluxos de trabalho de aprovação e avaliações de acesso totalmente integradas para que possa identificar, descobrir e prevenir atividades maliciosas de papéis privilegiados em tempo real. |
| [Relatórios e Monitorização](../reports-monitoring/plan-monitoring-and-reporting.md)| O design da sua solução de relatório e monitorização da AD Azure depende dos seus requisitos legais, de segurança e operacionais, bem como do seu ambiente e processos existentes. Este artigo apresenta as várias opções de design e guia-o para a estratégia de implementação certa. |

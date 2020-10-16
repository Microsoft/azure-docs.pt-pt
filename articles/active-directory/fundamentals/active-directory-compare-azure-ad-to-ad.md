---
title: Comparação entre o Active Directory e o Azure Active Directory
description: Este documento compara os Serviços de Domínio do Diretório Ativo (ADDS) ao Azure Ative Directory (AD). Ele descreve conceitos-chave em ambas as soluções de identidade e explica como é diferente ou similar.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: e71ed9655c7b195fea8a2eeeaa76d8a28717637f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89318561"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Comparação entre o Active Directory e o Azure Active Directory

O Azure Ative Directory é a próxima evolução das soluções de gestão de identidade e acesso para a nuvem. A Microsoft introduziu os Ative Directory Domain Services no Windows 2000 para dar às organizações a capacidade de gerir vários componentes e sistemas de infraestrutura no local utilizando uma única identidade por utilizador.

O Azure AD aborda esta abordagem para o próximo nível, fornecendo às organizações uma solução de Identidade como Serviço (IDaaS) para todas as suas aplicações em nuvem e no local.

A maioria dos administradores de TI estão familiarizados com os conceitos de Serviços de Domínio do Diretório Ativo. O quadro seguinte descreve as diferenças e semelhanças entre os conceitos de Ative Directory e o Azure Ative Directory.

|Conceito|Diretório Ativo (AD)|Azure Active Directory |
|:-|:-|:-|
|**Utilizadores**|||
|Provisionamento: utilizadores | As organizações criam utilizadores internos manualmente ou utilizam um sistema de provisionamento interno ou automatizado, como o Microsoft Identity Manager, para integrar-se num sistema de RH.|As organizações de AD existentes usam [o Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) para sincronizar identidades na nuvem.</br> O Azure AD adiciona suporte para criar automaticamente utilizadores a partir de [sistemas de RH em nuvem](../saas-apps/workday-tutorial.md). </br>O Azure AD pode fornecer identidades no [SCIM que permitiu que](../app-provisioning/use-scim-to-provision-users-and-groups.md) as aplicações do SaaS fornecessem automaticamente as aplicações com os detalhes necessários para permitir o acesso dos utilizadores. |
|Provisionamento: identidades externas| As organizações criam utilizadores externos manualmente como utilizadores regulares numa floresta de AD externa dedicada, resultando em sobrecargas administrativas para gerir o ciclo de vida das identidades externas (utilizadores convidados)| A Azure AD fornece uma classe especial de identidade para apoiar identidades externas. [O Azure AD B2B](/azure/active-directory/b2b/) irá gerir a ligação com a identidade externa do utilizador para se certificar de que são válidos. |
| Gestão de direitos e grupos| Os administradores fazem dos utilizadores membros de grupos. Os proprietários de aplicações e recursos dão então aos grupos acesso a apps ou recursos.| [Os grupos](./active-directory-groups-create-azure-portal.md) também estão disponíveis em Azure AD e os administradores também podem usar grupos para conceder permissões aos recursos. No Azure AD, os administradores podem atribuir a adesão a grupos manualmente ou usar uma consulta para incluir dinamicamente os utilizadores a um grupo. </br> Os administradores podem utilizar [a gestão de direitos](../governance/entitlement-management-overview.md) em Azure AD para dar aos utilizadores acesso a uma coleção de apps e recursos usando fluxos de trabalho e, se necessário, critérios baseados no tempo. |
| Gestão de administração|As organizações utilizarão uma combinação de domínios, unidades organizacionais e grupos em AD para delegar direitos administrativos para gerir o diretório e os recursos que controla.| A Azure AD fornece [funções incorporadas](./active-directory-users-assign-role-azure-portal.md) com o seu sistema de controlo de acesso baseado em funções Azure AD (Azure AD RBAC), com suporte limitado para [criar papéis personalizados](../users-groups-roles/roles-custom-overview.md) para delegar acesso privilegiado ao sistema de identidade, aplicações e recursos que controla.</br>A gestão de funções pode ser reforçada com [a Gestão de Identidade Privilegiada (PIM)](../privileged-identity-management/pim-configure.md) para fornecer acesso just-in-time, restrito ao tempo ou trabalho baseado em funções privilegiadas. |
| Gestão credencial| As credenciais no Ative Directory baseiam-se em palavras-passe, autenticação de certificados e autenticação de smartcard. As palavras-passe são geridas usando políticas de palavra-passe baseadas no comprimento da palavra-passe, na expiração e na complexidade.|O Azure AD utiliza [uma proteção](../authentication/concept-password-ban-bad.md) inteligente de senha para nuvem e no local. A proteção inclui bloqueio inteligente e bloquear frases de senha comuns e personalizadas e substituições. </br>O Azure AD aumenta significativamente a segurança [através da autenticação de vários fatores](../authentication/concept-mfa-howitworks.md) e tecnologias [sem palavras-passe,](../authentication/concept-authentication-passwordless.md) como o FIDO2. </br>O Azure AD reduz os custos de suporte fornecendo aos utilizadores um sistema [de reset de senha de autosserviço.](../authentication/concept-sspr-howitworks.md) |
| **Aplicações**|||
| Aplicativos de infraestrutura|O Ative Directy forma a base para muitos componentes de infraestruturas no local, por exemplo, DNS, DHCP, IPSec, WiFi, NPS e acesso VPN|Num novo mundo em nuvem, o Azure AD, é o novo plano de controlo para aceder a apps versus confiar em controlos de rede. Quando os utilizadores autenticarem,[o acesso condicional (CA)](../conditional-access/overview.md), controlará quais os utilizadores, terão acesso às quais as aplicações em condições exigidas.|
| Aplicativos tradicionais e legados| A maioria das aplicações no local usam LDAP, Windows-Integrated Authentication (NTLM e Kerberos), ou autenticação baseada em cabeçalho para controlar o acesso aos utilizadores.| O Azure AD pode fornecer acesso a este tipo de aplicações no local usando agentes de procuração de [aplicações AD AZure](../manage-apps/application-proxy.md) que executam no local. Utilizando este método, o Azure AD pode autenticar utilizadores do Ative Directory no local usando Kerberos enquanto migra ou precisa de coexistir com aplicações antigas. |
| Aplicações SaaS|O Ative Directory não suporta aplicações SaaS de forma nativa e requer sistema de federação, como AD FS.|As aplicações SaaS que suportam a autenticação OAuth2, SAML e WS \* podem ser integradas para utilizar a Azure AD para autenticação. |
| Aplicativos de linha de negócios (LOB) com autenticação moderna|As organizações podem usar FS AD com Ative Directory para apoiar aplicações LOB que requerem autenticação moderna.| As aplicações LOB que requerem autenticação moderna podem ser configuradas para usar a Azure AD para autenticação. |
| Serviços mid-tier/Daemon|Os serviços que funcionam em ambientes no local normalmente utilizam contas de serviçoS AD ou contas de serviço geridas (gMSA) para executar. Estas aplicações herdarão então as permissões da conta de serviço.| A Azure AD fornece [identidades geridas](../managed-identities-azure-resources/index.yml) para executar outras cargas de trabalho na nuvem. O ciclo de vida destas identidades é gerido pela Azure AD e está ligado ao fornecedor de recursos não pode ser usado para outros fins para obter acesso backdoor.|
| **Dispositivos**|||
| Telemóvel|O Ative Directory não suporta dispositivos móveis sem soluções de terceiros.| A solução de gestão de dispositivos móveis da Microsoft, a Microsoft Intune, está integrada com o Azure AD. O Microsoft Intune fornece informações do estado do dispositivo ao sistema de identidade para avaliar durante a autenticação. |
| Computadores de trabalho do Windows|O Ative Directory fornece a capacidade de se juntar a dispositivos Windows para geri-los usando a Política de Grupo, o Gestor de Configuração do Centro de Sistema ou outras soluções de terceiros.|Os dispositivos Windows podem ser [associados ao Azure AD](../devices/index.yml). O acesso condicional pode verificar se um dispositivo é Azure AD unido como parte do processo de autenticação. Os dispositivos Windows também podem ser geridos com [o Microsoft Intune](/intune/what-is-intune). Neste caso, o acesso condicional irá considerar se um dispositivo está em conformidade (por exemplo, patches de segurança atualizados e assinaturas de vírus) antes de permitir o acesso às apps.|
| Servidores Windows| O Ative Directory oferece fortes capacidades de gestão para servidores Windows no local utilizando a Política de Grupo ou outras soluções de gestão.| As máquinas virtuais dos servidores windows em Azure podem ser geridas com [serviços de domínio AD AD Azure](../../active-directory-domain-services/index.yml). [As identidades geridas](../managed-identities-azure-resources/index.yml) podem ser utilizadas quando os VMs precisam de acesso ao diretório ou recursos do sistema de identidade.|
| Cargas de trabalho Linux/Unix|O Ative Directory não suporta de forma nativa o não-Windows sem soluções de terceiros, embora as máquinas Linux possam ser configuradas para autenticar com o Ative Directory como um reino Kerberos.|Os VMs Linux/Unix podem usar [identidades geridas](../managed-identities-azure-resources/index.yml) para aceder ao sistema de identidade ou recursos. Algumas organizações migram estas cargas de trabalho para tecnologias de contentores em nuvem, que também podem usar identidades geridas.|

## <a name="next-steps"></a>Passos seguintes

- [O que é o Azure Active Directory?](./active-directory-whatis.md)
- [Compare os serviços de domínio do diretório ativo auto-geridos, o Azure Ative Directory e os serviços de domínio do diretório ativo geridos a Azure Ative Directory](../../active-directory-domain-services/compare-identity-solutions.md)
- [Perguntas frequentes sobre O Diretório Ativo Azure](./active-directory-faq.md)
- [Quais as novidades no Diretório Ativo do Azure?](./whats-new.md)
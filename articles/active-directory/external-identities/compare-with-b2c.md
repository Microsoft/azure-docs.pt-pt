---
title: Comparar identidades externas - Azure Ative Directory | Microsoft Docs
description: As identidades externas da AD AD permitem que pessoas fora da sua organização acedam às suas apps e recursos usando a sua própria identidade. Compare soluções para identidades externas, incluindo a colaboração do Azure Ative Directory B2B e do Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 03/02/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78fd91e2ba7badb1936b8603c8ed089130b29ffb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644070"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>O que são identidades externas no Diretório Ativo Azure?

Com identidades externas em Azure AD, pode permitir que pessoas fora da sua organização acedam às suas apps e recursos, deixando-as assinar em qualquer identidade que prefiram. Os seus parceiros, distribuidores, fornecedores, fornecedores e outros utilizadores convidados podem "trazer as suas próprias identidades". Quer tenham uma identidade digital corporativa ou emitida pelo governo, ou uma identidade social não gerida como o Google ou o Facebook, podem usar as suas próprias credenciais para se inscreverem. O fornecedor de identidade do utilizador externo gere a sua identidade e gere o acesso às suas apps com Azure AD para manter os seus recursos protegidos.

## <a name="external-identities-scenarios"></a>Cenários de identidades externas

As identidades externas do AD Azure focam-se menos na relação de um utilizador com a sua organização e mais na forma como o utilizador quer iniciar seduca nas suas apps e recursos. Neste contexto, a Azure AD apoia uma variedade de cenários, desde a colaboração empresa-a-empresa (B2B) até à gestão de acesso a aplicações viradas para consumidores/clientes ou cidadãos (empresa-a-cliente, ou B2C).

- **Partilhe as suas apps e recursos com utilizadores externos (colaboração B2B)**. Convide os utilizadores externos para o seu próprio inquilino como utilizadores "convidados" para que possa atribuir permissões (para autorização) enquanto os permite utilizar as suas credenciais existentes (para autenticação). Os utilizadores inscrevem-se nos recursos partilhados utilizando um simples processo de convite e resgate com o seu trabalho, escola ou outra conta de e-mail. Também pode utilizar [a gestão de direitos AD da Azure](../governance/entitlement-management-overview.md) para configurar políticas que [gerem o acesso a utilizadores externos.](../governance/entitlement-management-external-users.md#how-access-works-for-external-users) E agora com a disponibilidade de fluxos de [utilizador de inscrição de autosserviço,](self-service-sign-up-overview.md)pode permitir que utilizadores externos se inscrevam para as próprias aplicações. A experiência pode ser personalizada para permitir a inscrição com uma identidade de trabalho, escola ou social (como Google ou Facebook). Também pode recolher informações sobre o utilizador durante o processo de inscrição. Para mais informações, consulte a [documentação Azure AD B2B](index.yml).

- **Construa viagens de utilizador com uma solução de gestão de identidade de rótulo branco para aplicações viradas para o consumidor e para o cliente (Azure AD B2C)**. Se você é um negócio ou desenvolvedor que cria aplicações viradas para o cliente, você pode escalar para milhões de consumidores, clientes ou cidadãos usando Azure AD B2C. Os desenvolvedores podem usar o Azure AD como o sistema completo de Gestão de Identidade e Acesso do Cliente (CIAM) para as suas aplicações. Os clientes podem iniciar sôms com uma identidade que já estabeleceram (como o Facebook ou o Gmail). Com o Azure AD B2C, pode personalizar e controlar completamente a forma como os clientes se inscrevem, se inscrevem e gerem os seus perfis ao utilizarem as suas aplicações. Para mais informações, consulte a [documentação Azure AD B2C](../../active-directory-b2c/index.yml).

## <a name="compare-external-identities-solutions"></a>Comparar soluções de Identidades Externas

A tabela seguinte dá uma comparação detalhada dos cenários que pode permitir com identidades externas AD AZure.

|   | Colaboração externa do utilizador (B2B) | Acesso a aplicações viradas para o consumidor/cliente (B2C)  |
| ---- | --- | --- |
| **Cenário primário** | Colaboração utilizando aplicações da Microsoft (Microsoft 365, Teams, etc.) ou as suas próprias aplicações (aplicações SaaS, aplicações desenvolvidas sob medida, etc.).  | Gestão de identidade e acesso para aplicações modernas do SaaS ou de desenvolvimento personalizado (não aplicações da Microsoft de primeira).   |
| **Destina-se a**    | Colaborando com parceiros de negócios de organizações externas como fornecedores, parceiros, fornecedores. Os utilizadores aparecem como utilizadores convidados no seu diretório. Estes utilizadores podem ou não ter gerido a TI.  | Clientes do seu produto. Estes utilizadores são geridos num diretório AD Azure separado.  |
| **Fornecedores de identidade apoiados** | Os utilizadores externos podem colaborar usando contas de trabalho, contas escolares, qualquer endereço de e-mail, SAML e fornecedores de identidade baseados em WS-Fed, Gmail e Facebook.  | Utilizadores de consumidores com contas de aplicações locais (qualquer endereço de e-mail ou nome de utilizador), várias identidades sociais apoiadas e utilizadores com identidades corporativas e emitidas pelo governo através de federação direta.       |
| **Gestão externa de utilizadores**   | Os utilizadores externos são geridos no mesmo diretório que os funcionários, mas são tipicamente anotados como utilizadores convidados. Os utilizadores convidados podem ser geridos da mesma forma que os funcionários, adicionados aos mesmos grupos, e assim por diante.    | Os utilizadores externos são geridos no diretório Azure AD B2C. São geridos separadamente do diretório de empregados e parceiros da organização (se houver).  |
| **Início de sessão único (SSO)**      | O SSO para todas as aplicações ligadas a Ad Azure é suportado. Por exemplo, pode fornecer acesso a aplicações microsoft 365 ou no local, e a outras aplicações SaaS como Salesforce ou Workday.    | É suportado o SSO em aplicações pertencentes aos cliente nos inquilinos do Azure AD B2C. SSO para o Microsoft 365 ou para outras aplicações da Microsoft SaaS não é suportado.    |
| **Política de segurança e conformidade**        | Gerido pela organização de anfitrião/convite (por exemplo, com [políticas de Acesso Condicional).](conditional-access.md) | Gerido pela organização através do Acesso Condicional e Proteção de Identidade.        |
| **Imagem corporativa**  | É utilizada a marca de organização de anfitriões/convidativos.    | Marca totalmente personalizável por aplicação ou organização.   |
| **Modelo de faturação** | [Preços de identidades externas](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) com base em utilizadores ativos mensais (MAU). <br>(Ver também: [detalhes da configuração B2B)](external-identities-pricing.md) | [Preços de identidades externas](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) com base em utilizadores ativos mensais (MAU). <br>(Ver também: [detalhes da configuração B2C)](../../active-directory-b2c/billing.md) |
| **Mais informações** | [Blog post](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentação](what-is-b2b.md)                   | [Página do produto](https://azure.microsoft.com/services/active-directory-b2c/), [Documentação](../../active-directory-b2c/index.yml)       |

Proteja e gere clientes e parceiros para além dos seus limites organizacionais com identidades externas Azure AD.

## <a name="about-multitenant-applications"></a>Sobre aplicações multitenantes

Se está a fornecer uma aplicação como serviço e não quer gerir as contas de utilizador dos seus clientes, uma aplicação multitenante é provavelmente a escolha certa para si. Quando desenvolve aplicações destinadas a outros inquilinos da AD Azure, pode direcionar os utilizadores de uma única organização (inquilino único), ou utilizadores de qualquer organização que já tenha um inquilino AZure AD (aplicações multitenantes). As inscrições de aplicativos em Azure AD são inquilinos individuais por padrão, mas você pode fazer o seu registo multitenant. Esta aplicação multitenant é registada uma vez por si no seu próprio Azure AD. Mas então qualquer utilizador AZure AD de qualquer organização pode usar a aplicação sem trabalho adicional da sua parte. Para mais informações, consulte [Gerir a identidade em aplicações multitenantes](/azure/architecture/multitenant-identity/), Como [guiar.](../develop/howto-convert-app-to-be-multi-tenant.md)

## <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Acerca do Azure AD B2C](../../active-directory-b2c/overview.md)
---
title: Gerir Aplicações com o Microsoft Azure Active Directory | Microsoft Docs
description: Este artigo descreve os benefícios de integrar o Azure Ative Directory com as suas aplicações no local, cloud e SaaS.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 06/05/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee9a9d404e9a08d8b795a3d73907e84d25b73107
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739758"
---
# <a name="application-management-with-azure-active-directory"></a>Gestão de aplicações com o Azure Active Directory

O Azure Ative Directory (Azure AD) simplifica a forma como gere as suas aplicações, fornecendo um único sistema de identidade para as suas aplicações cloud e on-premises. Pode adicionar o seu software como aplicações de serviço (SaaS), aplicações no local e aplicações de linha de negócios (LOB) à Azure AD. Em seguida, os utilizadores acedem uma vez para aceder de forma segura e perfeita a estas aplicações, juntamente com o Office 365 e outras aplicações empresariais da Microsoft. Pode reduzir os custos administrativos [automatizando o fornecimento de utilizadores.](../app-provisioning/user-provisioning.md) Também pode utilizar políticas de autenticação multifactor e acesso condicional para fornecer acesso seguro à aplicação.

![Diagrama que mostra apps federadas via Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Por que razão deve gerir as aplicações com uma solução na cloud?

As organizações têm, muitas vezes, centenas de aplicações das quais os utilizadores dependem para fazerem o seu trabalho. Os utilizadores acedem a estas aplicações a partir de vários dispositivos e localizações. São adicionadas, programadas e lançadas aplicações novas todos os dias. Com tantas aplicações e pontos de acesso, é mais crítico do que nunca usar uma solução baseada na nuvem para gerir o acesso dos utilizadores a todas as aplicações.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Que tipos de aplicações posso integrar com a Azure AD?

Existem quatro tipos principais de aplicações que pode adicionar às suas **aplicações Enterprise** e gerir com a Azure AD:

- **As aplicações da Azure AD Gallery** – Azure AD dispõem de uma galeria que contém milhares de aplicações que foram pré-integradas para um único sign-on com a Azure AD. É provável que algumas das aplicações que a sua organização utiliza estão na galeria. Saiba mais sobre o planeamento da [integração da sua aplicação,](plan-an-application-integration.md)ou obtenha passos de integração detalhados para aplicações individuais nos [tutoriais de aplicações SaaS.](https://docs.microsoft.com/azure/active-directory/saas-apps/)

- **Aplicações no local com Application Proxy** – Com procuração de aplicação AD Azure, pode integrar as suas aplicações web no local com a Azure AD para suportar um único sign-on. Em seguida, os utilizadores finais podem aceder às suas aplicações web no local da mesma forma que acedem ao Office 365 e a outras aplicações SaaS. [Saiba por que usar o Proxy da Aplicação e como funciona.](what-is-application-proxy.md)

- **Aplicações desenvolvidas sob medida** – Ao construir as suas próprias aplicações de linha de negócio, pode integrá-las com a Azure AD para suportar um único sign-on. Ao registar a sua aplicação na Azure AD, tem controlo sobre a política de autenticação da aplicação. Para mais informações, consulte [a orientação para os desenvolvedores.](developer-guidance-for-integrating-applications.md)

- **Aplicações não-Galeria** – Traga as suas próprias aplicações! Suporte um único sinal para outras aplicações adicionando-as ao Azure AD. Pode integrar qualquer ligação web que pretenda, ou qualquer aplicação que torne um campo de username e password, suporte os protocolos SAML ou OpenID Connect ou suporte o SCIM. Para mais informações, consulte [o configure single sign-on para aplicações não-gallery](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Gerir o risco com políticas de Acesso Condicional

Acoplamento de um único sinal de Acesso [Conditional Access](../conditional-access/concept-conditional-access-cloud-apps.md) (SSO) azure acessar proporciona elevados níveis de segurança para aceder a aplicações. As capacidades de segurança incluem proteção de identidade em escala em nuvem, controlo de acesso baseado em risco, autenticação de vários fatores nativos e políticas de acesso condicional. Estas capacidades permitem políticas de controlo granular com base em aplicações ou em grupos que precisam de níveis mais elevados de segurança.

## <a name="improve-productivity-with-single-sign-on"></a>Aumentar a produtividade com o início de sessão único

Ativar o início de sessão único (SSO) nas aplicações e no Office 365 proporciona uma experiência de início de sessão superior para os utilizadores existentes ao reduzir ou eliminar pedidos de início de sessão. O ambiente do utilizador é mais coeso e com menos distrações, sem vários pedidos ou a necessidade de gerir várias palavras-passe. O grupo empresarial pode gerir e aprovar o acesso através da associação dinâmica e de gestão personalizada. Permitir que as pessoas certas na empresa giram o acesso a uma aplicação melhora a segurança do sistema de identidade.

O SSO melhora a segurança. *Sem o início de sessão único*, os administradores precisam de criar e atualizar as contas de utilizador para cada aplicação individual, o que demora algum tempo. Além disso, os utilizadores precisam de controlar várias credenciais para acederem às suas aplicações. Como resultado, os utilizadores tendem a anotar as palavras-passe ou utilizar outras soluções de gestão de palavras-passe que apresentam riscos de segurança de dados. [Leia mais sobre o único sinal.](what-is-single-sign-on.md)

## <a name="address-governance-and-compliance"></a>Abordar a governação e conformidade

Com o Microsoft Azure AD, pode monitorizar inícios de sessão em aplicações por meio de relatórios que tiram partido das ferramentas de Monitorização de Eventos e Incidentes de Segurança (SIEM). Pode aceder aos relatórios no portal ou em APIs. Por meio da programação de auditoria, defina quem tem acesso às suas aplicações e remova o acesso de utilizadores inativos por meio de revisões de acesso.

## <a name="manage-costs"></a>Gerir os custos

Ao migrar para o Microsoft Azure AD, pode reduzir os custos e remover a complicação de gerir a sua infraestrutura no local. O Microsoft Azure AD também proporciona acesso de gestão personalizada às aplicações, o que poupa o tempo dos administradores e utilizadores. O início de sessão único elimina as palavras-passe específicas da aplicação. Esta capacidade de iniciar sessão uma vez reduz os custos relacionados com a reposição da palavra-passe e a perda de produtividade ao obter as palavras-passe.

## <a name="next-steps"></a>Passos seguintes

- [O que é o Proxy de Aplicação?](what-is-application-proxy.md)
- [Quickstart: Adicione uma aplicação de galeria ao seu inquilino Azure AD](add-application-portal.md)

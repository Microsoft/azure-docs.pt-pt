---
title: Gerir Aplicações com o Microsoft Azure Active Directory | Microsoft Docs
description: Este artigo descreve os benefícios da integração do Azure Active Directory com os seus locais, na cloud e aplicações SaaS.
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
ms.openlocfilehash: fcd0338f9195afae0e326155dbc4e10dd4fc1f91
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723898"
---
# <a name="application-management-with-azure-active-directory"></a>Gestão de aplicações com o Microsoft Azure Active Directory

Azure Active Directory (Azure AD) simplifica a forma como gerencia seus aplicativos ao fornecer um sistema de identidade única para as suas aplicações na cloud e no local. Pode adicionar o seu software como serviço (SaaS) aplicações, aplicações no local e aplicações linha de negócio (LOB) para o Azure AD. Em seguida, os utilizadores iniciar sessão uma vez para forma segura e aceder a estas aplicações, juntamente com o Office 365 e outros aplicativos de negócios da Microsoft. Pode reduzir os custos administrativos ao automatizar o aprovisionamento de utilizadores. Também pode utilizar a autenticação multifator e políticas de acesso condicional para fornecer acesso de aplicativo seguro.

![Diagrama que mostra a aplicações federadas através do Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Por que razão deve gerir as aplicações com uma solução na cloud?

As organizações têm, muitas vezes, centenas de aplicações das quais os utilizadores dependem para fazerem o seu trabalho. Os utilizadores acedem a estas aplicações a partir de vários dispositivos e localizações. São adicionadas, programadas e lançadas aplicações novas todos os dias. Com tantos aplicativos e os pontos de acesso, é mais crítico do que nunca para utilizar uma solução baseada na nuvem para gerir o acesso de utilizador para todos os aplicativos.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Que tipos de aplicativos pode integrar com o Azure AD?

Existem quatro tipos principais de aplicações que pode adicionar a sua **aplicações empresariais** e gerir com o Azure AD:

- **As aplicações do Azure AD galeria** – o Azure AD tem uma galeria que contém a milhares de aplicações que tenham sido pré-integradas para início de sessão único com o Azure AD. É provável que algumas das aplicações que a sua organização utiliza estão na galeria. [Saiba mais sobre o planejamento de sua integração de aplicações](plan-an-application-integration.md), ou obter os passos de integração detalhadas para aplicações individuais no [tutoriais de aplicações SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **Aplicações com o Proxy de aplicações no local** – Proxy da aplicação com o Azure AD, pode integrar aplicações web no local com o Azure AD para suportar o início de sessão único. Em seguida, os utilizadores finais podem aceder a aplicações web no local da mesma forma que aceder ao Office 365 e outras aplicações SaaS. [Saiba por que usar o Proxy de aplicações e como ela funciona](what-is-application-proxy.md).

- **Aplicações com programação personalizada** – ao criar seus próprios aplicativos de linha de negócio, poderá integrá-las com o Azure AD para suportar o início de sessão único. Ao registar a sua aplicação com o Azure AD, tem controlo sobre a política de autenticação para a aplicação. Para obter mais informações, consulte [documentação de orientação para programadores](developer-guidance-for-integrating-applications.md).

- **Aplicações não à galeria** – traga os seus próprios aplicativos! Suporta início de sessão único para outras aplicações, adicionando-os para o Azure AD. Pode integrar qualquer ligação web que pretende, ou qualquer aplicativo que compõe um campo de nome de utilizador e palavra-passe, que oferece suporte a protocolos SAML ou OpenID Connect ou suporta SCIM. Para obter mais informações, consulte [configurar o início de sessão único para aplicações externas à galeria](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Gerir o risco com políticas de acesso condicional

O acoplamento do Azure AD início de sessão único (SSO) com [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) fornece altos níveis de segurança para aceder a aplicações. Capacidades de segurança incluem a proteção de identidade de escala da cloud, controlo de acesso baseado em risco, a autenticação multifator nativa e políticas de acesso condicional. Estas capacidades permitem políticas de controlo granular com base em aplicações ou em grupos que precisam de níveis mais elevados de segurança.

## <a name="improve-productivity-with-single-sign-on"></a>Aumentar a produtividade com o início de sessão único

Ativar o início de sessão único (SSO) nas aplicações e no Office 365 proporciona uma experiência de início de sessão superior para os utilizadores existentes ao reduzir ou eliminar pedidos de início de sessão. O ambiente do utilizador é mais coeso e com menos distrações, sem vários pedidos ou a necessidade de gerir várias palavras-passe. O grupo empresarial pode gerir e aprovar o acesso através da associação dinâmica e de gestão personalizada. Permitir que as pessoas certas na empresa giram o acesso a uma aplicação melhora a segurança do sistema de identidade.

O SSO melhora a segurança. *Sem o início de sessão único*, os administradores precisam de criar e atualizar as contas de utilizador para cada aplicação individual, o que demora algum tempo. Além disso, os utilizadores precisam de controlar várias credenciais para acederem às suas aplicações. Como resultado, os utilizadores tendem a anotar as palavras-passe ou utilizar outras soluções de gestão de palavras-passe que apresentam riscos de segurança de dados. [Saiba mais sobre o início de sessão único](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Abordar a governação e conformidade

Com o Microsoft Azure AD, pode monitorizar inícios de sessão em aplicações por meio de relatórios que tiram partido das ferramentas de Monitorização de Eventos e Incidentes de Segurança (SIEM). Pode aceder aos relatórios no portal ou em APIs. Por meio da programação de auditoria, defina quem tem acesso às suas aplicações e remova o acesso de utilizadores inativos por meio de revisões de acesso.

## <a name="manage-costs"></a>Gerir os custos

Ao migrar para o Microsoft Azure AD, pode reduzir os custos e remover a complicação de gerir a sua infraestrutura no local. O Microsoft Azure AD também proporciona acesso de gestão personalizada às aplicações, o que poupa o tempo dos administradores e utilizadores. O início de sessão único elimina as palavras-passe específicas da aplicação. Esta capacidade de iniciar sessão uma vez reduz os custos relacionados com a reposição da palavra-passe e a perda de produtividade ao obter as palavras-passe.

## <a name="next-steps"></a>Passos Seguintes

- [O que é o Proxy de aplicações?](what-is-application-proxy.md)
- [Quickstart: Adicionar uma aplicação da galeria com o seu inquilino do Azure AD](add-application-portal.md)

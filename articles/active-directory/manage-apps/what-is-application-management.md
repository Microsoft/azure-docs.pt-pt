---
title: Gerir Aplicações com o Microsoft Azure Active Directory | Microsoft Docs
description: Este artigo descreve os benefícios da integração de Azure Active Directory com seus aplicativos locais, de nuvem e SaaS.
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
ms.openlocfilehash: 5d1ebb4b094ab5c03343486e7af6624273288453
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421199"
---
# <a name="application-management-with-azure-active-directory"></a>Gestão de aplicações com o Microsoft Azure Active Directory

O Azure Active Directory (AD do Azure) simplifica a maneira como você gerencia seus aplicativos, fornecendo um único sistema de identidade para seus aplicativos locais e na nuvem. Você pode adicionar aplicativos SaaS (software como serviço), aplicativos locais e aplicativos LOB (linha de negócios) ao Azure AD. Em seguida, os usuários entram uma vez para acessar com segurança e perfeição esses aplicativos, juntamente com o Office 365 e outros aplicativos de negócios da Microsoft. Você pode reduzir os custos administrativos [automatizando o provisionamento de usuários](user-provisioning.md). Você também pode usar a autenticação multifator e políticas de acesso condicional para fornecer acesso seguro ao aplicativo.

![Diagrama que mostra aplicativos federados por meio do Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Por que razão deve gerir as aplicações com uma solução na cloud?

As organizações têm, muitas vezes, centenas de aplicações das quais os utilizadores dependem para fazerem o seu trabalho. Os utilizadores acedem a estas aplicações a partir de vários dispositivos e localizações. São adicionadas, programadas e lançadas aplicações novas todos os dias. Com tantos aplicativos quanto pontos de acesso, é mais crítico do que nunca usar uma solução baseada em nuvem para gerenciar o acesso do usuário a todos os aplicativos.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Que tipos de aplicativos posso integrar ao Azure AD?

Há quatro tipos principais de aplicativos que você pode adicionar a seus **aplicativos empresariais** e gerenciar com o Azure AD:

- **Aplicativos da galeria do Azure ad** – o Azure ad tem uma galeria que contém milhares de aplicativos que foram previamente integrados para logon único com o Azure AD. É provável que algumas das aplicações que a sua organização utiliza estão na galeria. [Saiba mais sobre como planejar a integração](plan-an-application-integration.md)de aplicativos ou obtenha etapas de integração detalhadas para aplicativos individuais nos [tutoriais do aplicativo SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/).

- **Aplicativos locais com o proxy de aplicativo** – com o Azure proxy de aplicativo do AD, você pode integrar seus aplicativos Web locais ao Azure ad para dar suporte ao logon único. Em seguida, os usuários finais podem acessar seus aplicativos Web locais da mesma forma que acessam o Office 365 e outros aplicativos SaaS. [Saiba por que usar o proxy de aplicativo e como ele funciona](what-is-application-proxy.md).

- **Aplicativos desenvolvidos de forma personalizada** – ao criar seus próprios aplicativos de linha de negócios, você pode integrá-los ao Azure ad para dar suporte ao logon único. Ao registrar seu aplicativo com o Azure AD, você tem controle sobre a política de autenticação para o aplicativo. Para obter mais informações, consulte [diretrizes para desenvolvedores](developer-guidance-for-integrating-applications.md).

- **Aplicativos que não são da Galeria** – traga seus próprios aplicativos! Dê suporte ao logon único para outros aplicativos adicionando-os ao Azure AD. Você pode integrar qualquer link da Web desejado ou qualquer aplicativo que renderiza um campo de nome de usuário e senha, dá suporte a protocolos SAML ou OpenID Connect, ou dá suporte a SCIM. Para obter mais informações, consulte [Configurar logon único para aplicativos que não são da Galeria](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Gerenciar riscos com políticas de acesso condicional

O acoplamento do SSO (logon único) do Azure AD com [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) fornece altos níveis de segurança para acessar aplicativos. Os recursos de segurança incluem proteção de identidade em escala de nuvem, controle de acesso baseado em risco, autenticação multifator nativa e políticas de acesso condicional. Estas capacidades permitem políticas de controlo granular com base em aplicações ou em grupos que precisam de níveis mais elevados de segurança.

## <a name="improve-productivity-with-single-sign-on"></a>Aumentar a produtividade com o início de sessão único

Ativar o início de sessão único (SSO) nas aplicações e no Office 365 proporciona uma experiência de início de sessão superior para os utilizadores existentes ao reduzir ou eliminar pedidos de início de sessão. O ambiente do utilizador é mais coeso e com menos distrações, sem vários pedidos ou a necessidade de gerir várias palavras-passe. O grupo empresarial pode gerir e aprovar o acesso através da associação dinâmica e de gestão personalizada. Permitir que as pessoas certas na empresa giram o acesso a uma aplicação melhora a segurança do sistema de identidade.

O SSO melhora a segurança. *Sem o início de sessão único*, os administradores precisam de criar e atualizar as contas de utilizador para cada aplicação individual, o que demora algum tempo. Além disso, os utilizadores precisam de controlar várias credenciais para acederem às suas aplicações. Como resultado, os utilizadores tendem a anotar as palavras-passe ou utilizar outras soluções de gestão de palavras-passe que apresentam riscos de segurança de dados. [Leia mais sobre o logon único](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Abordar a governação e conformidade

Com o Microsoft Azure AD, pode monitorizar inícios de sessão em aplicações por meio de relatórios que tiram partido das ferramentas de Monitorização de Eventos e Incidentes de Segurança (SIEM). Pode aceder aos relatórios no portal ou em APIs. Por meio da programação de auditoria, defina quem tem acesso às suas aplicações e remova o acesso de utilizadores inativos por meio de revisões de acesso.

## <a name="manage-costs"></a>Gerir os custos

Ao migrar para o Microsoft Azure AD, pode reduzir os custos e remover a complicação de gerir a sua infraestrutura no local. O Microsoft Azure AD também proporciona acesso de gestão personalizada às aplicações, o que poupa o tempo dos administradores e utilizadores. O início de sessão único elimina as palavras-passe específicas da aplicação. Esta capacidade de iniciar sessão uma vez reduz os custos relacionados com a reposição da palavra-passe e a perda de produtividade ao obter as palavras-passe.

## <a name="next-steps"></a>Passos seguintes

- [O que é o proxy de aplicativo?](what-is-application-proxy.md)
- [Quickstart: Adicionar um aplicativo de galeria ao seu locatário do Azure AD](add-application-portal.md)

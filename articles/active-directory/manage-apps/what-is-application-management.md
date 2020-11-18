---
title: O que é a gestão de aplicações no Azure Ative Directory
description: Uma visão geral da utilização do Azure Ative Directory (AD) como um sistema de Gestão de Identidade e Acesso (IAM) para as suas aplicações em nuvem e no local.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a157396318075522d5b3263c9cf0f749eafc2476
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658813"
---
# <a name="what-is-application-management"></a>O que é a gestão de aplicações?

O Azure AD é um sistema de Gestão de Identidades e Acessos (IAM). Fornece um único local para armazenar informações sobre identidades digitais. Pode configurar as suas aplicações de software para utilizar o Azure AD como o local onde as informações do utilizador são armazenadas. 

O Azure AD deve ser configurado para integrar-se com uma aplicação. Por outras palavras, precisa de saber que aplicações a estão a usar como sistema de identidade. O processo de manter a Azure AD ciente destas aplicações, e como deve lidar com elas, é conhecido como gestão de aplicações.

Gere aplicações na lâmina de **aplicações** da Enterprise localizada na secção Gestão do portal Azure Ative Directory.

![A opção de aplicações da Enterprise sob a secção Gerir do portal AZure AD.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>O que é um sistema de Gestão de Identidade e Acesso (IAM) ?
Uma aplicação é uma peça de software que é usada para algum propósito. A maioria das aplicações exige que os utilizadores entrem para que a aplicação possa fornecer uma experiência personalizada para esse utilizador em particular. Ou seja, a aplicação necessita de saber a identidade do utilizador através da aplicação. Porque sabe que funcionalidade oferecer, ou remover, para o utilizador.

Se cada aplicação acompanhasse separadamente os utilizadores, o resultado seria um silo de diferentes nomes de utilizadores e logins para cada aplicação. Uma aplicação não saberia nada sobre os utilizadores noutras aplicações.

Um sistema de identidade centralizado resolve este problema fornecendo um único local para armazenar informações dos utilizadores que podem ser usadas por todas as aplicações. Estes sistemas passaram a ser conhecidos como sistemas de Gestão de Identidade e Acesso (IAM). Azure Ative AD é o sistema IAM para a nuvem da Microsoft.

>[!TIP]
>Um sistema IAM fornece um único local para acompanhar as identidades dos utilizadores. Azure AD é o sistema IAM para a nuvem da Microsoft.


## <a name="why-manage-applications-with-a-cloud-solution"></a>Por que razão deve gerir as aplicações com uma solução na cloud?

As organizações têm, muitas vezes, centenas de aplicações das quais os utilizadores dependem para fazerem o seu trabalho. Os utilizadores acedem a estas aplicações a partir de vários dispositivos e localizações. São adicionadas, programadas e lançadas aplicações novas todos os dias. Com tantas aplicações e pontos de acesso, é mais crítico do que nunca utilizar uma solução baseada na nuvem para gerir o acesso dos utilizadores a todas as aplicações.

>[!TIP]
>A galeria de aplicações AD AZure contém muitas aplicações populares que já estão pré-configuradas para trabalhar com a Azure AD como fornecedor de identidade.

## <a name="how-does-azure-ad-work-with-applications"></a>Como funciona a Azure AD com aplicações?

O Azure AD simplifica a forma como gere as suas aplicações, fornecendo um sistema de identidade único para as suas aplicações em nuvem e no local. Pode adicionar o seu software como aplicações de serviço (SaaS), aplicações no local e aplicações de linha de negócios (LOB) ao Azure AD. Em seguida, os utilizadores insinuam-se uma vez para aceder de forma segura e perfeita a estas aplicações, juntamente com o Microsoft 365 e outras aplicações empresariais da Microsoft. Pode reduzir os custos administrativos automatizando o [fornecimento de utilizadores](../app-provisioning/user-provisioning.md). Também pode utilizar políticas de autenticação de vários fatores e de acesso condicional para fornecer acesso seguro à aplicação.

![Diagrama que mostra apps federadas via AZure AD](media/what-is-application-management/app-management-overview.png)

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Que tipo de aplicações posso integrar com a Azure AD?

Existem quatro tipos principais de aplicações que pode adicionar às suas **aplicações Enterprise** e gerir com a Azure AD:

- **Azure AD Gallery aplicações** – Azure AD tem uma galeria que contém milhares de aplicações que foram pré-integradas para um único sign-on com Azure AD. É provável que algumas das aplicações que a sua organização utiliza estão na galeria. [Saiba como planejar a integração da sua aplicação,](plan-an-application-integration.md)ou obtenha passos de integração detalhados para aplicações individuais nos [tutoriais de aplicações saaS.](/azure/active-directory/saas-apps/)

- **Aplicações no local com Aplicação Proxy** – Com Proxy aplicação AD AZure, pode integrar as suas aplicações web no local com Azure AD para suportar um único s-on. Em seguida, os utilizadores finais podem aceder às suas aplicações web no local da mesma forma que acedem ao Microsoft 365 e a outras aplicações SaaS, ver [fornecer acesso remoto a aplicações no local através do Proxy de aplicação da Azure AD.](application-proxy.md)

- **Aplicações personalizadas** – Ao construir as suas próprias aplicações de linha de negócio, pode integrá-las com Azure AD para suportar uma única sação. Ao registar a sua aplicação com a Azure AD, tem controlo sobre a política de autenticação para a aplicação. Para obter mais informações, consulte [a orientação para os desenvolvedores.](developer-guidance-for-integrating-applications.md)

- **Aplicações não-Galeria –** Traga as suas próprias aplicações! Suporte um único sinal para outras aplicações adicionando-as ao Azure AD. Existem várias formas de integrar uma aplicação, algumas delas estão listadas abaixo. Para obter mais informações, consulte [configurar o único sinal de inscrição da SAML.](configure-saml-single-sign-on.md)

>[!TIP]
>Pode integrar o Azure AD com uma aplicação mesmo que ainda não esteja pré-configurada e na galeria de aplicações. Pode **integrar o AZure AD com qualquer um** dos seguintes
> - Qualquer ligação web, ou aplicação, que torne um **nome de utilizador e um campo de palavra-passe.**
> - Qualquer aplicação que suporte **protocolos SAML ou OpenID Connect**.
> - Qualquer aplicação que suporte a norma **System for Cross-domain Identity Management (SCIM).**

## <a name="manage-risk-with-conditional-access-policies"></a>Gerir o risco com políticas de acesso condicional

O acoplamento Azure AD single sign-on (SSO) com [Acesso Condicional](../conditional-access/concept-conditional-access-cloud-apps.md) fornece altos níveis de segurança para aceder a aplicações. As capacidades de segurança incluem proteção de identidade em escala em nuvem, controlo de acesso baseado no risco, autenticação de vários fatores nativos e políticas de Acesso Condicional. Estas capacidades permitem políticas de controlo granular com base em aplicações ou em grupos que precisam de níveis mais elevados de segurança.

## <a name="improve-productivity-with-single-sign-on"></a>Aumentar a produtividade com o início de sessão único

Ativar um único sign-on (SSO) através de aplicações e o Microsoft 365 proporciona uma experiência de pré-in superior para os utilizadores existentes, reduzindo ou eliminando as solicitações de inscrição. O ambiente do utilizador é mais coeso e com menos distrações, sem vários pedidos ou a necessidade de gerir várias palavras-passe. O grupo empresarial pode gerir e aprovar o acesso através da associação dinâmica e de gestão personalizada. Permitir que as pessoas certas na empresa giram o acesso a uma aplicação melhora a segurança do sistema de identidade.

O SSO melhora a segurança. *Sem o início de sessão único*, os administradores precisam de criar e atualizar as contas de utilizador para cada aplicação individual, o que demora algum tempo. Além disso, os utilizadores precisam de controlar várias credenciais para acederem às suas aplicações. Como resultado, os utilizadores tendem a anotar as palavras-passe ou utilizar outras soluções de gestão de palavras-passe que apresentam riscos de segurança de dados. [Leia mais sobre um único sign-on](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Abordar a governação e conformidade

Com o Microsoft Azure AD, pode monitorizar inícios de sessão em aplicações por meio de relatórios que tiram partido das ferramentas de Monitorização de Eventos e Incidentes de Segurança (SIEM). Pode aceder aos relatórios no portal ou em APIs. Por meio da programação de auditoria, defina quem tem acesso às suas aplicações e remova o acesso de utilizadores inativos por meio de revisões de acesso.

## <a name="manage-costs"></a>Gerir os custos

Ao migrar para o Microsoft Azure AD, pode reduzir os custos e remover a complicação de gerir a sua infraestrutura no local. O Microsoft Azure AD também proporciona acesso de gestão personalizada às aplicações, o que poupa o tempo dos administradores e utilizadores. O início de sessão único elimina as palavras-passe específicas da aplicação. Esta capacidade de iniciar sessão uma vez reduz os custos relacionados com a reposição da palavra-passe e a perda de produtividade ao obter as palavras-passe.

Para aplicações focadas em Recursos Humanos, ou outras aplicações com um grande conjunto de utilizadores, pode aproveitar o fornecimento de App para automatizar o processo de provisionamento e desprovisionamento de utilizadores, ver O que é o provisionamento de [aplicações?](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Próximos passos

- [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)
- [Começar com a integração de aplicações](plan-an-application-integration.md)
- [Saiba como automatizar o provisionamento](../app-provisioning/user-provisioning.md)
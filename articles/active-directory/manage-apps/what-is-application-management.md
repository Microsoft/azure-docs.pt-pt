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
ms.date: 01/22/2021
ms.author: kenwith
ms.reviewer: ''
ms.openlocfilehash: ad572188ceb15a948e4242d0521b8304db45e65b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732348"
---
# <a name="what-is-application-management"></a>O que é a gestão de aplicações?

O Azure AD é um sistema de Gestão de Identidades e Acessos (IAM). Fornece um único local para armazenar informações sobre identidades digitais. Pode configurar as suas aplicações de software para utilizar o Azure AD como o local onde as informações do utilizador são armazenadas. 

O Azure AD deve ser configurado para integrar-se com uma aplicação. Por outras palavras, precisa de saber que aplicações estão a usá-lo para identidades. Fazer com que a AZure AD saiba destas aplicações, e como deve lidar com as mesmos, é conhecida como gestão de aplicações.

Gere aplicações na página de **aplicações** da Enterprise localizada na secção Gestão do portal Azure Ative Directory.

![A opção de aplicações da Enterprise sob a secção Gerir do portal AZure AD.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>O que é um sistema de Gestão de Identidade e Acesso (IAM) ?
Uma aplicação é uma peça de software que é usada para algum propósito. A maioria das aplicações exige que os utilizadores entrem.

Um sistema de identidade centralizado fornece um único local para armazenar informações do utilizador que podem ser usadas por todas as aplicações. Estes sistemas passaram a ser conhecidos como sistemas de Gestão de Identidade e Acesso (IAM). O Azure Ative Directory é o sistema IAM para a nuvem microsoft.

>[!TIP]
>Um sistema IAM fornece um único local para acompanhar as identidades dos utilizadores. Azure AD é o sistema IAM para a nuvem da Microsoft.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Por que razão deve gerir as aplicações com uma solução na cloud?

As organizações têm, muitas vezes, centenas de aplicações das quais os utilizadores dependem para fazerem o seu trabalho. Os utilizadores acedem a estas aplicações a partir de vários dispositivos e localizações. Novas aplicações são adicionadas, desenvolvidas e pôr-do-sol a toda a hora. Com tantas apps e pontos de acesso, é importante usar uma solução de identidade que funcione com todas elas.

>[!TIP]
>A galeria de aplicações AD AZure contém muitas aplicações populares que já estão pré-configuradas para trabalhar com a Azure AD como fornecedor de identidade.

## <a name="how-does-azure-ad-work-with-apps"></a>Como funciona a Azure AD com apps?

A Azure AD fica no meio e fornece gestão de identidade para aplicações em nuvem e no local. 

![Diagrama que mostra apps federadas via AZure AD](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Reduza os custos administrativos [automatizando o fornecimento de utilizadores](../app-provisioning/user-provisioning.md) para que os utilizadores sejam automaticamente adicionados ao Azure AD quando os adicionar ao sistema de RH da sua empresa. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Que tipo de aplicações posso integrar com a Azure AD?

Você pode usar a Azure AD como o seu sistema de identidade para qualquer aplicação. Muitas aplicações já estão pré-configuradas e podem ser configuradas com o mínimo de esforço. Estas aplicações pré-configuradas são publicadas na Galeria de [Aplicações AD AZure.](/azure/active-directory/saas-apps/) 

Pode configurar manualmente a maioria das aplicações para um único sign-on se ainda não estiverem na galeria. A Azure AD fornece várias opções SSO. Alguns dos mais populares são SSO baseado em SAML e SSO baseado em OIDC. Para saber mais sobre a integração de apps para ativar o SSO, consulte [as opções de inscrição únicas.](sso-options.md) 

A sua organização utiliza aplicações no local? Pode integrá-los usando app Proxy. Para saber mais, consulte [fornecer acesso remoto a aplicações no local através do Proxy de Aplicação da Azure AD.](application-proxy.md)

>[!TIP]
>Ao construir as suas próprias aplicações de linha de negócio, pode integrá-las com a Azure AD para suportar uma única inscrição. Para saber mais sobre o desenvolvimento de apps para Azure AD, consulte a [plataforma de identidade da Microsoft.](..//develop/v2-overview.md)

## <a name="manage-risk-with-conditional-access-policies"></a>Gerir o risco com políticas de acesso condicional

O acoplamento Azure AD single sign-on (SSO) com [Acesso Condicional](../conditional-access/concept-conditional-access-cloud-apps.md) fornece altos níveis de segurança para aceder a aplicações. As políticas de Acesso Condicional fornecem controlo granular a aplicações com base nas condições definidas. 

## <a name="improve-productivity-with-single-sign-on"></a>Aumentar a produtividade com o início de sessão único

O sign-on único (SSO) proporciona uma experiência unificada de utilizador entre o Microsoft 365 e todas as outras aplicações que utiliza. Diga adeus a inserir constantemente o seu nome de utilizador e senha!

Para saber mais sobre um único sign-on, veja [o que é um único sign-on](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Abordar a governação e conformidade

Monitorize as aplicações através de relatórios que utilizam ferramentas de Monitorização de Incidentes de Segurança e Monitorização de Eventos (SIEM). Pode aceder aos relatórios no portal ou em APIs. Por meio da programação de auditoria, defina quem tem acesso às suas aplicações e remova o acesso de utilizadores inativos por meio de revisões de acesso.

## <a name="manage-costs"></a>Gerir os custos

Ao migrar para o Microsoft Azure AD, pode reduzir os custos e remover a complicação de gerir a sua infraestrutura no local. O Microsoft Azure AD também proporciona acesso de gestão personalizada às aplicações, o que poupa o tempo dos administradores e utilizadores. O início de sessão único elimina as palavras-passe específicas da aplicação. Esta capacidade de iniciar sessão uma vez reduz os custos relacionados com a reposição da palavra-passe e a perda de produtividade ao obter as palavras-passe.

Para aplicações focadas em Recursos Humanos, ou outras aplicações com um grande conjunto de utilizadores, você pode usar o fornecimento de aplicativos para facilitar a sua vida. A aplicação automatiza o processo de adição e remoção dos utilizadores. Para saber mais, veja [o que é o provisionamento da aplicação?](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Próximos passos

- [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)
- [Começar com a integração de aplicações](plan-an-application-integration.md)
- [Saiba como automatizar o provisionamento](../app-provisioning/user-provisioning.md)
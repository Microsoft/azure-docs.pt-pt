---
title: Construa resiliência utilizando estados de dispositivo no Azure Ative Directory
description: Um guia para arquitetos e administradores de TI para construir resiliência usando estados de dispositivo
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4075cb71cb4dae7ac506c16e3987070cfa10bb09
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919857"
---
# <a name="build-resilience-with-device-states"></a>Construir resiliência com estados de dispositivo

Ao permitir que [os estados do dispositivo](../devices/overview.md) com Azure AD, os administradores podem autorizar políticas de Acesso [Condicional](../conditional-access/overview.md) que controlam o acesso a aplicações com base no estado do dispositivo. O benefício adicional dos dispositivos é que satisfaz requisitos de autenticação forte para o acesso aos recursos, reduzindo assim os pedidos adicionais de autenticação de MFA e melhorando a resiliência. 

O gráfico de fluxo a seguir apresenta as diferentes formas de embarcar dispositivos em Azure AD que permitem os estados do dispositivo. Pode usar mais do que um na sua organização.

![gráfico de fluxo para escolher estados de dispositivo](./media/resilience-with-device-states/admin-resilience-devices.png)

Quando utilizar [os estados do dispositivo,](../devices/overview.md)os utilizadores experimentarão, na maioria dos casos, um único sinal de inscrição nos recursos através de um [Token de Atualização Primária](../devices/concept-primary-refresh-token.md) (PRT). O PRT contém reclamações sobre o utilizador e o dispositivo e pode ser usado para obter fichas de autenticação para aceder a aplicações a partir do dispositivo. O PRT é válido por 14 dias e é continuamente renovado desde que o utilizador utilize ativamente o dispositivo, proporcionando aos utilizadores uma experiência resiliente. Um PRT também pode obter uma reivindicação de autenticação multi-factor de várias maneiras. Para obter mais informações, consulte [Quando é que um PRT obtém uma reclamação de MFA](../devices/concept-primary-refresh-token.md).

## <a name="how-do-device-states-help"></a>Como é que os estados dos dispositivos ajudam?

Quando um PRT é usado para solicitar o acesso a uma aplicação, o seu dispositivo, sessão e alegações de MFA são confiáveis pela Azure AD. Quando os administradores criam políticas que requerem um controlo baseado no dispositivo ou um controlo de autenticação multi-factor, então o requisito da política pode ser cumprido através do seu estado de dispositivo sem tentar a autenticação de vários fatores. Os utilizadores não verão pedidos de autenticação multi-factor adicionais no mesmo dispositivo. Isto aumenta a resiliência a uma rutura do serviço Azure MFA, ou às suas dependências, como os fornecedores locais de telecomunicações.

## <a name="how-do-i-implement-device-states"></a>Como implemento estados de dispositivos?

* Ativar [a Azure AD Join](../devices/hybrid-azuread-join-plan.md) e [Azure AD Join](../devices/azureadjoin-plan.md) híbridas para dispositivos Windows da empresa, e exigir que sejam unidos se possível. Se não for possível, exija que sejam registados.

  Se existirem versões mais antigas do Windows na sua organização, atualize estes dispositivos para utilizar o Windows 10.

* Standardize o acesso do navegador do utilizador para usar o [Microsoft Edge](https://docs.microsoft.com/deployedge/microsoft-edge-security-identity) ou o Google Chrome com [extensões](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) [suportadas](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) que permitiram que sSO sem costura para aplicações web usando o PRT.

* Para dispositivos pessoais ou empresas iOS e Android implementam a [App Microsoft Authenticator](../user-help/user-help-auth-app-overview.md). Além da autenticação multi-fator e do sinal sem palavra-passe, a aplicação Microsoft Authenticator irá permitir um único sinal através de uma aplicação nativa através de [autenticação intermediada](../develop/brokered-auth.md) com menos pedidos de autenticação para os utilizadores finais.

* Para dispositivos iOS e Android de propriedade pessoal ou empresa, utilize [a gestão de aplicações móveis](https://docs.microsoft.com/mem/intune/apps/app-management.md) para aceder de forma segura aos recursos da empresa com menos pedidos de autenticação. 

* [Utilize o plug-in Microsoft Enterprise SSO para dispositivos Apple (pré-visualização)](../develop/apple-sso-plugin.md). Este regista o dispositivo e fornece SSO em todo o navegador e aplicações AD nativas do Azure. 

## <a name="next-steps"></a>Passos seguintes
Recursos de resiliência para administradores e arquitetos
 
* [Construir resiliência com gestão credencial](resilience-in-credentials.md)

* [Construa resiliência utilizando a Avaliação contínua de Acesso (CAE)](resilience-with-continuous-access-evaluation.md)

* [Construir resiliência na autenticação externa do utilizador](resilience-b2b-authentication.md)

* [Construa resiliência na sua autenticação híbrida](resilience-in-hybrid.md)

* [Criar resiliência no acesso à aplicação com Aplicação Proxy](resilience-on-premises-access.md)


Recursos de resiliência para desenvolvedores

* [Construa resiliência do IAM nas suas aplicações](resilience-app-development-overview.md)

* [Construa resiliência nos seus sistemas CIAM](resilience-b2c.md)

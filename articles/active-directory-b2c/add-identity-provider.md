---
title: Adicione um fornecedor de identidade - Azure Ative Directory B2C | Microsoft Docs
description: Saiba como adicionar um fornecedor de identidade ao seu inquilino Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 03/03/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: dbb2aeefc22fae4a3c83956dd7681b49fc435500
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034001"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Adicione um fornecedor de identidade ao seu inquilino Azure Ative Directory B2C

Pode configurar o Azure AD B2C para permitir que os utilizadores entrem na sua aplicação com credenciais de fornecedores externos de identidade social ou empresarial (IdP). O Azure AD B2C suporta fornecedores de identidade externos como os protocolos Facebook, Microsoft, Google, Twitter e qualquer fornecedor de identidade que suporte protocolos OAuth 1.0, OAuth 2.0, OpenID Connect e SAML.

Com a federação externa de fornecedores de identidade, pode oferecer aos seus consumidores a capacidade de assinar com as suas contas sociais ou empresariais existentes, sem ter de criar uma nova conta apenas para a sua aplicação.

Na página de inscrição ou inscrição, o Azure AD B2C apresenta uma lista de fornecedores de identidade externos que o utilizador pode escolher para iniciar sação. Assim que selecionarem um dos fornecedores de identidade externos, são levados (redirecionados) para o website do fornecedor selecionado para completar o sinal em processo. Depois de o utilizador se ter apresentado com sucesso, são devolvidos ao Azure AD B2C para autenticação da conta na sua aplicação.

![Exemplo de acesso móvel com uma conta social (Facebook)](media/add-identity-provider/external-idp.png)

Pode adicionar fornecedores de identidade que são suportados pelo Azure Ative Directory B2C (Azure AD B2C) aos [fluxos](user-flow-overview.md) do seu utilizador utilizando o portal Azure. Também pode adicionar fornecedores de identidade às suas [políticas personalizadas.](custom-policy-get-started.md)

## <a name="select-an-identity-provider"></a>Selecione um fornecedor de identidade

Normalmente, usa apenas um fornecedor de identidade nas suas aplicações, mas tem a opção de adicionar mais. Os artigos abaixo mostram como criar a aplicação do fornecedor de identidade, adicionar o fornecedor de identidade ao seu inquilino e adicionar o fornecedor de identidade ao seu fluxo de utilizador ou política personalizada.

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Apple](identity-provider-apple-id.md)
* [Azure AD (Inquilino único)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (Multi-inquilino)](identity-provider-azure-ad-multi-tenant.md)
* [Azure AD B2C](identity-provider-azure-ad-b2c.md)
* [Facebook](identity-provider-facebook.md)
* [Fornecedores de identidade genéricos](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Conta Microsoft](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Salesforce (protocolo SAML)](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)

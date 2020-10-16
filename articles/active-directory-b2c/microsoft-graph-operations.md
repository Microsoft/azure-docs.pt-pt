---
title: Operações suportadas no Microsoft Graph
titleSuffix: Azure AD B2C
description: Um índice das operações do Microsoft Graph suportado para a gestão de recursos Azure AD B2C, incluindo utilizadores, fluxos de utilizadores, fornecedores de identidade, políticas personalizadas, chaves de política, entre outros.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d8d898d3825fa40cbfd13337067c24cf14f9a544
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102022"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Operações do Microsoft Graph disponíveis para Azure AD B2C

As seguintes operações de API do Microsoft Graph são suportadas para a gestão de recursos Azure AD B2C, incluindo utilizadores, fornecedores de identidade, fluxos de utilizadores, políticas personalizadas e chaves de política.

Cada link nas seguintes secções tem como alvo a página correspondente na referência API do Microsoft Graph para essa operação.

## <a name="user-management"></a>Gestão de utilizadores

- [Listar utilizadores](https://docs.microsoft.com/graph/api/user-list)
- [Criar um consumidor](https://docs.microsoft.com/graph/api/user-post-users)
- [Obter um utilizador](https://docs.microsoft.com/graph/api/user-get)
- [Atualizar um utilizador](https://docs.microsoft.com/graph/api/user-update)
- [Eliminar um utilizador](https://docs.microsoft.com/graph/api/user-delete)

Para obter mais informações sobre a gestão das contas de utilizadores do Azure AD B2C com a API do Gráfico microsoft, consulte [contas de utilizadores do Ad B2C do Manage AD B2C com o Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="user-phone-number-management"></a>Gestão de números de telefone do utilizador

- [Adicionar](https://docs.microsoft.com/graph/api/authentication-post-phonemethods)
- [Obter](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get)
- [Atualizar](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update)
- [Eliminar](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete)

Para obter mais informações sobre a gestão do número de telefone de inscrição do utilizador com a API do Gráfico Microsoft, consulte [métodos de autenticação B2C](https://docs.microsoft.com/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Fornecedores de identidade (fluxo de utilizador)

Gerencie os fornecedores de identidade disponíveis para os fluxos do seu utilizador no seu inquilino Azure AD B2C.

- [Fornecedores de identidade registados no inquilino Azure AD B2C](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Criar um fornecedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Obter um fornecedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Atualizar fornecedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Eliminar um fornecedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Fluxo de utilizador

Configure as políticas pré-construídas para inscrição, inscrição, inscrição combinada e inscrição, reset de palavra-passe e atualização de perfil.

- [Listar fluxos de utilizadores](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Criar um fluxo de utilizador](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Obtenha um fluxo de utilizador](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Eliminar um fluxo de utilizador](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Políticas personalizadas

As seguintes operações permitem-lhe gerir as suas políticas Azure AD B2C Trust Framework, conhecidas como [políticas personalizadas.](custom-policy-overview.md)

- [Listar todas as políticas de enquadramento de confiança configuradas num inquilino](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Criar política de quadros de confiança](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Leia as propriedades de uma política de enquadramento de confiança existente](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Atualizar ou criar uma política de enquadramento de confiança.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Eliminar uma política-quadro de confiança existente](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Chaves da política

O Quadro de Experiência de Identidade armazena os segredos referenciados numa política personalizada para estabelecer confiança entre componentes. Estes segredos podem ser chaves/valores simétricos ou assimétricos. No portal Azure, estas entidades são mostradas como **chaves de Política**.

O recurso de alto nível para chaves de política na API do Gráfico microsoft é o [Conjunto de Chaves Quadros Fidedignos](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Cada **tecla** contém pelo menos uma **chave.** Para criar uma chave, primeiro crie um tecla vazio e, em seguida, gere uma chave no tecla. Pode criar um segredo manual, fazer upload de um certificado ou uma chave PKCS12. A chave pode ser um segredo gerado, uma cadeia que você define (como o segredo da aplicação do Facebook), ou um certificado que você carrega. Se um conjunto de teclas tiver várias teclas, apenas uma das teclas está ativa.

### <a name="trust-framework-policy-keyset"></a>Conjunto de chave de política de quadro de confiança

- [Listar os conjuntos de chaves-quadro de confiança](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Criar conjuntos de chaves de quadro de confiança](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Obter um tecla](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Atualizar os chaves-chave do quadro de confiança](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Eliminar os teclas-quadro de confiança](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Chave da política do Quadro de Confiança

- [Obtenha a chave atualmente ativa no teclado](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Gerar uma chave no tecla](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Carre faça upload de um segredo baseado em cordas](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Faça upload de um certificado X.509](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Faça upload de um certificado de formato PKCS12](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplicações

- [Listar aplicações](https://docs.microsoft.com/graph/api/application-list)
- [Criar uma aplicação](https://docs.microsoft.com/graph/api/resources/application)
- [Aplicação de atualização](https://docs.microsoft.com/graph/api/application-update)
- [Criar serviçoPrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Criar bolsa de oauth2Permission](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Excluir aplicação](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Propriedades de extensão de aplicação

- [Propriedades de extensão de lista](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

O Azure AD B2C fornece um diretório que pode conter 100 atributos personalizados por utilizador. Para os fluxos de utilizador, estas propriedades de extensão são [geridas através do portal Azure](custom-policy-custom-attributes.md). Para políticas personalizadas, o Azure AD B2C cria o imóvel para si, a primeira vez que a apólice escreve um valor para a propriedade de extensão.

## <a name="audit-logs"></a>Registos de auditoria

- [Listar registos de auditoria](https://docs.microsoft.com/graph/api/directoryaudit-list)

Para obter mais informações sobre o acesso aos registos de auditoria do Azure AD B2C com a Microsoft Graph API, consulte os registos de auditoria do [Azure AD B2C](view-audit-logs.md).

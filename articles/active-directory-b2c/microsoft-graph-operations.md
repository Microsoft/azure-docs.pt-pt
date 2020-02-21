---
title: Operações suportadas pelo Microsoft Graph
titleSuffix: Azure AD B2C
description: Um índice das operações do Microsoft Graph suportado para a gestão de recursos Azure AD B2C, incluindo utilizadores, fluxos de utilizadores, fornecedores de identidade, políticas personalizadas, chaves de política, e muito mais.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1f23d3ffa14deccae9825568dee2f3c8b9ac7e7e
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492958"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Operações do Microsoft Graph disponíveis para Azure AD B2C

As seguintes operações da Microsoft Graph API são suportadas para a gestão de recursos Azure AD B2C, incluindo utilizadores, fornecedores de identidade, fluxos de utilizadores, políticas personalizadas e chaves de política.

Cada link nas seguintes secções visa a página correspondente dentro da referência API do Microsoft Graph Para essa operação.

## <a name="user-management"></a>Gestão de utilizadores

- [Listar utilizadores](https://docs.microsoft.com/graph/api/user-list)
- [Criar um utilizador de consumo](https://docs.microsoft.com/graph/api/user-post-users)
- [Obter um utilizador](https://docs.microsoft.com/graph/api/user-get)
- [Atualizar um utilizador](https://docs.microsoft.com/graph/api/user-update)
- [Eliminar um utilizador](https://docs.microsoft.com/graph/api/user-delete)

Para obter mais informações sobre a gestão das contas de utilizadores do Azure AD B2C com o Microsoft Graph API, consulte gerir as contas de utilizador do [Azure AD AD B2C com](manage-user-accounts-graph-api.md)o Microsoft Graph .

## <a name="identity-providers-user-flow"></a>Fornecedores de identidade (fluxo de utilizador)

Gerencie os fornecedores de identidade disponíveis para os fluxos de utilizador no seu inquilino Azure AD B2C.

- [Lista de fornecedores de identidade registados no inquilino Azure AD B2C](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Criar um fornecedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Obter um fornecedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Atualizar fornecedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Eliminar um fornecedor de identidade](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Fluxo de utilizador

Configure as políticas pré-construídas para iniciar sessão, iniciar o pré-início, iniciar o comução combinada e inscrição, reset de palavra-passe e atualização de perfil.

- [Lista de fluxos de utilizadores](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Criar um fluxo de utilizador](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Obtenha um fluxo de utilizador](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Eliminar um fluxo de utilizador](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Políticas personalizadas

As seguintes operações permitem-lhe gerir as suas políticas de enquadramento fidedignidade Azure AD B2C, conhecidas como [políticas personalizadas.](custom-policy-overview.md)

- [Enumerar todas as políticas de enquadramento de confiança configuradas num inquilino](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies.md)
- [Criar uma política de quadro de confiança](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Leia as propriedades de uma política de quadro de confiança existente](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Atualizar ou criar uma política de enquadramento de confiança.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Eliminar uma política de quadro de confiança existente](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Chaves políticas

O Quadro de Experiência de Identidade armazena os segredos referenciados numa política personalizada para estabelecer a confiança entre componentes. Estes segredos podem ser chaves/valores simétricas ou assimétricas. No portal Azure, estas entidades são mostradas como **chaves políticas.**

O recurso de alto nível para teclas de política na API do Microsoft Graph é o [Keyset Quadro Confiável](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Cada **Tecla** contém pelo menos uma **chave**. Para criar uma chave, primeiro crie um teclado vazio e, em seguida, gere uma chave no teclado. Pode criar um segredo manual, fazer upload de um certificado ou uma chave PKCS12. A chave pode ser um segredo gerado, uma cadeia que define (como o segredo da aplicação do Facebook) ou um certificado que você carrega. Se um teclado tiver várias teclas, apenas uma das teclas está ativa.

### <a name="trust-framework-policy-keyset"></a>Conjunto de chaves de política de quadro de confiança

- [Enumerar os conjuntos de quadros de confiança](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Criar um quadro de confiança conjuntos](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Obtenha um teclado](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Atualizar um quadro de confiança conjuntos](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Eliminar um conjunto de chaves de quadro de confiança](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Chave da política do quadro de confiança

- [Obtenha achave ativa atualmente no teclado](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Gerar uma chave no teclado](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Faça upload de um segredo baseado em cordas](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Faça upload de um certificado X.509](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Faça upload de um certificado de formato PKCS12](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplicações

- [Listar aplicações](https://docs.microsoft.com/graph/api/application-list)
- [Criar uma aplicação](https://docs.microsoft.com/graph/api/resources/application)
- [Aplicação de atualização](https://docs.microsoft.com/graph/api/application-update)
- [Criar serviçoPrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Criar oauth2Permission Grant](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Excluir aplicação](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Propriedades de extensão de aplicação

- [Propriedades de extensão de lista](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

O Azure AD B2C fornece um diretório que pode conter 100 atributos personalizados por utilizador. Para os fluxos de utilizadores, estas propriedades de extensão são [geridas utilizando o portal Azure](custom-policy-custom-attributes.md). Para políticas personalizadas, o Azure AD B2C cria a propriedade para si na primeira vez que a apólice escreve um valor para a propriedade de extensão.

## <a name="audit-logs"></a>Registos de auditoria

- [Registos de auditoria de lista](https://docs.microsoft.com/graph/api/directoryaudit-list)

Para obter mais informações sobre o acesso aos registos de auditoria do Azure AD B2C com a Microsoft Graph API, consulte aceder aos registos de auditoria do [Azure AD B2C](view-audit-logs.md).

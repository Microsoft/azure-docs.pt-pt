---
title: Personalize as reclamações de aplicativos de inquilino Azure AD (PowerShell)
titleSuffix: Microsoft identity platform
description: Esta página descreve o Azure Ative Directory alega mapeamento.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: e77155f8a6efd3916ae90fcb562d688bb5b5126f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598895"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Como: Personalizar reclamações emitidas em fichas para uma aplicação específica num inquilino (Preview)

> [!NOTE]
> Esta funcionalidade substitui e substitui a personalização de [reclamações](active-directory-saml-claims-customization.md) oferecida através do portal de hoje. Na mesma aplicação, se personalizar as reclamações utilizando o portal para além do método Gráfico/PowerShell detalhado neste documento, os tokens emitidos para essa aplicação ignorarão a configuração no portal. As configurações es feitas através dos métodos detalhados neste documento não serão refletidas no portal.

> [!NOTE]
> Esta capacidade encontra-se atualmente em visualização pública. Esteja preparado para reverter ou remover todas as alterações. A funcionalidade está disponível em qualquer subscrição do Azure Ative Directory (Azure AD) durante a pré-visualização pública. No entanto, quando a funcionalidade se torna geralmente disponível, alguns aspetos da funcionalidade podem necessitar de uma subscrição premium AZURE. Esta funcionalidade suporta a configuração de políticas de mapeamento de reclamações para os protocolos WS-Fed, SAML, OAuth e OpenID Connect.

Esta funcionalidade é utilizada pelos administradores inquilinos para personalizar as reclamações emitidas em fichas para uma aplicação específica no seu inquilino. Pode utilizar políticas de mapeamento de reclamações para:

- Selecione quais as reclamações incluídas em fichas.
- Crie tipos de reclamação que já não existem.
- Escolha ou altere a fonte de dados emitidos em reclamações específicas.

Neste artigo, passamos por alguns cenários comuns que podem ajudá-lo a compreender como usar o [tipo de política de mapeamento de sinistros.](reference-claims-mapping-policy-type.md)

Ao criar uma política de mapeamento de reclamações, também pode emitir uma reclamação a partir de um atributo de extensão de esquema de diretório em fichas. Utilize *extensionID* para o atributo de extensão em vez de *ID* no `ClaimsSchema` elemento.  Para obter mais informações sobre os atributos de extensão, consulte [utilizando atributos de extensão de esquema de diretório](active-directory-schema-extensions.md).

## <a name="prerequisites"></a>Pré-requisitos

Nos exemplos seguintes, cria, atualiza, liga e elimina políticas para os principais serviços. As políticas de mapeamento de sinistros só podem ser atribuídas a objetos principais de serviço. Se você é novo no AZure AD, recomendamos que você [aprenda sobre como obter um inquilino AZure AD](quickstart-create-new-tenant.md) antes de prosseguir com estes exemplos.

Para começar, faça os seguintes passos:

1. Descarregue o mais recente lançamento público do [Módulo Ad PowerShell Azure.](https://www.powershellgallery.com/packages/AzureADPreview)
1. Executar o comando 'Ligar' para iniciar súm na sua conta de administração Azure AD. Executar este comando cada vez que começar uma nova sessão.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Para ver todas as políticas que foram criadas na sua organização, executar o seguinte comando. Recomendamos que comande este comando após a maioria das operações nos seguintes cenários, para verificar se as suas políticas estão a ser criadas como esperado.

   ``` powershell
   Get-AzureADPolicy
   ```

## <a name="omit-the-basic-claims-from-tokens"></a>Omitir as alegações básicas de tokens

Neste exemplo, cria-se uma política que elimina a [alegação básica definida](reference-claims-mapping-policy-type.md#claim-sets) de fichas emitidas a princípios de serviços ligados.

1. Crie uma política de mapeamento de reclamações. Esta política, ligada a princípios de serviço específicos, elimina o conjunto de alegações básicas dos tokens.
   1. Para criar a política, executar este comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Para ver a sua nova política, e para obter a política ObjectId, executar o seguinte comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o ObjectId do seu principal de serviço.
   1. Para ver todos os principais de serviço da sua organização, pode [consultar a Microsoft Graph API](/graph/traverse-the-graph). Ou, no [Microsoft Graph Explorer,](https://developer.microsoft.com/graph/graph-explorer)inscreva-se na sua conta AZure AD.
   2. Quando tiver o ObjectId do seu principal de serviço, executar o seguinte comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>Inclua o EmployeeID e o TenantCountry como reclamações em fichas

Neste exemplo, cria-se uma política que adiciona o StaffID e o TenantCountry aos tokens emitidos aos principais de serviços ligados. O EmployeeID é emitido como o tipo de reclamação de nome tanto em fichas SAML como JWTs. O TenantCountry é emitido como o tipo de reivindicação país/região em fichas SAML e JWTs. Neste exemplo, continuamos a incluir as alegações básicas fixadas nos tokens.

1. Crie uma política de mapeamento de reclamações. Esta política, ligada a princípios de serviço específicos, adiciona o StaffID e o TenantCountry reclama a tokens.
   1. Para criar a apólice, executar o seguinte comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Para ver a sua nova política, e para obter a política ObjectId, executar o seguinte comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o ObjectId do seu principal de serviço.
   1. Para ver todos os principais de serviço da sua organização, pode [consultar a Microsoft Graph API](/graph/traverse-the-graph). Ou, no [Microsoft Graph Explorer,](https://developer.microsoft.com/graph/graph-explorer)inscreva-se na sua conta AZure AD.
   2. Quando tiver o ObjectId do seu principal de serviço, executar o seguinte comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>Use uma transformação de sinistros em fichas

Neste exemplo, cria-se uma política que emite uma reivindicação personalizada "JoinedData" a JWTs emitida aos principais de serviços ligados. Esta alegação contém um valor criado juntando os dados armazenados no atributo extensionattribute1 no objeto do utilizador com ".sandbox". Neste exemplo, excluímos as alegações básicas fixadas nos tokens.

1. Crie uma política de mapeamento de reclamações. Esta política, ligada a princípios de serviço específicos, adiciona o StaffID e o TenantCountry reclama a tokens.
   1. Para criar a apólice, executar o seguinte comando:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Para ver a sua nova política, e para obter a política ObjectId, executar o seguinte comando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribua a apólice ao seu diretor de serviço. Também precisa de obter o ObjectId do seu principal de serviço.
   1. Para ver todos os principais de serviço da sua organização, pode [consultar a Microsoft Graph API](/graph/traverse-the-graph). Ou, no [Microsoft Graph Explorer,](https://developer.microsoft.com/graph/graph-explorer)inscreva-se na sua conta AZure AD.
   2. Quando tiver o ObjectId do seu principal de serviço, executar o seguinte comando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Considerações de segurança

As aplicações que recebem fichas baseiam-se no facto de os valores de reclamação serem emitidos de forma autoritária pela Azure AD e não poderem ser adulterados. No entanto, quando modificar o conteúdo do token através de políticas de mapeamento de sinistros, estes pressupostos podem deixar de estar corretos. As aplicações devem reconhecer explicitamente que os tokens foram modificados pelo criador da política de mapeamento de sinistros para se protegerem das políticas de mapeamento de sinistros criadas por atores mal-intencionados. Isto pode ser feito das seguintes formas:

- Configurar uma chave de assinatura personalizada
- Atualizar o manifesto de aplicação para aceitar reclamações mapeadas.
 
Sem isso, a Azure AD devolverá um [ `AADSTS50146` código de erro](reference-aadsts-error-codes.md#aadsts-error-codes).

### <a name="custom-signing-key"></a>Chave de assinatura personalizada

Para adicionar uma chave de assinatura personalizada ao objeto principal de serviço, pode utilizar o cmdlet Azure PowerShell [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) para criar uma credencial chave de certificado para o seu objeto Aplicação.

As aplicações que tenham pedido mapeamento de sinistros habilitadas devem validar as suas chaves de assinatura simbólicas, anexando `appid={client_id}` os seus [pedidos de metadados OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Abaixo está o formato do documento de metadados OpenID Connect que deve utilizar:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Atualizar o manifesto de aplicação

Em alternativa, pode definir o `acceptMappedClaims` imóvel `true` no manifesto de [aplicação.](reference-app-manifest.md) Como documentado no [tipo de recurso apiApplication,](/graph/api/resources/apiapplication#properties)isto permite que uma aplicação utilize mapeamento de sinistros sem especificar uma chave de assinatura personalizada.

Isto requer que o público simbólico solicitado utilize um nome de domínio verificado do seu inquilino AZure AD, o que significa que deve garantir a definição `Application ID URI` do (representado pelo `identifierUris` manifesto de aplicação) por exemplo para `https://contoso.com/my-api` ou (simplesmente usando o nome de inquilino padrão) `https://contoso.onmicrosoft.com/my-api` .

Se não estiver a utilizar um domínio verificado, o Azure AD devolverá um código de `AADSTS501461` erro com a mensagem *"AcceptMappedClaims é suportado apenas para um público simbólico que corresponda à aplicação GUID ou a uma audiência dentro dos domínios verificados do inquilino. Ou altera o identificador de recursos ou usa uma chave de assinatura específica para aplicações."*

## <a name="next-steps"></a>Passos seguintes

- Leia o artigo de referência [do tipo de referência do tipo de mapeamento de reclamações](reference-claims-mapping-policy-type.md) para saber mais.
- Para saber como personalizar as reclamações emitidas no token SAML através do portal Azure, consulte [Como: Personalizar reclamações emitidas no token SAML para aplicações empresariais](active-directory-saml-claims-customization.md)
- Para saber mais sobre os atributos de extensão, consulte [utilizando atributos de extensão de esquema de diretório em sinistros](active-directory-schema-extensions.md).

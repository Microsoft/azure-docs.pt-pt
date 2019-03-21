---
title: Metadados de Federação do AD do Azure | Documentos da Microsoft
description: Este artigo descreve o documento de metadados de Federação do Active Directory do Azure, publica para os serviços que aceitam tokens de acesso do Azure Active Directory.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcabf51b3a368841f7f135a32c4824eb3db571ee
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57864937"
---
# <a name="federation-metadata"></a>Metadados de federação
Azure Active Directory (Azure AD) publica um documento de metadados de Federação para serviços que está configurado para aceitar os tokens de segurança do Azure AD emite. O formato de documento de metadados de Federação é descrito no [Web Services Federation Language (WS-Federation) versão 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), que estende [metadados para a versão de OASIS Security Assertion Markup Language (SAML) 2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Pontos de extremidade de metadados de inquilino independentes e específicos de inquilino
O Azure AD publica os pontos de extremidade específico de inquilino e independente de inquilino.

Pontos de extremidade específico de inquilino foram concebidos para um inquilino específico. Os metadados de Federação do inquilino específico incluem informações sobre o inquilino, incluindo informações de emissor e o ponto final específico de inquilino. As aplicações que restringem o acesso a um único inquilino utilizar pontos de extremidade específico de inquilino.

Pontos finais de inquilino independente fornecem informações que são comuns aos inquilinos de contas do Azure AD. Essas informações se aplicam aos inquilinos alojados *login.microsoftonline.com* e é partilhado entre inquilinos. Pontos finais de independente de inquilino são recomendados para aplicativos de multi-inquilinos, uma vez que não estão associados com nenhum inquilino específico.

## <a name="federation-metadata-endpoints"></a>Pontos de extremidade de metadados de Federação
O Azure AD publica os metadados de Federação em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Para **pontos de extremidade específico de inquilino**, o `TenantDomainName` pode ser um dos seguintes tipos:

* Inquilino de um nome de domínio registado de um Azure AD, tal como: `contoso.onmicrosoft.com`.
* O imutável, tais como a ID do domínio, de inquilino `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Para **pontos de extremidade do inquilino independente**, o `TenantDomainName` é `common`. Este documento apresenta apenas os elementos de metadados de federação que são comuns a todos os inquilinos do Azure AD que estão alojados em login.microsoftonline.com.

Por exemplo, um ponto de extremidade específico de inquilino pode ser `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. O ponto final de independente de inquilino está [ https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml ](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Pode ver o documento de metadados de Federação ao escrever este URL num browser.

## <a name="contents-of-federation-metadata"></a>Conteúdo de metadados de Federação
A secção seguinte fornece informações necessárias por serviços que consomem tokens emitidos pelo Azure AD.

### <a name="entity-id"></a>ID de entidade
O `EntityDescriptor` elemento contém um `EntityID` atributo. O valor da `EntityID` atributo representa o emissor, ou seja, o token serviço de segurança (STS) que emitiu o token. É importante validar o emissor quando receber um token.

Os seguintes metadados mostra um exemplo específico de inquilino `EntityDescriptor` elemento com um `EntityID` elemento.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Pode substituir o ID de inquilino, o ponto final de independente de inquilino com o seu ID de inquilino para criar um inquilino específico `EntityID` valor. O valor resultante será o mesmo que o emissor de tokens. A estratégia permite que um aplicativo de multi-inquilino validar o emissor para um determinado inquilino.

Os seguintes metadados mostra um exemplo independente de inquilino `EntityID` elemento. Tenha em atenção, que o `{tenant}` é um literal, não um marcador de posição.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificados de assinatura de tokens
Quando um serviço recebe um token emitido por um inquilino do Azure AD, a assinatura do token têm de ser validada com uma chave de assinatura publicada no documento de metadados de Federação. Os metadados de Federação incluem a parte pública dos certificados que os inquilinos utilizam para assinatura de tokens. Os bytes brutos do certificado aparecem no `KeyDescriptor` elemento. O certificado de assinatura de token é válido para a assinatura apenas quando o valor do `use` atributo é `signing`.

Um documento de metadados de Federação publicado pelo Azure AD pode ter várias chaves de assinatura, por exemplo, quando o Azure AD está a preparar para atualizar o certificado de assinatura. Quando um documento de metadados de Federação inclui mais de um certificado, um serviço que está a validar os tokens deve suportar todos os certificados no documento.

Os seguintes metadados mostra um exemplo `KeyDescriptor` elemento com uma chave de assinatura.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

O `KeyDescriptor` elemento aparece em dois locais no documento de metadados de Federação; a secção de WS-Federation-específicas e na secção de SAML específicas. Os certificados publicados em ambas as secções serão os mesmos.

Na secção de WS-Federation-específicas, um leitor de metadados de WS-Federation poderia ler os certificados de um `RoleDescriptor` elemento com o `SecurityTokenServiceType` tipo.

Os seguintes metadados mostra um exemplo `RoleDescriptor` elemento.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

Na secção específica de SAML, um leitor de metadados de WS-Federation leria os certificados a partir de um `IDPSSODescriptor` elemento.

Os seguintes metadados mostra um exemplo `IDPSSODescriptor` elemento.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Não existem diferenças no formato de certificados de inquilino específica e independente do inquilino.

### <a name="ws-federation-endpoint-url"></a>URL de ponto de extremidade do WS-Federation
Os metadados de Federação incluem o URL que é o Azure AD utiliza para o início de sessão único e de fim de sessão no protocolo WS-Federation único. Este ponto final é apresentado no `PassiveRequestorEndpoint` elemento.

Os seguintes metadados mostra um exemplo `PassiveRequestorEndpoint` elemento para um ponto de extremidade específico de inquilino.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Para o ponto final de inquilino independente, o URL de WS-Federation aparece no ponto de extremidade WS-Federation, como mostrado no exemplo a seguir.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL de ponto final de protocolo SAML
Os metadados de Federação incluem o URL que o Azure AD utiliza para o início de sessão único e single sign-out no protocolo SAML 2.0. Estes pontos finais são apresentados no `IDPSSODescriptor` elemento.

Os URLs de início de sessão e fim de sessão são apresentados no `SingleSignOnService` e `SingleLogoutService` elementos.

Os seguintes metadados mostra um exemplo `PassiveResistorEndpoint` para um ponto de extremidade específico de inquilino.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Da mesma forma, os pontos finais para pontos de extremidade do protocolo SAML 2.0 comuns são publicados nos metadados da Federação independente de inquilino, conforme mostrado no exemplo a seguir.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```

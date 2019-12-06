---
title: Metadados de Federação do Azure AD | Microsoft Docs
description: Este artigo descreve o documento de metadados de Federação que Azure Active Directory publica para serviços que aceitam tokens de Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0da348d87d1dedbdc1f97d3a4d0a1a001871a1d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843789"
---
# <a name="federation-metadata"></a>Metadados de federação
O Azure Active Directory (Azure AD) publica um documento de metadados de Federação para serviços que são configurados para aceitar os tokens de segurança que o Azure AD emite. O formato de documento de metadados de Federação é descrito na [versão 1,2 do especificação Web Services Federation Language (WS-Federation)](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), que estende [os metadados para o Oasis Security Assertion Markup Language (SAML) v 2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Pontos de extremidade de metadados específicos do locatário e independentes de locatário
O Azure AD publica pontos de extremidade específicos do locatário e independentes de locatário.

Os pontos de extremidade específicos do locatário são projetados para um locatário específico. Os metadados de Federação específicos do locatário incluem informações sobre o locatário, incluindo informações de emissor e de ponto de extremidade específicas do locatário. Os aplicativos que restringem o acesso a um único locatário usam pontos de extremidade específicos do locatário.

Os pontos de extremidade independentes de locatário fornecem informações que são comuns a todos os locatários do Azure AD. Essas informações se aplicam a locatários hospedados em *login.microsoftonline.com* e são compartilhadas entre locatários. Os pontos de extremidade independentes de locatário são recomendados para aplicativos multilocatários, pois não estão associados a nenhum locatário específico.

## <a name="federation-metadata-endpoints"></a>Pontos de extremidade de metadados de Federação
O Azure AD publica metadados de Federação em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Para **pontos de extremidade específicos do locatário**, o `TenantDomainName` pode ser um dos seguintes tipos:

* Um nome de domínio registrado de um locatário do Azure AD, como: `contoso.onmicrosoft.com`.
* A ID de locatário imutável do domínio, como `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Para **pontos de extremidade independentes de locatário**, o `TenantDomainName` é `common`. Este documento lista somente os elementos de metadados de Federação que são comuns a todos os locatários do Azure AD hospedados em login.microsoftonline.com.

Por exemplo, um ponto de extremidade específico de locatário pode ser `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. O ponto de extremidade independente de locatário é [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Você pode exibir o documento de metadados de Federação digitando esta URL em um navegador.

## <a name="contents-of-federation-metadata"></a>Conteúdo dos metadados de Federação
A seção a seguir fornece as informações necessárias para os serviços que consomem os tokens emitidos pelo Azure AD.

### <a name="entity-id"></a>ID da entidade
O elemento `EntityDescriptor` contém um atributo `EntityID`. O valor do atributo `EntityID` representa o emissor, ou seja, o serviço de token de segurança (STS) que emitiu o token. É importante validar o emissor quando você recebe um token.

Os metadados a seguir mostram um elemento de `EntityDescriptor` de exemplo específico do locatário com um elemento `EntityID`.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Você pode substituir a ID do locatário no ponto de extremidade independente do locatário por sua ID de locatário para criar um valor de `EntityID` específico do locatário. O valor resultante será o mesmo que o emissor do token. A estratégia permite que um aplicativo multilocatário valide o emissor para um determinado locatário.

Os metadados a seguir mostram um elemento de `EntityID` de exemplo independente de locatário. Observe que o `{tenant}` é um literal, não um espaço reservado.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificados de assinatura de token
Quando um serviço recebe um token emitido por um locatário do Azure AD, a assinatura do token deve ser validada com uma chave de assinatura que é publicada no documento de metadados de Federação. Os metadados de Federação incluem a parte pública dos certificados que os locatários usam para autenticação de token. Os bytes brutos do certificado aparecem no elemento `KeyDescriptor`. O certificado de autenticação de tokens é válido para assinatura somente quando o valor do atributo `use` é `signing`.

Um documento de metadados de Federação publicado pelo Azure AD pode ter várias chaves de assinatura, como quando o Azure AD está se preparando para atualizar o certificado de autenticação. Quando um documento de metadados de Federação inclui mais de um certificado, um serviço que está validando os tokens deve dar suporte a todos os certificados no documento.

Os metadados a seguir mostram um elemento de `KeyDescriptor` de exemplo com uma chave de assinatura.

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

O elemento `KeyDescriptor` aparece em dois locais no documento de metadados de Federação; na seção específica do WS-Federation e na seção específica do SAML. Os certificados publicados em ambas as seções serão os mesmos.

Na seção específica do WS-Federation, um leitor de metadados do WS-Federation lerá os certificados de um elemento `RoleDescriptor` com o tipo `SecurityTokenServiceType`.

Os metadados a seguir mostram um elemento de `RoleDescriptor` de exemplo.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

Na seção específica do SAML, um leitor de metadados do WS-Federation lerá os certificados de um elemento `IDPSSODescriptor`.

Os metadados a seguir mostram um elemento de `IDPSSODescriptor` de exemplo.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Não há diferenças no formato de certificados específicos de locatários e independentes de locatário.

### <a name="ws-federation-endpoint-url"></a>URL de ponto de extremidade do WS-Federation
Os metadados de Federação incluem a URL que o Azure AD usa para logon único e saída única no protocolo WS-Federation. Esse ponto de extremidade aparece no elemento `PassiveRequestorEndpoint`.

Os metadados a seguir mostram um elemento de `PassiveRequestorEndpoint` de exemplo para um ponto de extremidade específico do locatário.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Para o ponto de extremidade independente de locatário, a URL do WS-Federation aparece no ponto de extremidade do WS-Federation, conforme mostrado no exemplo a seguir.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL do ponto de extremidade do protocolo SAML
Os metadados de Federação incluem a URL que o AD do Azure usa para logon único e saída única no protocolo SAML 2,0. Esses pontos de extremidade aparecem no elemento `IDPSSODescriptor`.

As URLs de entrada e saída são exibidas nos elementos `SingleSignOnService` e `SingleLogoutService`.

Os metadados a seguir mostram um `PassiveResistorEndpoint` de exemplo para um ponto de extremidade específico do locatário.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Da mesma forma, os pontos de extremidade para os pontos de extremidade do protocolo SAML 2,0 comuns são publicados nos metadados de Federação independentes de locatário, conforme mostrado no exemplo a seguir.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```

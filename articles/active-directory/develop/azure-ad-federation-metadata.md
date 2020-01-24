---
title: Metadados da Federação Azure AD Microsoft Docs
description: Este artigo descreve o documento de metadados da federação que o Azure Ative Directory publica para serviços que aceitam fichas de Diretório Ativo Azure.
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
ms.openlocfilehash: 74ce45067dbe2d89d7bb7e267bffa6bbb02defa3
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697885"
---
# <a name="federation-metadata"></a>Metadados de federação
O Azure Ative Directory (Azure AD) publica um documento de metadados da federação para serviços configurados para aceitar os tokens de segurança que a Azure AD emite. O formato de documento de metadados da federação é descrito na versão 1.2 da Federação de [Serviços Web (WS-Federação),](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)que alarga metadados para a linguagem de marcação de afirmação de [segurança oasis (SAML) v2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Pontos finais de metadados específicos do arrendatário e independentes do arrendatário
A Azure AD publica pontos finais específicos e independentes de inquilinos.

Os pontos finais específicos do inquilino são projetados para um inquilino particular. Os metadados da federação específica sinuoso incluem informações sobre o inquilino, incluindo informações específicas do inquilino e do ponto final. As aplicações que restringem o acesso a um único inquilino usam pontos finais específicos do arrendatário.

Os pontos finais independentes dos inquilinos fornecem informações que são comuns a todos os inquilinos da AD Azure. Esta informação aplica-se aos inquilinos alojados em *login.microsoftonline.com* e é partilhada entre inquilinos. Os pontos finais independentes dos inquilinos são recomendados para pedidos de multi-inquilinos, uma vez que não estão associados a nenhum inquilino em particular.

## <a name="federation-metadata-endpoints"></a>Pontos finais de metadados da Federação
A Azure AD publica metadados da federação em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Para **os pontos finais específicos do arrendatário,** o `TenantDomainName` pode ser um dos seguintes tipos:

* Um nome de domínio registado de um inquilino da AD Azure, tais como: `contoso.onmicrosoft.com`.
* A imutável identificação do inquilino do domínio, como `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Para **os pontos finais independentes dos inquilinos,** o `TenantDomainName` é `common`. Este documento lista apenas os elementos da Federação de Metadados que são comuns a todos os inquilinos da AD Azure que estão hospedados em login.microsoftonline.com.

Por exemplo, um ponto final específico do arrendatário pode ser `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. O ponto final independente do inquilino é [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml. ](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml) Pode ver o documento de metadados da federação digitando este URL num browser.

## <a name="contents-of-federation-metadata"></a>Conteúdo dos Metadados da Federação
A secção seguinte fornece informações necessárias pelos serviços que consomem as fichas emitidas pela Azure AD.

### <a name="entity-id"></a>ID da entidade
O elemento `EntityDescriptor` contém um atributo `EntityID`. O valor do atributo `EntityID` representa o emitente, ou seja, o serviço de token de segurança (STS) que emitiu o símbolo. É importante validar o emitente quando recebe um símbolo.

Os seguintes metadados mostram uma amostra específica do `EntityDescriptor` elemento com um elemento `EntityID`.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Você pode substituir a identificação do inquilino no ponto final independente do inquilino com o seu ID de inquilino para criar um valor de `EntityID` específico para inquilino. O valor resultante será o mesmo que o emitente simbólico. A estratégia permite que uma aplicação multi-arrendatária validar o emitente para um determinado inquilino.

Os seguintes metadados mostram uma amostra de elemento `EntityID` independente do inquilino. Por favor, note que o `{tenant}` é um literal, não um espaço reservado.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificados de assinatura token
Quando um serviço recebe um símbolo emitido por um inquilino da AD Azure, a assinatura do token deve ser validada com uma chave de assinatura publicada no documento de metadados da federação. Os metadados da federação incluem a parte pública dos certificados que os inquilinos usam para a assinatura simbólica. Os bytes crus do certificado aparecem no elemento `KeyDescriptor`. O certificado de assinatura simbólica só é válido para a assinatura quando o valor do atributo `use` for `signing`.

Um documento de metadados da federação publicado pela Azure AD pode ter várias chaves de assinatura, como quando a Azure AD se prepara para atualizar o certificado de assinatura. Quando um documento de metadados da federação inclui mais de um certificado, um serviço que esteja a validar as fichas deve suportar todos os certificados do documento.

Os seguintes metadados mostram uma amostra `KeyDescriptor` elemento com uma chave de assinatura.

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

O elemento `KeyDescriptor` aparece em dois locais do documento de metadados da federação; na secção específica da WS-Federação e na secção específica da SAML. Os certificados publicados em ambas as secções serão os mesmos.

Na secção específica da WS-Federação, um leitor de metadados da WS-Federação leria os certificados de um elemento `RoleDescriptor` com o tipo `SecurityTokenServiceType`.

Os seguintes metadados mostram uma amostra `RoleDescriptor` elemento.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

Na secção específica da SAML, um leitor de metadados da WS-Federation leria os certificados de um elemento `IDPSSODescriptor`.

Os seguintes metadados mostram uma amostra `IDPSSODescriptor` elemento.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Não existem diferenças no formato dos certificados específicos dos inquilinos e dos inquilinos independentes.

### <a name="ws-federation-endpoint-url"></a>URL final da WS-Federação
Os metadados da federação incluem o URL que é Azure AD usa para um único sign-in e um único sign-out no protocolo WS-Federation. Este ponto final aparece no elemento `PassiveRequestorEndpoint`.

Os seguintes metadados mostram uma amostra `PassiveRequestorEndpoint` elemento para um ponto final específico do arrendatário.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Para o ponto final independente do inquilino, o URL da WS-Federação aparece no ponto final da WS-Federação, como mostra a amostra seguinte.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL final do protocolo SAML
Os metadados da federação incluem o URL que o Azure AD utiliza para um único login e inscrição única no protocolo SAML 2.0. Estes pontos finais aparecem no elemento `IDPSSODescriptor`.

Os URLs de inscrição e inscrição aparecem nos elementos `SingleSignOnService` e `SingleLogoutService`.

Os seguintes metadados mostram uma amostra `PassiveResistorEndpoint` para um ponto final específico do inquilino.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Da mesma forma, os pontos finais dos pontos finais comuns do protocolo SAML 2.0 são publicados nos metadados da federação independente de inquilinos, como mostra a amostra seguinte.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```

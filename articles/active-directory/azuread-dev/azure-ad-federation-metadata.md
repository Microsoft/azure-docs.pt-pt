---
title: Metadados da Federação Azure AD Microsoft Docs
description: Este artigo descreve o documento de metadados da federação que o Azure Ative Directory publica para serviços que aceitam fichas de Diretório Ativo Azure.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: bcc44f61ccb7b4a19e7df39ab979669c5aa37da1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154904"
---
# <a name="federation-metadata"></a>Metadados de federação

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O Azure Ative Directory (Azure AD) publica um documento de metadados da federação para serviços configurados para aceitar os tokens de segurança que a Azure AD emite. O formato de documento de metadados da federação é descrito na versão 1.2 da Federação de [Serviços Web (WS-Federação),](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)que alarga metadados para a linguagem de marcação de afirmação de [segurança oasis (SAML) v2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Pontos finais de metadados específicos do arrendatário e independentes do arrendatário
A Azure AD publica pontos finais específicos e independentes de inquilinos.

Os pontos finais específicos do inquilino são projetados para um inquilino particular. Os metadados da federação específica sinuoso incluem informações sobre o inquilino, incluindo informações específicas do inquilino e do ponto final. As aplicações que restringem o acesso a um único inquilino usam pontos finais específicos do arrendatário.

Os pontos finais independentes dos inquilinos fornecem informações que são comuns a todos os inquilinos da AD Azure. Esta informação aplica-se aos inquilinos alojados em *login.microsoftonline.com* e é partilhada entre inquilinos. Os pontos finais independentes dos inquilinos são recomendados para pedidos de multi-inquilinos, uma vez que não estão associados a nenhum inquilino em particular.

## <a name="federation-metadata-endpoints"></a>Pontos finais de metadados da Federação
A Azure AD publica `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`metadados da federação em .

Para **os pontos finais específicos do arrendatário,** pode `TenantDomainName` ser um dos seguintes tipos:

* Um nome de domínio registado de um inquilino `contoso.onmicrosoft.com`da AD Azure, tais como: .
* A imutável identificação do inquilino `72f988bf-86f1-41af-91ab-2d7cd011db45`do domínio, como.

Para **os pontos finais independentes dos inquilinos,** o `TenantDomainName` é `common`. Este documento lista apenas os elementos da Federação de Metadados que são comuns a todos os inquilinos da AD Azure que estão hospedados em login.microsoftonline.com.

Por exemplo, um ponto final `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`específico do inquilino pode ser. O ponto final independente [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml)do inquilino é. Pode ver o documento de metadados da federação digitando este URL num browser.

## <a name="contents-of-federation-metadata"></a>Conteúdo dos Metadados da Federação
A secção seguinte fornece informações necessárias pelos serviços que consomem as fichas emitidas pela Azure AD.

### <a name="entity-id"></a>ID da entidade
O `EntityDescriptor` elemento `EntityID` contém um atributo. O valor `EntityID` do atributo representa o emitente, ou seja, o serviço de fichas de segurança (STS) que emitiu o símbolo. É importante validar o emitente quando recebe um símbolo.

Os seguintes metadados mostram `EntityDescriptor` um elemento `EntityID` específico do inquilino com um elemento.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Você pode substituir a identificação do inquilino no ponto final independente `EntityID` do inquilino com a sua identificação de inquilino para criar um valor específico para o inquilino. O valor resultante será o mesmo que o emitente simbólico. A estratégia permite que uma aplicação multi-arrendatária validar o emitente para um determinado inquilino.

Os seguintes metadados mostram `EntityID` um elemento independente do inquilino da amostra. Por favor, note que o é `{tenant}` literal, não um espaço reservado.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificados de assinatura token
Quando um serviço recebe um símbolo emitido por um inquilino da AD Azure, a assinatura do token deve ser validada com uma chave de assinatura publicada no documento de metadados da federação. Os metadados da federação incluem a parte pública dos certificados que os inquilinos usam para a assinatura simbólica. Os bytes crus `KeyDescriptor` do certificado aparecem no elemento. O certificado de assinatura simbólica só é válido `use` para `signing`a assinatura quando o valor do atributo for .

Um documento de metadados da federação publicado pela Azure AD pode ter várias chaves de assinatura, como quando a Azure AD se prepara para atualizar o certificado de assinatura. Quando um documento de metadados da federação inclui mais de um certificado, um serviço que esteja a validar as fichas deve suportar todos os certificados do documento.

Os seguintes metadados `KeyDescriptor` mostram um elemento de amostra com uma chave de assinatura.

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

O `KeyDescriptor` elemento aparece em dois lugares no documento de metadados da federação; na secção específica da WS-Federação e na secção específica da SAML. Os certificados publicados em ambas as secções serão os mesmos.

Na secção específica da WS-Federação, um leitor de metadados da `RoleDescriptor` WS-Federação leria os certificados de um elemento com o `SecurityTokenServiceType` tipo.

Os seguintes metadados `RoleDescriptor` mostram um elemento de amostra.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

Na secção específica da SAML, um leitor de metadados da `IDPSSODescriptor` WS-Federation leria os certificados de um elemento.

Os seguintes metadados `IDPSSODescriptor` mostram um elemento de amostra.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Não existem diferenças no formato dos certificados específicos dos inquilinos e dos inquilinos independentes.

### <a name="ws-federation-endpoint-url"></a>URL final da WS-Federação
Os metadados da federação incluem o URL que é Azure AD usa para um único sign-in e um único sign-out no protocolo WS-Federation. Este ponto final aparece `PassiveRequestorEndpoint` no elemento.

Os seguintes metadados `PassiveRequestorEndpoint` mostram um elemento de amostra para um ponto final específico do inquilino.

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
Os metadados da federação incluem o URL que o Azure AD utiliza para um único login e inscrição única no protocolo SAML 2.0. Estes pontos finais aparecem no `IDPSSODescriptor` elemento.

Os URLs de inscrição e inscrição aparecem nos `SingleSignOnService` elementos e `SingleLogoutService` elementos.

Os seguintes metadados `PassiveResistorEndpoint` mostram uma amostra para um ponto final específico do inquilino.

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

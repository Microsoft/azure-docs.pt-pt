---
title: Metadados da Federação AD Azure | Microsoft Docs
description: Este artigo descreve o documento de metadados da federação que o Azure Ative Directory publica para serviços que aceitam fichas do Azure Ative Directory.
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
ms.openlocfilehash: 7f3bd8851fe723461c618499e539c987d79c0d68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650146"
---
# <a name="federation-metadata"></a>Metadados de federação

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

O Azure Ative Directory (Azure AD) publica um documento de metadados da federação para serviços configurados para aceitar os tokens de segurança que a Azure AD emite. O formato do documento de metadados da federação é descrito na [Versão 1.2 da Federação de Serviços Web (WS-Federation),](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)que alarga [os metadados para a linguagem de marcação de afirmação de segurança OASIS (SAML) v2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Pontos finais de metadados específicos para inquilinos e independentes do arrendatário
A Azure AD publica pontos finais específicos do inquilino e independentes do inquilino.

Os pontos finais específicos do inquilino são projetados para um inquilino particular. Os metadados da federação específicos do inquilino incluem informações sobre o inquilino, incluindo informações específicas do inquilino e ponto final. As aplicações que restringem o acesso a um único inquilino utilizam pontos finais específicos do arrendatário.

Os pontos finais independentes do inquilino fornecem informações que são comuns a todos os inquilinos da AD Azure. Esta informação aplica-se aos inquilinos hospedados em *login.microsoftonline.com* e é partilhada entre inquilinos. Os pontos finais independentes do arrendatário são recomendados para aplicações multi-arrendatários, uma vez que não estão associados a nenhum inquilino em particular.

## <a name="federation-metadata-endpoints"></a>Pontos finais de metadados da Federação
A Azure AD publica metadados da federação em `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` .

Para **os pontos finais específicos do arrendatário,** pode ser um dos `TenantDomainName` seguintes tipos:

* Um nome de domínio registado de um inquilino da AD Azure, tais como: `contoso.onmicrosoft.com` .
* O imutável imutável id do domínio, tais como `72f988bf-86f1-41af-91ab-2d7cd011db45` .

Para **os pontos finais independentes dos inquilinos,** o `TenantDomainName` is `common` . Este documento lista apenas os elementos de metadados da Federação que são comuns a todos os inquilinos AD Azure que estão hospedados em login.microsoftonline.com.

Por exemplo, um ponto final específico do inquilino pode ser `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml` . O ponto final independente do inquilino [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml) é. Pode ver o documento de metadados da federação digitando este URL num browser.

## <a name="contents-of-federation-metadata"></a>Conteúdo dos Metadados da Federação
A secção seguinte fornece informações necessárias pelos serviços que consomem os tokens emitidos pela Azure AD.

### <a name="entity-id"></a>ID de entidade
O `EntityDescriptor` elemento contém um `EntityID` atributo. O valor do `EntityID` atributo representa o emitente, ou seja, o serviço de ficha de segurança (STS) que emitiu o token. É importante validar o emitente quando receber um token.

Os metadados que se seguem mostram um elemento específico do inquilino da amostra `EntityDescriptor` com um `EntityID` elemento.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Você pode substituir a identificação do inquilino no ponto final independente do inquilino por seu ID inquilino para criar um valor específico do `EntityID` inquilino. O valor resultante será o mesmo que o emitente simbólico. A estratégia permite que uma aplicação multi-inquilino valide o emitente para um determinado inquilino.

Os seguintes metadados mostram um elemento independente do inquilino da `EntityID` amostra. Por favor, note que `{tenant}` o é um espaço reservado literal, não não um espaço reservado.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificados de assinatura token
Quando um serviço recebe um token emitido por um inquilino AZure AD, a assinatura do token deve ser validada com uma chave de assinatura que é publicada no documento de metadados da federação. Os metadados da federação incluem a parte pública dos certificados que os inquilinos usam para a assinatura de fichas. Os bytes crus do certificado aparecem no `KeyDescriptor` elemento. O certificado de assinatura simbólica é válido para a assinatura apenas quando o valor do `use` atributo for `signing` .

Um documento de metadados da federação publicado pela Azure AD pode ter várias chaves de assinatura, como quando a Azure AD se prepara para atualizar o certificado de assinatura. Quando um documento de metadados da federação inclui mais de um certificado, um serviço que está a validar os tokens deve suportar todos os certificados do documento.

Os metadados que se seguem mostram um elemento de amostra `KeyDescriptor` com uma chave de assinatura.

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

O `KeyDescriptor` elemento aparece em dois lugares no documento de metadados da federação; na secção específica da WS-Federação e na secção específica do SAML. Os certificados publicados em ambas as secções serão os mesmos.

Na secção específica da WS-Federação, um leitor de metadados WS-Federation leria os certificados a partir de um `RoleDescriptor` elemento com o `SecurityTokenServiceType` tipo.

Os metadados que se seguem mostram um elemento de `RoleDescriptor` amostra.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType" protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

Na secção específica da SAML, um leitor de metadados WS-Federation leria os certificados a partir de um `IDPSSODescriptor` elemento.

Os metadados que se seguem mostram um elemento de `IDPSSODescriptor` amostra.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Não existem diferenças no formato de certificados específicos para inquilinos e independentes de inquilinos.

### <a name="ws-federation-endpoint-url"></a>URL de ponta WS-Federation
Os metadados da federação incluem o URL que é Azure AD usa para um único sign-in e único sign-out no protocolo WS-Federation. Este ponto final aparece no `PassiveRequestorEndpoint` elemento.

Os metadados que se seguem mostram um elemento de amostra `PassiveRequestorEndpoint` para um ponto final específico do inquilino.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Para o ponto final independente do inquilino, o URL WS-Federation aparece no ponto final WS-Federation, como mostra a amostra seguinte.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL de ponta de protocolo SAML
Os metadados da federação incluem o URL que o Azure AD utiliza para um único sinal de sação e uma única sindestrução no protocolo SAML 2.0. Estes pontos finais aparecem no `IDPSSODescriptor` elemento.

Os URLs de inscrição e de inscrição aparecem nos `SingleSignOnService` elementos e `SingleLogoutService` elementos.

Os metadados que se seguem mostram uma amostra `PassiveResistorEndpoint` para um ponto final específico do inquilino.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Do mesmo modo, os pontos finais dos pontos finais comuns do protocolo SAML 2.0 são publicados nos metadados da federação independente do arrendatário, tal como indicado na amostra seguinte.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```

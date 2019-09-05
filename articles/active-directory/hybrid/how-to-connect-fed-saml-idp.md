---
title: 'Azure AD Connect: Usar um provedor de identidade SAML 2,0 para o logon único no Azure'
description: Este documento descreve como usar um IDP em conformidade com o SAML 2,0 para logon único.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff4f1b81f476159c13d3725cd3cb1766aec7dc87
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305102"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Usar um IdP (provedor de identidade) do SAML 2,0 para logon único

Este documento contém informações sobre como usar um provedor de identidade baseado em perfil SP-Lite em conformidade com o SAML 2,0 como o STS (serviço de token de segurança) preferencial/provedor de identidade. Esse cenário é útil quando você já tem um diretório de usuário e um armazenamento de senha no local que podem ser acessados usando o SAML 2,0. Esse diretório de usuário existente pode ser usado para logon no Office 365 e em outros recursos protegidos pelo Azure AD. O perfil do SAML 2,0 SP-Lite é baseado no padrão de identidade federada Security Assertion Markup Language (SAML) amplamente usado para fornecer uma estrutura de logon e de troca de atributos.

>[!NOTE]
>Para obter uma lista de IDPs de terceiros que foram testados para uso com o Azure AD, consulte a [lista de compatibilidade de Federação do Azure ad](how-to-connect-fed-compatibility.md)

A Microsoft dá suporte a essa experiência de logon como a integração de um serviço de nuvem da Microsoft, como o Office 365, com o IdP baseado em perfil SAML 2,0 configurado corretamente. Os provedores de identidade SAML 2,0 são produtos de terceiros e, portanto, a Microsoft não oferece suporte para as práticas recomendadas de implantação, configuração e solução de problemas em relação a eles. Uma vez configurado corretamente, a integração com o provedor de identidade SAML 2,0 pode ser testada para configuração adequada usando a ferramenta Analisador de conectividade da Microsoft, que é descrita mais detalhadamente abaixo. Para obter mais informações sobre o provedor de identidade baseado em perfil do SAML 2,0 SP-Lite, pergunte à organização que o forneceu.

> [!IMPORTANT]
> Somente um conjunto limitado de clientes está disponível neste cenário de logon com os provedores de identidade SAML 2,0, incluindo:
> 
> - Clientes baseados na Web, como o Outlook Acesso via Web e o SharePoint Online
> - Clientes de email avançados que usam autenticação básica e um método de acesso do Exchange com suporte, como IMAP, POP, Active Sync, MAPI, etc. (o ponto de extremidade do protocolo de cliente aprimorado deve ser implantado), incluindo:
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (várias versões do iOS)
>     - Vários dispositivos do Google Android
>     - Windows Phone 7, Windows Phone 7,8 e Windows Phone 8,0
>     - Cliente de email do Windows 8 e cliente do Windows 8.1 mail
>     - Cliente de email do Windows 10

Todos os outros clientes não estão disponíveis neste cenário de logon com seu provedor de identidade SAML 2,0. Por exemplo, o cliente de área de trabalho do Lync 2010 não é capaz de entrar no serviço com seu provedor de identidade SAML 2,0 configurado para logon único.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Requisitos do protocolo SAML 2,0 do Azure AD
Este documento contém requisitos detalhados sobre o protocolo e a formatação de mensagens que seu provedor de identidade SAML 2,0 deve implementar para federar com o Azure AD para habilitar o logon em um ou mais serviços de nuvem da Microsoft (como o Office 365). A terceira parte confiável SAML 2,0 (SP-STS) para um serviço de nuvem da Microsoft usado neste cenário é o Azure AD.

É recomendável que você garanta que as mensagens de saída do provedor de identidade SAML 2,0 sejam semelhantes aos rastreamentos de exemplo fornecidos como possível. Além disso, use valores de atributo específicos dos metadados do Azure AD fornecidos sempre que possível. Quando estiver satisfeito com suas mensagens de saída, você poderá testar com o analisador de conectividade da Microsoft, conforme descrito abaixo.

Os metadados do Azure AD podem ser baixados desta URL [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml):.
Para clientes na China usando a instância específica da China do Office 365, o seguinte ponto de extremidade de Federação deve [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml)ser usado:.

## <a name="saml-protocol-requirements"></a>Requisitos de protocolo SAML
Esta seção detalha como os pares de mensagens de solicitação e resposta são colocados juntos para ajudá-lo a Formatar suas mensagens corretamente.

O Azure AD pode ser configurado para trabalhar com provedores de identidade que usam o perfil SAML 2,0 SP Lite com alguns requisitos específicos, conforme listado abaixo. Usando as mensagens de solicitação e resposta SAML de exemplo juntamente com os testes automatizados e manuais, você pode trabalhar para obter interoperabilidade com o Azure AD.

## <a name="signature-block-requirements"></a>Requisitos de bloco de assinatura
Na mensagem de resposta SAML, o nó assinatura contém informações sobre a assinatura digital para a mensagem em si. O bloco de assinatura tem os seguintes requisitos:

1. O nó de asserção em si deve ser assinado
2.  O algoritmo RSA-SHA1 deve ser usado como o DigestMethod. Outros algoritmos de assinatura digital não são aceitos.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Você também pode assinar o documento XML. 
4.  O algoritmo de transformação deve corresponder aos valores no exemplo a seguir:`<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  O algoritmo SignatureMethod deve corresponder ao seguinte exemplo:`<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Associações com suporte
Associações são os parâmetros de comunicação relacionados ao transporte que são necessários. Os seguintes requisitos se aplicam às associações

1. HTTPS é o transporte necessário.
2.  O AD do Azure exigirá HTTP POST para envio de token durante a entrada.
3.  O AD do Azure usará HTTP POST para a solicitação de autenticação para o provedor de identidade e redirecionará a mensagem de saída para o provedor de identidade.

## <a name="required-attributes"></a>Atributos necessários
Esta tabela mostra os requisitos para atributos específicos na mensagem SAML 2,0.
 
|Atributo|Descrição|
| ----- | ----- |
|NameID|O valor dessa declaração deve ser o mesmo que o usuário imutável do Azure AD. Pode ter até 64 caracteres numéricos alfanuméricos. Todos os caracteres seguros não HTML devem ser codificados, por exemplo, um caractere "+" é mostrado como ". 2B".|
|Idpemail.|O nome principal do usuário (UPN) é listado na resposta SAML como um elemento com o nome Idpemail. o UserPrincipalName (UPN) do usuário no Azure AD/Office 365. O UPN está no formato de endereço de email. Valor de UPN no Windows Office 365 (Azure Active Directory).|
|Emissor|Necessário para ser um URI do provedor de identidade. Não reutilize o emissor das mensagens de exemplo. Se você tiver vários domínios de nível superior em seus locatários do Azure AD, o emissor deverá corresponder à configuração de URI especificada configurada por domínio.|

>[!IMPORTANT]
>Atualmente, o Azure AD dá suporte ao seguinte URI de formato NameID para SAML 2.0: urn: Oasis: names: TC: SAML: 2.0: NameID-Format: persistent.

## <a name="sample-saml-request-and-response-messages"></a>Exemplo de mensagens de solicitação e resposta SAML
Um par de mensagens de solicitação e resposta é mostrado para a troca de mensagens de logon.
Veja a seguir um exemplo de mensagem de solicitação enviada do Azure AD para um provedor de identidade do SAML 2,0 de exemplo. O provedor de identidade SAML 2,0 de exemplo é Serviços de Federação do Active Directory (AD FS) (AD FS) configurado para usar o protocolo SAML-P. O teste de interoperabilidade também foi concluído com outros provedores de identidade SAML 2,0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

A seguir está um exemplo de mensagem de resposta que é enviada do provedor de identidade em conformidade com o SAML 2,0 de exemplo para o Azure AD/Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Configurar seu provedor de identidade em conformidade com o SAML 2,0
Esta seção contém diretrizes sobre como configurar seu provedor de identidade SAML 2,0 para federar com o Azure AD para habilitar o acesso de logon único a um ou mais serviços de nuvem da Microsoft (como o Office 365) usando o protocolo SAML 2,0. A terceira parte confiável SAML 2,0 para um serviço de nuvem da Microsoft usado neste cenário é o Azure AD.

## <a name="add-azure-ad-metadata"></a>Adicionar metadados do Azure AD
Seu provedor de identidade SAML 2,0 precisa aderir a informações sobre a terceira parte confiável do Azure AD. O Azure AD publica metadados em https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

É recomendável que você sempre importe os metadados mais recentes do Azure AD ao configurar seu provedor de identidade SAML 2,0.

>[!NOTE]
>O Azure AD não lê os metadados do provedor de identidade.

## <a name="add-azure-ad-as-a-relying-party"></a>Adicionar o Azure AD como uma terceira parte confiável
Você deve habilitar a comunicação entre seu provedor de identidade SAML 2,0 e o Azure AD. Essa configuração será dependente de seu provedor de identidade específico e você deverá consultar a documentação para ela. Normalmente, você definiria a ID da terceira parte confiável para o mesmo que a EntityId dos metadados do Azure AD.

>[!NOTE]
>Verifique se o relógio no seu servidor do provedor de identidade SAML 2,0 está sincronizado com uma fonte de tempo precisa. Um tempo de relógio impreciso pode causar falha nos logons federados.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instalar o Windows PowerShell para logon com o provedor de identidade SAML 2,0
Depois de configurar seu provedor de identidade SAML 2,0 para uso com o logon do Azure AD, a próxima etapa é baixar e instalar o módulo Azure Active Directory para o Windows PowerShell. Uma vez instalado, você usará esses cmdlets para configurar seus domínios do Azure AD como domínios federados.

O módulo Azure Active Directory para o Windows PowerShell é um download para gerenciar os dados de sua organização no Azure AD. Este módulo instala um conjunto de cmdlets para o Windows PowerShell; você executa esses cmdlets para configurar o acesso de logon único ao Azure AD e, por sua vez, a todos os serviços de nuvem nos quais você se inscreveu. Para obter instruções sobre como baixar e instalar os cmdlets, consulte[https://technet.microsoft.com/library/jj151815.aspx](https://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Configurar uma relação de confiança entre seu provedor de identidade SAML e o Azure AD
Antes de configurar a Federação em um domínio do Azure AD, ele deve ter um domínio personalizado configurado. Você não pode federar o domínio padrão que é fornecido pela Microsoft. O domínio padrão da Microsoft termina com "onmicrosoft.com".
Você executará uma série de cmdlets na interface de linha de comando do Windows PowerShell para adicionar ou converter domínios para logon único.

Cada domínio Azure Active Directory que você deseja federar usando seu provedor de identidade SAML 2,0 deve ser adicionado como um domínio de logon único ou convertido para ser um domínio de logon único de um domínio padrão. Adicionar ou converter um domínio configura uma relação de confiança entre seu provedor de identidade SAML 2,0 e o Azure AD.

O procedimento a seguir orienta você pela conversão de um domínio padrão existente para um domínio federado usando o SAML 2,0 SP-Lite. 

>[!NOTE]
>Seu domínio pode sofrer uma interrupção que afete os usuários até 2 horas depois de realizar esta etapa.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Configurando um domínio em seu diretório do Azure AD para Federação


1. Conecte-se ao seu diretório do Azure AD como um administrador de locatários: Connect-MsolService.
2.  Configure o domínio do Office 365 desejado para usar a Federação com o SAML 2,0:`$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $BrandName -Authentication Federated -PassiveLogOnUri $LogOnUrl -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $MyURI -LogOffUri $LogOffUrl -PreferredAuthenticationProtocol $Protocol` 

3.  Você pode obter a cadeia de caracteres codificada em base64 do certificado de autenticação de seu arquivo de metadados IDP. Um exemplo desse local foi fornecido, mas pode diferir ligeiramente com base na sua implementação.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Para obter mais informações sobre "Set-MsolDomainAuthentication", consulte [https://technet.microsoft.com/library/dn194112.aspx](https://technet.microsoft.com/library/dn194112.aspx):.

>[!NOTE]
>Você deve executar o `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` uso somente se configurar uma extensão ECP para seu provedor de identidade. Clientes do Exchange Online, excluindo o OWA (Outlook Web Application), contam com um ponto de extremidade ativo baseado em POSTAgem. Se o STS SAML 2,0 implementar um ponto de extremidade ativo semelhante à implementação de ECP do Shibboleth de um ponto de extremidade ativo, talvez seja possível que esses clientes avançados interajam com o serviço Exchange Online.

Depois que a Federação tiver sido configurada, você poderá voltar para "não federado" (ou "gerenciada"). no entanto, essa alteração leva até duas horas para ser concluída e requer a atribuição de novas senhas aleatórias para entrada baseada em nuvem para cada usuário. Voltar para "gerenciado" pode ser necessário em alguns cenários para redefinir um erro em suas configurações. Para obter mais informações sobre conversão de domínio [https://msdn.microsoft.com/library/windowsazure/dn194122.aspx](https://msdn.microsoft.com/library/windowsazure/dn194122.aspx), consulte:.

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Provisionar entidades de usuário no Azure AD/Office 365
Antes de poder autenticar seus usuários no Office 365, você deve provisionar o Azure AD com entidades de usuário que correspondam à asserção na declaração SAML 2,0. Se essas entidades de usuário não forem conhecidas pelo Azure AD com antecedência, elas não poderão ser usadas para entrada federada. O Azure AD Connect ou o Windows PowerShell podem ser usados para provisionar entidades de usuário.

Azure AD Connect pode ser usado para provisionar entidades para seus domínios em seu diretório do Azure AD do Active Directory local. Para obter informações mais detalhadas, consulte [integrar seus diretórios locais com o Azure Active Directory](whatis-hybrid-identity.md).

O Windows PowerShell também pode ser usado para automatizar a adição de novos usuários ao Azure AD e sincronizar as alterações do diretório local. Para usar os cmdlets do Windows PowerShell, você deve baixar os [módulos de Azure Active Directory](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Este procedimento mostra como adicionar um único usuário ao Azure AD.


1. Conecte-se ao seu diretório do Azure AD como um administrador de locatários: Connect-MsolService.
2.  Criar uma nova entidade de usuário:
    ```powershell
    New-MsolUser
      -UserPrincipalName elwoodf1@contoso.com
      -ImmutableId ABCDEFG1234567890
      -DisplayName "Elwood Folk"
      -FirstName Elwood 
      -LastName Folk 
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
      -UsageLocation "US" 
    ```

Para obter mais informações sobre o check-out de "New-MsolUser",[https://technet.microsoft.com/library/dn194096.aspx](https://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>O valor "UserPrinciplName" deve corresponder ao valor que você enviará para "Idpemail." em sua declaração SAML 2,0 e o valor "imutávelid" deve corresponder ao valor enviado na sua declaração "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Verificar o logon único com o IDP do SAML 2,0
Como administrador, antes de verificar e gerenciar o logon único (também chamado de Federação de identidade), revise as informações e execute as etapas nos artigos a seguir para configurar o logon único com seu provedor de identidade baseado em SAML 2,0 SP-Lite:


1.  Você examinou os requisitos do protocolo SAML 2,0 do Azure AD
2.  Você configurou seu provedor de identidade SAML 2,0
3.  Instalar o Windows PowerShell para logon único com o provedor de identidade SAML 2,0
4.  Configurar uma relação de confiança entre o provedor de identidade SAML 2,0 e o Azure AD
5.  Provisionado uma entidade de usuário de teste conhecida para o Azure Active Directory (Office 365) por meio do Windows PowerShell ou Azure AD Connect.
6.  Configure a sincronização de diretório usando [Azure ad Connect](whatis-hybrid-identity.md).

Depois de configurar o logon único com seu provedor de identidade com base no SAML 2,0 SP-Lite, você deve verificar se ele está funcionando corretamente.

>[!NOTE]
>Se você converteu um domínio, em vez de adicionar um, pode levar até 24 horas para configurar o logon único.
Antes de verificar o logon único, você deve concluir a configuração da sincronização de Active Directory, sincronizar seus diretórios e ativar os usuários sincronizados.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Use a ferramenta para verificar se o logon único foi configurado corretamente
Para verificar se o logon único foi configurado corretamente, você pode executar o procedimento a seguir para confirmar que é possível entrar no serviço de nuvem com suas credenciais corporativas.

A Microsoft forneceu uma ferramenta que você pode usar para testar seu provedor de identidade baseado em SAML 2,0. Antes de executar a ferramenta de teste, você deve ter configurado um locatário do Azure AD para federar com seu provedor de identidade.

>[!NOTE]
>O analisador de conectividade requer o Internet Explorer 10 ou posterior.



1. Baixe o analisador de conectividade [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client)do,.
2.  Clique em instalar agora para começar a baixar e instalar a ferramenta.
3.  Selecione "não consigo configurar a Federação com o Office 365, o Azure ou outros serviços que usam Azure Active Directory".
4.  Depois que a ferramenta for baixada e executada, você verá a janela diagnóstico de conectividade. A ferramenta o orientará durante o teste de sua conexão de Federação.
5.  O analisador de conectividade abrirá seu IDP SAML 2,0 para você entrar, insira as credenciais para a entidade de usuário que você está testando: ![SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  Na janela de entrada de teste de Federação, você deve inserir um nome de conta e uma senha para o locatário do Azure AD que está configurado para ser federado com seu provedor de identidade SAML 2,0. A ferramenta tentará entrar usando essas credenciais e os resultados detalhados de testes executados durante a tentativa de entrada serão fornecidos como saída.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Esta janela mostra um resultado com falha de teste. Clicar em examinar resultados detalhados mostrará informações sobre os resultados de cada teste que foi executado. Você também pode salvar os resultados em disco para compartilhá-los.
 
>[!NOTE]
>O analisador de conectividade também testa a federação ativa usando os protocolos ECP/PAOS e baseados em WS *. Se você não estiver usando esses itens, poderá desconsiderar o seguinte erro: Testando o fluxo de entrada ativo usando o ponto de extremidade de Federação ativo do provedor de identidade.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Verificar manualmente se o logon único foi configurado corretamente
A verificação manual fornece etapas adicionais que você pode executar para garantir que seu provedor de identidade SAML 2,0 esteja funcionando corretamente em muitos cenários.
Para verificar se o logon único foi configurado corretamente, conclua as seguintes etapas:


1. Em um computador ingressado no domínio, entre em seu serviço de nuvem usando o mesmo nome de entrada que você usa para suas credenciais corporativas.
2.  Clique dentro da caixa de senha. Se o logon único estiver configurado, a caixa senha será sombreada e você verá a seguinte mensagem: "Agora você precisa entrar na &lt;sua empresa&gt;."
3.  Clique no link entrar no &lt;seu empresa.&gt; Se você for capaz de entrar, então o logon único foi configurado.

## <a name="next-steps"></a>Próximos Passos


- [Gerenciamento e personalização de Serviços de Federação do Active Directory (AD FS) com Azure AD Connect](how-to-connect-fed-management.md)
- [Lista de compatibilidades de federação do Azure AD](how-to-connect-fed-compatibility.md)
- [Azure AD Connect instalação personalizada](how-to-connect-install-custom.md)

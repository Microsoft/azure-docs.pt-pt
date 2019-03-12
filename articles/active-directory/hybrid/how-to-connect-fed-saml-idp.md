---
title: 'Azure AD Connect: Utilize um fornecedor de identidade 2.0 para início de sessão único no | Documentos da Microsoft'
description: Este documento descreve a utilizar um Idp de conformidade de SAML 2.0 para início de sessão único no.
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
ms.openlocfilehash: 7b82c2261e949c724b1310ee43c6f3fe29766945
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531016"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Utilize um fornecedor de identidade 2.0 (IdP) para início de sessão único no

Este documento contém informações sobre como utilizar um SAML 2.0 compatível com SP Lite com base no perfil fornecedor de identidade como o preferencial Security Token Service (STS) / fornecedor de identidade. Este cenário é útil quando já tiver um diretório de utilizador e palavra-passe armazenar no local que pode ser acessado usando SAML 2.0. Este diretório de utilizador existente pode ser utilizado para início de sessão no Office 365 e outros recursos protegidos por AD do Azure. O perfil de SP de 2.0 de SAML-Lite baseia-se no padrão de identidade federada de Security Assertion Markup Language (SAML) amplamente usados para fornecer uma estrutura de início de sessão e atributo exchange.

>[!NOTE]
>Para obter uma lista de 3rd party Idps que foram testados para utilização com o Azure AD, consulte o [lista de compatibilidades de Federação do AD do Azure](how-to-connect-fed-compatibility.md)

A Microsoft suporta esta experiência de início de sessão, como a integração de um serviço cloud da Microsoft, como o Office 365, com o SAML 2.0 com base no perfil IdP corretamente configurado. Fornecedores de identidade de SAML 2.0 são produtos de terceiros e, portanto, a Microsoft não fornece suporte para a implementação, configuração, melhores práticas em relação à-los de resolução de problemas. Uma vez configurado corretamente, a integração com o SAML 2.0 fornecedor de identidade pode ser testado para configuração adequada, utilizando a ferramenta de analisador de conectividade do Microsoft, que é descrito mais detalhadamente abaixo. Para obter mais informações sobre o seu fornecedor de identidade baseada no perfil de SP de 2.0 de SAML-Lite, peça a organização que fornecido-lo.

>[!IMPORTANT]
>Só um conjunto limitado de clientes está disponível neste cenário de início de sessão com fornecedores de identidade de SAML 2.0, isto inclui:

>- Clientes baseados na Web, como o Outlook Web Access e o SharePoint Online
- E-mail sofisticados os clientes que utilizam autenticação básica e um método de acesso do Exchange suportado, como o IMAP, POP, Active Sync, MAPI, etc. (o ponto de final de protocolo de cliente avançada é necessário implementar), incluindo:
    - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, iPhone da Apple (várias versões do iOS)
    - Vários dispositivos de Google Android
    - Windows Phone 7, Windows Phone 7.8 e Windows Phone 8.0
    - Cliente de correio do Windows 8 e o cliente de correio do Windows 8.1
    - Cliente de correio do Windows 10

Todos os outros clientes não estão disponíveis neste cenário de início de sessão com o seu fornecedor de identidade do SAML 2.0. Por exemplo, o cliente de desktop do Lync 2010 não é capaz de iniciar sessão no serviço junto do seu fornecedor de identidade do SAML 2.0 configurada para início de sessão único.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Requisitos de protocolo do Azure AD SAML 2.0
Este documento contém os requisitos detalhados sobre o protocolo e a mensagem de formatação que o fornecedor de identidade de SAML 2.0 tem de implementar para federar com o Azure AD para ativar o início de sessão num ou mais serviços cloud da Microsoft (como o Office 365). A entidade da entidade confiadora de SAML 2.0 (SP-STS), para um serviço cloud da Microsoft utilizada neste cenário é o Azure AD.

É recomendável que verificar o seu fornecedor de identidade de SAML 2.0 mensagens de saída seja o mais semelhante para os rastreios de exemplo fornecidos possível. Além disso, utilize os valores de atributo específico do fornecido metadados do Azure AD sempre que possível. Quando estiver satisfeito com as mensagens de saída, pode testar com o Microsoft Connectivity Analyzer conforme descrito abaixo.

Os metadados do Azure AD podem ser transferido a partir deste URL: [ https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml ](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Para os clientes na China a utilizar a instância de China específicas do Office 365, o seguinte ponto de extremidade de Federação deve ser utilizado: [ https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml ](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>Requisitos de protocolo SAML
Esta seção detalhes como os pares de contratos de mensagem de solicitação e resposta são estruturadas para poder ajudá-lo a formatar as mensagens corretamente.

O Azure AD pode ser configurado para trabalhar com os fornecedores de identidade que utilizam o perfil de SAML 2.0 SP Lite com alguns requisitos específicos, conforme indicado abaixo. Utilizar as mensagens de solicitação e resposta SAML de exemplo juntamente com testes automatizados e manuais, pode trabalhar para conseguir a interoperabilidade com o Azure AD.

## <a name="signature-block-requirements"></a>Requisitos de bloco de assinatura
Na mensagem de resposta SAML, o nó de assinatura contém informações sobre a assinatura digital para a própria mensagem. O bloco de assinaturas tem os seguintes requisitos:

1. O nó de asserção em si tem de ser assinado
2.  O algoritmo RSA sha1 deve ser utilizado como o DigestMethod. Outros algoritmos de assinatura digital não são aceites.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Também pode inscrever-se o documento XML. 
4.  O algoritmo de transformar tem de corresponder ao valores no exemplo a seguir:    `<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  O algoritmo de SignatureMethod tem de corresponder ao exemplo a seguir:   `<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Enlaces suportados
Enlaces são os parâmetros de comunicações relacionadas com o transporte que são necessários. Os seguintes requisitos aplicam-se para as ligações

1. O HTTPS é o transporte necessário.
2.  Azure AD irá exigir HTTP POST para a submissão de token durante o início de sessão
3.  Azure AD irá utilizar o HTTP POST para o pedido de autenticação para o fornecedor de identidade e o REDIRECIONAMENTO para a mensagem de Logoff para o fornecedor de identidade.

## <a name="required-attributes"></a>Atributos necessários
Esta tabela mostra os requisitos de atributos específicos na mensagem de SAML 2.0.
 
|Atributo|Descrição|
| ----- | ----- |
|NameID|O valor desta asserção tem de ser o mesmo que o ImmutableID do utilizador do Azure AD. Pode ser até 64 carateres de alfanuméricos. Quaisquer carateres de seguros de não-html devem ser codificados, por exemplo um caráter "+" é mostrado como ".2B".|
|IDPEmail|O nome Principal de utilizador (UPN) está listado na resposta SAML como um elemento com o nome IDPEmail. o UserPrincipalName (UPN do utilizador) no Azure AD/Office 365. O UPN é no formato de endereço de e-mail. Valor do UPN no Windows Office 365 (Azure Active Directory).|
|Emissor|Necessário para ser um URI do fornecedor de identidade. Não reutilize o emissor de mensagens de exemplo. Se tiver vários domínios de nível superior na sua inquilinos do Azure AD o emissor tem de corresponder a definição de URI especificada configurada por domínio.|

>[!IMPORTANT]
>Atualmente o Azure AD suporta o URI seguinte de formato NameID de SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-formato: persistente.

## <a name="sample-saml-request-and-response-messages"></a>Mensagens de solicitação e resposta SAML do exemplo
Um par de mensagem de solicitação e resposta é apresentado para a troca de mensagens de início de sessão.
Segue-se uma mensagem de pedido de exemplo que é enviada do Azure AD para um fornecedor de identidade de SAML 2.0 de exemplo. O fornecedor de identidade de SAML 2.0 de exemplo é configurado para utilizar o protocolo SAML-P nos serviços de Federação do Active Directory (AD FS). Testes de interoperabilidade também foi concluída com outros fornecedores de identidade de SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Segue-se uma mensagem de resposta de exemplo que é enviada a partir do fornecedor de identidade em conformidade de SAML 2.0 de exemplo para o Azure AD / Office 365.

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

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Configurar o seu fornecedor de identidade em conformidade SAML 2.0
Esta secção contém diretrizes sobre como configurar o fornecedor de identidade de SAML 2.0 para federar com o Azure AD para ativar o acesso de início de sessão único para um ou mais serviços cloud da Microsoft (como o Office 365) utilizando o protocolo SAML 2.0. O SAML 2.0 entidade confiadora para um serviço cloud da Microsoft utilizado neste cenário é o Azure AD.

## <a name="add-azure-ad-metadata"></a>Adicionar metadados do Azure AD
O fornecedor de identidade de SAML 2.0 tem de cumprir para obter informações sobre o Azure AD da entidade confiadora de terceiros. O Azure AD publica os metadados em https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

Recomenda-se que sempre importa os metadados mais recente do Azure AD quando configurar o fornecedor de identidade de SAML 2.0.

>[!NOTE]
>O Azure AD não ler metadados do fornecedor de identidade.

## <a name="add-azure-ad-as-a-relying-party"></a>Adicionar o Azure AD como uma entidade confiadora
Tem de ativar a comunicação entre o seu fornecedor de identidade de SAML 2.0 e o Azure AD. Esta configuração será dependente de seu fornecedor de identidade específico e deve consultar a documentação para o mesmo. Normalmente, seria definido o ID da entidade confiadora de terceiros para o mesmo que o entityID dos metadados do Azure AD.

>[!NOTE]
>Certifique-se de que o relógio no seu servidor de fornecedor de identidade de SAML 2.0 está sincronizado a uma origem de hora exata. Uma hora de relógio imprecisa pode causar inícios de sessão federados efetuar a ativação.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instalar o Windows PowerShell para início de sessão com o fornecedor de identidade de SAML 2.0
Depois de ter configurado o fornecedor de identidade de SAML 2.0 para utilização com o início de sessão no Azure AD, a próxima etapa é baixar e instalar o módulo Azure Active Directory para Windows PowerShell. Uma vez instalado, irá utilizar estes cmdlets para configurar seus domínios do Azure AD como domínios federados.

O módulo Azure Active Directory para Windows PowerShell é um download para gerir os seus dados de que as organizações no Azure AD. Este módulo instala um conjunto de cmdlets para o Windows PowerShell; executar os cmdlets para configurar o acesso de início de sessão único para o Azure AD e, por sua vez, para todos os serviços de cloud está a subscrever. Para obter instruções sobre como transferir e instalar os cmdlets, consulte [https://technet.microsoft.com/library/jj151815.aspx](https://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Configurar uma confiança entre o seu fornecedor de identidade SAML e o Azure AD
Antes de configurar o Federação num domínio do Azure AD, tem de ter um domínio personalizado configurado. Não é possível federar o domínio predefinido que é fornecido pela Microsoft. O domínio padrão da Microsoft termina com "onmicrosoft.com".
Irá executar uma série de cmdlets na interface de linha de comandos do Windows PowerShell para adicionar ou converter domínios para início de sessão único.

Cada domínio do Azure Active Directory que pretende federar com seu fornecedor de identidade de SAML 2.0 tem de ser adicionado como um domínio de início de sessão único ou convertido para ser um domínio único início de sessão de um domínio padrão. Adicionar ou converter um domínio configura uma confiança entre o seu fornecedor de identidade de SAML 2.0 e o Azure AD.

O procedimento seguinte orienta-o através de conversão de um domínio padrão existente para um domínio Federado com o SP de 2.0 de SAML-Lite. 

>[!NOTE]
>Seu domínio poderá experienciar uma falha que afeta os utilizadores até 2 horas depois de colocar este passo.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Configurar um domínio do Azure AD Directory para a Federação


1. Ligar ao diretório do Azure AD como um administrador de inquilino: Connect-MsolService.
2.  Configure o seu domínio do Office 365 pretendido para usar a Federação com o SAML 2.0: `$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  Pode obter a cadeia de caracteres de codificada em base64 assinatura do certificado no seu ficheiro de metadados do IDP. Um exemplo desse local foi fornecido, mas pode diferir ligeiramente com base na sua implementação.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Para obter mais informações sobre "Set-MsolDomainAuthentication", consulte: [ https://technet.microsoft.com/library/dn194112.aspx ](https://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Tem de executar a utilização "$ecpUrl ="https://WS2012R2-0.contoso.com/PAOS"" apenas se configurar uma extensão ECP para o seu fornecedor de identidade. Os clientes Exchange Online, excluindo a aplicação OWA (Outlook Web), dependem uma publicação com base em ponto final de Active Directory. Se o seu STS do SAML 2.0 implementa um ponto de final Active Directory semelhante à implementação de ECP do Shibboleth de um ponto final de Active Directory pode ser possível que estes clientes avançados interagir com o serviço Exchange Online.

Depois de configurar a Federação pode voltar a mudar para "não federadas" (ou "gerido"), no entanto esta alteração demora duas horas a concluir e requer que a atribuição de novas palavras-passe aleatórias com base na cloud para início de sessão a cada utilizador. Mudar para "gerido" poderá ser necessário em alguns cenários para repor um erro nas definições do. Para obter mais informações sobre a conversão de domínio, consulte: [ https://msdn.microsoft.com/library/windowsazure/dn194122.aspx ](https://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Aprovisionar principais de utilizador para o Azure AD / Office 365
Pode autenticar os utilizadores do Office 365, terá de aprovisionar o Azure AD com entidades de utilizador que correspondem a asserção no afirmações de SAML 2.0. Se essas entidades de utilizador não são conhecidas para o Azure AD com antecedência, em seguida, eles não podem ser usados para o início de sessão federado. O Azure AD Connect ou o Windows PowerShell pode ser utilizado para aprovisionar principais de utilizador.

O Azure AD Connect pode ser utilizado para aprovisionar entidades de segurança para seus domínios no Azure AD Directory do Active Directory no local. Para obter mais informações, consulte [integrar seus diretórios no local com o Azure Active Directory](whatis-hybrid-identity.md).

Também pode ser utilizado o Windows PowerShell para automatizar a adição de novos utilizadores ao Azure AD e para sincronizar as alterações do diretório no local. Para utilizar os cmdlets do Windows PowerShell, tem de transferir os [do Azure Active Directory módulos](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Este procedimento mostra como adicionar um único utilizador para o Azure AD.


1. Ligar ao diretório do Azure AD como um administrador de inquilino: Connect-MsolService.
2.  Crie um novo principal de utilizador: ` New-MsolUser
        -UserPrincipalName elwoodf1@contoso.com
        -ImmutableId ABCDEFG1234567890
        -DisplayName "Elwood Folk"
        -FirstName Elwood 
        -LastName Folk 
        -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
        -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
        -UsageLocation "US" ` 

Para obter mais informações sobre o Check-out de "New-MsolUser", [https://technet.microsoft.com/library/dn194096.aspx](https://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>O valor de "UserPrinciplName" tem de corresponder ao valor que irá enviar para "IDPEmail" no seu afirmações de SAML 2.0 e o valor de "ImmutableID" tem de corresponder ao valor enviado no seu asserção "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Certifique-se de início de sessão único com o IDP do SAML 2.0
Como administrador, antes de verificar e gerir o início de sessão único (também denominado Federação de identidades), reveja as informações e efetue os passos nos seguintes artigos para configurar o início de sessão único com o seu fornecedor de identidade com base SP de 2.0 de SAML-Lite:


1.  Rever os requisitos de protocolo do Azure AD SAML 2.0
2.  Tiver configurado o fornecedor de identidade de SAML 2.0
3.  Instalar o Windows PowerShell para início de sessão único com o fornecedor de identidade de SAML 2.0
4.  Configurar uma confiança entre o fornecedor de identidade de SAML 2.0 e o Azure AD
5.  Aprovisionado um principal de utilizador de teste conhecidos para o Azure Active Directory (Office 365) através do Windows PowerShell ou do Azure AD Connect.
6.  Configurar a sincronização de diretório usando [do Azure AD Connect](whatis-hybrid-identity.md).

Depois de configurar início de sessão único com a sua identidade de SAML 2.0 SP-Lite com base em fornecedor, deve verificar se está a funcionar corretamente.

>[!NOTE]
>Se tiver convertido um domínio, em vez de adição de um deles, poderá demorar até 24 horas para configurar o início de sessão único.
Antes de o verificar início de sessão único, deve concluir a configuração da sincronização do Active Directory, sincronização dos diretórios e ativar os utilizadores sincronizados.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Utilize a ferramenta para verificar que esse início de sessão único foi configurado corretamente
Para verificar que esse início de sessão único foi configurado corretamente, pode efetuar o procedimento seguinte para confirmar a capacidade de início de sessão para o serviço cloud com as credenciais da sua empresa.

A Microsoft forneceu uma ferramenta que pode usar para testar o seu fornecedor de identidade baseada em SAML 2.0. Antes de executar a ferramenta de teste, tem de ter configurado um inquilino do Azure AD a federar com o seu fornecedor de identidade.

>[!NOTE]
>O analisador de conectividade requer o Internet Explorer 10 ou posterior.



1. Transferir o analisador de conectividade, [ https://testconnectivity.microsoft.com/?tabid=Client ](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Clique em instalar agora para começar a baixar e instalar a ferramenta.
3.  Selecione "Não é possível configurar a Federação com o Office 365, Azure ou noutros serviços que utilizam o Azure Active Directory".
4.  Assim que a ferramenta é transferida e em execução, verá a janela de diagnóstico de conectividade. A ferramenta apresentará a por meio do teste a ligação de Federação.
5.  O analisador de conectividade abrirá o SAML 2.0 IDP-lhe iniciar sessão, introduza as credenciais de principal de utilizador que está a testar: ![SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  No teste de início de sessão na janela de Federação, que deve introduzir um nome de conta e palavra-passe para o inquilino do Azure AD que está configurado para ser Federado com o seu fornecedor de identidade de SAML 2.0. A ferramenta irá tentar inicie a sessão com as credenciais e resultados detalhados de testes executados durante a tentativa de início de sessão serão fornecidos como saída.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Esta janela exibe um resultado de falha de teste. Clicar em revisão dos resultados em pormenor mostrará informações sobre os resultados para cada teste que foi executada. Também pode guardar os resultados para o disco para partilhá-los.
 
>[!NOTE]
>O analisador de conectividade também testa o Federação ativa com o WS *-com base e ECP/PAOS protocolos. Se não estiver a utilizar estas pode ignorar o erro seguinte: Teste o início de sessão fluxo ativo através do ponto final de Federação do Active Directory do seu fornecedor de identidade.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Verifique manualmente esse início de sessão único foi configurado corretamente
A verificação manual fornece passos adicionais que pode tomar para se certificar de que o seu fornecedor de identidade de SAML 2.0 está a funcionar corretamente em muitos cenários.
Para verificar que esse início de sessão único foi configurado corretamente, conclua os seguintes passos:


1. Num computador associado a um domínio, início de sessão ao seu serviço de nuvem com o mesmo nome de início de sessão que utilizam as suas credenciais empresariais.
2.  Clique dentro da caixa de palavra-passe. Se o início de sessão único estiver configurado, a caixa de palavra-passe será sombreada e verá a seguinte mensagem: "Agora tem de iniciar sessão no &lt;da sua empresa&gt;."
3.  Clique no início de sessão no &lt;da sua empresa&gt; ligação. Se for capaz de início de sessão, em seguida, único início de sessão foi configurado.

## <a name="next-steps"></a>Próximos Passos


- [Gestão de serviços de Federação de diretório Active Directory e a personalização com o Azure AD Connect](how-to-connect-fed-management.md)
- [Lista de compatibilidades de federação do Azure AD](how-to-connect-fed-compatibility.md)
- [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md)

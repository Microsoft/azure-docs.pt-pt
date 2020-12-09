---
title: 'Azure AD Connect: Use um fornecedor de identidade SAML 2.0 para sinal único - Azure'
description: Este documento descreve a utilização de um Idp compatível COM SAML 2.0 para um único sinal.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20088ccc79abe978eb5b3f1c98e958f6c26bcb86
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861804"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Utilizar um Fornecedor de Identidade (IdP) SAML 2.0 para Início de Sessão Único

Este documento contém informações sobre a utilização de um Fornecedor de Identidade SP-Lite baseado em perfis SAML 2.0 como o fornecedor preferido do Serviço de Token de Segurança (STS) /fornecedor de identidade. Este cenário é útil quando já tem um diretório de utilizadores e uma loja de senhas no local que podem ser acedidos usando o SAML 2.0. Este diretório de utilizadores existente pode ser usado para iniciar sing-on no Microsoft 365 e outros recursos seguros AZure AD. O perfil de SP-Lite SAML 2.0 baseia-se na padrão de identidade federada de marcação de afirmação de segurança (SAML) amplamente utilizada para fornecer um quadro de troca de sinais e atributos.

>[!NOTE]
>Para uma lista de 3º partido IDPs que foram testados para uso com Azure AD ver a lista de [compatibilidade da federação AD Azure](how-to-connect-fed-compatibility.md)

A Microsoft suporta esta experiência de início de sessão como a integração de um serviço de cloud da Microsoft, como o Microsoft 365, com o idP baseado em perfis SAML 2.0 devidamente configurado. Os fornecedores de identidade SAML 2.0 são produtos de terceiros e, por isso, a Microsoft não fornece suporte para a implementação, configuração, resolução de problemas das melhores práticas em relação aos mesmos. Uma vez configurada corretamente, a integração com o fornecedor de identidade SAML 2.0 pode ser testada para uma configuração adequada utilizando a Ferramenta de Analisador de Conectividade da Microsoft, que é descrita mais detalhadamente abaixo. Para mais informações sobre o seu SAML 2.0 SP-Lite fornecedor de identidade baseado em perfis, pergunte à organização que o forneceu.

> [!IMPORTANT]
> Apenas um conjunto limitado de clientes estão disponíveis neste cenário de login com os fornecedores de identidade SAML 2.0, isto inclui:
> 
> - Clientes baseados na Web, como o Outlook Web Access e o SharePoint Online
> - Clientes ricos em e-mail que utilizam a autenticação básica e um método de acesso de Intercâmbio suportado, tais como IMAP, POP, Ative Sync, MAPI, etc. (o ponto final do Protocolo de Cliente Melhorado é necessário para ser implementado), incluindo:
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (várias versões iOS)
>     - Vários dispositivos Android do Google
>     - Windows Phone 7, Windows Phone 7.8 e Windows Phone 8.0
>     - Cliente de correio windows 8 e cliente de correio windows 8.1
>     - Cliente de correio windows 10

Todos os outros clientes não estão disponíveis neste cenário de login com o seu Fornecedor de Identidade SAML 2.0. Por exemplo, o cliente de desktop Lync 2010 não é capaz de iniciar seduca no serviço com o seu Fornecedor de Identidade SAML 2.0 configurado para uma única sinteção.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Requisitos do protocolo Azure AD SAML 2.0
Este documento contém requisitos detalhados sobre o protocolo e formatação de mensagens que o seu fornecedor de identidade SAML 2.0 deve implementar para federar com Azure AD para permitir o início de um ou mais serviços na nuvem da Microsoft (como o Microsoft 365). O partido de confiante SAML 2.0 (SP-STS) para um serviço de nuvem microsoft utilizado neste cenário é Azure AD.

Recomenda-se que garanta que as suas mensagens de saída do fornecedor de identidade SAML 2.0 sejam tão semelhantes aos possíveis dos vestígios de amostragem fornecidos. Além disso, utilize valores de atributos específicos dos metadados Azure AD fornecidos sempre que possível. Uma vez satisfeito com as suas mensagens de saída, pode testar com o Analisador de Conectividade da Microsoft, conforme descrito abaixo.

Os metadados AD AD Azure podem ser descarregados a partir deste URL: [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml) .
Para os clientes na China que utilizam a instância específica da China da Microsoft 365, deve ser utilizado o seguinte ponto final da federação: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml) .

## <a name="saml-protocol-requirements"></a>Requisitos do protocolo SAML
Esta secção detalha como os pares de mensagens de pedido e resposta são reunidos de forma a ajudá-lo a formatar corretamente as suas mensagens.

O Azure AD pode ser configurado para trabalhar com fornecedores de identidade que usam o perfil SAML 2.0 SP Lite com alguns requisitos específicos como listados abaixo. Utilizando a amostra de pedidos e mensagens de resposta SAML juntamente com testes automatizados e manuais, pode trabalhar para alcançar a interoperabilidade com a Azure AD.

## <a name="signature-block-requirements"></a>Requisitos de bloco de assinatura
Dentro da mensagem de Resposta SAML, o nó Assinatura contém informações sobre a assinatura digital para a própria mensagem. O bloco de assinaturas tem os seguintes requisitos:

1. O nó de afirmação em si deve ser assinado
2.  O algoritmo RSA-sha1 deve ser usado como o DigestMethod. Outros algoritmos de assinatura digital não são aceites.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Também pode assinar o documento XML. 
4.  O Algoritmo de Transformação deve corresponder aos valores da seguinte amostra:    `<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  O algoritmo SignatureMethod deve corresponder à seguinte amostra:   `<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Encadernações apoiadas
As ligações são os parâmetros de comunicações relacionados com o transporte que são necessários. Os seguintes requisitos aplicam-se às encadernações

1. HTTPS é o transporte necessário.
2.  A Azure AD necessitará de HTTP POST para submissão de fichas durante a sinvação.
3.  A Azure AD utilizará HTTP POST para o pedido de autenticação ao fornecedor de identidade e redirecionará para a mensagem de assinatura para o fornecedor de identidade.

## <a name="required-attributes"></a>Atributos necessários
Esta tabela apresenta requisitos para atributos específicos na mensagem SAML 2.0.
 
|Atributo|Descrição|
| ----- | ----- |
|NameID|O valor desta afirmação deve ser o mesmo que o ImutávelID do utilizador da Azure. Pode ser até 64 caracteres alfa numéricos. Qualquer caracteres seguros não html deve ser codificado, por exemplo, um caracteres "+" é mostrado como ".2B".|
|IDPEmail|O Nome Principal do Utilizador (UPN) está listado na resposta SAML como um elemento com o nome IDPEmail O Nome Do UtilizadorPrincipalName (UPN) em Azure AD/Microsoft 365. A UPN está no formato de endereço de e-mail. Valor UPN no Windows Microsoft 365 (Azure Ative Directory).|
|Emissor|Necessário para ser um URI do fornecedor de identidade. Não reutilizá o Emitente a partir das mensagens de amostra. Se tiver vários domínios de nível superior nos seus inquilinos AD Azure, o Emitente deve corresponder à definição URI especificada configurada por domínio.|

>[!IMPORTANT]
>A Azure AD suporta atualmente o seguinte formato NameID URI para SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-formato:persistente.

## <a name="sample-saml-request-and-response-messages"></a>Amostra de pedidos e mensagens de resposta SAML
É apresentado um par de mensagens de pedido e resposta para a troca de mensagens de inscrição.
Segue-se uma mensagem de pedido de amostra enviada da Azure AD para uma amostra de saml 2.0 fornecedor de identidade. A amostra SAML 2.0 fornecedor de identidade é Ative Directory Federation Services (AD FS) configurada para usar o protocolo SAML-P. Os testes de interoperabilidade também foram concluídos com outros fornecedores de identidade SAML 2.0.

```xml
    <samlp:AuthnRequest 
        xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" 
        xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" 
        ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" 
        IssueInstant="2014-01-30T16:18:35Z" 
        Version="2.0" 
        AssertionConsumerServiceIndex="0" >
            <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
            <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>
```

Segue-se uma mensagem de resposta à amostra enviada da amostra SAML 2.0 fornecedor de identidade conforme a Azure AD / Microsoft 365.

```xml
    <samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
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
    </samlp:Response>
```

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Configure o seu fornecedor de identidade compatível COM SAML 2.0
Esta secção contém diretrizes sobre como configurar o seu fornecedor de identidade SAML 2.0 para federar com Azure AD para permitir o acesso único a um ou mais serviços na nuvem da Microsoft (como o Microsoft 365) utilizando o protocolo SAML 2.0. O partido de assistência SAML 2.0 para um serviço de nuvem microsoft usado neste cenário é Azure AD.

## <a name="add-azure-ad-metadata"></a>Adicionar metadados Azure AD
O seu fornecedor de identidade SAML 2.0 precisa de aderir a informações sobre a parte de confiança Azure AD. A Azure AD publica metadados em https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml .

Recomenda-se que importe sempre os metadados AD AD mais recentes ao configurar o seu fornecedor de identidade SAML 2.0.

>[!NOTE]
>A Azure AD não lê metadados do fornecedor de identidade.

## <a name="add-azure-ad-as-a-relying-party"></a>Adicione Azure AD como uma festa de confiação
Deve ativar a comunicação entre o seu fornecedor de identidade SAML 2.0 e a Azure AD. Esta configuração será dependente do seu fornecedor de identidade específico e deverá consultar a documentação para o mesmo. Normalmente, definiria o ID do partido dependente para o mesmo que a entidadeID dos metadados AD Azure.

>[!NOTE]
>Verifique se o relógio do seu servidor de fornecedor de identidade SAML 2.0 está sincronizado com uma fonte de tempo precisa. Um tempo de relógio impreciso pode fazer com que os logins federados falhem.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instale o Windows PowerShell para iniciar scrita com o fornecedor de identidade SAML 2.0
Depois de configurar o seu fornecedor de identidade SAML 2.0 para utilização com o sinal de Ad Azure, o próximo passo é descarregar e instalar o Módulo de Diretório Ativo Azure para o Windows PowerShell. Uma vez instalados, utilizará estes cmdlets para configurar os seus domínios AD Azure como domínios federados.

O Módulo de Diretório Ativo Azure para Windows PowerShell é um download para gerir os dados das suas organizações em Azure AD. Este módulo instala um conjunto de cmdlets para o Windows PowerShell; você executou esses cmdlets para configurar um único acesso de inscrição ao Azure AD e, por sua vez, a todos os serviços na nuvem a que está subscrito. Para obter instruções sobre como descarregar e instalar os cmdlets, consulte [/versões anteriores/azure/jj151815 (v=azure.100)](/previous-versions/azure/jj151815(v=azure.100))

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Crie um fundo entre o seu fornecedor de identidade SAML e a Azure AD
Antes de configurar a federação num domínio AD Azure, deve ter um domínio personalizado configurado. Não é possível federar o domínio predefinido fornecido pela Microsoft. O domínio padrão da Microsoft termina com "onmicrosoft.com".
Irá executar uma série de cmdlets na interface da linha de comando Do Windows PowerShell para adicionar ou converter domínios para um único sinal de acesso.

Cada domínio do Azure Ative Directory que pretende federar utilizando o seu fornecedor de identidade SAML 2.0 deve ser adicionado como um único domínio de sinalização ou convertido para ser um único domínio de sinalização de um domínio padrão. Adicionar ou converter um domínio cria uma confiança entre o seu fornecedor de identidade SAML 2.0 e Azure AD.

O procedimento que se segue acompanha a conversão de um domínio padrão existente para um domínio federado utilizando o SAML 2.0 SP-Lite. 

>[!NOTE]
>O seu domínio pode sofrer uma paragem que impacte os utilizadores até 2 horas após este passo.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Configurar um domínio no seu Diretório AD Azure para a federação


1. Ligue-se ao seu Diretório AD Azure como administrador de inquilinos:

    ```powershell
    Connect-MsolService
    ```
    
2. Configure o seu domínio Microsoft 365 desejado para utilizar a federação com SAML 2.0:

    ```powershell
    $dom = "contoso.com" 
    $BrandName - "Sample SAML 2.0 IDP" 
    $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" 
    $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" 
    $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" 
    $MyURI = "urn:uri:MySamlp2IDP" 
    $MySigningCert = "MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" 
    $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" 
    $Protocol = "SAMLP" 
    Set-MsolDomainAuthentication `
        -DomainName $dom `
        -FederationBrandName $BrandName `
        -Authentication Federated `
        -PassiveLogOnUri $LogOnUrl `
        -ActiveLogOnUri $ecpUrl `
        -SigningCertificate $MySigningCert `
        -IssuerUri $MyURI `
        -LogOffUri $LogOffUrl `
        -PreferredAuthenticationProtocol $Protocol
    ``` 

3.  Pode obter a base de certificado de assinatura 64 cadeia codificada a partir do seu ficheiro de metadados IDP. Um exemplo desta localização foi fornecido, mas pode diferir ligeiramente com base na sua implementação.

    ```xml
    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
        <KeyDescriptor use="signing">
          <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
             <X509Data>
                 <X509Certificate> MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate>
              </X509Data>
            </KeyInfo>
        </KeyDescriptor>
    </IDPSSODescriptor>
    ``` 

Para obter mais informações sobre "Set-MsolDomainAuthentication", consulte: [/anteriores versões/azure/dn194112 (v=azure.100)](/previous-versions/azure/dn194112(v=azure.100)).

>[!NOTE]
>Só deve utilizar `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` se configurar uma extensão ECP para o seu fornecedor de identidade. Os clientes exchange online, excluindo a Aplicação Web Outlook (OWA), contam com um ponto final ativo baseado em POST. Se o seu SAML 2.0 STS implementar um ponto final ativo semelhante à implementação do ECP da Shibboleth de um ponto final ativo, pode ser possível que estes clientes ricos interajam com o serviço Exchange Online.

Uma vez configurada a federação, pode voltar a ser "não federada" (ou "gerida"), no entanto esta alteração demora até duas horas a ser concluída e requer a atribuição de novas palavras-passe aleatórias para o início de sessão baseado na nuvem a cada utilizador. Pode ser necessário voltar a "gerido" em alguns cenários para redefinir um erro nas suas definições. Para obter mais informações sobre a conversão do Domínio consulte: [/versões anteriores/azure/dn194122(v=azure.100)](/previous-versions/azure/dn194122(v=azure.100)).

## <a name="provision-user-principals-to-azure-ad--microsoft-365"></a>Principais utilizadores da Provisão para Azure AD / Microsoft 365
Antes de autenticar os seus utilizadores para o Microsoft 365, tem de providenciar a Azure AD com os principais utilizadores que correspondam à afirmação na reclamação SAML 2.0. Se estes principais utilizadores não forem conhecidos antecipadamente da Azure AD, então não podem ser utilizados para o súpido federado. Ou o Azure AD Connect ou o Windows PowerShell podem ser utilizados para obter os principais do utilizador.

O Azure AD Connect pode ser utilizado para a provisionar os principais dos seus domínios no seu Diretório AD Azure a partir do Ative Directory. Para obter informações mais [detalhadas, consulte Integrar os seus diretórios no local com o Azure Ative Directory](whatis-hybrid-identity.md).

O Windows PowerShell também pode ser utilizado para automatizar a adição de novos utilizadores ao Azure AD e para sincronizar as alterações do diretório no local. Para utilizar os cmdlets Windows PowerShell, tem de descarregar os [Módulos de Diretório Ativo Azure](/powershell/azure/active-directory/install-adv2).

Este procedimento mostra como adicionar um único utilizador ao Azure AD.


1. Ligue-se ao seu Diretório AD Azure como administrador de inquilino: Connect-MsolService.
2. Criar um novo responsável pelo utilizador:

    ```powershell
    New-MsolUser `
      -UserPrincipalName elwoodf1@contoso.com `
      -ImmutableId ABCDEFG1234567890 `
      -DisplayName "Elwood Folk" `
      -FirstName Elwood `
      -LastName Folk `
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" `
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" `
      -UsageLocation "US" 
    ```

Para mais informações sobre o check-out "New-MsolUser", [/versões anteriores/azure/dn194096 (v=azure.100)](/previous-versions/azure/dn194096(v=azure.100))

>[!NOTE]
>O valor "UserPrinciplName" deve corresponder ao valor que irá enviar para "IDPEmail" na sua reclamação SAML 2.0 e o valor "ImuttableID" deve corresponder ao valor enviado na sua afirmação "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Verifique o único sinal de s-on com o seu IDP SAML 2.0
Como administrador, antes de verificar e gerir um único sign-on (também chamado federação de identidade), reveja as informações e execute os passos nos seguintes artigos para configurar um único sign-on com o seu SAML 2.0 SP-Lite fornecedor de identidade baseado:


1.  Reviu os Requisitos do Protocolo Azure AD SAML 2.0
2.  Configuraste o teu fornecedor de identidade SAML 2.0
3.  Instale o Windows PowerShell para um único sinal de saúde com o fornecedor de identidade SAML 2.0
4.  Criar um fundo entre o prestador de identidade SAML 2.0 e o Azure AD
5.  Forte um conhecido diretor de teste ao Azure Ative Directory (Microsoft 365) através do Windows PowerShell ou do Azure AD Connect.
6.  Configure a sincronização do diretório utilizando [o Azure AD Connect](whatis-hybrid-identity.md).

Depois de configurar um único sismo com o seu FORNECEDOR de identidade baseado em SP-Lite SAML 2.0, deverá verificar se está a funcionar corretamente.

>[!NOTE]
>Se converteu um domínio, em vez de adicionar um, pode levar até 24 horas para configurar uma única inscrição.
Antes de verificar uma única sposição, deve terminar a configuração da sincronização do Ative Directory, sincronizar os seus diretórios e ativar os seus utilizadores sincronizados.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Utilize a ferramenta para verificar se o único sinal foi configurado corretamente
Para verificar se o único sindes de sôm numa sação foi configurado corretamente, pode executar o seguinte procedimento para confirmar que é capaz de iniciar sação no serviço de cloud com as suas credenciais corporativas.

A Microsoft forneceu uma ferramenta que pode usar para testar o seu fornecedor de identidade baseado em SAML 2.0. Antes de executar a ferramenta de teste, você deve ter configurado um inquilino AZure AD para federar com o seu fornecedor de identidade.

>[!NOTE]
>O Analisador de Conectividade requer o Internet Explorer 10 ou mais tarde.



1. Descarregue o Analisador de Conectividade a partir de, [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client) .
2.  Clique em Instalar Agora para começar a descarregar e instalar a ferramenta.
3.  Selecione "Não posso criar uma federação com o Office 365, Azure, ou outros serviços que utilizem o Azure Ative Directory".
4.  Assim que a ferramenta for descarregada e em funcionamento, verá a janela de Diagnóstico de Conectividade. A ferramenta irá adiá-lo através do teste da sua ligação à federação.
5.  O Analisador de Conectividade abrirá o seu IDP SAML 2.0 para iniciar scriminação, insira as credenciais para o principal utilizador que está a testar: ![ Screenshot que mostra a janela de entrada para o seu IDP SAML 2.0.](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  Na janela de teste da Federação, deve introduzir um nome de conta e senha para o inquilino AZURE AD que está configurado para ser federado com o seu fornecedor de identidade SAML 2.0. A ferramenta tentará iniciar s presenciação utilizando essas credenciais e os resultados detalhados dos testes realizados durante a tentativa de entrada serão fornecidos como saída.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Esta janela mostra um resultado falhado dos testes. Clicar em Rever os resultados detalhados mostrará informações sobre os resultados de cada teste que foi realizado. Também pode guardar os resultados para o disco para partilhá-los.
 
>[!NOTE]
>O analisador de conectividade também testa a Federação Ativa utilizando os protocolos WS*e ECP/PAOS. Se não estiver a utilizar estes, pode ignorar o seguinte erro: Testar o fluxo de entrada ativo utilizando o ponto final da federação ativa do seu fornecedor de identidade.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Verifique manualmente se o único sinal foi configurado corretamente
A verificação manual fornece medidas adicionais que pode tomar para garantir que o seu Fornecedor de Identidade SAML 2.0 está a funcionar corretamente em muitos cenários.
Para verificar se o único sinal de inscrição foi corretamente configurado, complete os seguintes passos:


1. Num computador ligado a domínio, inscreva-se no seu serviço de cloud usando o mesmo nome de inscrição que utiliza para as suas credenciais corporativas.
2.  Clique dentro da caixa de senha. Se for configurado um único sinal, a caixa de palavra-passe será sombreada e verá a seguinte mensagem: "Agora é obrigado a iniciar seduca na &lt; sua &gt; empresa."
3.  Clique no início de &lt; &gt; sôm. Se conseguir iniciar surgiu, foi configurado um único sinse.

## <a name="next-steps"></a>Passos Seguintes


- [Gestão e personalização de Serviços da Federação de Diretórios Ativos com Azure AD Connect](how-to-connect-fed-management.md)
- [Lista de compatibilidades de federação do Azure AD](how-to-connect-fed-compatibility.md)
- [Azure AD Connect Custom Installation](how-to-connect-install-custom.md)

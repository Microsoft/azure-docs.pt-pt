---
title: Proteger o seu serviço RESTful utilizando certificados de cliente no Azure Active Directory B2C | Documentos da Microsoft
description: Proteger suas trocas de afirmações de REST API personalizadas no seu Azure AD B2C ao utilizar certificados de cliente
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b007aa4619effbd34e4e969e4ce7b58f3b0c4cf6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510531"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Proteger o seu serviço RESTful usando certificados de cliente

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Num artigo relacionado, [criar um serviço RESTful](active-directory-b2c-custom-rest-api-netfw.md) que interage com o Azure Active Directory B2C (Azure AD B2C).

Neste artigo, saiba como restringir o acesso à sua aplicação web do Azure (RESTful API) ao utilizar um certificado de cliente. Esse mecanismo é chamado de autenticação mútua de TLS, ou *autenticação de certificado de cliente*. Apenas os serviços que têm os certificados adequados, como o Azure AD B2C, podem aceder ao seu serviço.

>[!NOTE]
>Também pode proteger o seu serviço RESTful usando [autenticação básica HTTP](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). No entanto, a autenticação básica HTTP é considerada menos segura através de um certificado de cliente. Nossa recomendação é proteger o serviço RESTful com autenticação de certificado de cliente conforme descrito neste artigo.

Este artigo descreve como:
* Configure a sua aplicação web para utilizar a autenticação de certificado de cliente.
* Carregue o certificado para chaves de política do Azure AD B2C.
* Configure a política personalizada para utilizar o certificado de cliente.

## <a name="prerequisites"></a>Pré-requisitos
* Conclua os passos a [trocas de afirmações de API do REST de integrar](active-directory-b2c-custom-rest-api-netfw.md) artigo.
* Obtenha um certificado válido (um ficheiro. pfx com uma chave privada).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Passo 1: Configurar uma aplicação web para autenticação de certificados de cliente
Para configurar **App Service do Azure** para exigir certificados de cliente, defina a aplicação web `clientCertEnabled` definição do site *verdadeiro*. Para efetuar esta alteração, no portal do Azure, abra a página da aplicação web. No painel de navegação esquerdo, sob **configurações** selecionar **definições de SSL**. Na **certificados de cliente** secção, ative os **certificado de cliente de entrada** opção.

>[!NOTE]
>Certifique-se de que o seu plano do serviço de aplicações do Azure é Standard ou superior. Para obter mais informações, consulte [descrição geral aprofundada dos planos do App Service do Azure](https://docs.microsoft.com/azure/app-service/overview-hosting-plans).

>[!NOTE]
>Para obter mais informações sobre como o **clientCertEnabled** propriedade, veja [autenticação mútua de TLS configurar para aplicações web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Passo 2: Carregue o certificado para chaves de política do Azure AD B2C
Depois de definir `clientCertEnabled` para *true*, a comunicação com a sua API RESTful requer um certificado de cliente. Para obter e carregar para armazenar o certificado de cliente no seu inquilino do Azure AD B2C, efetue o seguinte: 
1. No seu inquilino do Azure AD B2C, selecione **definições do B2C** > **Framework de experiência de identidade**.

2. Para ver as chaves que estão disponíveis no seu inquilino, selecione **chaves de política**.

3. Selecione **Adicionar**.  
    O **crie uma chave** é aberta a janela.

4. Na **opções** caixa, selecione **carregar**.

5. Na **Name** , escreva **B2cRestClientCertificate**.  
    O prefixo *B2C_1A_* é adicionado automaticamente.

6. Na **carregamento de ficheiros** caixa, selecione o ficheiro do seu certificado. pfx com uma chave privada.

7. Na **palavra-passe** , escreva a palavra-passe do certificado.

    ![Carregar a chave da política](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Selecione **Criar**.

8. Para exibir as chaves que estão disponíveis no seu inquilino e confirme que criou o `B2C_1A_B2cRestClientCertificate` principais, selecione **chaves da política**.

## <a name="step-3-change-the-technical-profile"></a>Passo 3: Alterar o perfil técnico
Para suportar a autenticação de certificado de cliente na sua política personalizada, altere o perfil técnico efetuando o seguinte procedimento:

1. No seu diretório de trabalho, abra a *TrustFrameworkExtensions.xml* ficheiro de política de extensão.

2. Procure o `<TechnicalProfile>` nó que inclui `Id="REST-API-SignUp"`.

3. Localize o `<Metadata>` elemento.

4. Alteração da *AuthenticationType* ao *ClientCertificate*, da seguinte forma:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Imediatamente após o fecho `<Metadata>` elemento, adicione o seguinte fragmento XML: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Depois de adicionar o fragmento, o perfil técnico deve ter um aspeto semelhante ao seguinte código XML:

    ![Definir os elementos XML de autenticação de ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Passo 4: Carregar a política para o seu inquilino

1. Na [portal do Azure](https://portal.azure.com), mude para o [contexto do inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, selecione **do Azure AD B2C**.

2. Selecione **arquitetura de experiências de identidade**.

3. Selecione **todas as políticas**.

4. Selecione **carregar política**.

5. Selecione o **substituir a política, se existir** caixa de verificação.

6. Carregar o *TrustFrameworkExtensions.xml* de ficheiros e, em seguida, certifique-se de que ele passa a validação.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Passo 5: Testar a política personalizada com executar agora
1. Open **definições do Azure AD B2C**e, em seguida, selecione **Framework de experiência de identidade**.

    >[!NOTE]
    >Executar agora requer, pelo menos, um aplicativo para ser foi pré-registado no inquilino. Para saber como registar aplicações, veja o Azure AD B2C [começar](active-directory-b2c-get-started.md) artigo ou o [registo de aplicação](active-directory-b2c-app-registration.md) artigo.

2. Open **B2C_1A_signup_signin**, a política personalizada de terceiros (RP) da entidade confiadora que carregou e, em seguida, selecione **executar agora**.

3. Testar o processo, escrevendo **teste** no **nome fornecido** caixa.  
    O Azure AD B2C apresenta uma mensagem de erro na parte superior da janela.    

    ![Testar a API de identidade](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Na **determinado nome** , escreva um nome (diferente de "teste").  
    O Azure AD B2C, o utilizador se inscreve e, em seguida, envia um número de fidelidade à sua aplicação. Tenha em atenção o número neste exemplo JWT:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
     "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
     "acr": "b2c_1a_signup_signin",
     "nonce": "defaultNonce",
     "iat": 1507122303,
     "auth_time": 1507122303,
     "loyaltyNumber": "290",
     "given_name": "Emily",
     "emails": ["B2cdemo@outlook.com"]
   }
   ```

   >[!NOTE]
   >Se receber a mensagem de erro *o nome não é válido, forneça um nome válido*, significa que o Azure AD B2C chamado com êxito o serviço RESTful enquanto ele apresentou o certificado de cliente. A próxima etapa é validar o certificado.

## <a name="step-6-add-certificate-validation"></a>Passo 6: Adicionar a validação de certificado
O certificado de cliente do Azure AD B2C envia ao seu serviço RESTful não tem de passar pela validação pela plataforma do App Service do Azure, exceto para verificar se o certificado existe. A validar o certificado é da responsabilidade da aplicação web. 

Nesta secção, adicione o código ASP.NET de exemplo que valida as propriedades do certificado para fins de autenticação.

> [!NOTE]
>Para obter mais informações sobre como configurar o serviço de aplicações do Azure para a autenticação de certificado de cliente, consulte [autenticação mútua de TLS configurar para aplicações web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 adicionar as definições da aplicação ao arquivo Web. config de seu projeto
No projeto do Visual Studio que criou anteriormente, adicione as seguintes definições de aplicação para o *Web. config* ficheiro após o `appSettings` elemento:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Substituir o certificado **nome do requerente**, **nome do emissor**, e **thumbprint do certificado** valores pelos seus valores de certificado.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 Adicione a função de IsValidClientCertificate
Abra o *Controllers\IdentityController.cs* de ficheiros e, em seguida, adicionar ao `Identity` a seguinte função de classe de controlador: 

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

No código de exemplo anterior, podemos aceitar o certificado como válido apenas se todas as condições seguintes forem cumpridas:
* O certificado não expirou e estiver ativo durante a hora atual no servidor.
* O `Subject` nome do certificado é igual ao `ClientCertificate:Subject` valor de definição de aplicação.
* O `Issuer` nome do certificado é igual ao `ClientCertificate:Issuer` valor de definição de aplicação.
* O `thumbprint` do certificado é igual ao `ClientCertificate:Thumbprint` valor de definição de aplicação.

>[!IMPORTANT]
>Dependendo da confidencialidade do seu serviço, poderá ter de adicionar mais validações. Por exemplo, poderá ter de testar se o certificado está ligado a uma autoridade de raiz fidedigna, validação do nome de organização de emissor e assim por diante.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 chamar a função de IsValidClientCertificate
Abra o *Controllers\IdentityController.cs* ficheiro e, em seguida, no início do `SignUp()` de função, adicione o seguinte fragmento de código: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Depois de adicionar o trecho de código, seu `Identity` controlador deve ter um aspeto semelhante ao seguinte código:

![Adicione o código de validação de certificado](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Passo 7: Publicar o seu projeto para o Azure e testá-lo
1. Na **Explorador de soluções**, clique com botão direito a **Contoso.AADB2C.API** projeto e, em seguida, selecione **publicar**.

2. Repita "Passo 6" e testar novamente a sua política personalizada com a validação de certificado. Tente executar a política e certifique-se de que tudo funciona depois de adicionar a validação.

3. No seu *Web. config* de ficheiros, altere o valor de `ClientCertificate:Subject` para **inválido**. Execute novamente a política e deverá ver uma mensagem de erro.

4. Alterar o valor de volta para **válido**e certifique-se de que a política pode chamar a API REST.

Se precisar de resolver este passo, consulte [recolher registos com o Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Transferir os ficheiros de política concluída e o código
* Depois de concluir o [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md) passo a passo, é recomendável que criar seu cenário com seus próprios arquivos de política personalizada. Para referência, nós fornecemos [arquivos de diretiva de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Pode baixar o código completo da [solução do Visual Studio de exemplo para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 

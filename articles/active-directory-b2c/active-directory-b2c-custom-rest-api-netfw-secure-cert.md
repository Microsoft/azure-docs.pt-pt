---
title: Proteger um serviço RESTful usando certificados de cliente
titleSuffix: Azure AD B2C
description: Proteger suas trocas de declarações da API REST personalizadas no seu Azure AD B2C usando certificados de cliente
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1d396de57a12285fb6cc682510fa6f95585465d0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949862"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Proteger seu serviço RESTful usando certificados de cliente

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Em um artigo relacionado, você [cria um serviço RESTful](active-directory-b2c-custom-rest-api-netfw.md) que interage com Azure Active Directory B2C (Azure ad B2C).

Neste artigo, você aprenderá a restringir o acesso ao seu aplicativo Web do Azure (API RESTful) usando um certificado de cliente. Esse mecanismo é chamado de autenticação mútua TLS ou *autenticação de certificado do cliente*. Somente os serviços que têm os certificados apropriados, como Azure AD B2C, podem acessar seu serviço.

>[!NOTE]
>Você também pode proteger seu serviço RESTful usando a [autenticação básica http](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). No entanto, a autenticação básica HTTP é considerada menos segura em um certificado de cliente. Nossa recomendação é proteger o serviço RESTful usando a autenticação de certificado do cliente, conforme descrito neste artigo.

Este artigo fornece detalhes sobre como:
* Configure seu aplicativo Web para usar a autenticação de certificado do cliente.
* Carregue o certificado para Azure AD B2C chaves de política.
* Configure sua política personalizada para usar o certificado do cliente.

## <a name="prerequisites"></a>Pré-requisitos
* Conclua as etapas no artigo [integrar trocas de declarações da API REST](active-directory-b2c-custom-rest-api-netfw.md) .
* Obtenha um certificado válido (um arquivo. pfx com uma chave privada).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Etapa 1: configurar um aplicativo Web para autenticação de certificado de cliente
Para configurar **Azure app serviço** para exigir certificados de cliente, defina a configuração do aplicativo Web `clientCertEnabled` site como *true*. Para fazer essa alteração, na portal do Azure, abra a página do aplicativo Web. No painel de navegação esquerdo, em **configurações** , selecione **configurações de SSL**. Na seção **certificados de cliente** , ative a opção de **certificado de cliente de entrada** .

>[!NOTE]
>Verifique se seu plano de serviço Azure App é Standard ou superior. Para obter mais informações, consulte [Azure app visão geral detalhada dos planos de serviço](https://docs.microsoft.com/azure/app-service/overview-hosting-plans).

>[!NOTE]
>Para obter mais informações sobre como definir a propriedade **clientCertEnabled** , consulte [Configurar a autenticação mútua de TLS para aplicativos Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Etapa 2: carregar seu certificado para Azure AD B2C chaves de política
Depois de definir `clientCertEnabled` como *true*, a comunicação com a API RESTful requer um certificado de cliente. Para obter, carregar e armazenar o certificado do cliente em seu locatário Azure AD B2C, faça o seguinte:
1. No locatário do Azure AD B2C, selecione **configurações do B2C** > **estrutura de experiência de identidade**.

2. Para exibir as chaves que estão disponíveis em seu locatário, selecione **chaves de política**.

3. Selecione **Adicionar**.
    A janela **criar uma chave** é aberta.

4. Na caixa **Opções** , selecione **carregar**.

5. Na caixa **nome** , digite **B2cRestClientCertificate**.
    O prefixo *B2C_1A_* é adicionado automaticamente.

6. Na caixa **upload de arquivo** , selecione o arquivo. pfx do certificado com uma chave privada.

7. Na caixa **senha** , digite a senha do certificado.

    ![Carregue a chave de política na página criar uma chave no portal do Azure](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Selecione **Criar**.

8. Para exibir as chaves que estão disponíveis em seu locatário e confirmar que você criou a chave de `B2C_1A_B2cRestClientCertificate`, selecione **chaves de política**.

## <a name="step-3-change-the-technical-profile"></a>Etapa 3: alterar o perfil técnico
Para dar suporte à autenticação de certificado de cliente em sua política personalizada, altere o perfil técnico fazendo o seguinte:

1. No diretório de trabalho, abra o arquivo de política de extensão *TrustFrameworkExtensions. xml* .

2. Procure o nó `<TechnicalProfile>` que inclui `Id="REST-API-SignUp"`.

3. Localize o elemento `<Metadata>`.

4. Altere a *AuthenticationType* para *ClientCertificate*, da seguinte maneira:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Imediatamente após o elemento `<Metadata>` de fechamento, adicione o seguinte trecho de código XML:

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Depois de adicionar o trecho, seu perfil técnico deve se parecer com o seguinte código XML:

    ![Definir elementos XML de autenticação ClientCertificate](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Etapa 4: carregar a política para seu locatário

1. No [portal do Azure](https://portal.azure.com), alterne para o [contexto do seu locatário do Azure ad B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, selecione **Azure ad B2C**.

2. Selecione **Identity Experience Framework**.

3. Selecione **todas as políticas**.

4. Selecione **carregar política**.

5. Marque a caixa de seleção **substituir a política se ela existir** .

6. Carregue o arquivo *TrustFrameworkExtensions. xml* e verifique se ele passou na validação.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Etapa 5: testar a política personalizada usando executar agora
1. Abra **Azure ad B2C configurações**e, em seguida, selecione **estrutura de experiência de identidade**.

    >[!NOTE]
    >Executar agora exige que pelo menos um aplicativo seja preregistrado no locatário. Para saber como registrar aplicativos, consulte o artigo Azure AD B2C [introdução](active-directory-b2c-get-started.md) ou o artigo [registro de aplicativo](active-directory-b2c-app-registration.md) .

2. Abra **B2C_1A_signup_signin**, a política personalizada de RP (terceira parte confiável) que você carregou e, em seguida, selecione **executar agora**.

3. Teste o processo digitando **teste** na caixa **nome especificado** .
    Azure AD B2C exibe uma mensagem de erro na parte superior da janela.

    ![Caixa de texto de nome fornecido realçada e erro de validação de entrada mostrado](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Na caixa **nome determinado** , digite um nome (diferente de "teste").
    Azure AD B2C inscreve o usuário e, em seguida, envia um número de fidelidade ao seu aplicativo. Observe o número neste exemplo de JWT:

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
   >Se você receber a mensagem de erro, *o nome não é válido, forneça um nome válido*, isso significa que Azure ad B2C chamado com êxito o serviço RESTful enquanto ele apresentou o certificado do cliente. A próxima etapa é validar o certificado.

## <a name="step-6-add-certificate-validation"></a>Etapa 6: Adicionar validação de certificado
O certificado do cliente que Azure AD B2C envia para o serviço RESTful não passa pela validação da plataforma de serviço Azure App, exceto para verificar se o certificado existe. A validação do certificado é responsabilidade do aplicativo Web.

Nesta seção, você adiciona um código ASP.NET de exemplo que valida as propriedades do certificado para fins de autenticação.

> [!NOTE]
>Para obter mais informações sobre como configurar o serviço de Azure App para autenticação de certificado de cliente, consulte [Configurar a autenticação mútua TLS para aplicativos Web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6,1 adicionar configurações de aplicativo ao arquivo Web. config do seu projeto
No projeto do Visual Studio que você criou anteriormente, adicione as seguintes configurações do aplicativo ao arquivo *Web. config* após o elemento `appSettings`:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Substitua o nome da **entidade**do certificado, o **nome do emissor**e os valores de **impressão digital do certificado** pelos valores do certificado.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6,2 adicionar a função IsValidClientCertificate
Abra o arquivo *Controllers\IdentityController.cs* e, em seguida, adicione à classe controlador de `Identity` a seguinte função:

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

No código de exemplo anterior, aceitaremos o certificado como válido somente se todas as condições a seguir forem atendidas:
* O certificado não expirou e está ativo para a hora atual no servidor.
* O nome `Subject` do certificado é igual ao valor de configuração do aplicativo `ClientCertificate:Subject`.
* O nome `Issuer` do certificado é igual ao valor de configuração do aplicativo `ClientCertificate:Issuer`.
* O `thumbprint` do certificado é igual ao valor de configuração do aplicativo `ClientCertificate:Thumbprint`.

>[!IMPORTANT]
>Dependendo da sensibilidade do seu serviço, talvez seja necessário adicionar mais validações. Por exemplo, talvez seja necessário testar se o certificado se encadeia a uma autoridade raiz confiável, validação de nome da organização do emissor e assim por diante.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6,3 chamar a função IsValidClientCertificate
Abra o arquivo *Controllers\IdentityController.cs* e, no início da função `SignUp()`, adicione o seguinte trecho de código:

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Depois de adicionar o trecho, seu controlador de `Identity` deve se parecer com o código a seguir:

![Adicionar código de validação de certificado](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Etapa 7: publicar seu projeto no Azure e testá-lo
1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **contoso. AADB2C. API** e selecione **publicar**.

2. Repita a "etapa 6" e teste novamente sua política personalizada com a validação do certificado. Tente executar a política e verifique se tudo funciona depois de adicionar a validação.

3. No arquivo *Web. config* , altere o valor de `ClientCertificate:Subject` para **inválido**. Execute a política novamente e você verá uma mensagem de erro.

4. Altere o valor de volta para **válido**e verifique se a política pode chamar sua API REST.

Se você precisar solucionar esse problema, consulte [coleta de logs usando Application insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>Adicional Baixar o código e os arquivos de política completos
* Depois de concluir as instruções introdução [às políticas personalizadas](active-directory-b2c-get-started-custom.md) , recomendamos que você crie seu cenário usando seus próprios arquivos de política personalizados. Para sua referência, fornecemos [arquivos de política de exemplo](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Você pode baixar o código completo da [solução de exemplo do Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

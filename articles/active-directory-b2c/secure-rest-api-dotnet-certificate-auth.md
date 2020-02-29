---
title: Assegurar um serviço RESTful utilizando certificados de cliente
titleSuffix: Azure AD B2C
description: Proteja as suas bolsas personalizadas REST API reclama trocas no seu Azure AD B2C utilizando certificados de cliente
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 98606443707c183a15a4bacd26646c3eac6c8a69
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186918"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Proteja o seu serviço RESTful utilizando certificados de cliente

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Num artigo relacionado, [cria-se um serviço RESTful](rest-api-claims-exchange-dotnet.md) que interage com o Azure Ative Directory B2C (Azure AD B2C).

Neste artigo, aprende a restringir o acesso à sua aplicação web Azure (RESTful API) utilizando um certificado de cliente. Este mecanismo chama-se autenticação mútua TLS ou autenticação de certificado de *cliente.* Apenas os serviços que possuam certificados adequados, como o Azure AD B2C, podem aceder ao seu serviço.

>[!NOTE]
>Também pode assegurar o seu serviço RESTful utilizando a [autenticação básica HTTP](secure-rest-api-dotnet-basic-auth.md). No entanto, a autenticação básica http é considerada menos segura sobre um certificado de cliente. A nossa recomendação é garantir o serviço RESTful utilizando a autenticação do certificado de cliente, tal como descrito neste artigo.

Este artigo detalha como:
* Configurar a sua aplicação web para utilizar a autenticação do certificado de cliente.
* Faça o upload do certificado para as teclas políticas Do Azure AD B2C.
* Configure a sua política personalizada para utilizar o certificado de cliente.

## <a name="prerequisites"></a>Pré-requisitos
* Complete os passos no artigo de permuta de reclamações da [API integração REST.](rest-api-claims-exchange-dotnet.md)
* Obtenha um certificado válido (um ficheiro .pfx com uma chave privada).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Passo 1: Configurar uma aplicação web para autenticação de certificado de cliente
Para criar o **Azure App Service** para exigir certificados de cliente, defina a aplicação web `clientCertEnabled` definição do site *para verdadeira*. Para fazer esta alteração, no portal Azure, abra a sua página de aplicações web. Na navegação à esquerda, em **Definições** selecione **Definições SSL**. Na secção **Certificados de Cliente,** ligue a opção de certificado de **cliente.**

>[!NOTE]
>Certifique-se de que o seu plano de Serviço de Aplicações Azure é Standard ou superior. Para mais informações, consulte os planos do Serviço de [Aplicações Azure em profundidade.](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)

>[!NOTE]
>Para obter mais informações sobre a definição da propriedade **clienteCertEnabled,** consulte [configure TLS de autenticação mútua para aplicações web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Passo 2: Faça o upload do seu certificado para as teclas políticas Do Azure AD B2C
Depois de ter definido `clientCertEnabled` *verdadeiramente,* a comunicação com a sua API RESTful requer um certificado de cliente. Para obter, carregar e armazenar o certificado de cliente no seu inquilino Azure AD B2C, faça o seguinte:
1. No seu inquilino Azure AD B2C, selecione **As configurações B2C** > **Quadro de Experiência de Identidade**.

2. Para ver as chaves disponíveis no seu inquilino, selecione **Teclas políticas**.

3. Selecione **Adicionar**.
    Abre-se uma janela **chave.**

4. Na caixa **Opções,** **selecione Upload**.

5. Na caixa **nome,** escreva **B2cRestClientCertificate**.
    O prefixo *B2C_1A_* é adicionado automaticamente.

6. Na caixa de upload do **Ficheiro,** selecione o ficheiro .pfx do seu certificado com uma chave privada.

7. Na caixa **password,** digite a palavra-passe do certificado.

    ![Chave de política de upload na página chave Criar uma página chave no portal Azure](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-cert-upload.png)

7. Selecione **Criar**.

8. Para ver as chaves disponíveis no seu inquilino e confirmar que criou a chave `B2C_1A_B2cRestClientCertificate`, selecione **Policy Keys**.

## <a name="step-3-change-the-technical-profile"></a>Passo 3: Alterar o perfil técnico
Para apoiar a autenticação do certificado de cliente na sua política personalizada, altere o perfil técnico fazendo o seguinte:

1. No seu diretório de trabalho, abra o ficheiro de política de extensão *TrustFrameworkExtensions.xml.*

2. Procure o nó `<TechnicalProfile>` que inclua `Id="REST-API-SignUp"`.

3. Localize o elemento `<Metadata>`.

4. Alterar o *AutenticaçãoTipo* para O Certificado de *Cliente,* da seguinte forma:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Imediatamente após o elemento `<Metadata>` de fecho, adicione o seguinte corte XML:

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Depois de adicionar o corte, o seu perfil técnico deve parecer o seguinte código XML:

    ![Definir elementos xML de autenticação do ClientCertificate](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Passo 4: Faça upload da apólice para o seu inquilino

1. No [portal Azure,](https://portal.azure.com)selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

1. No portal Azure, procure e selecione **Azure AD B2C**.

1. Selecione Quadro de **Experiência de Identidade**.

1. Selecione **todas as políticas**.

1. Selecione **Política de Upload**.

1. Selecione a **sobreescrita da apólice se existir** uma caixa de verificação.

1. Faça upload do ficheiro *TrustFrameworkExtensions.xml* e, em seguida, certifique-se de que passa a validação.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Passo 5: Teste a política personalizada usando run now
1. Abra **as definições ad B2C do Azure,** e, em seguida, selecione Quadro de **Experiência de Identidade**.

    >[!NOTE]
    >Run Now requer pelo menos um pedido para ser pré-registrado no inquilino. Para saber registar as candidaturas, consulte o artigo [de registo](tutorial-create-tenant.md) de Candidaturas Do Azure AD B2C Get ou o artigo de inscrição de [aplicação.](tutorial-register-applications.md)

2. Abra **B2C_1A_signup_signin,** a política personalizada do partido que você carregou, e depois selecione **Run agora**.

3. Teste o processo digitando **teste** na caixa **Nome Dado.**
    Azure AD B2C exibe uma mensagem de erro na parte superior da janela.

    ![Caixa de texto de nome dado realçada e erro de validação de entrada mostrado](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-test.png)

4. Na caixa **Nome Dado,** digite um nome (com a não ser "Teste").
    O Azure AD B2C inscreve o utilizador e envia um número de fidelização para a sua aplicação. Note o número neste exemplo jWT:

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
   >Se receber a mensagem de erro, *o nome não é válido, por favor forneça um nome válido,* significa que o Azure AD B2C ligou com sucesso para o seu serviço RESTful enquanto apresentava o certificado de cliente. O próximo passo é validar o certificado.

## <a name="step-6-add-certificate-validation"></a>Passo 6: Adicionar validação de certificado
O certificado de cliente que o Azure AD B2C envia para o seu serviço RESTful não é validado pela plataforma Azure App Service, exceto para verificar se o certificado existe. A validação do certificado é da responsabilidade da aplicação web.

Nesta secção, adiciona-se amostra ASP.NET código que valida as propriedades do certificado para fins de autenticação.

> [!NOTE]
>Para obter mais informações sobre a configuração do Serviço de Aplicações Azure para autenticação de certificado de cliente, consulte a [autenticação mútua Configure TLS para aplicações web](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Adicione as definições de aplicação ao ficheiro web.config do seu projeto
No projeto Do Estúdio Visual que criou anteriormente, adicione as seguintes definições de aplicação ao ficheiro *web.config* após o elemento `appSettings`:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Substitua o **nome**do assunto do certificado, **nome emitente**e valores de **impressão digital** do certificado por valores do certificado.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 Adicione a função IsValidClientCertificate
Abra o ficheiro *Controllers\IdentityController.cs* e, em seguida, adicione à classe controlador`Identity` a seguinte função:

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

No código de amostra anterior, só aceitamos o certificado como válido se todas as seguintes condições forem satisfeitas:
* O certificado não está expirado e está ativo durante o tempo atual no servidor.
* O nome `Subject` do certificado é igual ao valor de definição de aplicação `ClientCertificate:Subject`.
* O nome `Issuer` do certificado é igual ao valor de definição de aplicação `ClientCertificate:Issuer`.
* O `thumbprint` do certificado é igual ao valor de definição de aplicação `ClientCertificate:Thumbprint`.

>[!IMPORTANT]
>Dependendo da sensibilidade do seu serviço, poderá ter de adicionar mais validações. Por exemplo, você pode precisar testar se as cadeias de certificados para uma autoridade de raiz confiável, validação de nome de organização emitente, e assim por diante.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 Ligue para a função IsValidClientCertificate
Abra o ficheiro *Controllers\IdentityController.cs* e, em seguida, no início da função `SignUp()`, adicione o seguinte código de corte:

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Depois de adicionar o corte, o seu controlador `Identity` deve parecer o seguinte código:

![Adicionar código de validação de certificado](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Passo 7: Publique o seu projeto no Azure e teste-o
1. No **Solution Explorer,** clique à direita no projeto **Contoso.AADB2C.API** e, em seguida, selecione **Publicar**.

2. Repita "Passo 6", e reteste a sua política personalizada com a validação do certificado. Tente executar a apólice e certifique-se de que tudo funciona depois de adicionar a validação.

3. No seu ficheiro *web.config,* altere o valor da `ClientCertificate:Subject` para **inválido**. Faça a apólice novamente e deve ver uma mensagem de erro.

4. Mude o valor de volta para **válido**, e certifique-se de que a apólice pode chamar a sua API REST.

Se precisar de resolver este passo, consulte [a Recolha de registos utilizando insights](troubleshoot-with-application-insights.md)de aplicação .

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcional) Descarregue os ficheiros e código sintetizadores
* Depois de completar o [Get iniciado com políticas personalizadas,](custom-policy-get-started.md) recomendamos que construa o seu cenário utilizando os seus próprios ficheiros de política personalizadas. Para sua referência, fornecemos ficheiros de [política de amostras](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Pode descarregar o código completo da [solução Sample Visual Studio para referência](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

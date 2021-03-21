---
title: 'Quickstart: Ligue para o Microsoft Graph de um | java daemon Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, você aprende como uma aplicação Java pode obter um token de acesso e chamar uma API protegida pelo ponto final da plataforma de identidade microsoft, usando a própria identidade da app
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/22/2021
ms.author: nacanuma
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 196b80a704b8a270a4cbb7d3505d5f9be1e23479
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99820329"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>Quickstart: Adquira um token e ligue para a Microsoft Graph API a partir de uma aplicação de consola Java usando a identidade da app

Neste quickstart, você descarrega e execute uma amostra de código que demonstra como uma aplicação Java pode obter um token de acesso usando a identidade da aplicação para ligar para a Microsoft Graph API e mostrar uma [lista de utilizadores](/graph/api/user-list) no diretório. A amostra de código demonstra como um trabalho sem supervisão ou serviço windows pode funcionar com uma identidade de aplicação, em vez da identidade de um utilizador. 

> [!div renderon="docs"]
> ![Mostra como funciona a app de amostras gerada por este quickstart](media/quickstart-v2-java-daemon/java-console-daemon.svg)

## <a name="prerequisites"></a>Pré-requisitos

Para fazer esta amostra, precisa:

- [Kit de Desenvolvimento de Java (JDK)](https://openjdk.java.net/) 8 ou superior
- [Maven](https://maven.apache.org/)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido

> [!div renderon="docs" class="sxs-lookup"]
>
> Tem duas opções para iniciar a sua aplicação de arranque rápido: Express (Opção 1 abaixo) e Manual (Opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá ao <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaDaemonQuickstartPage/sourceType/docs" target="_blank">portal Azure - Aplicação registra</a> experiência de arranque rápido.
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Introduza um **Nome** para a sua aplicação, por `Daemon-console` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
> 1. Selecione **Registar**.
> 1. Em **Manage**, selecione  **Certificados & segredos**.
> 1. Sob **os segredos do Cliente,** selecione **Novo segredo de cliente,** insira um nome e, em seguida, selecione **Add**. Grave o valor secreto num local seguro para ser utilizado num passo posterior.
> 1. Em **Gestão**, selecione **Permissões API**  >  **Adicione uma permissão**. Selecione **Microsoft Graph**.
> 1. Selecione **permissões de aplicação**.
> 1. No nó **do utilizador,** selecione **User.Read.All** e, em seguida, selecione **Adicionar permissões**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-quickstart-app"></a>Descarregue e configuure a app quickstart
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal Azure
> Para que a amostra de código para este arranque rápido funcione, é necessário criar um segredo de cliente e adicionar a permissão de aplicação do **Graph API.Read.All.**
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-netcore-daemon/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-java-project"></a>Passo 2: Descarregue o projeto Java

> [!div renderon="docs"]
> [Descarregue o projeto Java Daemon](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-the-java-project"></a>Passo 3: Configurar o projeto Java
>
> 1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, *C:\Azure-Samples*.
> 1. Navegue para a sub-pasta **msal-cliente-credencial-segredo**.
> 1. Editar *src\main\resources\application.properties* e substituir os valores dos `AUTHORITY` `CLIENT_ID` campos, e `SECRET` pelo seguinte corte:
>
>    ```
>    AUTHORITY=https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/
>    CLIENT_ID=Enter_the_Application_Id_Here
>    SECRET=Enter_the_Client_Secret_Here
>    ```
>    Em que:
>    - `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.
>    - `Enter_the_Tenant_Id_Here`- substituir este valor pelo **nome** **de Inquilino** Ou Inquilino (por exemplo, contoso.microsoft.com).
>    - `Enter_the_Client_Secret_Here` - substituir este valor pelo segredo do cliente criado no passo 1.
>
> > [!TIP]
> > Para encontrar os valores de ID de **aplicação (cliente),** **ID de diretório (inquilino),** aceda à página **geral** da aplicação no portal Azure. Para gerar uma nova chave, aceda à página **de segredos & Certificados.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Passo 3: Consentimento administrativo

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Passo 4: Consentimento administrativo

Se tentar executar a aplicação neste momento, receberá *HTTP 403 - Erro proibido:* `Insufficient privileges to complete the operation` . Este erro acontece porque qualquer *permissão apenas de aplicação* requer o consentimento do Administrador: um administrador global do seu diretório deve dar consentimento à sua aplicação. Selecione uma das opções abaixo, dependendo da sua função:

##### <a name="global-tenant-administrator"></a>Administrador de inquilino global

> [!div renderon="docs"]
> Se for administrador de inquilinos globais, vá à página **de Permissões da API** nos **registos da App** no portal Azure e selecione o consentimento de **administração de Grant para {Nome do Inquilino}** (Onde {Nome do Inquilino} é o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se for administrador global, aceda à página **de permissões da API,** selecione **Grant admin consent para Enter_the_Tenant_Name_Here**.
> > [!div id="apipermissionspage"]
> > [Aceda à página de Permissões da API]()

##### <a name="standard-user"></a>Utilizador padrão

Se é um utilizador padrão do seu inquilino, então precisa pedir a um administrador global que conceda consentimento administrativo para a sua aplicação. Para isso, dê o seguinte URL ao seu administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Em que:
>> * `Enter_the_Tenant_Id_Here`- substituir este valor pelo **nome** **de Inquilino Ou** Inquilino (por exemplo, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Passo 4: Executar a aplicação

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Passo 5: Executar a aplicação

Pode testar a amostra diretamente executando o método principal de ClientCredentialGrant.java a partir do seu IDE.

Da sua concha ou linha de comando:

```
$ mvn clean compile assembly:single
```

Isto gerará um ficheiro msal-cliente-credencial-secreto-1.0.0.jar no seu diretório /targets. Execute isto usando o seu Java executável como abaixo:

```
$ java -jar msal-client-credential-secret-1.0.0.jar
```

Após a execução, a aplicação deve apresentar a lista de utilizadores no inquilino configurado.


> [!IMPORTANT]
> Esta aplicação quickstart usa um segredo de cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto simples aos seus ficheiros do projeto, por razões de segurança, recomenda-se que utilize um certificado em vez de um segredo de cliente antes de considerar a aplicação como aplicação de produção. Para obter mais informações sobre como utilizar um certificado, consulte [estas instruções](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate) no mesmo repositório GitHub para esta amostra, mas na segunda pasta **msal-cliente-certificado credencial**.

## <a name="more-information"></a>Mais informações

### <a name="msal-java"></a>MSAL Java

[MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) é a biblioteca usada para assinar nos utilizadores e pedir fichas usadas para aceder a uma API protegida pela plataforma de identidade Microsoft. Como descrito, este quickstart solicita fichas usando a própria identidade da aplicação em vez de permissões delegadas. O fluxo de autenticação utilizado neste caso é conhecido como *[o fluxo de credenciais de clientes](v2-oauth2-client-creds-grant-flow.md)*. Para obter mais informações sobre como utilizar o MSAL Java com aplicações daemon, consulte [este artigo.](scenario-daemon-overview.md)

Adicione MSAL4J à sua aplicação utilizando Maven ou Gradle para gerir as suas dependências, fazendo as seguintes alterações ao ficheiro pom.xml (Maven) ou build.gradle (Gradle).

Em pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

Em build.gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Adicione uma referência ao MSAL para a Java adicionando o seguinte código à parte superior do ficheiro onde utilizará o MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

Em seguida, inicialize a MSAL com o código seguinte:

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

> | Em que: |Description |
> |---------|---------|
> | `CLIENT_SECRET` | É o segredo do cliente criado para a aplicação no Portal Azure. |
> | `CLIENT_ID` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |
> | `AUTHORITY`    | O ponto final STS para o utilizador autenticar. Normalmente `https://login.microsoftonline.com/{tenant}` para nuvem pública, onde {inquilino} é o nome do seu inquilino ou do seu id do seu inquilino.|

### <a name="requesting-tokens"></a>Pedir tokens

Para solicitar um token usando a identidade da app, use `acquireToken` o método:

```Java
IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
```

> |Em que:| Description |
> |---------|---------|
> | `SCOPE` | Contém os âmbitos solicitados. Para clientes confidenciais, este deve utilizar o formato semelhante `{Application ID URI}/.default` para indicar que os âmbitos que estão a ser solicitados são os que estão definidos estáticamente no objeto da aplicação definido no portal Azure (para o Microsoft Graph, aponta `{Application ID URI}` `https://graph.microsoft.com` para). Para APIs web personalizado, `{Application ID URI}` é definido sob a secção Expor uma **API** em **registos de Aplicações** no Portal Azure.|

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre aplicações daemon, consulte a página de aterragem do cenário.

> [!div class="nextstepaction"]
> [Aplicação Daemon que chama APIs web](scenario-daemon-overview.md)

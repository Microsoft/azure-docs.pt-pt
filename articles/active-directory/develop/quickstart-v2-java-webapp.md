---
title: 'Quickstart: Adicione o início de sôs-in com a Microsoft a uma aplicação web Java | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste arranque rápido, você vai aprender a adicionar o início de sôr-in com a Microsoft a uma aplicação web Java usando OpenID Connect.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: a7337175241834cef862b4af07c7bcf7c8b845d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103775"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Quickstart: Adicione o início de sôs com a Microsoft a uma aplicação web java

Neste quickstart, você descarrega e execute uma amostra de código que demonstra como uma aplicação web Java pode iniciar sôm nos utilizadores e ligar para a Microsoft Graph API. Os utilizadores de qualquer organização do Azure Ative Directory (Azure AD) podem iniciar sação na aplicação.

 Para uma visão geral, consulte o [diagrama de como a amostra funciona.](#how-the-sample-works)

## <a name="prerequisites"></a>Pré-requisitos

Para fazer esta amostra, precisa:

- [Kit de Desenvolvimento de Java (JDK)](https://openjdk.java.net/) 8 ou mais tarde. 
- [Maven.](https://maven.apache.org/)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Existem duas formas de iniciar a sua aplicação de arranque rápido: expresso (opção 1) e manual (opção 2).
>
> ### <a name="option-1-register-and-automatically-configure-your-app-and-then-download-the-code-sample"></a>Opção 1: Registar e configurar automaticamente a sua aplicação e, em seguida, descarregar a amostra de código
>
> 1. Vá ao <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs" target="_blank">portal Azure - Aplicação registra</a> experiência de arranque rápido.
> 1. Introduza um nome para a sua inscrição e, em seguida, **selecione Registar..**
> 1. Siga as instruções na experiência de arranque rápido do portal para descarregar o código de aplicação configurado automaticamente.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> Para registar a sua aplicação e adicionar manualmente as informações de registo da aplicação, siga estes passos:
>
> 1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar a inscrição.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gerir**, selecione **Registos de aplicações**.
> 1. Selecione **Novo registo**.
> 1. Introduza um **Nome** para a sua aplicação, por exemplo **java-webapp**. Os utilizadores da sua aplicação podem ver este nome. Pode mudá-lo mais tarde.
> 1. Selecione **Registar**.
> 1. Na página **geral,** note o **ID de Aplicação (cliente)** e o **ID do Diretório (inquilino).** Vai precisar destes valores mais tarde.
> 1. Em **Gestão**, **selecione Autenticação**.
> 1. **Selecione Adicionar uma plataforma**  >  **Web**.
> 1. Na secção **URIs de redirecionamento,** insira `https://localhost:8443/msal4jsample/secure/aad` .
> 1. Selecione **Configurar**.
> 1. Na secção **Web,** em **Redirecionar URIs,** insira `https://localhost:8443/msal4jsample/graph/me` como um segundo URI de redirecionamento.
> 1. Em **Gerir**, selecione **Certificados e segredos**. Na secção **de segredos** do Cliente, selecione **Novo segredo de cliente.**
> 1. Introduza uma descrição chave (por exemplo, o segredo da *aplicação),* deixe a expiração predefinitiva e selecione **Adicionar**.
> 1. Note o **valor** do segredo do cliente. Precisará dela mais tarde.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua candidatura no portal Azure
>
> Para utilizar a amostra de código neste arranque rápido:
>
> 1. Adicione URLs de resposta `https://localhost:8443/msal4jsample/secure/aad` e `https://localhost:8443/msal4jsample/graph/me` .
> 1. Criar um segredo de cliente.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-code-sample"></a>Passo 2: Descarregue a amostra de código
> [!div renderon="docs"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Descarregue o projeto e extrate o ficheiro .zip para uma pasta perto da raiz da sua unidade. Por exemplo, *C:\Azure-Samples*.
>
> Para utilizar HTTPS com localidade, forneça as `server.ssl.key` propriedades. Para gerar um certificado auto-assinado, utilize o utilitário keytool (incluído no JRE).
>
> Eis um exemplo:
>  ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Coloque o ficheiro de teclas gerado na pasta *de recursos.*

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Passo 3: Configurar a amostra de código
> 1. Extraia o ficheiro zip para uma pasta local.
> 1. *É opcional.* Se utilizar um ambiente de desenvolvimento integrado, abra a amostra nesse ambiente.
> 1. Abra o ficheiro *application.properties.* Pode encontrá-lo na *src/main/resources/pasta.* Substitua os valores nos `aad.clientId` campos, `aad.authority` e pelos `aad.secretKey` valores de identificação do pedido, identificação de inquilinos e valores secretos do cliente, respectivamente. Eis o que deve parecer:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
>    No código anterior:
>
>    - `Enter_the_Application_Id_here` é o ID de aplicação para o pedido que registou.
>    - `Enter_the_Client_Secret_Here` é o Segredo de **Cliente** que criou em **Certificados & segredos** para a aplicação que registou.
>    - `Enter_the_Tenant_Info_Here` é o valor de identificação do **Diretório (inquilino)** do pedido que registou.
> 1. Para utilizar HTTPS com localidade, forneça as `server.ssl.key` propriedades. Para gerar um certificado auto-assinado, utilize o utilitário keytool (incluído no JRE).
>
>    Eis um exemplo:
>
>     ```
>      keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>      server.ssl.key-store-type=PKCS12
>      server.ssl.key-store=classpath:keystore.p12
>      server.ssl.key-store-password=password
>      server.ssl.key-alias=testCert
>      ```
>   1. Coloque o ficheiro de teclas gerado na pasta *de recursos.*


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Passo 3: Executar a amostra de código
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Passo 4: Executar a amostra de código

Para executar o projeto, tome um destes passos:

- Execute-o diretamente a partir do seu IDE utilizando o servidor Boot de mola incorporado. 
- Embale-o num ficheiro WAR utilizando [o Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html), e depois desloque-o para uma solução de contentor J2EE como [o Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-the-project-from-an-ide"></a>Executar o projeto a partir de um IDE

Para executar a aplicação web a partir de um IDE, selecione run e, em seguida, vá para a página inicial do projeto. Para esta amostra, o URL de página inicial padrão é https://localhost:8443 .

1. Na primeira página, selecione o botão **De início de Sessão** para redirecionar os utilizadores para o Azure Ative Directory e insorte-os para obter credenciais.

1. Depois de os utilizadores serem autenticados, são redirecionados para `https://localhost:8443/msal4jsample/secure/aad` . Estão agora inscritos e a página mostrará informações sobre a conta do utilizador. A UI da amostra tem estes botões:
    - **Assine**: Assina o utilizador atual fora da aplicação e redireciona esse utilizador para a página inicial.
    - **Mostrar informações do utilizador**: Adquire um símbolo para o Microsoft Graph e liga para o Microsoft Graph com um pedido que contém o token, que devolve informações básicas sobre o utilizador inscrito.

##### <a name="running-the-project-from-tomcat"></a>Executando o projeto de Tomcat

Se quiser colocar a amostra web no Tomcat, faça algumas alterações ao código fonte.

1. Abra *ms-identity-java-webapp/pom.xml*.
    - Em `<name>msal-web-sample</name>` baixo , adicionar `<packaging>war</packaging>` .

2. Abra *ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication*.

    - Elimine todo o código fonte e substitua-o por este código:

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

3.   A porta HTTP padrão da Tomcat é 8080, mas precisa de uma ligação HTTPS sobre a porta 8443. Para configurar este cenário:
        - Vá a *tomcat/conf/server.xml*.
        - Procure a `<connector>` etiqueta e substitua o conector existente com este conector:

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

4. Abra uma janela de Linha de Comandos. Vá à pasta raiz desta amostra (onde está localizado o ficheiro pom.xml) e corra `mvn package` para construir o projeto.
    - Este comando gerará um ficheiro *msal-web-sample-0.1.0.war* no seu diretório */targets.*
    - Mude o nome deste ficheiro para *msal4jsample.war*.
    - Desloque o ficheiro WAR utilizando o Tomcat ou qualquer outra solução de recipiente J2EE.
        - Para implementar o ficheiro msal4jsample.war, copie-o para o */webapps/diretório* na sua instalação Tomcat e, em seguida, inicie o servidor Tomcat.

5. Depois de o ficheiro ser implementado, aceda através https://localhost:8443/msal4jsample de um browser.


> [!IMPORTANT]
> Esta aplicação quickstart usa um segredo de cliente para se identificar como um cliente confidencial. Como o segredo do cliente é adicionado como texto simples aos seus ficheiros do projeto, recomendamos que use um certificado em vez de um segredo de cliente antes de usar a aplicação em um ambiente de produção. Para obter mais informações sobre como utilizar um certificado, consulte [as credenciais do Certificado para autenticação de pedidos.](./active-directory-certificate-credentials.md)

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Diagrama que mostra como funciona a aplicação da amostra gerada por este quickstart.](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>Obter MSAL

MSAL for Java (MSAL4J) é a biblioteca Java usada para assinar nos utilizadores e solicitar fichas que são usadas para aceder a uma API que está protegida pela plataforma de identidade da Microsoft.

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

### <a name="initialize-msal"></a>Inicializar o MSAL

Adicione uma referência ao MSAL para a Java adicionando o seguinte código no início do ficheiro onde utilizará o MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para uma discussão mais aprofundada sobre a construção de aplicações web que assinam nos utilizadores na plataforma de identidade da Microsoft, consulte a série de cenários multipart:

> [!div class="nextstepaction"]
> [Cenário: aplicação web que assina nos utilizadores](scenario-web-app-sign-user-overview.md?tabs=java)

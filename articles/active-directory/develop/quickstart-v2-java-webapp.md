---
title: 'Quickstart: Adicione o início de sing-in com a Microsoft a uma aplicação web java / Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste arranque rápido, aprenda a implementar o início de sôs-in da Microsoft numa aplicação web Java utilizando o OpenID Connect.
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
ms.openlocfilehash: 620039ec43009693d09f732913264eff94d662c9
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533247"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Quickstart: Adicione o início de sôs com a Microsoft a uma aplicação web java

Neste arranque rápido, aprenderás a integrar uma aplicação web java com a plataforma de identidade da Microsoft. A sua aplicação irá iniciar seduca num utilizador, obter um token de acesso para ligar para a API do Gráfico da Microsoft e fazer um pedido para a API do Microsoft Graph.

Quando tiver concluído este quickstart, a sua aplicação aceitará inscrições de contas pessoais da Microsoft (incluindo outlook.com, live.com e outras) e contas de trabalho ou escola de qualquer empresa ou organização que utilize o Azure Ative Directory. (Ver [como funciona a amostra](#how-the-sample-works) para uma ilustração.)

## <a name="prerequisites"></a>Pré-requisitos

Para executar esta amostra, você precisará:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 ou maior, e [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a sua aplicação de arranque rápido: expresso (Opção 1) ou manual (Opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá ao [portal Azure - Aplicação registra](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs) experiência de arranque rápido.
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções na experiência de arranque rápido do portal para descarregar o código de aplicação configurado automaticamente.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> Para registar a sua aplicação e adicionar manualmente as informações de registo da aplicação à sua aplicação, siga estes passos:
>
> 1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
>
> 1. Navegue para a plataforma de identidade da Microsoft para programadores [Página de registos de aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Selecione **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>    - Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por `java-webapp` exemplo.
>    - Selecione **Registar**.
> 1. Na página **geral,** encontre o **ID da Aplicação (cliente)** e os valores de ID do **Diretório (inquilino)** da aplicação. Copie estes valores para mais tarde.
> 1. Selecione a **Autenticação** do menu e, em seguida, adicione as seguintes informações:
>    - Adicione a configuração da plataforma **Web.**  Adicione estes `https://localhost:8443/msal4jsample/secure/aad` e `https://localhost:8443/msal4jsample/graph/me` como **URIs redirecionado**..
>    - Selecione **Guardar**.
> 1. Selecione os **Certificados & segredos** do menu e na secção segredos do **Cliente,** clique em **Novo segredo de cliente:**
>
>    - Digite uma descrição chave (por exemplo, segredo de aplicações).
>    - Selecione uma duração de chave **Em 1 ano**.
>    - O valor da chave será exibido quando selecionar **Adicionar**.
>    - Copie o valor da chave para mais tarde. Este valor chave não será exibido novamente, nem recuperável por qualquer outro meio, por isso grave-o assim que for visível a partir do portal Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua candidatura no portal Azure
>
> Para que a amostra de código para este arranque rápido funcione, você precisa:
>
> 1. Adicione URLs de resposta como `https://localhost:8443/msal4jsample/secure/aad` e `https://localhost:8443/msal4jsample/graph/me`
> 1. Criar um Segredo de Cliente.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-code-sample"></a>Passo 2: Descarregue a amostra de código
> [!div renderon="docs"]
> [Descarregue a Amostra de Código](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Descarregue o projeto e extrate o ficheiro zip para uma pasta local mais próxima da pasta raiz - por exemplo, **C:\Azure-Samples**
>
> Para utilizar https com a loja local, preencha as propriedades .key servidores do servidor.ssl. Para gerar um certificado auto-assinado, utilize o utilitário keytool (incluído no JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Coloque o ficheiro de teclas gerado na pasta "recursos".

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Passo 3: Configurar a amostra de código
> 1. Extraia o ficheiro zip para uma pasta local.
> 1. Se utilizar um ambiente de desenvolvimento integrado, abra a amostra no seu IDE favorito (opcional).
> 1. Abra o ficheiro application.properties, que pode ser encontrado em src/main/resources/pasta e substituir o valor dos campos *aad.clientId,* *aad.authority* e *aad.secretKey* pelos respetivos valores de ID de **aplicação,** **Id de Inquilino** e Segredo de **Cliente** como seguintes:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Em que:
>
> - `Enter_the_Application_Id_here` - é o Id da Aplicação que registou.
> - `Enter_the_Client_Secret_Here` - é o Segredo de **Cliente** que criou em **Certificados & Segredos** para a aplicação que registou.
> - `Enter_the_Tenant_Info_Here` - é o valor de identificação do **Diretório (inquilino)** do pedido que registou.
> 1. Para utilizar https com a loja local, preencha as propriedades .key servidores do servidor.ssl. Para gerar um certificado auto-assinado, utilize o utilitário keytool (incluído no JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Coloque o ficheiro de teclas gerado na pasta "recursos".


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Passo 3: Executar a amostra de código
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Passo 4: Executar a amostra de código

Para executar o projeto, você pode:

Execute-o diretamente a partir do seu IDE utilizando o servidor de arranque de mola incorporado ou embalando-o num ficheiro WAR utilizando [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) e coloque-o numa solução de contentor J2EE, como [o Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Correndo do IDE

Se estiver a executar a aplicação web a partir de um IDE, clique em execução e, em seguida, navegue para a página inicial do projeto. Para esta amostra, o URL de página inicial padrão é https://localhost:8443

1. Na primeira página, selecione o botão **De início de Sessão** para redirecionar para o Azure Ative Directory e instrua o utilizador para as suas credenciais.

1. Depois de autenticado o utilizador, são redirecionados para *https://localhost:8443/msal4jsample/secure/aad* . Eles estão agora assinados, e a página mostrará informações sobre a conta de assinatura. A UI da amostra tem os seguintes botões:
    - *Assine* : Assina o utilizador atual fora da aplicação e redireciona-os para a página inicial.
    - *Mostrar informações do utilizador* : Adquire um símbolo para o Microsoft Graph e liga para o Microsoft Graph com um pedido que contém o token, que devolve informações básicas sobre o utilizador inscrito.

##### <a name="running-from-tomcat"></a>Correndo de Tomcat

Se quiser colocar a amostra web no Tomcat, terá de efetuar algumas alterações ao código fonte.

1. Abra ms-identity-java-webapp/pom.xml
    - Sob `<name>msal-web-sample</name>` adição `<packaging>war</packaging>`

2. Open ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication

    - Elimine todo o código fonte e substitua-o pelo seguinte:

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

3.   A porta HTTP padrão da Tomcat é 8080, embora seja necessária uma ligação HTTPS sobre a porta 8443. Para configurar isto:
        - Ir a tomcat/conf/server.xml
        - Procure a `<connector>` etiqueta e substitua o conector existente com:

        ```xml
        <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
        ```

4. Abra um pedido de comando, vá à pasta raiz desta amostra (onde está localizado o ficheiro pom.xml) e corra `mvn package` para construir o projeto
    - Isto irá gerar um `msal-web-sample-0.1.0.war` ficheiro no seu diretório de alvos.
    - Mude o nome deste ficheiro para `msal4jsample.war`
    - Implemente este ficheiro de guerra utilizando o Tomcat ou qualquer outra solução de recipiente J2EE.
        - Para implementar, copie o ficheiro msal4jsample.war para o `/webapps/` diretório na sua instalação Tomcat e, em seguida, inicie o servidor Tomcat.

5. Uma vez implementado, vá para https://localhost:8443/msal4jsample o seu navegador


> [!IMPORTANT]
> Esta aplicação quickstart usa um segredo de cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto simples aos seus ficheiros do projeto, por razões de segurança é recomendado que use um certificado em vez de um segredo de cliente antes de considerar a aplicação como aplicação de produção. Para obter mais informações sobre como utilizar um certificado, consulte [as credenciais do Certificado para autenticação de pedidos.](./active-directory-certificate-credentials.md)

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Mostra como funciona a app de amostras gerada por este quickstart](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Obtenção de MSAL

MSAL for Java (MSAL4J) é a biblioteca Java usada para assinar nos utilizadores e pedir fichas usadas para aceder a uma API protegida pela Plataforma de Identidade da Microsoft.

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

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para uma discussão mais aprofundada sobre a construção de aplicações web que assinam nos utilizadores na plataforma de identidade da Microsoft, passe para a nossa série de cenários multi-partes:

> [!div class="nextstepaction"]
> [Cenário: aplicação web que assina nos utilizadores](scenario-web-app-sign-user-overview.md?tabs=java)

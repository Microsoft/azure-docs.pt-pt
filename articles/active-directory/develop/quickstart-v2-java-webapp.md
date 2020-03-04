---
title: Microsoft plataforma de identidade Java web app quickstart  Azure
description: Saiba como implementar o Microsoft Sign-In numa Aplicação Web java usando o OpenID Connect
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: 3bfcc1ef8c58f71811af604fbc07736a13102e83
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249089"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Quickstart: Adicione o sessão com a Microsoft a uma aplicação web java

Neste arranque rápido, aprenderá a integrar uma aplicação web Java com a plataforma de identidade microsoft. A sua aplicação irá iniciar sessão de sessão de assinatura de um utilizador, obter á ficha de acesso para ligar para a Microsoft Graph API e fazer um pedido ao Microsoft Graph API.

Quando tiver concluído este quickstart, a sua aplicação aceitará inscrições de contas pessoais da Microsoft (incluindo outlook.com, live.com e outras) e contas de trabalho ou escola de qualquer empresa ou organização que utilize o Diretório Ativo Azure. (Ver [como funciona a amostra](#how-the-sample-works) para uma ilustração.)

## <a name="prerequisites"></a>Pré-requisitos

Para executar esta amostra, você precisará:

- Kit de [Desenvolvimento Java (JDK)](https://openjdk.java.net/) 8 ou superior, e [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a sua aplicação de arranque rápido: expresso (Opção 1) ou manual (Opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá ao [portal Azure - Registos de aplicações.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para descarregar e configure automaticamente a sua nova aplicação.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> Para registar a sua aplicação e adicionar manualmente as informações de registo da aplicação à sua solução, siga estes passos:
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
>
> 1. Navegue na plataforma de identidade da Microsoft para programadores da página de registos de [aplicações.](/azure/active-directory/develop/)
> 1. Selecione **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>    - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `java-webapp`.
>    - Deixe **redirecionar uri** em branco por enquanto e selecione **Register**.
> 1. Na página **'Visão Geral',** consulte os valores de ID do **Pedido (cliente)** e do **Diretório (inquilino)** da aplicação. Copie estes valores para mais tarde.
> 1. Selecione a **Autenticação** no menu e, em seguida, adicione as seguintes informações:
>    - Em **Redirecionamento de URIs,** adicione `https://localhost:8080/msal4jsample/secure/aad` e `https://localhost:8080/msal4jsample/graph/me`.
>    - Selecione **Guardar**.
> 1. Selecione os **Certificados e segredos** do menu e na secção de segredos do **Cliente,** clique em **Novo segredo do cliente:**
>
>    - Digite uma descrição da chave (por exemplo, segredo de aplicação).
>    - Selecione uma duração chave **Em 1 ano**.
>    - O valor-chave será visualizado quando selecionar **Adicionar**.
>    - Copie o valor da chave para mais tarde. Este valor-chave não voltará a ser apresentado, nem será recuperável por qualquer outro meio, pelo que o grave assim que for visível a partir do portal Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configure a sua aplicação no portal Azure
>
> Para a amostra de código para este início rápido funcionar, é necessário:
>
> 1. Adicione URLs de resposta como `https://localhost:8080/msal4jsample/secure/aad` e `https://localhost:8080/msal4jsample/graph/me`.
> 1. Criar um Segredo de Cliente.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas mudanças para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-code-sample"></a>Passo 2: Descarregue a amostra de código
> [!div renderon="docs"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Descarregue o projeto e extraio o ficheiro zip para uma pasta local mais próxima da pasta raiz - por exemplo, **C:\Azure-Samples**
> 
> Para utilizar https com o local anfitrião, preencha as propriedades do servidor.ssl.key. Para gerar um certificado auto-assinado, utilize o utilitário keytool (incluído no JRE).
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
>   Coloque o ficheiro da loja de chaves gerado na pasta "recursos".
   
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código]()

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Passo 3: Configurar a amostra de código
> 1. Extrai o ficheiro zip para uma pasta local.
> 1. Se utilizar um ambiente de desenvolvimento integrado, abra a amostra no seu IDE favorito (opcional).
> 1. Abra o ficheiro application.properties, que pode ser encontrado em sRC/main/resources/folder e substituir o valor dos campos *aad.clientId,* *aad.authority* e *aad.secretKey* com os respetivos valores de Id de **Aplicação,** **Id do Inquilino** e Segredo de **Cliente** como os seguintes:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8080/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8080/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Em que:
>
> - `Enter_the_Application_Id_here` - é o Id da Aplicação que registou.
> - `Enter_the_Client_Secret_Here` - é o Segredo do **Cliente** que criou em **Certificados e Segredos** para a aplicação que registou.
> - `Enter_the_Tenant_Info_Here` - é o valor de id do **Diretório (inquilino)** do pedido que registou.
> 1. Para utilizar https com o local anfitrião, preencha as propriedades do servidor.ssl.key. Para gerar um certificado auto-assinado, utilize o utilitário keytool (incluído no JRE).
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
>   Coloque o ficheiro da loja de chaves gerado na pasta "recursos".


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Passo 3: Executar a amostra de código
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Passo 4: Executar a amostra de código

Para executar o projeto, pode:

Executa-o diretamente a partir do iDE utilizando o servidor de boot de mola incorporado ou embala-o para um ficheiro WAR usando [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) e implemente-o para uma solução de contentorJ2EE, como [apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Correndo do IDE

Se estiver a executar a aplicação web a partir de um IDE, clique em executar e navegue para a página inicial do projeto. Para esta amostra, o URL da página inicial padrão é https://localhost:8080.

1. Na primeira página, selecione o botão **Login** para redirecionar para o Diretório Ativo Do Azure e instituem o utilizador para as suas credenciais.

1. Após a autenticação do utilizador, são redirecionados para *https://localhost:8080/msal4jsample/secure/aad* . Eles estão agora inscritos, e a página mostrará informações sobre a conta assinada. A amostra UI tem os seguintes botões:
    - *Sign out*: Assina o utilizador atual para fora da aplicação e redireciona-os para a página inicial.
    - *Mostrar informações do utilizador*: Adquire um símbolo para o Microsoft Graph e liga para o Microsoft Graph com um pedido que contém o símbolo, que devolve informações básicas sobre o utilizador inscrito.


   
> [!IMPORTANT]
> Esta aplicação quickstart usa um segredo de cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto simples aos seus ficheiros de projeto, por razões de segurança é recomendado que use um certificado em vez de um segredo de cliente antes de considerar a aplicação como aplicação de produção. Para obter mais informações sobre como utilizar um certificado, consulte as credenciais de [certificado para autenticação de aplicação.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials)

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Mostra como funciona a aplicação de amostragerada por este quickstart](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Obtenção de MSAL

MSAL for Java (MSAL4J) é a biblioteca Java usada para assinar utilizadores e solicitar fichas usadas para aceder a uma API protegida pela Plataforma de Identidade da Microsoft.

Adicione mSAL4J à sua aplicação utilizando Maven ou Gradle para gerir as suas dependências, fazendo as seguintes alterações ao ficheiro pom.xml (Maven) ou build.gradle (Gradle).

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Adicione uma referência ao MSAL para Java adicionando o seguinte código à parte superior do ficheiro onde utilizará o MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre permissões e consentimento:

> [!div class="nextstepaction"]
> [Permissões e Consentimento](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Para saber mais sobre o fluxo de auth para este cenário, consulte o fluxo de código de autorização Oauth 2.0:

> [!div class="nextstepaction"]
> [Fluxo de Código de Autorização Oauth](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

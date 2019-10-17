---
title: Configurar um aplicativo para acessar APIs Web-plataforma de identidade da Microsoft
description: Saiba como configurar uma aplicação registada na plataforma de identidade da Microsoft para incluir URI(s) de redirecionamento, credenciais ou permissões para aceder a APIs Web.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/07/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5100af99046a03345230ed0468071766aae1c77b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389613"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Início rápido: configurar um aplicativo cliente para acessar APIs Web

Para que uma aplicação cliente Web/confidencial possa participar num fluxo de concessão de autorização que precise de autenticação (e obter um token de acesso), essa aplicação tem de estabelecer credenciais seguras. O método de autenticação predefinido que o portal do Azure suporta é ID de cliente + chave secreta.

Além disso, antes de um cliente poder aceder a uma API Web exposta por uma aplicação de recurso (como a Microsoft Graph API), a arquitetura de consentimento garante que o cliente obtém a concessão de permissão necessária, com base nas permissões pedidas. Por predefinição, todas as aplicações podem escolher permissões da Microsoft Graph API. A [permissão “Iniciar e ler perfil de utilizador” da Graph API](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) está selecionada por predefinição. Pode escolher de entre [dois tipos de permissões](developer-glossary.md#permissions) para cada API Web pretendida:

* **Permissões da aplicação** - a aplicação cliente tem de aceder à API Web diretamente como a própria (sem contexto de utilizador). Este tipo de permissão requer consentimento do administrador e também não está disponível para aplicações cliente públicas (de ambiente de trabalho e móveis).
* **Permissões delegadas** - a aplicação cliente tem de aceder à API Web como o utilizador com sessão iniciada, mas com acesso limitado pela permissão selecionada. Este tipo de permissão pode ser concedido por um utilizador, a não ser que exija consentimento do administrador.

  > [!NOTE]
  > Adicionar uma permissão delegada a uma aplicação não concede automaticamente consentimento aos utilizadores no inquilino. Os utilizadores continuam a ter de consentir manualmente as permissões delegadas adicionadas no runtime, a não ser que o administrador conceda acesso em nome de todos eles.

Neste início rápido, vamos mostrar-lhe como configurar a sua app para:

* [Adicionar URIs de redirecionamento à aplicação](#add-redirect-uris-to-your-application)
* [Definir configurações avançadas para seu aplicativo](#configure-advanced-settings-for-your-application)
* [Modificar tipos de conta com suporte](#modify-supported-account-types)
* [Adicionar credenciais ao seu aplicativo Web](#add-credentials-to-your-web-application)
* [Adicionar permissões para aceder a APIs Web](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, certifique-se de que cumpre estes pré-requisitos:

* Saiba mais sobre as [permissões e consentimentos](v2-permissions-and-consent.md) suportados, que é importante compreender se estiver a criar aplicações que têm de ser utilizadas por outros utilizadores ou aplicações.
* Ter um inquilino com aplicações registadas.
  * Se não tiver aplicações registadas, [saiba como registar aplicações na plataforma de identidade da Microsoft](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Iniciar sessão no portal do Azure e selecionar a aplicação

Antes de poder configurar a aplicação, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina a sessão do portal para o locatário do Azure AD desejado.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e, em seguida, selecione **registros de aplicativo**.
1. Encontre e selecione a aplicação que quer configurar. Depois de selecionar a aplicação, verá a página **Descrição geral** da aplicação ou a página de registo principal.
1. Siga os passos para configurar a aplicação para aceder às APIs Web:
    * [Adicionar URIs de redirecionamento à aplicação](#add-redirect-uris-to-your-application)
    * [Definir configurações avançadas para seu aplicativo](#configure-advanced-settings-for-your-application)
    * [Modificar tipos de conta com suporte](#modify-supported-account-types)
    * [Adicionar credenciais ao seu aplicativo Web](#add-credentials-to-your-web-application)
    * [Adicionar permissões para aceder a APIs Web](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Adicionar URI(s) de redirecionamento à aplicação

Para adicionar um URI de redirecionamento à aplicação:

1. Na página **Descrição Geral** da aplicação, selecione a secção **Autenticação**.
1. Para adicionar um URI de redirecionamento personalizado para aplicações cliente da Web e públicas, siga estes passos:
   1. Localize a secção **URI de redirecionamento**.
   1. Selecione o tipo de aplicação que está a compilar, **Web** ou **Cliente público (móvel e ambiente de trabalho)** .
   1. Introduza o URI de redirecionamento da aplicação,
      * Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, `http://localhost:31544` pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação cliente Web.
      * Para aplicações públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Insira um valor específico para seu aplicativo, por exemplo: `https://MyFirstApp`.

1. Para escolher de entre os URIs de Redirecionamento sugeridos para clientes públicos (móveis, ambiente de trabalho), siga estes passos:
    1. Localize a secção **URIs de Redirecionamento sugeridos para clientes públicos (móveis, ambiente de trabalho)** .
    1. Utilize as caixas de verificação para selecionar o URI ou URIs de Redirecionamento adequados para a sua aplicação. Você também pode inserir um URI de redirecionamento personalizado. Se você não tiver certeza do que usar, confira a documentação da biblioteca.

Há certas restrições que se aplicam a URIs de redirecionamento. Saiba mais sobre [restrições e limitações de URI de redirecionamento](https://docs.microsoft.com/azure/active-directory/develop/reply-url).
> [!NOTE]
> Experimente a nova experiência de configurações de **autenticação** , na qual você pode definir configurações para seu aplicativo com base na plataforma ou no dispositivo que você deseja direcionar.
>
> Para ver essa exibição, selecione **experimentar a nova experiência** na exibição de página de **autenticação** padrão.
>
> ![Clique em "Experimente a nova experiência" para ver o modo de configuração de plataforma](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Isso levará você para a [nova página de **configurações de plataforma** ](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Definir configurações avançadas para seu aplicativo

Dependendo do aplicativo que você está registrando, há algumas configurações adicionais que talvez você precise configurar, como:

* **URL de logout**
* Para aplicativos de página única, você pode habilitar a **concessão implícita** e selecionar os tokens que você deseja que o ponto de extremidade de autorização emita.
* Para aplicativos de área de trabalho que estão adquirindo tokens com autenticação integrada do Windows, fluxo de código de dispositivo ou nome de usuário/senha na seção **tipo de cliente padrão** , defina a configuração **tratar aplicativo como cliente público como** **Sim**.
* Para aplicativos herdados que estavam usando o Live SDK para integração com o serviço de conta Microsoft, configure o **suporte ao Live SDK**. Novos aplicativos não precisam dessa configuração.
* **Tipo de cliente padrão**

### <a name="modify-supported-account-types"></a>Modificar tipos de conta com suporte

Os **tipos de conta com suporte** especificam quem pode usar o aplicativo ou acessar a API.

Depois [de configurar os tipos de conta com suporte](quickstart-register-app.md) ao registrar inicialmente o aplicativo, você só poderá alterar essa configuração usando o editor de manifesto do aplicativo se:

* Você altera os tipos de conta de **AzureADMyOrg** ou **AzureADMultipleOrgs** para **AzureADandPersonalMicrosoftAccount**, ou vice-versa.
* Você altera os tipos de conta de **AzureADMyOrg** para **AzureADMultipleOrgs**, ou vice-versa.

Para alterar os tipos de conta com suporte para um registro de aplicativo existente:

* Consulte [Configurar o manifesto do aplicativo](reference-app-manifest.md) e atualizar a chave `signInAudience`.

## <a name="configure-platform-settings-for-your-application"></a>Definir configurações de plataforma para seu aplicativo

[configurações de @no__t 1Configure para seu aplicativo com base na plataforma ou no dispositivo](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-small.png#lightbox)

Para definir as configurações do aplicativo com base na plataforma ou no dispositivo, você está direcionando:

1. Na página **configurações de plataforma** , selecione **Adicionar uma plataforma** e escolha entre as opções disponíveis.

   ![Mostra a página Configurar plataformas](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Insira as informações de configurações com base na plataforma selecionada.

   | Plataforma                | Durante              | Definições de configuração            |
   |-------------------------|----------------------|-----------------------------------|
   | **Aplicações Web**    | **Web**              | Insira o **URI de redirecionamento** para seu aplicativo. |
   | **Aplicativos móveis** | **iOS**              | Insira a ID do **pacote**do aplicativo, que pode ser encontrada no Xcode no info. plist ou configurações de compilação. Adicionar a ID do pacote cria automaticamente um URI de redirecionamento para o aplicativo. |
   |                         | **Android**          | * Forneça o nome do **pacote**do aplicativo, que pode ser encontrado no arquivo AndroidManifest. xml.<br/>* Gerar e inserir o **hash de assinatura**. Adicionar o hash de assinatura cria automaticamente um URI de redirecionamento para o aplicativo.  |
   | **Dispositivos desktop +**   | **Dispositivos desktop +** | Adicional. Selecione um dos URIs de **redirecionamento sugeridos** se você estiver criando aplicativos para desktops e dispositivos.<br/>Adicional. Insira um **URI de redirecionamento personalizado**, que é usado como o local onde o Azure ad redirecionará os usuários em resposta às solicitações de autenticação. Por exemplo, para aplicativos .NET Core em que você deseja interação, use `https://localhost`. |

   > [!IMPORTANT]
   > Para aplicativos móveis que não estão usando a biblioteca MSAL mais recente ou não usando um agente, você deve configurar os URIs de redirecionamento para esses aplicativos em **dispositivos desktop +** .

1. Dependendo da plataforma escolhida, pode haver configurações adicionais que podem ser configuradas. Para aplicativos **Web** , você pode:
    * Adicionar mais URIs de redirecionamento
    * Configure a **concessão implícita** para selecionar os tokens que você deseja que sejam emitidos pelo ponto de extremidade de autorização:
        * Para aplicativos de página única, selecione **tokens de acesso** e **tokens de ID**
        * Para aplicativos Web, selecione **tokens de ID**

## <a name="add-credentials-to-your-web-application"></a>Adicionar credenciais à aplicação Web

Para adicionar uma credencial à aplicação Web:

1. Na página **Descrição Geral** da aplicação, selecione a secção **Certificados e segredos**.

1. Para adicionar um certificado, siga estes passos:

    1. Selecione **Carregar o certificado**.
    1. Selecione o ficheiro que pretende carregar. Tem de ser do tipo .cer, .pem ou .crt.
    1. Selecione **Adicionar**.

1. Para adicionar um segredo do cliente, siga estes passos:

    1. Selecione **Novo segredo do cliente**.
    1. Adicione uma descrição do segredo do cliente.
    1. Selecione uma duração.
    1. Selecione **Adicionar**.

> [!NOTE]
> Depois de guardar as alterações à configuração, a coluna mais a direita incluirá o valor do segredo do cliente. **Certifique-se de que copia o valor** para utilizar no código da sua aplicação cliente, pois aquele deixa de estar acessível quando sair desta página.

## <a name="add-permissions-to-access-web-apis"></a>Adicionar permissões para aceder a APIs Web

Para adicionar uma ou mais permissões para aceder às APIs de recursos a partir do seu cliente:

1. Na página **Descrição Geral** da aplicação, selecione **Permissões de API**.
1. Selecione o botão **Adicionar uma permissão**.
1. Por predefinição, a vista permite-lhe selecionar de entre **APIs da Microsoft**. Selecione a secção de APIs em que está interessado:
    * **APIs da Microsoft** - permite-lhe selecionar permissões para APIs da Microsoft, como a Microsoft Graph.
    * **APIs que a minha organização utiliza** - permite-lhe selecionar permissões para as APIs que a sua organização expôs ou APIs com que a organização se integrou.
    * **As minhas APIs** - permite-lhe selecionar permissões para as APIs expostas por si.
1. Depois de selecionar as APIs, verá a página **Pedir Permissões de API**. Se a API expuser as permissões delegadas e as permissões da aplicação, selecione o tipo de permissão de que a sua aplicação precisa.
1. Quando tiver concluído, selecione **Adicionar permissões**. É reencaminhado para a página **Permissões da API** na qual as permissões foram guardadas e adicionadas à tabela.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outros inícios rápidos de gestão de aplicações relacionados:

* [Registar uma aplicação na plataforma de identidade da Microsoft](quickstart-register-app.md)
* [Configurar uma aplicação para expor APIs Web](quickstart-configure-app-expose-web-apis.md)
* [Modificar as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md)
* [Remover uma aplicação registada na plataforma de identidade da Microsoft](quickstart-remove-app.md)

Para saber mais sobre os dois objetos do Azure AD que representam uma aplicação registada e o relacionamento entre os mesmos, veja [Application objects and service principal objects](app-objects-and-service-principals.md) (Objetos da aplicação e objetos do principal de serviço).

Para saber mais sobre as diretrizes de imagem corporativa que deve seguir quando desenvolver aplicações com o Azure Active Directory, veja [Diretrizes de imagem corporativa para aplicações](howto-add-branding-in-azure-ad-apps.md).

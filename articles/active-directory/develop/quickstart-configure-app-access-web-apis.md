---
title: 'Quickstart: Configurar uma app para aceder a uma API web Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, configura uma aplicação registada na plataforma de identidade da Microsoft para incluir URIs redirecionando URIs, credenciais ou permissões para aceder a APIs web.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 800b399e73be032cfd9d2849b004018aa9d9031f
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120869"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Quickstart: Configurar uma aplicação do cliente para aceder a uma API web

Neste arranque rápido, adiciona URIs redirecionando, credenciais ou permissões para aceder a APIs web para a sua aplicação. Uma aplicação web ou confidencial do cliente precisa de estabelecer credenciais seguras para participar num fluxo de concessão de autorização que requer autenticação. O método de autenticação predefinido que o portal do Azure suporta é ID de cliente + chave secreta. A aplicação obtém um token de acesso durante este processo.

Antes de um cliente poder aceder a uma API web exposta por uma aplicação de recursos, como a Microsoft Graph API, o quadro de consentimento garante que o cliente obtém a autorização necessária para as permissões solicitadas. Por predefinição, todas as aplicações podem solicitar permissões a partir da API do Gráfico da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Conclusão do [Quickstart: Configurar uma aplicação para expor uma API web](quickstart-configure-app-expose-web-apis.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Iniciar sessão no portal do Azure e selecionar a aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito. Desconfiem da sua sessão de portal para o inquilino da AD Azure que pretende.
1. Procure e selecione **Azure Active Directory**. Em **Gerir**, selecione **Registos de aplicações**.
1. Encontre e selecione a aplicação que quer configurar. Depois de selecionar a aplicação, consulte a **Visão Geral** da aplicação ou a página principal de registo.

Utilize os seguintes procedimentos para configurar a sua aplicação para aceder às APIs web.

## <a name="add-redirect-uris-to-your-application"></a>Adicionar URIs de redirecionamento à aplicação

Pode adicionar URIs de redirecionamento personalizado e sugerir URIs de redirecionamento para a sua aplicação. Para adicionar um URI de redirecionamento personalizado para aplicações web e cliente público:

1. A partir da página **geral** da aplicação, selecione **Autenticação.**
1. Localizar **redirecionar URIs**. Poderá ser necessário selecionar **a Switch para a experiência antiga.**
1. Selecione o tipo de aplicação que está a construir: **Web** ou **Cliente Público/nativo (mobile & desktop)**.
1. Introduza o URI de redirecionamento da aplicação,

   * Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, `http://localhost:31544` pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação cliente Web.
   * Para aplicações públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Introduza um valor específico da sua aplicação, por exemplo: `https://MyFirstApp` .
1. Selecione **Guardar**.

Para escolher entre URIs de redirecionamento sugerido para clientes públicos, siga estes passos:

1. A partir da página **geral** da aplicação, selecione **Autenticação.**
1. Localizar **URIs de redirecionamento sugerido para clientes públicos (mobile, desktop)**. Poderá ser necessário selecionar **a Switch para a experiência antiga.**
1. Selecione um ou mais URIs de redirecionamento para a sua aplicação. Também pode introduzir um URI de redirecionamento personalizado. Se não sabe o que usar, consulte a documentação da biblioteca.
1. Selecione **Guardar**.

Algumas restrições aplicam-se ao redirecionamento de URIs. Para obter mais informações, consulte [as restrições e limitações de URL de redirecionamento URI/resposta](./reply-url.md).

> [!NOTE]
> Experimente a experiência das novas definições **de autenticação** onde pode configurar as definições para a sua aplicação com base na plataforma ou dispositivo que pretende direcionar.
>
> Para ver esta vista, **selecione Experimente a nova experiência** na página **autenticação.**
>
> ![Clique em "Experimente a nova experiência" para ver a visualização da configuração da Plataforma](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Isto leva-o à nova página de [ **configurações da Plataforma.** ](#configure-platform-settings-for-your-application)

### <a name="configure-advanced-settings-for-your-application"></a>Configure definições avançadas para a sua aplicação

Dependendo da aplicação que está a registar, existem algumas definições adicionais que poderá necessitar de configurar, tais como:

* **URL logout**.
* Para aplicações de uma página única, pode ativar **a concessão implícita** e selecionar os tokens que pretende que o ponto final de autorização emita.
* Para aplicações de ambiente de trabalho que adquirem fichas utilizando autenticação integrada do Windows, fluxo de código do dispositivo ou nome de utilizador/palavra-passe na secção **de tipo cliente predefinido,** defina a **aplicação Treat como** configuração de cliente público para **Sim**.
* Para aplicações antigas que estavam a usar o Live SDK para integrar-se no serviço de conta da Microsoft, configurar o **suporte Live SDK**. As novas aplicações não precisam desta definição.
* **Tipo de cliente predefinido**.
* **Tipos de conta suportados**.

### <a name="modify-supported-account-types"></a>Modificar tipos de conta suportados

Os **tipos de conta suportados** especificam quem pode utilizar a aplicação ou aceder à API.

Se configurar os tipos de conta suportados quando registou a aplicação, só pode alterar esta definição utilizando o editor manifesto de aplicação se:

* Altera os tipos de conta de **AzureADMyOrg** ou **AzureADMultipleOrgs** para **AzureADandPersonalMicrosoftAccount,** ou ao contrário, ou
* Altera os tipos de conta de **AzureADMyOrg** para **AzureADMultipleOrgs,** ou o contrário.

Para alterar os tipos de conta suportados para um registo de aplicações existente, atualize a `signInAudience` chave. Para mais informações, consulte [o manifesto de inscrição.](reference-app-manifest.md#configure-the-app-manifest)

## <a name="configure-platform-settings-for-your-application"></a>Configurar as definições da plataforma para a sua aplicação

![Configurar configurações para a sua aplicação com base na plataforma ou dispositivo](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Para configurar as definições de aplicações com base na plataforma ou dispositivo, está a direcionar::

1. Na página de **configurações** da Plataforma, selecione **Adicionar uma plataforma** e escolha entre as opções disponíveis.

   ![Mostra a página de plataformas configurar](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Introduza as informações de definições com base na plataforma selecionada.

   | Plataforma                | Definições de configuração            |
   |-------------------------|-----------------------------------|
   | **Web**              | Introduza o **URI de redirecionamento** para a sua aplicação. |
   | **iOS / macOS**              | Introduza o **ID do pacote de**aplicações, que pode encontrar no XCode em Info.plist ou Build Settings. Adicionar o iD do pacote cria automaticamente um URI de redirecionamento para a aplicação. |
   | **Android**          | Forneça o **nome do pacote de**aplicações, que pode encontrar no ficheiro AndroidManifest.xml.<br/>Gerar e introduzir o **hash Signature**. Adicionar o hash de assinatura automaticamente cria um URI de redirecionamento para a aplicação.  |
   | **Aplicações móveis e desktop**  | Opcional. Selecione um dos **URIs recomendados de redirecionamento** sugerido se estiver a construir aplicativos para desktop e dispositivos.<br/>Opcional. Introduza um **URI de redirecionamento personalizado,** que é usado como o local onde o Azure AD irá redirecionar os utilizadores em resposta a pedidos de autenticação. Por exemplo, para aplicações .NET Core onde pretender interação, use `http://localhost` . |

   > [!NOTE]
   > Nos Serviços da Federação de Diretórios Ativos (AD FS) e Azure AD B2C, também deve especificar um número de porta.  Por exemplo: `http://localhost:1234`.

   > [!IMPORTANT]
   > Para aplicações móveis que não utilizem a mais recente Biblioteca de Autenticação da Microsoft (MSAL) ou que não utilizem um corretor, deve configurar os URIs de redirecionamento para estas aplicações em **dispositivos Desktop +**.

Dependendo da plataforma que escolheu, pode haver configurações adicionais que pode configurar. Para aplicações **web,** pode:

* Adicione mais URIs de redirecionamento
* Configure **a concessão implícita** para selecionar os tokens que gostaria de ser emitida pelo ponto final de autorização:

  * Para aplicações de uma só página, selecione **tokens de acesso** e **fichas de ID**
  * Para aplicações web, selecione **tokens de ID**

## <a name="add-credentials-to-your-web-application"></a>Adicionar credenciais à aplicação Web

Para adicionar uma credencial à sua aplicação web, adicione um certificado ou crie um segredo de cliente. Para adicionar um certificado:

1. Na página **'Visão Geral'** da aplicação, selecione a secção **Certificados & segredos.**
1. Selecione **Carregar o certificado**.
1. Selecione o ficheiro que pretende carregar. Tem de ser do tipo .cer, .pem ou .crt.
1. Selecione **Adicionar**.

Para adicionar um segredo de cliente:

1. Na página **'Visão Geral'** da aplicação, selecione a secção **Certificados & segredos.**
1. Selecione **Novo segredo do cliente**.
1. Adicione uma descrição do segredo do cliente.
1. Selecione uma duração.
1. Selecione **Adicionar**.

> [!NOTE]
> Depois de guardar as alterações à configuração, a coluna mais a direita incluirá o valor do segredo do cliente. **Certifique-se de que copia o valor** para utilizar no código da sua aplicação cliente, pois aquele deixa de estar acessível quando sair desta página.

## <a name="add-permissions-to-access-web-apis"></a>Adicionar permissões para aceder a APIs Web

A [permissão de inscrição e leitura do perfil do utilizador](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) da API é selecionada por predefinição. Pode selecionar entre [dois tipos de permissões](developer-glossary.md#permissions) para cada API web:

* **Permissões de inscrição**. A aplicação do seu cliente precisa de aceder diretamente à API web como ele próprio, sem contexto de utilizador. Este tipo de permissão requer o consentimento do administrador. Esta permissão não está disponível para aplicações de desktop e clientes móveis.
* **Permissões delegadas.** A sua aplicação ao cliente precisa de aceder à API web como utilizador inscrito, mas com acesso limitado pela permissão selecionada. Este tipo de permissão pode ser concedido por um utilizador, a não ser que exija consentimento do administrador.

  > [!NOTE]
  > Adicionar uma permissão delegada a uma aplicação não concede automaticamente consentimento aos utilizadores no inquilino. Os utilizadores continuam a ter de consentir manualmente as permissões delegadas adicionadas no runtime, a não ser que o administrador conceda acesso em nome de todos eles.

Para adicionar permissões de acesso a APIs de recurso do seu cliente:

1. A partir da página **geral** da aplicação, selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicione uma permissão**.
1. Por predefinição, a vista permite-lhe selecionar de entre **APIs da Microsoft**. Selecione a secção de APIs em que está interessado:

    * **APIs da Microsoft**. Permite-lhe selecionar permissões para APIs da Microsoft, como o Microsoft Graph.
    * **APIs a minha organização usa.** Permite-lhe selecionar permissões para APIs que a sua organização expõe, ou APIs com as quais a sua organização se integrou.
    * **Os meus APIs.** Permite-lhe selecionar permissões para APIs que expõe.

1. Depois de selecionar as APIs, verá a página **Pedir Permissões de API**. Se a API expuser as permissões delegadas e as permissões da aplicação, selecione o tipo de permissão de que a sua aplicação precisa.
1. Quando tiver concluído, selecione **Adicionar permissões**.

Volte para a página de permissões da **API.** As permissões foram guardadas e adicionadas à mesa.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Compreender permissões da API e consentimento administrativo UI

### <a name="configured-permissions"></a>Permissões configuradas

Esta secção mostra as permissões que foram explicitamente configuradas no objeto da aplicação. Estas permissões fazem parte da lista de acesso a recursos exigida da aplicação. Pode adicionar ou remover permissões desta tabela. Como administrador, também pode conceder ou revogar o consentimento administrativo para um conjunto de permissões ou permissões individuais da API.

### <a name="other-permissions-granted"></a>Outras permissões concedidas

Se o seu pedido estiver registado num inquilino, poderá consultar uma secção adicional intitulada **Outras permissões concedidas ao Arrendatário.** Esta secção mostra permissões concedidas ao inquilino que não foram explicitamente configuradas no objeto de aplicação. Estas permissões foram solicitadas e consentidas dinamicamente. Esta secção só aparece se houver pelo menos uma permissão que se aplique.

Pode adicionar um conjunto de permissões da API ou permissões individuais que aparecem nesta secção na secção **permissões Configuradas.** Como administrador, também pode revogar o consentimento administrativo para APIs individuais ou permissões nesta secção.

### <a name="admin-consent-button"></a>Botão de consentimento de administração

Se o seu pedido estiver registado num inquilino, consulte um consentimento administrativo para o botão **De inquilino.** É desativado se não for um administrador, ou se não tiverem sido configuradas permissões para a aplicação.
Este botão permite que um administrador conceda consentimento administrativo às permissões configuradas para a aplicação. Clicar no botão de consentimento administrativo lança uma nova janela com um pedido de consentimento mostrando todas as permissões configuradas.

> [!NOTE]
> Existe um atraso entre as permissões configuradas para o pedido e as que aparecem no pedido de consentimento. Se não vir todas as permissões configuradas na hora de consentimento, feche-as e volte a lançá-la.

Se tiver permissões que tenham sido concedidas mas não configuradas, o botão de consentimento administrativo solicita-lhe que manuseie estas permissões. Pode adicioná-las a permissões configuradas ou pode removê-las.

O pedido de consentimento fornece a opção de **Aceitar** ou **Cancelar**. Selecione **Aceitar** conceder o consentimento administrativo. Se selecionar **Cancelar,** o consentimento da administração não é concedido. Uma mensagem de erro diz que o consentimento foi recusado.

> [!NOTE]
> Existe um atraso entre a concessão do consentimento administrativo, selecionando **Aceitar** na pronta de consentimento e o estado do consentimento administrativo ser refletido no portal.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo quickstart da série para aprender a configurar quais os tipos de conta que podem aceder à sua aplicação. Por exemplo, você pode querer limitar o acesso apenas aos utilizadores da sua organização (inquilino único) ou permitir utilizadores em outros inquilinos AZure AD (multi-inquilino) e aqueles com contas pessoais da Microsoft (MSA).

> [!div class="nextstepaction"]
> [Modificar as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md)
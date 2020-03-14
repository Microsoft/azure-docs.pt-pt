---
title: 'Quickstart: Access web APIs for app - Microsoft identity platform  Azure'
description: Neste arranque rápido, configure uma aplicação registada na plataforma de identidade da Microsoft para incluir REdirecionamento de URIs, credenciais ou permissões para aceder a APIs web.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 5e628626f2db49ff67d6d7ab425a3a19870b1ebd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240899"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Quickstart: Configure uma aplicação de cliente para aceder a APIs web

Neste arranque rápido, adiciona URIs redirecionais, credenciais ou permissões para aceder a APIs web para a sua aplicação. Uma aplicação web ou cliente confidencial precisa estabelecer credenciais seguras para participar num fluxo de concessão de autorização que requer autenticação. O método de autenticação predefinido que o portal do Azure suporta é ID de cliente + chave secreta. A aplicação obtém um sinal de acesso durante este processo.

Antes de um cliente poder aceder a uma API web exposta por uma aplicação de recursos, como a Microsoft Graph API, a estrutura de consentimento garante que o cliente obtém a concessão de permissão necessária para as permissões solicitadas. Por predefinição, todas as aplicações podem solicitar permissões da API do Microsoft Graph.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](quickstart-register-app.md).
* Revisão de [Permissões e consentimento no ponto final da plataforma de identidade da Microsoft](v2-permissions-and-consent.md).
* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Iniciar sessão no portal do Azure e selecionar a aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito. Detete a sua sessão de portal para o inquilino da AD Azure que quiser.
1. Procure e selecione **Azure Ative Directory**. Under **Manage**, selecione **Registos de Aplicações**.
1. Encontre e selecione a aplicação que quer configurar. Depois de selecionar a aplicação, consulte a **visão geral** da aplicação ou a página principal de registo.

Utilize os seguintes procedimentos para configurar a sua aplicação para aceder a APIs web.

## <a name="add-redirect-uris-to-your-application"></a>Adicione URIs redirecionamento à sua aplicação

Pode adicionar URIs de redirecionamento personalizado e redirecionar URIs para a sua aplicação. Para adicionar um URI personalizado para aplicações de clientes web e públicos:

1. A partir da página de **visão geral** da aplicação, selecione **Autenticação**.
1. Localizar **URIs de redirecionamento**. Pode ser necessário selecionar **a Switch para a experiência antiga.**
1. Selecione o tipo de aplicação que está a construir: **Web** ou **Cliente Público/nativo (mobile & desktop)** .
1. Introduza o URI de redirecionamento da aplicação,

   * Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, `http://localhost:31544` pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação cliente Web.
   * Para aplicações públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Introduza um valor específico da sua aplicação, por exemplo: `https://MyFirstApp`.
1. Selecione **Guardar**.

Para escolher entre URIs de redirecionamento sugerido para clientes públicos, siga estes passos:

1. A partir da página de **visão geral** da aplicação, selecione **Autenticação**.
1. Localizar **URIs de redirecionamento sugeridos para clientes públicos (mobile, desktop)** . Pode ser necessário selecionar **a Switch para a experiência antiga.**
1. Selecione um ou mais URIs redirecionados para a sua aplicação. Também pode introduzir um URI de redirecionamento personalizado. Se não sabe o que usar, consulte a documentação da biblioteca.
1. Selecione **Guardar**.

Certas restrições aplicam-se à redirecionamento de URIs. Para mais informações, consulte [Redirecionamento de restrições e limitações de URL DE URI/resposta](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

> [!NOTE]
> Experimente a nova experiência de definições de **Autenticação** onde pode configurar as definições para a sua aplicação com base na plataforma ou dispositivo que pretende atingir.
>
> Para ver esta vista, selecione **Experimente a nova experiência** a partir da página De **autenticação.**
>
> ![Clique em "Experimente a nova experiência" para ver a vista de configuração da Plataforma](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Isto leva-o à [nova página de **configurações** ](#configure-platform-settings-for-your-application)da Plataforma .

### <a name="configure-advanced-settings-for-your-application"></a>Configure configurações avançadas para a sua aplicação

Dependendo da aplicação que está a registar, existem algumas definições adicionais que poderá necessitar de configurar, tais como:

* **URL de logout**.
* Para aplicações de página única, pode ativar a **concessão implícita** e selecionar os tokens que gostaria que o ponto final da autorização emitisse.
* Para aplicações de desktop que adquirem fichas utilizando a Autenticação Integrada do Windows, o fluxo de código do dispositivo ou o nome de utilizador/palavra-passe na secção do tipo de **cliente Predefinido,** defina a **aplicação Treat como** definição de cliente público para **Sim**.
* Para aplicações antigas que estavam a usar o Live SDK para integrar com o serviço de conta microsoft, configure suporte **Live SDK**. As novas aplicações não precisam deste cenário.
* **Tipo de cliente padrão**.
* Tipos de **conta suportados**.

### <a name="modify-supported-account-types"></a>Modificar tipos de conta suportados

Os **tipos de conta suportados** especificam quem pode usar a aplicação ou aceder à API.

Se configurar os tipos de conta suportados quando registou a aplicação, só pode alterar esta definição utilizando o editor manifesto de aplicação se:

* Altera os tipos de conta de **AzureADMyOrg** ou **AzureADMultipleOrgs** para **AzureADandPersonalMicrosoftAccount,** ou o contrário, ou o contrário, ou
* Muda os tipos de conta de **AzureADMyOrg** para **AzureADMultipleOrgs,** ou o contrário.

Para alterar os tipos de conta suportados para um registo de aplicações existente, atualize a chave `signInAudience`. Para mais informações, consulte [Configurar o manifesto de aplicação](reference-app-manifest.md#configure-the-app-manifest).

## <a name="configure-platform-settings-for-your-application"></a>Configure as definições da plataforma para a sua aplicação

![Configure as definições para a sua aplicação com base na plataforma ou dispositivo](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Para configurar as definições de aplicação com base na plataforma ou no dispositivo, está a segmentar:

1. Na página de configurações da **Plataforma,** selecione **Adicionar uma plataforma** e escolher entre as opções disponíveis.

   ![Mostra a página de plataformas Configure](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Introduza as informações de definições com base na plataforma selecionada.

   | Plataforma                | Definições de configuração            |
   |-------------------------|-----------------------------------|
   | **Web**              | Insira o **Redirect URI** para a sua candidatura. |
   | **iOS / macOS**              | Introduza o id do **pacote**da aplicação , que pode encontrar no XCode em Info.plist ou 'Build Settings'. Adicionar o ID do pacote cria automaticamente um URI redirecionado para a aplicação. |
   | **Android**          | Forneça o **nome pacote**da aplicação , que pode encontrar no ficheiro AndroidManifest.xml.<br/>Gerar e introduzir o **hash Signature**. Adicionar o hash de assinatura cria automaticamente um URI redirecionado para a aplicação.  |
   | **Aplicações móveis e de desktop**  | Opcional. Selecione um dos **URIs de redirecionamento sugerido** recomendado se estiver a construir aplicações para desktop e dispositivos.<br/>Opcional. Introduza um **URI de redirecionamento personalizado,** que é usado como local onde o Azure AD irá redirecionar os utilizadores em resposta aos pedidos de autenticação. Por exemplo, para aplicações .NET Core onde deseja interação, use `https://localhost`. |

   > [!IMPORTANT]
   > Para aplicações móveis que não estejam a utilizar a mais recente Biblioteca de Autenticação da Microsoft (MSAL) ou que não utilizem um corretor, tem de configurar os URIs de redirecionamento para estas aplicações em **dispositivos Desktop +** .

Dependendo da plataforma que escolheu, pode haver configurações adicionais que possa configurar. Para aplicações **Web,** pode:

* Adicione urIs mais redirecionais
* Configure **a subvenção implícita** para selecionar os tokens que deseja ser emitido pelo ponto final da autorização:

  * Para aplicações de uma página única, selecione tanto **fichas** de acesso como **fichas de identificação**
  * Para aplicações web, selecione **tokens de ID**

## <a name="add-credentials-to-your-web-application"></a>Adicionar credenciais à aplicação Web

Para adicionar uma credencial à sua aplicação web, adicione um certificado ou crie um segredo de cliente. Para adicionar um certificado:

1. Na página **'Overview'** da aplicação, selecione a secção **Certificados e Segredos.**
1. Selecione **Carregar o certificado**.
1. Selecione o ficheiro que pretende carregar. Tem de ser do tipo .cer, .pem ou .crt.
1. Selecione **Adicionar**.

Para adicionar um segredo de cliente:

1. Na página **'Overview'** da aplicação, selecione a secção **Certificados e Segredos.**
1. Selecione **Novo segredo do cliente**.
1. Adicione uma descrição do segredo do cliente.
1. Selecione uma duração.
1. Selecione **Adicionar**.

> [!NOTE]
> Depois de guardar as alterações à configuração, a coluna mais a direita incluirá o valor do segredo do cliente. **Certifique-se de que copia o valor** para utilizar no código da sua aplicação cliente, pois aquele deixa de estar acessível quando sair desta página.

## <a name="add-permissions-to-access-web-apis"></a>Adicionar permissões para aceder a APIs Web

O sinal de sinal de [API do gráfico e a permissão de perfil](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) do utilizador são selecionados por defeito. Pode selecionar entre [dois tipos de permissões](developer-glossary.md#permissions) para cada API web:

* **Permissões de candidatura**. A sua aplicação de cliente necessita de aceder diretamente à API web como ela própria, sem contexto de utilizador. Este tipo de permissão requer o consentimento do administrador. Esta permissão não está disponível para aplicações de desktop e clientes móveis.
* **Permissões delegadas.** A sua aplicação de cliente necessita de aceder à Web API como utilizador inscrito, mas com acesso limitado pela permissão selecionada. Este tipo de permissão pode ser concedido por um utilizador, a não ser que exija consentimento do administrador.

  > [!NOTE]
  > Adicionar uma permissão delegada a uma aplicação não concede automaticamente consentimento aos utilizadores no inquilino. Os utilizadores continuam a ter de consentir manualmente as permissões delegadas adicionadas no runtime, a não ser que o administrador conceda acesso em nome de todos eles.

Para adicionar permissões para aceder a APIs de recursos do seu cliente:

1. A partir da página de **visão geral** da aplicação, selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicionar uma permissão**.
1. Por predefinição, a vista permite-lhe selecionar de entre **APIs da Microsoft**. Selecione a secção de APIs em que está interessado:

    * **Microsoft APIs**. Permite selecionar permissões para APIs da Microsoft, como o Microsoft Graph.
    * **APIs que a minha organização usa.** Permite-lhe selecionar permissões para APIs que a sua organização expõe, ou APIs com as quais a sua organização se integrou.
    * **Os meus apis.** Permite-lhe selecionar permissões para APIs que expõe.

1. Depois de selecionar as APIs, verá a página **Pedir Permissões de API**. Se a API expuser as permissões delegadas e as permissões da aplicação, selecione o tipo de permissão de que a sua aplicação precisa.
1. Quando tiver concluído, selecione **Adicionar permissões**.

Volta para a página de **permissões da API.** As permissões foram guardadas e adicionadas à mesa.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Compreender permissões da API e consentimento administrativo UI

### <a name="configured-permissions"></a>Permissões configuradas

Esta secção mostra as permissões que foram explicitamente configuradas no objeto de aplicação. Estas permissões fazem parte da lista de acesso a recursos exigidos pela aplicação. Pode adicionar ou remover permissões desta tabela. Como administrador, também pode conceder ou revogar o consentimento do administrador para um conjunto de permissões ou permissões individuais de um API.

### <a name="other-permissions-granted"></a>Outras permissões concedidas

Se o seu pedido estiver registado num inquilino, poderá ver uma secção adicional intitulada **Outras permissões concedidas ao Arrendatário.** Esta secção mostra permissões concedidas ao inquilino que não foram explicitamente configuradas no objeto de aplicação. Estas permissões foram dinamicamente solicitadas e consentidas. Esta secção só aparece se houver pelo menos uma permissão que se aplique.

Pode adicionar um conjunto de permissões ou permissões individuais de uma API que apareçam nesta secção à secção de **permissões Configuradas.** Como administrador, também pode revogar o consentimento da administração para APIs individuais ou permissões nesta secção.

### <a name="admin-consent-button"></a>Botão de consentimento do administrador

Se o seu pedido estiver registado num inquilino, verá um consentimento de administrador do Grant para o botão **Tenant.** É desativado se não for administrador, ou se não tiver permissões para a aplicação.
Este botão permite que um administrador conceda consentimento ao administrador para as permissões configuradas para a aplicação. Clicar no botão de consentimento da administração lança uma nova janela com um pedido de consentimento que mostra todas as permissões configuradas.

> [!NOTE]
> Há um atraso entre as permissões que estão a ser configuradas para o pedido e que aparecem no pedido de consentimento. Se não vir todas as permissões configuradas no pedido de consentimento, feche-a e lance novamente.

Se tiver permissões que tenham sido concedidas mas não configuradas, o botão de consentimento do administrador pede-lhe que trate destas permissões. Pode adicioná-las a permissões configuradas ou pode removê-las.

O pedido de consentimento fornece a opção de **Aceitar** ou **Cancelar**. Selecione **Aceitar** conceder o consentimento do administrador. Se selecionar **cancelar,** o consentimento da administração não é concedido. Uma mensagem de erro afirma que o consentimento foi recusado.

> [!NOTE]
> Há um atraso entre a concessão do consentimento do administrador, selecionando **Aceitar** o pedido de consentimento e o estado do consentimento do administrador sendo refletido no portal.

## <a name="next-steps"></a>Passos seguintes

Avançar para o próximo artigo para aprender a expor APIs web.
> [!div class="nextstepaction"]
> [Quickstart: Configure uma aplicação para expor APIs web](quickstart-configure-app-expose-web-apis.md)

* Para saber mais sobre os dois objetos do Azure AD que representam uma aplicação registada e o relacionamento entre os mesmos, veja [Application objects and service principal objects](app-objects-and-service-principals.md) (Objetos da aplicação e objetos do principal de serviço).

* Para saber mais sobre as diretrizes de imagem corporativa que deve seguir quando desenvolver aplicações com o Azure Active Directory, veja [Diretrizes de imagem corporativa para aplicações](howto-add-branding-in-azure-ad-apps.md).

* [Quickstart: Registe uma aplicação com a plataforma de identidade da Microsoft](quickstart-register-app.md)

* [Quickstart: Modificar as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md)

* [Quickstart: Remova uma aplicação registada na plataforma de identidade da Microsoft](quickstart-remove-app.md)

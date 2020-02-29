---
title: Autenticação ad'azure configure
description: Saiba como configurar a autenticação do Diretório Ativo Azure como fornecedor de identidade para a sua aplicação App Service.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 717336e0ddfe99c96afda4861f4de1239ee949bf
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913213"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Configure a sua app de serviço de aplicações para utilizar o login azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra-lhe como configurar o Azure App Service para utilizar o Azure Ative Directory (Azure AD) como fornecedor de autenticação.

Siga estas boas práticas ao configurar a sua app e autenticação:

- Dê a cada app do App Service as suas próprias permissões e consentimento.
- Configure cada app do App Service com o seu próprio registo.
- Evite a partilha de permissões entre ambientes utilizando registos de aplicações separados para slots de implementação separados. Ao testar um novo código, esta prática pode ajudar a evitar que problemas afetem a aplicação de produção.

## <a name="express"> </a>Configure com configurações expressas

> [!NOTE]
> A opção **Express** não está disponível para nuvens governamentais. 

1. No [Portal do Azure]procure e selecione **Serviços de Aplicações**e, em seguida, selecione a sua aplicação.
2. A partir da navegação à esquerda, **selecione Autenticação /Autorização** > **Ligado**.
3. Selecione **Diretório Ativo Azure** > **Express**.

   Se quiser escolher um registo de aplicação existente:

   1. Escolha **a aplicação AD existente**e, em seguida, clique na **aplicação AD Azure**.
   2. Escolha o registo de uma aplicação existente e clique **em OK**.

3. Selecione **OK** para registar a app Service no Diretório Ativo Azure. É criado um novo registo de aplicações.
   
    ![Configurações expressas em Diretório Ativo Azure](./media/configure-authentication-provider-aad/express-settings.png)
   
4. (Opcional) Por padrão, o Serviço de Aplicações fornece a autenticação, mas não restringe o acesso autorizado aos conteúdos e APIs do seu site. Tem de autorizar os utilizadores no seu código de aplicações. Para restringir o acesso à aplicação apenas aos utilizadores autenticados pelo Diretório Ativo do Azure, deteteto **a Ação a tomar quando o pedido não for autenticado** para iniciar sessão com o **Diretório Ativo do Azure**. Ao definir esta funcionalidade, a sua aplicação requer que todos os pedidos sejam autenticados. Também redireciona todos os não autenticados para o Diretório Ativo Azure para autenticação.

    > [!CAUTION]
    > Restringir o acesso desta forma aplica-se a todas as chamadas para a sua app, o que pode não ser desejável para aplicações que tenham uma página inicial disponível ao público, como em muitas aplicações de página única. Para tais aplicações, **permitir pedidos anónimos (nenhuma ação)** pode ser preferido, com a aplicação a iniciar o login manualmente em si. Para mais informações, consulte o [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).
5. Selecione **Guardar**.

## <a name="advanced"> </a>Configurar com configurações avançadas

Pode configurar manualmente as definições de aplicações se pretender utilizar um registo de aplicações de um inquilino Azure AD diferente. Para completar esta configuração personalizada:

1. Crie uma inscrição em Azure AD.
2. Forneça alguns dos dados de registo ao Serviço de Aplicações.

### <a name="register"> </a>Crie um registo de aplicativos no Azure AD para a sua app App Service

Necessitará das seguintes informações quando configurar a sua aplicação de Serviço de Aplicações:

- ID de Cliente
- ID do inquilino
- Segredo do cliente (opcional)
- ID de aplicação URI

Execute os seguintes passos:

1. Inscreva-se no [Portal do Azure]procure e selecione **Serviços de Aplicações,** e depois selecione a sua aplicação. Tenha em anotao o **URL**da sua aplicação. Irá usá-lo para configurar o registo da sua aplicação Azure Ative Directory.
1. Selecione **Registos** de > de **Diretório Ativo do Azure** > **Nova inscrição.**
1. Na página **do Registo de inscrição,** insira um **Nome** para o registo da sua aplicação.
1. Em **Redirecione o URI,** selecione **Web** e escreva `<app-url>/.auth/login/aad/callback`. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Selecione **Criar**.
1. Após a criação do registo da aplicação, copie o ID da **Aplicação (cliente)** e o **ID do Diretório (inquilino)** para mais tarde.
1. Selecione **Branding**. No URL da **página inicial,** introduza o URL da sua aplicação App Service e selecione **Guardar**.
1. Selecione Expor um **conjunto**de > **API** . Comente no URL da sua app App Service e selecione **Save**.

   > [!NOTE]
   > Este valor é o **ID DE Aplicação URI** do registo da aplicação. Se a sua aplicação web necessitar de acesso a uma API na nuvem, precisa do **ID URI** da aplicação web quando configurar o recurso cloud App Service. Pode usá-lo, por exemplo, se quiser que o serviço de nuvem conceda explicitamente acesso à aplicação web.

1. Selecione **Adicionar âmbito**.
   1. No **nome Scope,** *introduza user_impersonation*.
   1. Nas caixas de texto, introduza o nome e descrição do âmbito de consentimento que pretende que os utilizadores vejam na página de consentimento. Por exemplo, insira *Aceder à minha aplicação*. 
   1. **Selecione Adicionar âmbito**.
1. (Opcional) Para criar um segredo de cliente, selecione **Certificados e segredos** > **novo segredo do cliente** > **Add**. Copie o valor secreto do cliente mostrado na página. Não voltará a ser mostrado.
1. (Opcional) Para adicionar **vários URLs**de Resposta, selecione **Autenticação**.

### <a name="secrets"> </a>Ativar o Diretório Ativo Azure na sua app App Service

1. No [Portal do Azure]procure e selecione **Serviços de Aplicações**e, em seguida, selecione a sua aplicação. 
1. No painel esquerdo, em **Definições,** **selecione Autenticação / Autorização** > **ligado**.
1. (Opcional) Por defeito, a autenticação do Serviço de Aplicações permite o acesso não autenticado à sua aplicação. Para impor a autenticação do utilizador, detete a **Ação a tomar quando o pedido não for autenticado** para iniciar sessão com o **Diretório Ativo Azure**.
1. No âmbito dos **Fornecedores de Autenticação,** selecione **Diretório Ativo Azure**.
1. No **modo de Gestão,** selecione autenticação **avançada** e configure o Serviço de Aplicações de acordo com a tabela seguinte:

    |Campo|Descrição|
    |-|-|
    |ID de Cliente| Utilize a identificação da **Aplicação (cliente)** do registo da aplicação. |
    |ID do emitente| Utilize `https://login.microsoftonline.com/<tenant-id>`e substitua *\<inquilino-id>* com o **D.I. do Diretório (inquilino)** do registo da aplicação. |
    |Segredo de Cliente (Opcional)| Use o segredo do cliente gerado no registo da aplicação.|
    |Audiências simbólicas permitidas| Se se trata de uma aplicação de nuvem ou servidor e pretende permitir fichas de autenticação a partir de uma aplicação web, adicione o **ID URI** da aplicação web aqui. O ID do **cliente** configurado é *sempre* considerado implicitamente como um público permitido. |

2. Selecione **OK**, e, em seguida, selecione **Guardar**.

Está agora pronto para utilizar o Diretório Ativo Azure para autenticação na sua aplicação App Service.

## <a name="configure-a-native-client-application"></a>Configurar uma aplicação de cliente nativo

Pode registar clientes nativos para permitir a autenticação utilizando uma biblioteca de clientes, como a Biblioteca de Autenticação do **Diretório Ativo.**

1. No [Portal do Azure]selecione **Registos** **de > De Diretório Ativo** > **Nova inscrição.**
1. Na página **do Registo de inscrição,** insira um **Nome** para o registo da sua aplicação.
1. No **Redirect URI,** selecione **cliente público (mobile & desktop)** e escreva o URL `<app-url>/.auth/login/aad/callback`. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Para uma aplicação Windows, utilize o [pacote SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como URI.
1. Selecione **Criar**.
1. Após a criação do registo da aplicação, copie o valor do ID da **Aplicação (cliente).**
1. Selecione **permissões API** > **Adicione uma permissão** > **Minhas APIs**.
1. Selecione o registo de aplicações que criou anteriormente para a sua aplicação App Service. Se não vir o registo da aplicação, certifique-se de que adicionou o âmbito **de user_impersonation** em [Criar uma inscrição de aplicação no Azure AD para](#register)a sua app App Service .
1. Selecione **user_impersonation**e, em seguida, selecione **Adicionar permissões**.

Já configurou uma aplicação de cliente nativo que pode aceder à sua aplicação app Service.

## <a name="related-content"> </a>Próximos passos

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/

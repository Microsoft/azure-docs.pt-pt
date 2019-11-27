---
title: Autorizar contas de desenvolvedor usando o Azure Active Directory-gerenciamento de API do Azure | Microsoft Docs
description: Saiba como autorizar usuários usando Azure Active Directory no gerenciamento de API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 067d4488b064ede572a4b3ad94c94fb1552c827d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454463"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizar contas de desenvolvedor usando Azure Active Directory no gerenciamento de API do Azure

Este artigo mostra como habilitar o acesso ao portal do desenvolvedor para usuários do Azure Active Directory (Azure AD). Este guia também mostra como gerenciar grupos de usuários do Azure AD adicionando grupos externos que contêm os usuários.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
- Importe e publique uma instância de gerenciamento de API do Azure. Para obter mais informações, consulte [importar e publicar](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizar contas de desenvolvedor usando o Azure AD

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
2. Selecione ![seta](./media/api-management-howto-aad/arrow.png).
3. Digite **API** na caixa de pesquisa.
4. Selecione **serviços de gerenciamento de API**.
5. Selecionar a sua instância de serviço de Gestão de API.
6. Em **segurança**, selecione **identidades**.
7. Selecione **+ Adicionar** na parte superior.

    O painel **Adicionar provedor de identidade** aparece à direita.
8. Em **tipo de provedor**, selecione **Azure Active Directory**.

    Controles que permitem que você insira outras informações necessárias aparecem no painel. Os controles incluem **ID do cliente** e **segredo do cliente**. (Você obterá informações sobre esses controles posteriormente neste artigo.)
9. Anote o conteúdo da **URL de redirecionamento**.
    
   ![Etapas para adicionar um provedor de identidade no portal do Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. No navegador, abra uma guia diferente. 
11. Navegue até o [portal do Azure-registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) para registrar um aplicativo no Active Directory.
12. Em **gerenciar**, selecione **registros de aplicativo**.
13. Selecione **novo registro**. Na página **registrar um aplicativo** , defina os valores da seguinte maneira:
    
* Defina o **nome** como um nome significativo. por exemplo, *portal do desenvolvedor*
* Defina os **tipos de conta com suporte** para **contas neste diretório organizacional somente**. 
* Defina o **URI de redirecionamento** para o valor obtido na etapa 9. 
* Escolha **registrar**. 

14.  Depois que o aplicativo for registrado, copie a **ID do aplicativo (cliente)** da página **visão geral** . 
15. Volte para a instância de gerenciamento de API. Na janela **Adicionar provedor de identidade** , Cole o valor da **ID do aplicativo (cliente)** na caixa **ID do cliente** .
16. Volte para a configuração do Azure AD, selecione **certificados & segredos** em **gerenciar**. Selecione o botão **novo segredo do cliente** . Insira um valor em **Descrição**, selecione qualquer opção para **expirar** e escolha **Adicionar**. Copie o valor do segredo do cliente antes de sair da página. Vai precisar dele no próximo passo. 
17. Em **gerenciar**, selecione **autenticação** e, em seguida, selecione **tokens de ID** em **concessão implícita**
18. Volte para a instância de gerenciamento de API, Cole o segredo na caixa **segredo do cliente** .

    > [!IMPORTANT]
    > Certifique-se de atualizar o **segredo do cliente** antes que a chave expire. 
    >  
    >

19. A janela **Adicionar provedor de identidade** também contém a caixa de texto **locatários permitidos** . Lá, especifique os domínios das instâncias do Azure AD aos quais você deseja conceder acesso às APIs da instância do serviço de gerenciamento de API. Você pode separar vários domínios com novas linhas, espaços ou vírgulas.

> [!NOTE]
> Você pode especificar vários domínios na seção **locatários permitidos** . Antes que qualquer usuário possa entrar de um domínio diferente do domínio original em que o aplicativo foi registrado, um administrador global do domínio diferente deve conceder permissão para que o aplicativo acesse os dados do diretório. Para conceder permissão, o administrador global deve: a. Vá para `https://<URL of your developer portal>/aadadminconsent` (por exemplo, https://contoso.portal.azure-api.net/aadadminconsent).
> b. Digite o nome de domínio do locatário do Azure AD ao qual deseja conceder acesso.
> c. Selecione **Enviar**. 

20.  Depois de especificar a configuração desejada, selecione **Adicionar**.

Depois que as alterações forem salvas, os usuários na instância especificada do Azure AD poderão entrar no portal do desenvolvedor seguindo as etapas em [entrar no portal do desenvolvedor usando uma conta do Azure ad](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Adicionar um grupo do Azure AD externo

Depois de habilitar o acesso para usuários em um locatário do Azure AD, você pode adicionar grupos do Azure AD ao gerenciamento de API. Como resultado, você pode controlar a visibilidade do produto usando grupos do Azure AD.

Para adicionar um grupo do Azure AD externo ao APIM, você deve primeiro concluir a seção anterior. Além disso, o aplicativo que você registrou deve receber acesso ao Azure Active Directory API do Graph com a permissão `Directory.ReadAll` seguindo as etapas abaixo: 

1. Volte para o registro do aplicativo que foi criado na seção anterior
2. Clique na guia **permissões de API** e, em seguida, clique em **+ Adicionar um botão de permissão** 
3. No painel **solicitar permissões de API** , selecione a guia **APIs da Microsoft** e role até a parte inferior para localizar o bloco **Azure Active Directory Graph** na seção APIs herdadas com suporte e clique nele. Em seguida, clique no botão **permissões do aplicativo** e selecione a permissão **Directory. ReadAll** e adicione essa permissão usando o botão na parte inferior. 
4. Clique no botão **conceder consentimento do administrador para {tenantname}** para conceder acesso a todos os usuários neste diretório. 

Agora você pode adicionar grupos externos do Azure AD na guia **grupos** da sua instância de gerenciamento de API.

1. Selecione o separador **Grupos**.
2. Selecione o botão **Adicionar grupo do AAD** .
   ![botão "Adicionar grupo do AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selecione o grupo que você deseja adicionar.
4. Pressione o botão **selecionar** .

Depois de adicionar um grupo do Azure AD externo, você pode revisar e configurar suas propriedades. Selecione o nome do grupo na guia **grupos** . Aqui, você pode editar as informações de **nome** e **Descrição** do grupo.
 
Os usuários da instância do Azure AD configurada agora podem entrar no portal do desenvolvedor. Eles podem exibir e assinar todos os grupos para os quais têm visibilidade.

## <a name="a-idlog_in_to_dev_portal-developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/> portal do desenvolvedor-adicionar autenticação de conta do Azure AD

No portal do desenvolvedor, é possível entrar com o AAD com o widget **botões do OAuth** . O widget já está incluído na página de entrada do conteúdo do portal do desenvolvedor padrão.

![Widget de botões do AAD](./media/api-management-howto-aad/portal-oauth-widget.png)

Embora uma nova conta seja criada automaticamente sempre que um novo usuário entrar com o AAD, você pode considerar adicionar o mesmo widget à página de inscrição.

> [!IMPORTANT]
> Você precisa [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações do AAD entrem em vigor.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Portal do desenvolvedor herdado-como entrar com o Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Para entrar no portal do desenvolvedor usando uma conta do Azure AD que você configurou nas seções anteriores:

1. Abra uma nova janela do navegador usando a URL de entrada da configuração do aplicativo Active Directory e selecione **Azure Active Directory**.

   ![Página de entrada][api-management-dev-portal-signin]

1. Insira as credenciais de um dos usuários no Azure AD e selecione **entrar**.

   ![Entrando com nome de usuário e senha][api-management-aad-signin]

1. Um formulário de registro poderá ser solicitado se qualquer informação adicional for necessária. Preencha o formulário de registro e selecione **inscrever-se**.

   ![Botão "inscrever-se" no formulário de registro][api-management-complete-registration]

Agora, o usuário está conectado ao portal do desenvolvedor da sua instância do serviço de gerenciamento de API.

![Portal do desenvolvedor após a conclusão do registro][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account

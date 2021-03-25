---
title: Autorizar contas de programadores utilizando o Azure Ative Directory
titleSuffix: Azure API Management
description: Saiba como autorizar os utilizadores utilizando o Azure Ative Directory em Gestão API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2021
ms.author: apimpm
ms.openlocfilehash: 743a7e7d34457405aa4be42b196dc994506c6587
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035812"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizar as contas de programador ao utilizar o Azure Active Directory na Gestão de API do Azure

Este artigo mostra-lhe como permitir o acesso ao portal do desenvolvedor para utilizadores a partir do Azure Ative Directory (Azure AD). Este guia também mostra como gerir grupos de utilizadores AZure AD adicionando grupos externos que contêm os utilizadores.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
- Importar e publicar um caso de Gestão API da Azure. Para mais informações, consulte [Importar e publicar.](import-and-publish.md)

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizar contas de programadores utilizando a Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione ![Ícone de seta.](./media/api-management-howto-aad/arrow.png).
3. Digite **api** na caixa de pesquisa.
4. Selecione **serviços de Gestão API.**
5. Selecionar a sua instância de serviço de Gestão de API.
6. No **portal Developer**, selecione **Identidades.**
7. **Selecione +Adicione** a partir de cima.

    O **painel de fornecedor de identidade Add** aparece à direita.
8. Sob **o tipo Provedor**, selecione **Azure Ative Directory**.

    Os controlos que lhe permitem introduzir outras informações necessárias aparecem no painel de mentes. Os controlos incluem **identificação do cliente** e **segredo do cliente.** (Obtém informações sobre estes controlos mais tarde no artigo.)
9. Tome nota do conteúdo do **URL de redirecionamento.**
    

    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="Adicionar fornecedor de identidade no portal Azure":::
    > [!NOTE]
    > Existem dois URLs de redirecionamento:<br/>
    > **Redirecionar URL** - aponta para o mais recente portal de desenvolvimento da Gestão da API.<br/>
    > **Redirecionar URL (portal precrito)** - aponta para o portal de desenvolvimento precrito da API Management.
    >
    > Recomenda-se a utilização do mais recente portal de desenvolvimento Redirect URL.
   
10. No seu navegador, abra um separador diferente. 
11. Navegue para o [portal Azure - Registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) para registar uma aplicação no Ative Directory.
12. Em **Gerir**, selecione **Registos de aplicações**.
13. Selecione **Novo registo**. Na página **registar uma página de candidatura,** definir os valores da seguinte forma:
    
    * Definir **Nome** para um nome significativo. por exemplo, *portal de desenvolvedores*
    * Definir **tipos de conta suportadas** para contas **apenas neste diretório organizacional**. 
    * **Desa redirecione o URI** para o valor que obteve do passo 9. 
    * Escolha **registar-se.** 

14.  Após o registo do pedido, copie o **ID de Aplicação (cliente)** na página **'Vista Geral'.** 
15. Volte para o seu caso de Gestão de API. Na janela do **fornecedor de identidade Add,** cole o valor **de ID da Aplicação (cliente)** na caixa **de ID do Cliente.**
16. Volte para a configuração AD Azure, selecione **certificados & segredos** em **Manage**. Selecione o botão **secreto do novo cliente.** Introduza um valor em **Descrição,** selecione qualquer opção para **Expirações** e escolha **Adicionar**. Copie o valor secreto do cliente antes de sair da página. Vai precisar deles no próximo passo. 
17. Em **Manage**, selecione **Autenticação** e, em seguida, selecione **fichas de ID** sob **Subvenção Implícita**
18. Volte para o seu caso de Gestão de API, cole o segredo na caixa secreta do **Cliente.**

    > [!IMPORTANT]
    > Por favor, certifique-se de atualizar o segredo do **Cliente** antes que a chave expire. 
    >  
    >

19. A janela **do fornecedor de identidade Add** também contém a caixa de texto dos **Inquilinos Permitidos.** Aí, especifique os domínios das instâncias AD Azure a que pretende conceder acesso às APIs da instância de serviço de Gestão da API. Pode separar vários domínios com novidades, espaços ou vírgulas.

    > [!NOTE]
    > Pode especificar vários domínios na secção **De Inquilinos Permitidos.** Antes que qualquer utilizador possa iniciar sposição a partir de um domínio diferente do domínio original onde a aplicação foi registada, um administrador global do diferente domínio deve conceder permissão para que a aplicação aceda a dados de diretório. Para conceder permissão, o administrador global deve: a. Vá a `https://<URL of your developer portal>/aadadminconsent` (por exemplo, https://contoso.portal.azure-api.net/aadadminconsent) .
    > b. Digite o nome de domínio do inquilino AZure AD a que queiram dar acesso.
    > c. Selecione **Submeter**. 

20.  Depois de especificar a configuração desejada, **selecione Adicionar**.

Após a resmoução das alterações, os utilizadores na instância AD Azure especificada podem iniciar sação no portal do desenvolvedor seguindo os passos [em Iniciar para o portal do desenvolvedor utilizando uma conta AD Azure](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Adicione um grupo AD Azure externo

Depois de ativar o acesso dos utilizadores num inquilino AZure AD, pode adicionar grupos AD AZure à API Management. Como resultado, pode controlar a visibilidade do produto utilizando grupos Azure AD.

Para adicionar um grupo AD Azure externo à APIM, tem primeiro de completar a secção anterior. Além disso, a aplicação que registou deve ter acesso à API do Microsoft Graph com `Directory.Read.All` permissão seguindo estes passos: 

1. Volte ao registo da sua App que foi criado na secção anterior.
2. Selecione **permissões API** e, em seguida, clique **em +Adicionar uma permissão**. 
3. No painel de permissões da **API do Pedido,** selecione o separador APIs da **Microsoft,** desloque-se para baixo e, em seguida, selecione o azulejo **Azure Ative Directory Graph.** Selecione **permissões de aplicação,** procure por **Diretório** e, em seguida, selecione a permissão **Do Diretório.Read.All.** 
4. Clique em **Adicionar permissões** na parte inferior do painel e, em seguida, clique em **Grant consentimento de administração para {tenantname}** para que você conceda acesso a todos os utilizadores neste diretório. 

Agora pode adicionar grupos AD externos do separador **Grupos** da sua instância de Gestão de API.

1. Selecione o separador **Grupos**.
2. Selecione o botão **do grupo Add AAD.**
    ![Botão "Adicionar grupo AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selecione o grupo que pretende adicionar.
4. Prima o **botão Seleção.**

Depois de adicionar um grupo Azure AD externo, pode rever e configurar as suas propriedades. Selecione o nome do grupo no **separador Grupos.** A partir daqui, pode editar informações de **Nome** e **Descrição** para o grupo.
 
Os utilizadores da instância AD Azure configurada podem agora iniciar sação no portal do desenvolvedor. Podem ver e subscrever quaisquer grupos para os quais tenham visibilidade.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> Portal do desenvolvedor - adicione a autenticação da conta Azure AD

No portal do desenvolvedor, o s-in com AAD é possível com o **botão de iniciar sposição: widget OAuth.** O widget já está incluído na página de inscrição do conteúdo do portal do desenvolvedor predefinido.

Apesar de uma nova conta ser criada automaticamente sempre que um novo utilizador entrar na AAD, poderá considerar adicionar o mesmo widget à página de inscrição.

O **formulário de inscrição: OAuth** widget representa um formulário usado para se inscrever na OAuth.

> [!IMPORTANT]
> É necessário [reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações da AAD entrem em vigor.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Portal de desenvolvimento legacy - como iniciar seduca com Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Para iniciar seduca no portal do desenvolvedor utilizando uma conta AD Azure que configurava nas secções anteriores:

1. Abra uma nova janela do navegador utilizando o URL de inscrição a partir da configuração da aplicação ative directory e selecione **O Diretório Ativo Azure**.

   ![Página de inscrição][api-management-dev-portal-signin]

1. Introduza as credenciais de um dos utilizadores em Azure AD e selecione **Iniciar sação .**

   ![Iniciar sessão com nome de utilizador e senha][api-management-aad-signin]

1. Pode ser solicitado com um formulário de inscrição se for necessária alguma informação adicional. Preencha o formulário de inscrição e **selecione Inscreva-se.**

   ![Botão "Inscrever-se" no formulário de registo][api-management-complete-registration]

O seu utilizador está agora inscrito no portal do programador para a sua instância de serviço de Gestão API.

![Portal do desenvolvedor após o registo estar completo][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account

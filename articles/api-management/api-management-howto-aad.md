---
title: Autorizar contas de desenvolvimento utilizando o Diretório Ativo do Azure
titleSuffix: Azure API Management
description: Saiba como autorizar os utilizadores utilizando o Diretório Ativo Azure na Gestão da API.
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
ms.openlocfilehash: 41f9f267880d199d2e221453eea5c3584ce96881
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868399"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizar as contas de programador ao utilizar o Azure Active Directory na Gestão de API do Azure

Este artigo mostra-lhe como permitir o acesso ao portal de desenvolvimento para utilizadores do Azure Ative Directory (Azure AD). Este guia também mostra como gerir grupos de utilizadores de Anúncios Azure, adicionando grupos externos que contêm os utilizadores.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
- Importar e publicar uma instância de Gestão API Azure. Para mais informações, consulte [Importar e publicar](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizar contas de desenvolvimento utilizando a AD Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione ![seta](./media/api-management-howto-aad/arrow.png).
3. Digite **api** na caixa de pesquisa.
4. Selecione **serviços de Gestão API**.
5. Selecionar a sua instância de serviço de Gestão de API.
6. No **portal Developer,** selecione **Identidades**.
7. Selecione **+Adicionar** a partir de cima.

    O painel do fornecedor de **identidade Add** aparece à direita.
8. Sob o **tipo de Fornecedor,** selecione **Diretório Ativo Azure**.

    Os controlos que lhe permitem introduzir outras informações necessárias aparecem no painel. Os controlos incluem **id cliente** e **segredo de cliente**. (Obtém informações sobre estes controlos mais tarde no artigo.)
9. Tome nota do conteúdo do **URL de redirecionamento**.
    
   ![Passos para adicionar um fornecedor de identidade no portal Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. No seu navegador, abra um separador diferente. 
11. Navegue para o [portal Azure - Registos](https://go.microsoft.com/fwlink/?linkid=2083908) de aplicações para registar uma app em Diretório Ativo.
12. Em **Gerir**, selecione **Registos de aplicações**.
13. Selecione **Novo registo**. No **Registo de uma** página de candidatura, delineie os valores da seguinte forma:
    
    * Definir **nome** para um nome significativo. por exemplo, *developer-portal*
    * Definir tipos de **conta suportados** a **contas apenas neste diretório organizacional**. 
    * **Desdirecione** o URI para o valor que obteve do passo 9. 
    * Escolha **Registar**. 

14.  Depois de registada a aplicação, copie o ID do **Pedido (cliente)** na página **'Visão Geral'.** 
15. Volte para a sua instância de Gestão aPI. Na janela adicionar fornecedor de **identidade,** colar o valor de ID da **Aplicação (cliente)** na caixa de ID do **cliente.**
16. Mude de volta para a configuração da AD Azure, selecione **Certificados & segredos** sob **a Gestão**. Selecione o botão secreto do **novo cliente.** Introduza um valor em **Descrição,** selecione qualquer opção para **Expirar** e escolha **Adicionar**. Copie o valor secreto do cliente antes de sair da página. Vai precisar deles no próximo passo. 
17. Under **Manage**, selecione **Autenticação** e, em seguida, selecione **fichas de ID** sob **Subvenção Implícita**
18. Volte para a sua instância de Gestão API, cola o segredo na caixa secreta do **Cliente.**

    > [!IMPORTANT]
    > Por favor, certifique-se de atualizar o segredo do **Cliente** antes que a chave expire. 
    >  
    >

19. A janela **do fornecedor** de identidade Add também contém a caixa de texto permitida para **inquilinos.** Aí, especifique os domínios dos casos de AD Azure a que pretende conceder acesso às APIs da instância de serviço de Gestão API. Pode separar vários domínios com novas linhas, espaços ou vírgulas.

    > [!NOTE]
    > Pode especificar vários domínios na secção **De Inquilinos Permitidos.** Antes que qualquer utilizador possa iniciar sessão a partir de um domínio diferente do domínio original onde a aplicação foi registada, um administrador global do diferente domínio deve conceder permissão para a aplicação aceder a dados de diretório. Para conceder permissão, o administrador global deve: a. Vá `https://<URL of your developer portal>/aadadminconsent` a (por https://contoso.portal.azure-api.net/aadadminconsent)exemplo, .
    > b. Digite o nome de domínio do inquilino azure a que queiram dar acesso.
    > c. Selecione **Submeter**. 

20.  Depois de especificar a configuração desejada, selecione **Adicionar**.

Após a resguardo das alterações, os utilizadores da instância AD Azure especificada podem iniciar sessão no portal do programador seguindo os passos de [entrada no portal do desenvolvedor utilizando uma conta Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Adicione um grupo aD Azure externo

Depois de permitir o acesso dos utilizadores a um inquilino DaAzure AD, pode adicionar grupos DeA Azure à API Management. Como resultado, pode controlar a visibilidade do produto utilizando grupos AD Azure.

Para adicionar um grupo aD Azure externo à APIM, tem primeiro de completar a secção anterior. Além disso, a aplicação que registou deve `Directory.Read.All` ter acesso à API do Microsoft Graph com permissão seguindo estas etapas: 

1. Volte ao registo da sua app que foi criada na secção anterior.
2. Selecione **Permissões API**e, em seguida, clique em **+Adicionar uma permissão**. 
3. No painel **de permissões Request API,** selecione o separador **APIs** da Microsoft e, em seguida, selecione o azulejo do **Microsoft Graph.** Selecione **permissões de aplicação,** procure o **Diretório**e, em seguida, selecione o **Diretório.Read.All** permission. 
4. Clique em **Adicionar permissões** na parte inferior do painel e, em seguida, clique **em Grant admin consent for {tenantname}** para que você conceda acesso a todos os utilizadores neste diretório. 

Agora pode adicionar grupos aD Azure externos do separador **Grupos** da sua instância de Gestão API.

1. Selecione o separador **Grupos**.
2. Selecione o botão de **grupo Add AAD.**
    ![Botão "Adicionar grupo AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selecione o grupo que pretende adicionar.
4. Prima o botão **Select.**

Depois de adicionar um grupo azure ad externo, pode rever e configurar as suas propriedades. Selecione o nome do grupo no separador **Grupos.** A partir daqui, pode editar informações de **Nome** e **Descrição** para o grupo.
 
Os utilizadores da instância Azure AD configurada podem agora iniciar sessão no portal do desenvolvedor. Podem ver e subscrever todos os grupos para os quais tenham visibilidade.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/>Portal de desenvolvimento - adicione autenticação da conta Azure AD

No portal de desenvolvimento, o iniciar sessão com a AAD é possível com o **botão 'Iniciar sessão': Widget OAuth.** O widget já está incluído na página de iniciar sessão do conteúdo do portal de desenvolvimento predefinido.

Embora uma nova conta seja criada automaticamente sempre que um novo utilizador se inscreva no AAD, poderá considerar adicionar o mesmo widget à página de inscrição.

O **formulário de inscrição: OAuth** widget representa um formulário usado para se inscrever na OAuth.

> [!IMPORTANT]
> É necessário [reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações da AAD entrem em vigor.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Portal de desenvolvimento legado - como iniciar sessão com a Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Para iniciar sessão no portal do desenvolvedor utilizando uma conta Azure AD que configurava nas secções anteriores:

1. Abra uma nova janela de navegador utilizando o URL de entrada da configuração da aplicação Ative Directory e selecione **Azure Ative Directory**.

   ![Página de sessão][api-management-dev-portal-signin]

1. Introduza as credenciais de um dos utilizadores em Azure AD e selecione **Iniciar sessão**.

   ![Iniciar sessão com nome de utilizador e senha][api-management-aad-signin]

1. Pode ser solicitado com um formulário de registo se forem necessárias informações adicionais. Preencha o formulário de inscrição e selecione **Inscrever-se**.

   ![Botão "Inscrever-se" no formulário de inscrição][api-management-complete-registration]

O utilizador está agora inscrito no portal de desenvolvimento para a sua instância de serviço de Gestão API.

![Portal de desenvolvimento após o registo estar completo][api-management-registration-complete]

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account

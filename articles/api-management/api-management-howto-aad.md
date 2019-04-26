---
title: Autorizar contas de programador com o Azure Active Directory - gestão de API do Azure | Documentos da Microsoft
description: Saiba como autorizar os utilizadores com o Azure Active Directory na gestão de API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: 95e501eca0f7765cc5201f7b315703c3ca43df8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60529553"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizar contas de programador com o Azure Active Directory na gestão de API do Azure

Este artigo mostra-lhe como permitir o acesso ao portal do programador para os utilizadores do Azure Active Directory (Azure AD). Este guia também mostra como gerir grupos de utilizadores do Azure AD através da adição de grupos externos que contenham os utilizadores.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o início rápido seguinte: [Criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).
- Importar e publicar uma instância de gestão de API do Azure. Para obter mais informações, consulte [importar e publicar](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizar contas de programador através do Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione ![seta](./media/api-management-howto-aad/arrow.png).
3. Tipo **api** na caixa de pesquisa.
4. Selecione **dos serviços de gestão de API**.
5. Selecionar a sua instância de serviço de Gestão de API.
6. Sob **Security**, selecione **identidades**.
7. Selecione **+ adicionar** da parte superior.

    O **fornecedor de identidade de adicionar** é apresentado o painel à direita.
8. Sob **tipo de fornecedor**, selecione **Azure Active Directory**.

    Controles que permitem que insira outras informações necessárias são exibidos no painel. Os controles incluem **ID de cliente** e **segredo do cliente**. (Obtenha informações sobre esses controles posteriormente neste artigo.)
9. Tome nota do conteúdo de **URL de redirecionamento**.
    
   ![Passos para adicionar um fornecedor de identidade no portal do Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. No seu browser, abra um separador de diferente. 
11. Navegue para o [portal do Azure – registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) para registar uma aplicação no Active Directory.
12. Sob **Manage**, selecione **registos das aplicações**.
13. Selecione **novo registo**. Sobre o **registar uma aplicação** página, defina os valores da seguinte forma:
    
* Definir **nome** para um nome significativo. e.g., *developer-portal*
* Definir **tipos de conta suportados** ao **contas neste diretório organizacional apenas**. 
* Definir **URI de redirecionamento** para o valor que obteve no passo 9. 
* Escolher **registar**. 

14.  Depois da aplicação fica registada, copie o **ID da aplicação (cliente)** partir a **descrição geral** página. 
15. Volte à sua instância de gestão de API. No **Adicionar fornecedor de identidade** janela, colar a **ID da aplicação (cliente)** valor para o **ID de cliente** caixa.
16. Volte para a configuração do Azure AD, selecione **certificados e segredos** sob **gerir**. Selecione o **novo segredo do cliente** botão. Introduza um valor na **Descrição**, selecione qualquer opção para **Expires** e escolha **Add**. Copie o valor do segredo de cliente antes de sair da página. Vai precisar deles no próximo passo. 
17. Voltar à sua instância de gestão de API, cole o segredo para o **segredo do cliente** caixa.

    > [!IMPORTANT]
    > Certifique-se atualizar o **segredo do cliente** antes da chave expirar. 
    >  
    >

18. O **Adicionar fornecedor de identidade** janela também contém o **inquilinos permitidos** caixa de texto. Lá, especifique os domínios das instâncias do Azure AD aos quais pretende conceder acesso às APIs de instância de serviço de gestão de API. É possível separar vários domínios com vírgulas, espaços ou garantidamente.

> [!NOTE]
> É possível especificar vários domínios no **inquilinos permitidos** secção. Antes de qualquer utilizador pode iniciar sessão a partir de um domínio diferente daquele domínio original em que a aplicação foi registrada, um administrador global do domínio diferente tem de conceder permissão para a aplicação para aceder a dados do diretório. Para conceder permissão, o administrador global deve: um. Aceda a `https://<URL of your developer portal>/aadadminconsent` (por exemplo, https://contoso.portal.azure-api.net/aadadminconsent).
> b. Escreva o nome de inquilino do Azure AD que pretende dar acesso ao domínio.
> c. Selecione **submeter**. 

19.  Depois de especificar a configuração pretendida, selecione **adicionar**.

Depois das alterações são guardadas, os utilizadores no Azure AD especificado instância pode iniciar sessão no portal do programador ao seguir os passos em [inicie sessão no portal do programador, utilizando uma conta do Azure AD](#log_in_to_dev_portal).


## <a name="add-an-external-azure-ad-group"></a>Adicionar um externo grupo do Azure AD

Depois de ativar o acesso de utilizadores numa instância do Azure AD, é possível adicionar grupos do Azure AD na gestão de API. Em seguida, pode gerir mais facilmente a associação de desenvolvedores no grupo de produtos desejados.

Para configurar um externo grupo do Azure AD, primeiro tem de configurar a instância do Azure AD no **identidades** separador, seguindo o procedimento na secção anterior. 

Adicionar o Azure externo de grupos do AD a **grupos** separador da sua instância de gestão de API.

1. Selecione o separador **Grupos**.
2. Selecione o **grupo do AAD adicionar** botão.
   ![Botão "Adicionar grupo do AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selecione o grupo que pretende adicionar.
4. Prima a **selecione** botão.

Depois de adicionar um Azure AD externo grupo, pode rever e configurar as respetivas propriedades. Selecione o nome do grupo a partir da **grupos** separador. A partir daqui, pode editar **Name** e **Descrição** informações para o grupo.
 
Os utilizadores configurada de instância do Azure AD agora pode iniciar sessão no portal do programador. Podem ver e subscrever todos os grupos para os quais têm visibilidade.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Inicie sessão no portal do programador, utilizando uma conta do Azure AD

Para iniciar sessão no portal do programador, utilizando uma conta do Azure AD que configurou nas secções anteriores:

1. Abra uma nova janela do browser, utilizando o URL de início de sessão da aplicação de configuração do Active Directory e selecione **do Azure Active Directory**.

   ![Página de início de sessão][api-management-dev-portal-signin]

1. Introduza as credenciais de um dos utilizadores no Azure AD e selecione **iniciar sessão**.

   ![Iniciar sessão com o nome de utilizador e palavra-passe][api-management-aad-signin]

1. Pode ser pedido com um formulário de Registro se qualquer informação adicional é necessária. Preencha o formulário de registro e selecione **Inscreva-se**.

   ![Botão "Inscrever-se" no formulário de registro][api-management-complete-registration]

O utilizador tem sessão iniciada agora no portal do programador para a sua instância do serviço de gestão de API.

![Portal do programador após a conclusão do registo][api-management-registration-complete]

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

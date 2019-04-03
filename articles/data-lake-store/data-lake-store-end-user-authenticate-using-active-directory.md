---
title: 'Autenticação de utilizador final: Geração 1 Lake armazenamento de dados do Azure com o Azure Active Directory | Documentos da Microsoft'
description: Saiba como conseguir a autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c0fe63e395ee08cb65e9bbbadc4ce1f03032ce95
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880088"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Geração de armazenamento 1 do Azure Data Lake utiliza o Azure Active Directory para autenticação. Antes de criar um aplicativo que funcione com o Data Lake Storage Gen1 ou o Azure Data Lake Analytics, deve decidir como autenticar a aplicação no Azure Active Directory (Azure AD). As duas opções principais disponíveis são:

* Autenticação de utilizador final (Este artigo)
* Autenticação serviço a serviço (Escolha esta opção da lista pendente acima)

Ambas estas opções resultam em seu aplicativo a ser fornecido com um token de OAuth 2.0, que é associado a cada pedido efetuado a geração 1 de armazenamento do Data Lake ou o Azure Data Lake Analytics.

Este artigo fala sobre como criar uma **aplicação nativa do Azure AD para autenticação de utilizador final**. Para obter instruções sobre configuração da aplicação do Azure AD para autenticação serviço a serviço, consulte [autenticação do serviço para serviço com a geração 1 de armazenamento do Data Lake com o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* ID da subscrição. Pode recuperá-la no portal do Azure. Por exemplo, está disponível a partir do painel de conta de geração 1 de armazenamento do Data Lake.
  
    ![Obter o ID de subscrição](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Seu nome de domínio do Azure AD. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. Na captura de tela em abaixo, é o nome de domínio **contoso.onmicrosoft.com**, e o GUID entre parênteses é o ID do inquilino. 
  
    ![Obter o domínio do AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* O ID de inquilino do Azure. Para obter instruções sobre como obter o ID de inquilino, consulte [obter o ID de inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).

## <a name="end-user-authentication"></a>Autenticação de utilizador final
Esse mecanismo de autenticação é a abordagem recomendada se pretender que um utilizador final para iniciar sessão na sua aplicação através do Azure AD. Seu aplicativo, em seguida, é possível aceder a recursos do Azure com o mesmo nível de acesso que o usuário final que iniciou sessão. O utilizador final tem de fornecer as credenciais periodicamente por ordem para a sua aplicação manter o acesso.

O resultado de ter o início de sessão do utilizador final é que seu aplicativo recebe um token de acesso e um token de atualização. O token de acesso anexado a cada pedido efetuado a geração 1 de armazenamento do Data Lake ou o Data Lake Analytics e é válido durante uma hora por predefinição. O token de atualização pode ser utilizado para obter um novo token de acesso e é válido para duas semanas por predefinição. Pode usar duas abordagens diferentes para início de sessão do utilizador final.

### <a name="using-the-oauth-20-pop-up"></a>Usando o pop-up de OAuth 2.0
Seu aplicativo pode acionar uma pop-up, de autorização de OAuth 2.0 em que o utilizador final pode introduzir as respetivas credenciais. Este pop-up também funciona com o processo de autenticação do Azure AD dois fatores (2FA), se necessário. 

> [!NOTE]
> Este método ainda não é suportado no Azure AD Authentication Library (ADAL) para o Python ou Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Na passagem direta em credenciais de utilizador
A aplicação diretamente pode fornecer as credenciais de utilizador para o Azure AD. Este método só funciona com contas de usuário de ID organizacionais. Não é compatível com o pessoal / "live ID" contas de utilizador, incluindo as contas que terminem @outlook.com ou @live.com. Além disso, este método não é compatível com contas de utilizador exigem autenticação do Azure AD dois fatores (2FA).

### <a name="what-do-i-need-for-this-approach"></a>O que é necessário para essa abordagem?
* Nome de domínio do Azure AD. Este requisito já está listado no pré-requisito deste artigo.
* ID de inquilino do Azure AD Este requisito já está listado no pré-requisito deste artigo.
* O Azure AD **aplicação nativa**
* ID da aplicação para a aplicação nativa do Azure AD
* URI de redirecionamento da aplicação nativa do Azure AD
* Definir permissões delegadas


## <a name="step-1-create-an-active-directory-native-application"></a>Passo 1: Criar uma aplicação nativa do Active Directory

Criar e configurar uma aplicação nativa do Azure AD para a autenticação do utilizador final com geração 1 de armazenamento do Data Lake com o Azure Active Directory. Para obter instruções, consulte [criar uma aplicação do Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Ao seguir as instruções na ligação, certifique-se de que seleciona **nativo** de tipo de aplicação, conforme mostrado na captura de ecrã seguinte:

![Criar aplicação web](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Criar aplicação nativa")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Passo 2: Obtenha o ID da aplicação e URI de redirecionamento

Ver [obter o ID da aplicação](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key) para obter o ID da aplicação.

Para obter o URI de redirecionamento, siga os passos abaixo.

1. A partir do portal do Azure, selecione **do Azure Active Directory**, clique em **registos das aplicações**e, em seguida, localize e clique na aplicação nativa do Azure AD que criou.

2. Partir do **definições** painel da aplicação, clique em **URIs de redirecionamento**.

    ![URI de redirecionamento de GET](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Copie o valor apresentado.


## <a name="step-3-set-permissions"></a>Passo 3: Definir permissões

1. A partir do portal do Azure, selecione **do Azure Active Directory**, clique em **registos das aplicações**e, em seguida, localize e clique na aplicação nativa do Azure AD que criou.

2. Do **configurações** painel da aplicação, clique em **permissões obrigatórias**e, em seguida, clique em **Add**.

    ![ID de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Na **adicionar acesso de API** painel, clique em **selecionar uma API**, clique em **Azure Data Lake**e, em seguida, clique em **selecione**.

    ![ID de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  Na **adicionar acesso de API** painel, clique em **selecionar permissões**, selecione a caixa de verificação para dar **acesso total ao Data Lake Store**e, em seguida, clique em **selecione**.

    ![ID de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Clique em **Concluído**.

5. Repita os dois últimos passos para conceder permissões de **API de gestão de serviço do Windows Azure** também.
   
## <a name="next-steps"></a>Passos Seguintes
Neste artigo, criou uma aplicação nativa do Azure AD e reunidas as informações necessárias em seus aplicativos de cliente que criar com o SDK de .NET, Java SDK, REST API, etc. Agora, pode avançar para os seguintes artigos que falam sobre como utilizar a aplicação web do Azure AD para autenticar primeiro Gen1 de armazenamento do Data Lake e, em seguida, executar outras operações na loja.

* [Finais autenticação de utilizador com a geração 1 de armazenamento do Data Lake com o SDK de Java](data-lake-store-end-user-authenticate-java-sdk.md)
* [Autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o Python](data-lake-store-end-user-authenticate-python.md)
* [Autenticação de utilizador final com geração 1 de armazenamento do Data Lake com a REST API](data-lake-store-end-user-authenticate-rest-api.md)


---
title: Autenticação de utilizador final - Data Lake Storage Gen1 com Azure AD
description: Saiba como obter a autenticação do utilizador final com a Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: a5272f7d580a3f8a68afda9150a1e95c1807eba6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103786"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticação de utilizador final com Azure Data Lake Storage Gen1 usando O Diretório Ativo Azure
> [!div class="op_single_selector"]
> * [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
>
>

Azure Data Lake Storage Gen1 utiliza o Azure Ative Directory para autenticação. Antes de autorizar uma aplicação que funcione com a Data Lake Storage Gen1 ou a Azure Data Lake Analytics, deve decidir como autenticar a sua aplicação com o Azure Ative Directory (Azure AD). As duas principais opções disponíveis são:

* Autenticação do utilizador final (este artigo)
* Autenticação de serviço-a-serviço (escolha esta opção a partir da descida acima)

Ambas as opções resultam em que a sua aplicação seja fornecida com um token OAuth 2.0, que é anexado a cada pedido feito à Data Lake Storage Gen1 ou ao Azure Data Lake Analytics.

Este artigo fala sobre como criar uma **aplicação nativa AZURE AD para a autenticação do utilizador final**. Para obter instruções sobre a configuração da aplicação AZure AD para autenticação de serviço-a-serviço, consulte [a autenticação de serviço-a-serviço com a Data Lake Storage Gen1 utilizando o Diretório Ativo Azure](./data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* O seu ID de assinatura. Pode recuperá-lo do portal Azure. Por exemplo, está disponível na lâmina da conta Gen1 de armazenamento de dados.

    ![Obtenha iD de assinatura](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* O seu nome de domínio Azure AD. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. A partir da imagem abaixo, o nome de domínio é **contoso.onmicrosoft.com**, e o GUID dentro dos parênteses é o ID do inquilino.

    ![Obtenha o domínio AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Sua identificação de inquilino Azure. Para instruções sobre como recuperar a identificação do inquilino, consulte [a identificação do inquilino.](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

## <a name="end-user-authentication"></a>Autenticação de utilizador final
Este mecanismo de autenticação é a abordagem recomendada se pretender que um utilizador final inscreva-se na sua aplicação através do Azure AD. A sua aplicação é então capaz de aceder aos recursos do Azure com o mesmo nível de acesso que o utilizador final que fez login. O utilizador final necessita de fornecer as suas credenciais periodicamente para que a sua aplicação mantenha o acesso.

O resultado de ter o sinal de utilizador final é que a sua aplicação recebe um token de acesso e um token de atualização. O token de acesso é anexado a cada pedido feito à Data Lake Storage Gen1 ou data lake analytics, e é válido por uma hora por padrão. O token de atualização pode ser usado para obter um novo token de acesso, e é válido por até duas semanas por padrão. Pode utilizar duas abordagens diferentes para o sômin do utilizador final.

### <a name="using-the-oauth-20-pop-up"></a>Usando o pop-up OAuth 2.0
A sua aplicação pode desencadear um pop-up de autorização OAuth 2.0, no qual o utilizador final pode introduzir as suas credenciais. Este pop-up também funciona com o processo Azure AD Dois fatores autenticação (2FA), se necessário.

> [!NOTE]
> Este método ainda não está suportado na Biblioteca de Autenticação AD Azure (ADAL) para Python ou Java.
>
>

### <a name="directly-passing-in-user-credentials"></a>Passagem direta nas credenciais dos utilizadores
A sua aplicação pode fornecer diretamente credenciais de utilizador ao Azure AD. Este método funciona apenas com contas de utilizadores de ID organizacionais; não é compatível com contas de utilizador pessoais /"live ID", incluindo as contas que terminam em @outlook.com ou @live.com . Além disso, este método não é compatível com contas de utilizador que requerem autenticação de dois fatores Azure AD (2FA).

### <a name="what-do-i-need-for-this-approach"></a>O que preciso para esta abordagem?
* Nome de domínio AD Azure. Esta exigência já está enumerada no pré-requisito deste artigo.
* Identificação do inquilino da AZure. Esta exigência já está enumerada no pré-requisito deste artigo.
* **Aplicação nativa** AZURE AD
* ID de aplicação para a aplicação nativa AZURE AD
* Redirecionar URI para a aplicação nativa AZURE AD
* Definir permissões delegadas


## <a name="step-1-create-an-active-directory-native-application"></a>Passo 1: Criar uma aplicação nativa do Diretório Ativo

Crie e configuure uma aplicação nativa AZURE AD para autenticação de utilizador final com data lake storage gen1 usando O Diretório Ativo Azure. Para obter instruções, consulte [Criar uma aplicação AD Azure](../active-directory/develop/howto-create-service-principal-portal.md).

Ao seguir as instruções no link, certifique-se de que seleciona **Native** para o tipo de aplicação, como mostrado na imagem seguinte:

![Criar aplicação Web](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Criar aplicativo nativo")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Passo 2: Obter ID de aplicação e redirecionar URI

Consulte [obter o ID da aplicação](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) para recuperar o ID da aplicação.

Para recuperar o URI de redirecionamento, faça os seguintes passos.

1. A partir do portal Azure, selecione **Azure Ative Directory**, clique nas **inscrições da App** e, em seguida, encontre e clique na aplicação nativa AD AZure que criou.

2. A partir da lâmina **Definições** para a aplicação, clique em **Redirecionar URIs**.

    ![Obter Redirecionamento URI](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Copie o valor apresentado.


## <a name="step-3-set-permissions"></a>Passo 3: Definir permissões

1. A partir do portal Azure, selecione **Azure Ative Directory**, clique nas **inscrições da App** e, em seguida, encontre e clique na aplicação nativa AD AZure que criou.

2. A partir da lâmina **Definições** para a aplicação, clique nas **permissões necessárias** e, em seguida, clique em **Adicionar**.

    ![Screenshot da lâmina de Definições com a opção Redirecionamento U R I chamada e a lâmina de redirecionamento U R I com o verdadeiro U R que chamei.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Na lâmina **add API Access,** clique em **Selecionar uma API,** clique em **Azure Data Lake** e, em seguida, clique em **Select**.

    ![Screenshot da lâmina de acesso Add API com a opção Select aPI chamada e a lâmina API Select com a opção Azure Data Lake e a opção Select chamada.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

4.  Na lâmina **add API Access,** clique em **Selecionar permissões,** selecione a caixa de verificação para dar **acesso total à Data Lake Store** e, em seguida, clique em **Select**.

    ![Screenshot da lâmina de acesso Add API com a opção Desempção Select chamada e a lâmina De Acesso Ativa com a ter acesso total à opção de serviço Azure Data Lake e a opção Select chamada.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Clique em **Concluído**.

5. Repita os dois últimos passos para conceder permissões para **a API de Gestão de Serviços windows Azure.**

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou uma aplicação nativa AZure AD e recolheu as informações de que necessita nas aplicações do seu cliente que você autoriza usando .NET SDK, Java SDK, REST API, etc. Pode agora proceder aos seguintes artigos que falam sobre como usar a aplicação web AZure AD para primeiro autenticar com data lake storage gen1 e, em seguida, realizar outras operações na loja.

* [Autenticação final do utilizador com data lake storage gen1 usando Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Autenticação de utilizador final com data lake storage gen1 usando .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Autenticação de utilizador final com data lake storage gen1 usando Python](data-lake-store-end-user-authenticate-python.md)
* [Autenticação de utilizador final com Data Lake Storage Gen1 usando REST API](data-lake-store-end-user-authenticate-rest-api.md)
---
title: Autenticação do utilizador final - Data Lake Storage Gen1 com Azure AD
description: Saiba como conseguir a autenticação de utilizador final com o Azure Data Lake Storage Gen1 utilizando o Diretório Ativo Azure
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: 5a0c3e1df5cd283ad08f905ed0bd4f329dcfcc7e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82688241"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticação de utilizador final com Azure Data Lake Storage Gen1 usando o Diretório Ativo Azure
> [!div class="op_single_selector"]
> * [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
>
>

O Azure Data Lake Storage Gen1 utiliza o Diretório Ativo Azure para autenticação. Antes de ser autor de uma aplicação que trabalhe com data Lake Storage Gen1 ou Azure Data Lake Analytics, deve decidir como autenticar a sua aplicação com o Azure Ative Directory (Azure AD). As duas principais opções disponíveis são:

* Autenticação do utilizador final (este artigo)
* Autenticação serviço-a-serviço (escolha esta opção a partir do drop-down acima)

Ambas as opções resultam na sua aplicação ser fornecida com um token OAuth 2.0, que fica anexado a cada pedido feito ao Data Lake Storage Gen1 ou Azure Data Lake Analytics.

Este artigo fala sobre como criar uma **aplicação nativa azure AD para autenticação de utilizador final.** Para obter instruções sobre a configuração da aplicação Azure AD para autenticação serviço-a-serviço, consulte [a autenticação serviço-a-serviço com data Lake Storage Gen1 utilizando o Diretório Ativo Azure](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* O seu ID de subscrição. Pode recuperá-lo do portal Azure. Por exemplo, está disponível a partir da lâmina da conta Data Lake Storage Gen1.

    ![Obtenha ID de subscrição](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* O seu nome de domínio Azure AD. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. A partir da imagem abaixo, o nome de domínio é **contoso.onmicrosoft.com**, e o GUID dentro dos suportes é o ID do inquilino.

    ![Obter domínio AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Sua identidade de inquilino Azure. Para obter instruções sobre como recuperar a identificação do inquilino, consulte [a identificação do inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="end-user-authentication"></a>Autenticação de utilizador final
Este mecanismo de autenticação é a abordagem recomendada se pretender que um utilizador final instifique a sua aplicação via Azure AD. A sua aplicação pode então aceder aos recursos do Azure com o mesmo nível de acesso que o utilizador final que fez login. O utilizador final precisa de fornecer as suas credenciais periodicamente para que a sua aplicação mantenha o acesso.

O resultado de ter o registo do utilizador final é que a sua aplicação recebe um token de acesso e um token de atualização. O token de acesso é anexado a cada pedido feito ao Data Lake Storage Gen1 ou Data Lake Analytics, e é válido por uma hora por padrão. O token de atualização pode ser usado para obter um novo token de acesso, e é válido por até duas semanas por padrão. Pode utilizar duas abordagens diferentes para iniciar sessão no utilizador final.

### <a name="using-the-oauth-20-pop-up"></a>Usando o pop-up OAuth 2.0
A sua aplicação pode desencadear um pop-up de autorização OAuth 2.0, no qual o utilizador final pode introduzir as suas credenciais. Este pop-up também funciona com o processo de autenticação de dois fatores Azure AD (2FA), se necessário.

> [!NOTE]
> Este método ainda não é suportado na Biblioteca de Autenticação AD Azure (ADAL) para Python ou Java.
>
>

### <a name="directly-passing-in-user-credentials"></a>Passando diretamente nas credenciais dos utilizadores
A sua aplicação pode fornecer diretamente credenciais de utilizador à Azure AD. Este método funciona apenas com contas de utilizadores de ID organizacionais; não é compatível com contas pessoais /"ID" de utilizadores, @outlook.com @live.comincluindo as contas que terminam em ou . Além disso, este método não é compatível com contas de utilizador que requerem autenticação de dois fatores Azure AD (2FA).

### <a name="what-do-i-need-for-this-approach"></a>O que preciso para esta abordagem?
* Nome de domínio Azure AD. Esta exigência já está listada no pré-requisito deste artigo.
* Identidade de inquilino da AD Azure. Esta exigência já está listada no pré-requisito deste artigo.
* **Aplicação nativa** azure AD
* ID de aplicação para a aplicação nativa Azure AD
* Redirecione o URI para a aplicação nativa da AD Azure
* Definir permissões delegadas


## <a name="step-1-create-an-active-directory-native-application"></a>Passo 1: Criar uma aplicação nativa de Diretório Ativo

Crie e configure uma aplicação nativa azure AD para autenticação de utilizador final com Data Lake Storage Gen1 usando o Diretório Ativo Azure. Para obter instruções, consulte [Criar uma aplicação Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Ao seguir as instruções no link, certifique-se de que seleciona **Nativo** para o tipo de aplicação, como mostra a seguinte imagem:

![Criar aplicação Web](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Criar app nativa")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Passo 2: Obter ID da aplicação e redirecionar URI

Consulte [o ID da aplicação](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in) para recuperar o ID da aplicação.

Para recuperar o URI redirecionamento, faça os seguintes passos.

1. A partir do portal Azure, selecione **Azure Ative Directory,** clique em registos de **Aplicações**e, em seguida, encontre e clique na aplicação nativa Azure AD que criou.

2. A partir da lâmina **Definições** para a aplicação, clique em **Redirecionar URIs**.

    ![Obter Redirecionamento URI](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Copie o valor apresentado.


## <a name="step-3-set-permissions"></a>Passo 3: Definir permissões

1. A partir do portal Azure, selecione **Azure Ative Directory,** clique em registos de **Aplicações**e, em seguida, encontre e clique na aplicação nativa Azure AD que criou.

2. A partir da lâmina **Definições** para a aplicação, clique em **permissões necessárias,** e depois clique em **Adicionar**.

    ![ID de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Na lâmina **de acesso Add API,** clique **em Selecione um API,** clique em **Azure Data Lake,** e, em seguida, clique em **Selecionar**.

    ![ID de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

4.  Na lâmina **de acesso Add API,** clique em **Selecionar permissões,** selecione a caixa de verificação para dar **acesso total à Data Lake Store**, e depois clique em **Selecionar**.

    ![ID de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Clique em **Concluído**.

5. Repita os dois últimos passos para conceder permissões para a API de Gestão de **Serviços Windows Azure** também.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou uma aplicação nativa azure AD e recolheu a informação de que necessita nas aplicações do seu cliente que é autor usando .NET SDK, Java SDK, REST API, etc. Pode agora proceder aos seguintes artigos que falam sobre como usar a aplicação web Azure AD para autenticar primeiro com data Lake Storage Gen1 e, em seguida, realizar outras operações na loja.

* [Autenticação final do utilizador com Data Lake Storage Gen1 usando Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Autenticação de utilizador final com Data Lake Storage Gen1 usando .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Autenticação de utilizador final com Data Lake Storage Gen1 usando Python](data-lake-store-end-user-authenticate-python.md)
* [Autenticação de utilizador final com Data Lake Storage Gen1 usando rest API](data-lake-store-end-user-authenticate-rest-api.md)

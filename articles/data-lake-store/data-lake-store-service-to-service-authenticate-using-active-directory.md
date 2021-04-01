---
title: Autenticação de serviço-a-serviço - Data Lake Storage Gen1 - Azure
description: Saiba como obter a autenticação de serviço-a-serviço com a Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 9dc195f98310e63cbde06885effe86ea3c239249
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91576103"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1 utilizando diretório ativo Azure
> [!div class="op_single_selector"]
> * [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 utiliza o Azure Ative Directory para autenticação. Antes de autorizar uma aplicação que funcione com a Data Lake Storage Gen1, deve decidir como autenticar a sua aplicação com o Azure Ative Directory (Azure AD). As duas principais opções disponíveis são:

* Autenticação de utilizador final 
* Autenticação de serviço-a-serviço (este artigo) 

Ambas as opções resultam em que a sua aplicação seja fornecida com um token OAuth 2.0, que é anexado a cada pedido feito à Data Lake Storage Gen1.

Este artigo fala sobre como criar uma **aplicação web AZure AD para autenticação de serviço-a-serviço**. Para obter instruções sobre a configuração da aplicação AD Azure para a autenticação do utilizador final, consulte [a autenticação do utilizador final com a Data Lake Storage Gen1 utilizando o Azure Ative Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Passo 1: Criar uma aplicação web ative Directory

Crie e configuure uma aplicação web Azure AD para autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1 usando O Diretório Ativo Azure. Para obter instruções, consulte [Criar uma aplicação AD Azure](../active-directory/develop/howto-create-service-principal-portal.md).

Ao seguir as instruções no link anterior, certifique-se de que seleciona **Web App / API** para o tipo de aplicação, como mostra a seguinte imagem:

![Criar aplicação Web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Criar aplicação Web")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Passo 2: Obtenha iD de aplicação, chave de autenticação e ID do inquilino
Ao iniciar sessão programática, precisa do ID para a sua aplicação. Se a aplicação for executado com as suas próprias credenciais, também precisa de uma chave de autenticação.

* Para obter instruções sobre como recuperar o ID da aplicação e a chave de autenticação (também chamada de segredo do cliente) para a sua aplicação, consulte [obter identificação de aplicação e chave de autenticação](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

* Para obter instruções sobre como recuperar a identificação do inquilino, consulte [a identificação do inquilino.](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Passo 3: Atribuir o pedido AD Azure ao ficheiro ou pasta da conta Azure Data Lake Storage Gen1


1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a conta De armazenamento de dados Gen1 que pretende associar à aplicação Azure Ative Directory que criou anteriormente.
2. Na sua lâmina de conta Gen1 de armazenamento de data lake, clique em **Data Explorer**.
   
    ![Criar diretórios na conta Desarmas de Armazenamento de Dados Gen1](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Criar diretórios na conta do Data Lake")
3. Na lâmina Data **Explorer,** clique no ficheiro ou pasta para o qual pretende fornecer acesso à aplicação AD Azure e, em seguida, clique em **Access**. Para configurar o acesso a um ficheiro, tem de clicar em **Access** a partir da lâmina **de pré-visualização** de ficheiros.
   
    ![Definir ACLs no sistema de ficheiros Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Definir ACLs no sistema de ficheiros Data Lake")
4. A lâmina **Access** lista o acesso padrão e o acesso personalizado já atribuído à raiz. Clique no ícone **Adicionar** para adicionar ACLs de nível personalizado.
   
    ![Listar acesso padrão e personalizado](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Listar acesso padrão e personalizado")
5. Clique no ícone **Adicionar** para abrir a lâmina **de acesso personalizado.** Nesta lâmina, clique **em Select User ou Group**, e em seguida, em Select User ou **Group** blade, procure a aplicação Azure Ative Directory que criou anteriormente. Se tiver muitos grupos para pesquisar, use a caixa de texto na parte superior para filtrar o nome do grupo. Clique no grupo que pretende adicionar e, em seguida, clique em **Selecionar**.
   
    ![Adicionar um grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Adicionar um grupo")
6. Clique **em 'Selecionar Permissões',** selecione as permissões e se pretende atribuir as permissões como ACL predefinido, aceda a ACL ou ambas. Clique em **OK**.
   
    ![Screenshot da lâmina de acesso personalizado adicionar com a opção 'Selecionar permissões' chamada e a lâmina 'Selecionar permissões' com a opção OK chamada.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Atribuir permissões ao grupo")
   
    Para obter mais informações sobre permissões em Data Lake Storage Gen1 e ACLs predefinidos/acesso, consulte [o Controlo de Acesso na Data Lake Storage Gen1](data-lake-store-access-control.md).
7. Na lâmina **de acesso personalizado,** clique em **OK**. Os grupos recém-adicionados, com as permissões associadas, estão listados na lâmina **de acesso.**
   
    ![Screenshot da lâmina Access com o grupo recém-adicionado chamado na secção Acesso Personalizado.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Atribuir permissões ao grupo")

> [!NOTE]
> Se planeia restringir a sua aplicação Azure Ative Directory a uma pasta específica, também terá de dar essa mesma aplicação de diretório Azure Ative **Execute** permissão à raiz para permitir o acesso à criação de ficheiros através do .NET SDK.

> [!NOTE]
> Se quiser utilizar os SDKs para criar uma conta Gen1 de armazenamento de dados, tem de atribuir a aplicação web AD AD como uma função ao Grupo de Recursos em que cria a conta Desebejamento de Armazenamento de Dados Gen1.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Passo 4: Obtenha o ponto final simbólico OAuth 2.0 (apenas para aplicações baseadas em Java)

1. Inscreva-se no [portal Azure](https://portal.azure.com) e clique no Ative Directory a partir do painel esquerdo.

2. A partir do painel esquerdo, clique nas **inscrições da App.**

3. A partir do topo da lâmina de registos da App, clique em **Pontos finais.**

    ![Screenshot do Ative Directory com a opção de registos da App e a opção Endpoints chamada.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Ponto final simbólico OAuth")

4. Da lista de pontos finais, copie o ponto final simbólico OAuth 2.0.

    ![Screenshot da lâmina Endpoints com o ícone de cópia O AUTH 2 ponto O TOKEN ENDPOINT chamado.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Ponto final simbólico OAuth")   

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou uma aplicação web Azure AD e recolheu as informações de que necessita nas aplicações do seu cliente que você autoriza usando .NET SDK, Java, Python, REST API, etc. Pode agora proceder aos seguintes artigos que falam sobre como usar a aplicação nativa AZURE AD para primeiro autenticar com data lake storage gen1 e, em seguida, realizar outras operações na loja.

* [Autenticação de serviço-a-serviço com Data Lake Storage Gen1 usando Java](data-lake-store-service-to-service-authenticate-java.md)
* [Autenticação de serviço-a-serviço com Data Lake Storage Gen1 usando .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Autenticação de serviço-a-serviço com data lake storage gen1 usando Python](data-lake-store-service-to-service-authenticate-python.md)
* [Autenticação de serviço-a-serviço com Data Lake Storage Gen1 usando REST API](data-lake-store-service-to-service-authenticate-rest-api.md)



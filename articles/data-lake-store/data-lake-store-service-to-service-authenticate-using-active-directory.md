---
title: 'Autenticação de serviço a serviço: Azure Data Lake Storage Gen1 com Diretório Ativo Azure [ Microsoft Docs'
description: Saiba como obter a autenticação de serviço-a-serviço com o Azure Data Lake Storage Gen1 utilizando o Diretório Ativo Azure
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
ms.openlocfilehash: 3fbf2f2540e8f1ca84aad2759b9a1fc790e4065d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241362"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticação serviço-a-serviço com Azure Data Lake Storage Gen1 usando o Diretório Ativo Azure
> [!div class="op_single_selector"]
> * [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

O Azure Data Lake Storage Gen1 utiliza o Diretório Ativo Azure para autenticação. Antes de ser autor de uma aplicação que trabalhe com data lake storage Gen1, você deve decidir como autenticar a sua aplicação com o Azure Ative Directory (Azure AD). As duas principais opções disponíveis são:

* Autenticação de utilizador final 
* Autenticação serviço-a-serviço (este artigo) 

Ambas as opções resultam na sua aplicação ser fornecida com um token OAuth 2.0, que fica anexado a cada pedido feito ao Data Lake Storage Gen1.

Este artigo fala sobre como criar uma **aplicação web Azure AD para autenticação serviço-a-serviço**. Para obter instruções sobre a configuração da aplicação Azure AD para autenticação de utilizador final, consulte a [autenticação do utilizador final com data lake storage Gen1 utilizando o Diretório Ativo Azure](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Passo 1: Criar uma aplicação web de Diretório Ativo

Crie e configure uma aplicação web Azure AD para autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory. Para obter instruções, consulte [Criar uma aplicação Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Ao seguir as instruções no link anterior, certifique-se de selecionar a **Web App / API** para o tipo de aplicação, como mostra a seguinte imagem:

![Criar aplicação Web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Criar aplicação Web")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Passo 2: Obter ID de aplicação, chave de autenticação e ID do inquilino
Quando iniciar sessão programática, precisa do ID para a sua aplicação. Se a aplicação for de acordo com as suas próprias credenciais, também necessita de uma chave de autenticação.

* Para obter instruções sobre como recuperar o ID da aplicação e a chave de autenticação (também chamada de segredo do cliente) para a sua aplicação, consulte Obter id de [aplicação e chave de autenticação](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

* Para obter instruções sobre como recuperar a identificação do inquilino, consulte [A DI do inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Passo 3: Atribuir a aplicação Azure AD ao ficheiro ou pasta da conta De armazenamento de lagos Azure


1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a conta Data Lake Storage Gen1 que pretende associar à aplicação azure Ative Diretório que criou anteriormente.
2. Na sua lâmina de conta Data Lake Storage Gen1, clique no **Data Explorer**.
   
    ![Criar diretórios na conta Gen1 de Armazenamento de Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Criar diretórios na conta Data Lake")
3. Na lâmina **do Data Explorer,** clique no ficheiro ou na pasta para o qual pretende fornecer acesso à aplicação Azure AD e, em seguida, clique em **Access**. Para configurar o acesso a um ficheiro, tem de clicar no **Acesso** a partir da lâmina de **pré-visualização** do ficheiro.
   
    ![Definir ACLs no sistema de ficheiros Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Definir ACLs no sistema de ficheiros Data Lake")
4. A lâmina **de acesso** lista o acesso padrão e o acesso personalizado já atribuído à raiz. Clique no ícone **Adicionar** para adicionar ACLs de nível personalizado.
   
    ![Lista de acesso sintetizado e personalizado](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Lista de acesso sintetizado e personalizado")
5. Clique no ícone **Adicionar** para abrir a lâmina **de acesso personalizado.** Nesta lâmina, clique em **Selecionar utilizador ou grupo**, e depois em Selecionar user ou **group** blade, procure a aplicação de Diretório Ativo Azure que criou anteriormente. Se tiver muitos grupos para procurar, utilize a caixa de texto na parte superior para filtrar o nome do grupo. Clique no grupo que pretende adicionar e, em seguida, clique em **Selecionar**.
   
    ![Adicione um grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Adicione um grupo")
6. Clique em **Selecionar Permissões,** selecione as permissões e se pretende atribuir as permissões como ACL padrão, aceda a ACL ou ambas. Clique em **OK**.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Atribuir permissões para agrupar")
   
    Para obter mais informações sobre permissões em Data Lake Storage Gen1, e Atpre/Access ACLs, consulte [Control de Acesso em Data Lake Storage Gen1](data-lake-store-access-control.md).
7. Na lâmina **de acesso personalizado adicionar,** clique **em OK**. O grupo recém-adicionado, com as permissões associadas, está listado na lâmina **de Acesso.**
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Atribuir permissões para agrupar")

> [!NOTE]
> Se planeia restringir a sua aplicação de Diretório Ativo Azure a uma pasta específica, também terá de dar a essa mesma aplicação de diretório Azure Ative **executar** permissão para a raiz para permitir o acesso à criação de ficheiros através do .NET SDK.

> [!NOTE]
> Se pretender utilizar os SDKs para criar uma conta Data Lake Storage Gen1, deve atribuir a aplicação web Azure AD como uma função ao Grupo de Recursos em que cria a conta Data Lake Storage Gen1.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Passo 4: Obtenha o ponto final de 2.0 token OAuth (apenas para aplicações baseadas em Java)

1. Inscreva-se no [portal Azure](https://portal.azure.com) e clique em Diretório Ativo a partir do painel esquerdo.

2. A partir do painel esquerdo, clique nas **inscrições da App**.

3. A partir do topo da lâmina de registoda App, clique em **Pontos Finais**.

    ![Ponto final simbólico da OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Ponto final simbólico da OAuth")

4. Da lista de pontos finais, copie o ponto final do token OAuth 2.0.

    ![Ponto final simbólico da OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Ponto final simbólico da OAuth")   

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou uma aplicação web Azure AD e recolheu a informação de que necessita nas aplicações do seu cliente que é autor usando .NET SDK, Java, Python, REST API, etc. Pode agora proceder aos seguintes artigos que falam sobre como usar a aplicação nativa Azure AD para autenticar primeiro com data Lake Storage Gen1 e, em seguida, realizar outras operações na loja.

* [Autenticação serviço-a-serviço com Data Lake Storage Gen1 usando Java](data-lake-store-service-to-service-authenticate-java.md)
* [Autenticação serviço-a-serviço com Data Lake Storage Gen1 usando .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Autenticação serviço-a-serviço com Data Lake Storage Gen1 usando Python](data-lake-store-service-to-service-authenticate-python.md)
* [Autenticação serviço-a-serviço com Data Lake Storage Gen1 usando REST API](data-lake-store-service-to-service-authenticate-rest-api.md)



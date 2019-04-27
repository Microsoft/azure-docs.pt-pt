---
title: 'Autenticação serviço a serviço: Geração 1 Lake armazenamento de dados do Azure com o Azure Active Directory | Documentos da Microsoft'
description: Saiba como conseguir a autenticação do serviço para serviço com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory
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
ms.openlocfilehash: a7fdcf396f586a65efa17e489d002f1c8847a193
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197011"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autenticação do serviço para serviço com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Geração de armazenamento 1 do Azure Data Lake utiliza o Azure Active Directory para autenticação. Antes de criar um aplicativo que funciona com o Data Lake Storage Gen1, deve decidir como autenticar a aplicação no Azure Active Directory (Azure AD). As duas opções principais disponíveis são:

* Autenticação de utilizador final 
* Autenticação serviço a serviço (Este artigo) 

Ambas estas opções resultam em seu aplicativo a ser fornecido com um token de OAuth 2.0, que é associado a cada pedido efetuado a geração 1 de armazenamento do Data Lake.

Este artigo fala sobre como criar uma **aplicação web do Azure AD para autenticação serviço a serviço**. Para obter instruções sobre configuração da aplicação do Azure AD para autenticação de utilizador final, consulte [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Passo 1: Criar uma aplicação web do Active Directory

Criar e configurar uma aplicação web do Azure AD para a autenticação do serviço para serviço com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory. Para obter instruções, consulte [criar uma aplicação do Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Ao seguir as instruções na ligação anterior, certifique-se de que seleciona **aplicação Web / API** de tipo de aplicação, conforme mostrado na captura de ecrã seguinte:

![Criar aplicação web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Criar aplicação web")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Passo 2: Obter ID da aplicação, a chave de autenticação e o ID do inquilino
Quando iniciar sessão programaticamente, precisa do ID para a sua aplicação. Se o aplicativo for executado sob suas próprias credenciais, também precisa de uma chave de autenticação.

* Para obter instruções sobre como obter a chave de autenticação e o ID de aplicação (também denominada o segredo do cliente) para a sua aplicação, consulte [chave de ID e a autenticação da aplicação Get](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Para obter instruções sobre como obter o ID de inquilino, consulte [obter ID de inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Passo 3: Atribuir a aplicação do Azure AD para a pasta ou ficheiro de conta de geração 1 de armazenamento do Azure Data Lake


1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a conta de geração 1 do Data Lake armazenamento que pretende associar à aplicação do Azure Active Directory que criou anteriormente.
2. No painel da conta do Data Lake Storage Gen1, clique em **Data Explorer**.
   
    ![Criar os diretórios numa conta do Data Lake Storage Gen1](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "criar diretórios numa conta do Data Lake")
3. Na **Data Explorer** painel, clique no ficheiro ou pasta para o qual pretende fornecer acesso à aplicação do Azure AD e, em seguida, clique em **acesso**. Para configurar o acesso a um ficheiro, tem de clicar em **acesso** partir do **pré-visualização do ficheiro** painel.
   
    ![Definir ACLs no sistema de ficheiros do Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "definir ACLs no sistema de ficheiros do Data Lake")
4. O **acesso** painel lista o acesso padrão e personalizada de acesso já atribuído para a raiz. Clique nas **adicionar** ícone para adicionar ACLs de nível personalizado.
   
    ![Lista de acesso padrão e personalizado](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "lista acesso padrão e personalizado")
5. Clique nas **Add** ícone para abrir o **adicionar acesso de personalizado** painel. Neste painel, clique em **selecionar utilizador ou grupo**e, em seguida, no **selecionar utilizador ou grupo** painel, procure a aplicação do Azure Active Directory que criou anteriormente. Se tiver muitos grupos a pesquisa, utilize a caixa de texto na parte superior para filtrar no nome do grupo. Clique no grupo que pretende adicionar e, em seguida, clique em **selecione**.
   
    ![Adicionar um grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "adicionar um grupo")
6. Clique em **selecionar permissões**, selecione as permissões e se pretende atribuir as permissões como uma ACL predefinida, aceder a ACL, ou ambos. Clique em **OK**.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "atribuir permissões ao grupo")
   
    Para obter mais informações sobre as permissões no Data Lake Storage Gen1 e ACLs de acesso/predefinido, consulte [controlo de acesso no Data Lake Storage Gen1](data-lake-store-access-control.md).
7. Na **adicionar acesso de personalizado** painel, clique em **OK**. O grupo recentemente adicionado, com as permissões associadas, estão listados na **acesso** painel.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "atribuir permissões ao grupo")

> [!NOTE]
> Se pretender restringir a sua aplicação do Azure Active Directory para uma pasta específica, também terá de fornecer esse mesmo aplicativo de diretório do Azure Active **Execute** permissão para a raiz para ativar o acesso de criação do ficheiro através do .NET SDK.

> [!NOTE]
> Se pretender utilizar os SDKs para criar uma conta do Data Lake Storage Gen1, tem de atribuir a aplicação web do Azure AD como uma função para o grupo de recursos que vai criar a conta de geração 1 de armazenamento do Data Lake.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Passo 4: Obter o ponto de final de token de OAuth 2.0 (apenas para aplicações baseadas em Java)

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e clique em Active Directory, no painel à esquerda.

2. No painel esquerdo, clique em **registos das aplicações**.

3. Na parte superior do painel de registos de aplicação, clique em **pontos de extremidade**.

    ![Ponto final de token de OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "ponto final de token de OAuth")

4. Na lista de pontos de extremidade, copie o ponto de final de token de OAuth 2.0.

    ![Ponto final de token de OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "ponto final de token de OAuth")   

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, criou uma aplicação web do Azure AD e reunidas as informações que necessárias em seus aplicativos de cliente que criar com o SDK de .NET, Java, Python, REST API, etc. Agora, pode avançar para os seguintes artigos que falam sobre como utilizar a aplicação nativa do Azure AD para autenticar primeiro Gen1 de armazenamento do Data Lake e, em seguida, executar outras operações na loja.

* [Autenticação do serviço para serviço com geração 1 de armazenamento do Data Lake com Java](data-lake-store-service-to-service-authenticate-java.md)
* [Autenticação do serviço para serviço com geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Autenticação do serviço para serviço com geração 1 de armazenamento do Data Lake com o Python](data-lake-store-service-to-service-authenticate-python.md)
* [Autenticação do serviço para serviço com a geração 1 de armazenamento do Data Lake com a REST API](data-lake-store-service-to-service-authenticate-rest-api.md)



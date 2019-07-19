---
title: Usar a autenticação do Azure AD para acessar a API dos serviços de mídia do Azure com REST | Microsoft Docs
description: Saiba como acessar a API dos serviços de mídia do Azure com a autenticação do Azure Active Directory usando o REST.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: 66c69552157df957e572a3af092131a3b7e560d5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871694"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Usar a autenticação do Azure AD para acessar a API dos serviços de mídia com REST

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Quando você estiver usando a autenticação do Azure AD com os serviços de mídia do Azure, poderá autenticar de uma destas duas maneiras:

- A **autenticação de usuário** autentica uma pessoa que está usando o aplicativo para interagir com os recursos dos serviços de mídia do Azure. O aplicativo interativo deve primeiro solicitar as credenciais ao usuário. Um exemplo é um aplicativo de console de gerenciamento que é usado por usuários autorizados para monitorar trabalhos de codificação ou transmissão ao vivo. 
- A **autenticação da entidade de serviço** autentica um serviço. Os aplicativos que normalmente usam esse método de autenticação são aplicativos que executam serviços daemon, serviços de camada intermediária ou trabalhos agendados, como aplicativos Web, aplicativos de funções, aplicativos lógicos, APIs ou microservices.

    Este tutorial mostra como usar a autenticação de **entidade de serviço** do Azure ad para acessar a API do AMS com REST. 

    > [!NOTE]
    > A **entidade de serviço** é a prática recomendada para a maioria dos aplicativos que se conectam aos serviços de mídia do Azure. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Obter as informações de autenticação do portal do Azure
> * Obter o token de acesso usando o postmaster
> * Testar a API de **ativos** usando o token de acesso


> [!IMPORTANT]
> Atualmente, os serviços de mídia oferecem suporte ao modelo de autenticação dos serviços de controle de acesso do Azure. No entanto, a autenticação de controle de acesso será preterida em 1º de junho de 2018. Recomendamos que migre para o modelo de autenticação do Azure AD assim que for possível.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Crie uma conta dos serviços de mídia do Azure usando o portal do Azure](media-services-portal-create-account.md).
- Examine o artigo [visão geral de autenticação dos serviços de mídia do Azure com a Azure ad Authentication](media-services-use-aad-auth-to-access-ams-api.md) .
- Instale o cliente REST do [postmaster](https://www.getpostman.com/) para executar as APIs REST mostradas neste artigo. 

    Neste tutorial, estamos usando o **postmaster** , mas qualquer ferramenta REST seria adequada. Outras alternativas: **Visual Studio Code** com o plug-in REST ou o **Fiddler Telerik**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Obter as informações de autenticação do portal do Azure

### <a name="overview"></a>Descrição geral

Para acessar a API dos serviços de mídia, você precisa coletar os seguintes pontos de dados.

|Definição|Exemplo|Descrição|
|---|-------|-----|
|Azure Active Directory domínio de locatário|microsoft.onmicrosoft.com|O Azure AD como um ponto de extremidade do STS (serviço de token seguro) é criado <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>usando o seguinte formato:. O Azure AD emite um JWT para acessar recursos (um token de acesso).|
|Ponto de extremidade da API REST|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Esse é o ponto de extremidade em relação ao qual todas as chamadas à API REST dos serviços de mídia em seu aplicativo são feitas.|
|ID do cliente (ID do aplicativo)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|ID do aplicativo (cliente) do Azure AD. A ID do cliente é necessária para obter o token de acesso. |
|Segredo do Cliente|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Chaves de aplicativo do Azure AD (segredo do cliente). O segredo do cliente é necessário para obter o token de acesso.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Obter informações de autenticação do AAD do portal do Azure

Para obter as informações, siga estas etapas:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue até a instância do AMS.
3. Selecione **acesso à API**.
4. Clique em **conectar à API dos serviços de mídia do Azure com a entidade de serviço**.

    ![Acesso à API](./media/connect-with-rest/connect-with-rest01.png)

5. Selecione um **aplicativo do Azure ad** existente ou crie um novo (mostrado abaixo).

    > [!NOTE]
    > Para que a solicitação REST de mídia do Azure seja concluída com sucesso, o usuário de chamada deve ter uma função de **colaborador** ou **proprietário** para a conta dos serviços de mídia que está tentando acessar. Se você receber uma exceção dizendo "o servidor remoto retornou um erro: (401) não autorizado, "consulte [controle de acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Se você precisar criar um novo aplicativo do AD, siga estas etapas:
    
   1. Pressione **criar novo**.
   2. Insira um nome.
   3. Pressione **criar novo** novamente.
   4. Prima **Guardar**.

      ![Acesso à API](./media/connect-with-rest/new-app.png)

      O novo aplicativo aparece na página.

6. Obtenha a **ID do cliente** (ID do aplicativo).
    
   1. Selecione o aplicativo.
   2. Obtenha a **ID do cliente** na janela à direita. 

      ![Acesso à API](./media/connect-with-rest/existing-client-id.png)

7. Obter a **chave** do aplicativo (segredo do cliente). 

   1. Clique no botão **gerenciar aplicativo** (Observe que as informações de ID do cliente estão sob a **ID do aplicativo**). 
   2. Pressione **as teclas**.
    
       ![Acesso à API](./media/connect-with-rest/manage-app.png)
   3. Gere a chave do aplicativo (segredo do cliente) preenchendo **Descrição** e **expira** e pressionando **salvar**.
    
       Depois que o botão **salvar** for pressionado, o valor da chave será exibido. Copie o valor da chave antes de sair da folha.

   ![Acesso à API](./media/connect-with-rest/connect-with-rest03.png)

Você pode adicionar valores para parâmetros de conexão do AD ao seu arquivo Web. config ou app. config, para uso posterior em seu código.

> [!IMPORTANT]
> A **chave do cliente** é um segredo importante e deve ser protegida corretamente em um cofre de chaves ou criptografada em produção.

## <a name="get-the-access-token-using-postman"></a>Obter o token de acesso usando o postmaster

Esta seção mostra como usar o **postmaster** para executar uma API REST que retorna um token de portador JWT (token de acesso). Para chamar qualquer API REST dos serviços de mídia, você precisa adicionar o cabeçalho "Authorization" às chamadas e adicionar o valor de "portador *your_access_token*" a cada chamada (conforme mostrado na próxima seção deste tutorial). 

1. Abrir o **postmaster**.
2. Selecione **PUBLICAR**.
3. Insira a URL que inclui o nome do locatário usando o seguinte formato: o nome do locatário deve terminar com **. onmicrosoft.com** e a URL deve terminar com **oauth2/token**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Selecione a guia **cabeçalhos** .
5. Insira as informações de **cabeçalhos** usando a grade de dados "chave/valor". 

    ![Grade de dados](./media/connect-with-rest/headers-data-grid.png)

    Como alternativa, clique no link de **edição em massa** à direita da janela do postmaster e cole o código a seguir.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Pressione a guia **corpo** .
7. Insira as informações de corpo usando a grade de dados "chave/valor" (substitua os valores de ID do cliente e segredo). 

    ![Grade de dados](./media/connect-with-rest/data-grid.png)

    Como alternativa, clique em **edição em massa** à direita da janela do postmaster e cole o corpo a seguir (substitua os valores de ID do cliente e segredo):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Prima **Enviar**.

    ![obter token](./media/connect-with-rest/connect-with-rest04.png)

A resposta retornada contém o **token de acesso** que você precisa usar para acessar as APIs do AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Testar a API de **ativos** usando o token de acesso

Esta seção mostra como acessar a API de **ativos** usando o **postmaster**.

1. Abrir o **postmaster**.
2. Selecione **OBTER**.
3. Cole o ponto de extremidade da API REST (por exemplo, https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Selecione a guia **autorização** . 
5. Selecione o **token**de portador.
6. Cole o token que foi criado na seção anterior.

    ![obter token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > O UX do postmaster pode ser diferente entre um Mac e um PC. Se a versão Mac não tiver a opção "token de portador" na lista suspensa da seção de **autenticação** , você deverá adicionar o cabeçalho de **autorização** manualmente no cliente Mac.

   ![Cabeçalho de autenticação](./media/connect-with-rest/auth-header.png)

7. Selecione **cabeçalhos**.
5. Clique no link **Editar em massa** à direita da janela do postmaster.
6. Cole os seguintes cabeçalhos:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Prima **Enviar**.

A resposta retornada contém os ativos que estão em sua conta.

## <a name="next-steps"></a>Passos Seguintes

* Experimente este código de exemplo [na autenticação do Azure ad para acesso aos serviços de mídia do Azure: Ambos via API REST](https://github.com/willzhan/WAMSRESTSoln)
* [Carregar arquivos com o .NET](media-services-dotnet-upload-files.md)

---
title: Autenticação de utilização do Azure AD para aceder à API de serviços de multimédia do Azure com REST | Documentos da Microsoft
description: Saiba como aceder à API de serviços de multimédia do Azure com a autenticação do Azure Active Directory utilizando o REST.
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
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: 4b6bd97d7e87832f774f7a09f7e0deeb4047e695
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294472"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Utilizar autenticação do Azure AD para aceder à API de serviços de multimédia com REST

Quando estiver usando autenticação do Azure AD com os serviços de multimédia do Azure, pode autenticar-se de uma de duas formas:

- **Autenticação de utilizador** efetua a autenticação de uma pessoa que está a utilizar a aplicação para interagir com os recursos de serviços de multimédia do Azure. O aplicativo interativo do primeiro deve pedir ao utilizador as credenciais. Um exemplo é uma aplicação de consola de gestão que é utilizada por utilizadores autorizados para monitorizar tarefas de codificação ou transmissão em direto. 
- **Autenticação do principal de serviço** autentica um serviço. Aplicativos geralmente usam este método de autenticação são as aplicações que executem serviços de daemon, serviços de camada intermediária ou tarefas agendadas, como aplicações web, aplicações de funções, aplicações lógicas, APIs ou microsserviços.

    Este tutorial mostra-lhe como utilizar o Azure AD **principal de serviço** autenticação para aceder à API AMS com REST. 

    > [!NOTE]
    > **Principal de serviço** é a prática recomendada para a maioria dos aplicativos que se ligam a serviços de suporte de dados do Azure. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Obter as informações de autenticação do portal do Azure
> * Obter o token de acesso com o Postman
> * Teste o **ativos** API com o token de acesso


> [!IMPORTANT]
> Atualmente, os Media Services suportam o modelo de autenticação de serviços de controlo de acesso do Azure. No entanto, vai ser preterida a autenticação de controlo de acesso de 1 de Junho de 2018. Recomendamos que migre para o modelo de autenticação do Azure AD assim que for possível.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Criar uma conta de Media Services do Azure com o portal do Azure](media-services-portal-create-account.md).
- Reveja os [aceder ao suporte de dados de API dos serviços Azure com a descrição geral da autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md) artigo.
- Instalar o [Postman](https://www.getpostman.com/) cliente REST para executar as APIs de REST mostrado neste artigo. 

    Neste tutorial, utilizamos **Postman** mas qualquer ferramenta REST seria adequada. Outras alternativas: **Visual Studio Code** com o plug-in do REST ou **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Obter as informações de autenticação do portal do Azure

### <a name="overview"></a>Descrição geral

Para aceder à API de serviços de multimédia, terá de recolher os seguintes pontos de dados.

|Definição|Exemplo|Descrição|
|---|-------|-----|
|Domínio de inquilino do Azure Active Directory|microsoft.onmicrosoft.com|Azure AD como um ponto de extremidade de proteger o serviço de Token (STS) é criado usando o seguinte formato: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>. O Azure AD emite um JWT para poder aceder aos recursos (um token de acesso).|
|Ponto final de API REST|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Este é o ponto final em relação a quais todas as API de REST de serviços de multimédia são efetuadas chamadas em seu aplicativo.|
|ID de cliente (ID de aplicação)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|ID de aplicação (cliente) do Azure AD É necessário o ID de cliente para obter o token de acesso. |
|Segredo do Cliente|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Chaves de aplicação do Azure AD (segredo do cliente). O segredo do cliente é necessária para obter o token de acesso.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Obter as informações de autenticação do AAD no portal do Azure

Para obter as informações, siga estes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue até à sua instância de AMS.
3. Selecione **acesso à API**.
4. Clique em **ligar à API de serviços de multimédia do Azure com o principal de serviço**.

    ![Acesso à API](./media/connect-with-rest/connect-with-rest01.png)

5. Selecione um existente **aplicação do Azure AD** ou criar um novo (mostrado abaixo).

    > [!NOTE]
    > Para a solicitação de REST de suporte de dados do Azure com êxito, o utilizador chamador tem de ter uma **contribuinte** ou **proprietário** função para a conta de Media Services está a tentar aceder. Se receber uma exceção que diz "o servidor remoto devolveu um erro: Não autorizada (401),"ver [controlo de acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Se precisar de criar uma nova aplicação do AD, siga estes passos:
    
   1. Prima **criar um novo**.
   2. Introduza um nome.
   3. Prima **criar novo** novamente.
   4. Prima **Guardar**.

      ![Acesso à API](./media/connect-with-rest/new-app.png)

      A nova aplicação aparece na página.

6. Obter o **ID de cliente** (ID de aplicação).
    
   1. Selecione a aplicação.
   2. Obter o **ID de cliente** na janela à direita. 

      ![Acesso à API](./media/connect-with-rest/existing-client-id.png)

7. Obtenha o aplicativo **chave** (segredo do cliente). 

   1. Clique nas **Gerir aplicação** botão (tenha em atenção que as informações de ID de cliente estão sob **ID de aplicação**). 
   2. Prima **chaves**.
    
       ![Acesso à API](./media/connect-with-rest/manage-app.png)
   3. Gerar a chave da aplicação (segredo do cliente), preenchendo **Descrição** e **EXPIRES** e premindo **guardar**.
    
       Uma vez a **guardar** botão for pressionado, o valor da chave é apresentada. Copie o valor da chave antes de deixarem o painel.

   ![Acesso à API](./media/connect-with-rest/connect-with-rest03.png)

Pode adicionar valores para parâmetros de ligação do AD ao ficheiro Web. config ou App. config, para utilização posterior no seu código.

> [!IMPORTANT]
> O **chave de cliente** é um segredo importante e deve ser corretamente protegidos num cofre de chaves ou criptografados em produção.

## <a name="get-the-access-token-using-postman"></a>Obter o token de acesso com o Postman

Esta secção mostra como usar **Postman** para executar uma API REST que devolve um Token de portador do JWT (token de acesso). Para chamar qualquer API de REST de serviços de suporte de dados, terá de adicionar o cabeçalho "Authorization" para as chamadas e adicione o valor de "portador *your_access_token*" para cada chamada (como mostrado na secção seguinte deste tutorial). 

1. Open **Postman**.
2. Selecione **PUBLICAR**.
3. Introduza o URL que inclua o nome do inquilino com o seguinte formato: o nome do inquilino deve terminar com **. onmicrosoft.com** e o URL deve terminar com **oauth2/token**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Selecione o **cabeçalhos** separador.
5. Introduza o **cabeçalhos** informações usando a grade de dados de "Chave/valor". 

    ![Grade de dados](./media/connect-with-rest/headers-data-grid.png)

    Em alternativa, clique em **editar em massa** associar à direita da janela do Postman e cole o seguinte código.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Prima a **corpo** separador.
7. Introduza as informações de corpo usando a grade de dados de "Chave/valor" (substitua o ID de cliente e os valores secretos). 

    ![Grade de dados](./media/connect-with-rest/data-grid.png)

    Em alternativa, clique em **editar em massa** no lado direito da janela do Postman e cole o seguinte corpo (substitua o ID de cliente e os valores secretos):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Prima **Enviar**.

    ![obter o token](./media/connect-with-rest/connect-with-rest04.png)

A resposta devolvida contém os **token de acesso** que tem de utilizar para aceder a qualquer APIs do AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Teste o **ativos** API com o token de acesso

Esta secção mostra como acessar o **ativos** através da API **Postman**.

1. Open **Postman**.
2. Selecione **OBTER**.
3. Cole o ponto final de REST API (por exemplo, https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Selecione o **autorização** separador. 
5. Selecione **Token de portador**.
6. Cole o token que foi criado na secção anterior.

    ![obter o token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > A experiência do Usuário Postman podem ser diferente entre um Mac e o PC. Se a versão de Mac não tem a opção "Portador Token" **autenticação** menu pendente de seção, deve adicionar o **autorização** cabeçalho manualmente no cliente Mac.

   ![Cabeçalho de autenticação](./media/connect-with-rest/auth-header.png)

7. Selecione **cabeçalhos**.
5. Clique em **editar em massa** ligação no lado direito da janela do Postman.
6. Cole os seguintes cabeçalhos:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Prima **Enviar**.

A resposta devolvida contém os recursos que estão na sua conta.

## <a name="next-steps"></a>Passos Seguintes

* Experimente este código de exemplo no [autenticação do Azure AD para acesso de serviços de multimédia do Azure: Ambos através da REST API](https://github.com/willzhan/WAMSRESTSoln)
* [Carregar ficheiros com .NET](media-services-dotnet-upload-files.md)

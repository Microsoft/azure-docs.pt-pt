---
title: Utilize a autenticação Azure AD para aceder à API azure Media Services com REST Microsoft Docs
description: Saiba como aceder à API azure Media Services com autenticação de Diretório Ativo Azure utilizando o REST.
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
ms.openlocfilehash: a693eb374365670da3fe8c4b2bb8ce664a024217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295447"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Utilizar a autenticação do Azure AD para aceder à API de Serviços de Multimédia com REST

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Quando estiver a utilizar a autenticação Azure AD com a Azure Media Services, pode autenticar de uma de duas formas:

- **A autenticação** do utilizador autentica uma pessoa que está a usar a app para interagir com os recursos da Azure Media Services. A aplicação interativa deve primeiro solicitar ao utilizador credenciais. Um exemplo é uma aplicação de consola de gestão que é usada por utilizadores autorizados para monitorizar trabalhos de codificação ou streaming ao vivo. 
- **A autenticação do diretor** de serviço autentica um serviço. Aplicações que usam comumente este método de autenticação são aplicações que executam serviços de daemon, serviços de nível médio ou empregos programados, tais como aplicações web, aplicações de função, aplicações lógicas, APIs ou microserviços.

    Este tutorial mostra-lhe como utilizar a autenticação **principal do serviço** Azure AD para aceder à AMS API com REST. 

    > [!NOTE]
    > **O diretor de serviço** é a melhor prática recomendada para a maioria das aplicações que ligam aos Serviços De Mídia Azure. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Obtenha a informação de autenticação do portal Azure
> * Obtenha o sinal de acesso usando o Carteiro
> * Testar a API **de ativos** utilizando o sinal de acesso


> [!IMPORTANT]
> Atualmente, a Media Services suporta o modelo de autenticação de serviços de Controlo de Acesso Azure. No entanto, a autenticação do Controlo de Acesso será depreciada a 1 de junho de 2018. Recomendamos que migre para o modelo de autenticação do Azure AD assim que for possível.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Crie uma conta Azure Media Services utilizando o portal Azure.](media-services-portal-create-account.md)
- Consulte o Accessing Azure Media Services API com o artigo de [autenticação da AD Azure.](media-services-use-aad-auth-to-access-ams-api.md)
- Instale o cliente [DO REPOUSADO](https://www.getpostman.com/) para executar as APIs REST mostradas neste artigo. 

    Neste tutorial, estamos a usar **o Carteiro,** mas qualquer ferramenta REST seria adequada. Outras alternativas são: **Visual Studio Code** com o plugin REST ou **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Obtenha a informação de autenticação do portal Azure

### <a name="overview"></a>Descrição geral

Para aceder à API dos Serviços de Media, é necessário recolher os seguintes pontos de dados.

|Definição|Exemplo|Descrição|
|---|-------|-----|
|Domínio de inquilino de Diretório Ativo Azure|microsoft.onmicrosoft.com|O Azure AD como ponto final do Serviço De Token Seguro <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>(STS) é criado utilizando o seguinte formato: . A Azure AD emite um JWT para aceder a recursos (um sinal de acesso).|
|Ponto final da API REST|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Este é o ponto final contra o qual todas as chamadas da Media Services REST API na sua aplicação são feitas.|
|ID do cliente (ID da aplicação)|f7fbbb29-a02d-4d91-bbc6-59a25792|ID de aplicação Azure AD (cliente). A identificação do cliente é necessária para obter o sinal de acesso. |
|Segredo do Cliente|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Chaves de aplicação Azure AD (segredo do cliente). O segredo do cliente é necessário para obter o sinal de acesso.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Obtenha informações auth AAD do portal Azure

Para obter a informação, siga estes passos:

1. Faça login no [portal Azure.](https://portal.azure.com)
2. Navegue para a sua instância AMS.
3. Selecione **acesso API**.
4. Clique em **Connect to Azure Media Services API com o principal serviço**.

    ![Acesso a API](./media/connect-with-rest/connect-with-rest01.png)

5. Selecione uma **aplicação Azure AD** existente ou crie uma nova (mostrada abaixo).

    > [!NOTE]
    > Para que o pedido do Azure Media REST tenha sucesso, o utilizador de chamadas deve ter uma função **de Contribuinte** ou **Proprietário** para a conta media Services a que está a tentar aceder. Se tiver uma exceção que diga "O servidor remoto devolveu um erro: (401) Não autorizado", consulte [o controlo](media-services-use-aad-auth-to-access-ams-api.md#access-control)de acesso .

    Se precisar de criar uma nova app AD, siga estes passos:
    
   1. Pressione **Criar Novo**.
   2. Insira um nome.
   3. Pressione **Criar Novo** novamente.
   4. Prima **Guardar**.

      ![Acesso a API](./media/connect-with-rest/new-app.png)

      A nova aplicação aparece na página.

6. Obtenha o ID do **Cliente** (ID de aplicação).
    
   1. Selecione a aplicação.
   2. Pegue a **identificação** do cliente da janela à direita. 

      ![Acesso a API](./media/connect-with-rest/existing-client-id.png)

7. Obtenha a **Chave** da aplicação (segredo do cliente). 

   1. Clique no botão **'Gerir'** (note que a informação de ID do Cliente está em **ID da aplicação**). 
   2. **Teclas**de pressão.
    
       ![Acesso a API](./media/connect-with-rest/manage-app.png)
   3. Gere a chave da aplicação (segredo do cliente) preenchendo **descrição** e **EXPIRAe** e pressionando **Save**.
    
       Uma vez premido o botão **Guardar,** o valor-chave aparece. Copie o valor-chave antes de deixar a lâmina.

   ![Acesso a API](./media/connect-with-rest/connect-with-rest03.png)

Pode adicionar valores para parâmetros de ligação AD ao seu ficheiro web.config ou app.config, para posterior utilização no seu código.

> [!IMPORTANT]
> A **chave Cliente** é um segredo importante e deve ser devidamente protegida num cofre chave ou encriptada na produção.

## <a name="get-the-access-token-using-postman"></a>Obtenha o sinal de acesso usando o Carteiro

Esta secção mostra como usar o **Carteiro** para executar uma API REST que devolve um JWT Bearer Token (ficha de acesso). Para ligar para qualquer Media Services REST API, você precisa adicionar o cabeçalho "Autorização" às chamadas, e adicionar o valor de "Bearer *your_access_token"* a cada chamada (como mostrado na secção seguinte deste tutorial). 

1. Carteiro **Postman**Aberto.
2. Selecione **PUBLICAR**.
3. Introduza o URL que inclui o nome do seu inquilino utilizando o seguinte formato: o nome do inquilino deve terminar com **.onmicrosoft.com** e o URL deve terminar com **oauth2/token:** 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Selecione o separador **Cabeçalhos.**
5. Introduza as informações **dos Cabeçalhos** utilizando a grelha de dados "Chave/Valor". 

    ![Grelha de Dados](./media/connect-with-rest/headers-data-grid.png)

    Em alternativa, clique no link **'Edição a granel'** à direita da janela do Carteiro e cole o seguinte código.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Pressione a guia **body.**
7. Introduza as informações do corpo utilizando a grelha de dados "Chave/Valor" (substitua o ID do cliente e os valores secretos). 

    ![Grelha de Dados](./media/connect-with-rest/data-grid.png)

    Em alternativa, clique em **Edição a granel** à direita da janela do Carteiro e cole o seguinte corpo (substitua o ID do cliente e os valores secretos):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Prima **Enviar**.

    ![obter símbolo](./media/connect-with-rest/connect-with-rest04.png)

A resposta devolvida contém o **sinal de acesso** que precisa de usar para aceder a quaisquer APIs ams.

## <a name="test-the-assets-api-using-the-access-token"></a>Testar a API **de ativos** utilizando o sinal de acesso

Esta secção mostra como aceder à API **de ativos** utilizando **o Carteiro.**

1. Carteiro **Postman**Aberto.
2. Selecione **OBTER**.
3. Colar o ponto final da API REST (por exemplo,https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Selecione o separador **Autorização.** 
5. Selecione **Token bearer**.
6. Colar o símbolo que foi criado na secção anterior.

    ![obter símbolo](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > O Postman UX pode ser diferente entre um Mac e pc. Se a versão Mac não tiver a opção "Bearer Token" na secção de **autenticação,** deverá adicionar manualmente o cabeçalho **de Autorização** no cliente Mac.

   ![Cabeçalho de Auth](./media/connect-with-rest/auth-header.png)

7. Selecione **Cabeçalhos**.
5. Clique no link **De edição** a granel à direita da janela do Carteiro.
6. Colar os seguintes cabeçalhos:

        x-ms-version:2.19
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Prima **Enviar**.

A resposta devolvida contém os ativos que estão na sua conta.

## <a name="next-steps"></a>Passos seguintes

* Experimente este código de amostra na [Autenticação AD Azure para acesso aos serviços de mídia Azure: Tanto através da API REST](https://github.com/willzhan/WAMSRESTSoln)
* [Upload de ficheiros com .NET](media-services-dotnet-upload-files.md)

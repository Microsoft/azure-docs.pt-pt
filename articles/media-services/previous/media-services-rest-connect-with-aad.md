---
title: Utilize a autenticação Azure AD para aceder à Azure Media Services API com REST | Microsoft Docs
description: Saiba como aceder à Azure Media Services API com autenticação do Azure Ative Directory utilizando REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: a2b4e7bf03ebb1fbc197b78287cb50b3f421d713
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017312"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Utilizar a autenticação do Azure AD para aceder à API de Serviços de Multimédia com REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Quando estiver a utilizar a autenticação AD AZure com a Azure Media Services, pode autenticar de duas formas:

- **A autenticação do utilizador** autentica uma pessoa que está a usar a app para interagir com os recursos do Azure Media Services. A aplicação interativa deve primeiro solicitar ao utilizador credenciais. Um exemplo é uma aplicação de consola de gestão que é usada por utilizadores autorizados para monitorizar trabalhos de codificação ou streaming ao vivo. 
- **A autenticação principal do serviço** autentica um serviço. As aplicações que normalmente utilizam este método de autenticação são aplicações que executam serviços daemon, serviços de nível médio ou trabalhos programados, tais como aplicações web, apps de funções, apps lógicas, APIs ou microserviços.

    Este tutorial mostra-lhe como usar a autenticação **principal do serviço** Azure AD para aceder à AMS API com REST. 

    > [!NOTE]
    > **O diretor de** serviço é a melhor prática recomendada para a maioria das aplicações que ligam ao Azure Media Services. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Obtenha a informação de autenticação do portal Azure
> * Obtenha o token de acesso usando o Carteiro
> * Testar a API **dos Ativos** utilizando o token de acesso


> [!IMPORTANT]
> Atualmente, a Media Services suporta o modelo de autenticação de serviços Azure Access Control. No entanto, a autenticação do Controlo de Acesso será depreciada a 1 de junho de 2018. Recomendamos que migre para o modelo de autenticação do Azure AD assim que for possível.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Criar uma conta Azure Media Services utilizando o portal Azure](media-services-portal-create-account.md).
- Reveja a API dos Serviços de Media de Acesso com o artigo [de autenticação AD Azure.](media-services-use-aad-auth-to-access-ams-api.md)
- Instale o cliente [Postman](https://www.getpostman.com/) REST para executar as APIs REST mostradas neste artigo. 

    Neste tutorial, estamos a usar **o Carteiro,** mas qualquer ferramenta REST seria adequada. Outras alternativas são: **Código de Estúdio Visual** com o plugin REST ou o **Fiddler Telerik**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Obtenha a informação de autenticação do portal Azure

### <a name="overview"></a>Descrição geral

Para aceder à API dos Serviços de Mídia, é necessário recolher os seguintes pontos de dados.

|Definição|Exemplo|Descrição|
|---|-------|-----|
|Domínio do inquilino do Diretório Ativo Azure|microsoft.onmicrosoft.com|A Azure AD como um ponto final secure Token Service (STS) é criado usando o seguinte formato: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token> . A Azure AD emite um JWT para aceder a recursos (um token de acesso).|
|Ponto final da API REST|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Este é o ponto final contra o qual todos os serviços de mídia REST API chama na sua aplicação.|
|ID do cliente (ID de aplicação)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|ID de aplicação AD Azure (cliente). A identificação do cliente é necessária para obter o token de acesso. |
|Segredo do Cliente|+mUERiNzMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Chaves de aplicação AD AZure (segredo do cliente). O segredo do cliente é necessário para obter o token de acesso.|

### <a name="get-azure-active-directory-auth-info-from-the-azure-portal"></a>Obtenha informações do Azure Ative Directory auth do portal Azure

Para obter a informação, siga estes passos:

1. Faça login no [portal Azure](https://portal.azure.com).
2. Navegue para o seu caso AMS.
3. Selecione **acesso API**.
4. Clique em **Connect to Azure Media Services API com o principal serviço.**

    ![Screenshot que mostra "A P I access" selecionado a partir do menu "Media Services" e "Connect to Azure Media Services A P I with service principal" selecionado a partir do painel direito.](./media/connect-with-rest/connect-with-rest01.png)

5. Selecione uma **aplicação AD Azure** existente ou crie uma nova (mostrada abaixo).

    > [!NOTE]
    > Para que o pedido de REPOUSO Azure Media seja bem sucedido, o utilizador chamador deve ter uma função **de Contribuinte** ou **Proprietário** para a conta de Serviços de Comunicação social a que está a tentar aceder. Se receber uma exceção que diga "O servidor remoto devolveu um erro: (401) Não Autorizado", consulte [o controlo de acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Se precisar de criar uma nova aplicação AD, siga estes passos:
    
   1. Pressione **Criar Novo**.
   2. Insira um nome.
   3. Pressione **Criar Novo** novamente.
   4. Prima **Guardar**.

      ![Screenshot que mostra o diálogo "Criar Novo" com a caixa de texto "Criar app" realçada e o botão "Guardar" selecionado.](./media/connect-with-rest/new-app.png)

      A nova aplicação aparece na página.

6. Obtenha o **ID do cliente** (ID de aplicação).
    
   1. Selecione a inscrição.
   2. Pegue a **identificação** do cliente da janela à direita. 

      ![Screenshot que mostra a "app Azure A D" e "Gerir a aplicação" selecionada, e o "Cliente I D" realçado no painel certo.](./media/connect-with-rest/existing-client-id.png)

7. Obtenha a **Chave** do pedido (segredo do cliente). 

   1. Clique no botão **de aplicação Manage** (note que a informação de ID do Cliente está no **ID da aplicação).** 
   2. **Teclas de pressão**.
    
       ![Screenshot que mostra o botão "Gerir a aplicação" selecionado, o "Aplicação I D" no painel central realçado e "Keys" selecionado no painel direito.](./media/connect-with-rest/manage-app.png)
   3. Gere a chave de aplicação (segredo do cliente) preenchendo **a DESCRIÇÃO** e **EXPIRA** E **pressionando o Save.**
    
       Uma vez premido o botão **Guardar,** o valor da tecla aparece. Copie o valor da chave antes de sair da lâmina.

   ![Acesso a API](./media/connect-with-rest/connect-with-rest03.png)

Pode adicionar valores para parâmetros de ligação AD ao seu ficheiro web.config ou app.config, para posterior utilização no seu código.

> [!IMPORTANT]
> A **chave cliente** é um segredo importante e deve ser devidamente segura num cofre ou encriptada na produção.

## <a name="get-the-access-token-using-postman"></a>Obtenha o token de acesso usando o Carteiro

Esta secção mostra como usar **o Carteiro** para executar uma API REST que devolve um Token do Portador JWT (token de acesso). Para ligar para qualquer API dos Serviços de Mídia, é necessário adicionar o cabeçalho "Autorização" às chamadas e adicionar o valor de "Bearer *your_access_token"* a cada chamada (como mostrado na secção seguinte deste tutorial). 

1. **Carteiro aberto.**
2. Selecione **PUBLICAR**.
3. Introduza o URL que inclui o nome do seu inquilino utilizando o seguinte formato: o nome do inquilino deve terminar com **.onmicrosoft.com** e o URL deve terminar com **oauth2/token**: 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Selecione o **separador Cabeçalhos.**
5. Introduza as **informações dos Cabeçalhos** utilizando a grelha de dados "Chave/Valor". 

    ![Screenshot que mostra o separador "Cabeçalhos" e a ação "Bulk Edit" selecionada.](./media/connect-with-rest/headers-data-grid.png)

    Em alternativa, clique no link **de edição** a granel à direita da janela do Carteiro e cole o seguinte código.

    ```javascript
    Content-Type:application/x-www-form-urlencoded
    Keep-Alive:true
    ```

6. Pressione a aba **do Corpo.**
7. Introduza a informação do corpo utilizando a grelha de dados "Chave/Valor" (substitua o ID do cliente e os valores secretos). 

    ![Rede de dados](./media/connect-with-rest/data-grid.png)

    Em alternativa, clique em **Edição a granel** à direita da janela do Carteiro e cole o seguinte corpo (substitua o ID do cliente e os valores secretos):

    ```javascript
    grant_type:client_credentials
    client_id:{Your Client ID that you got from your Azure AD Application}
    client_secret:{Your client secret that you got from your Azure AD Application's Keys}
    resource:https://rest.media.azure.net
    ```

8. Prima **Enviar**.

    ![Screenshot que mostra a caixa de texto "Post", os separadores "Cabeçalhos" e "Corpo" e "access_token" realçados, e o botão "Enviar" detetado.](./media/connect-with-rest/connect-with-rest04.png)

A resposta devolvida contém o **sinal de acesso** que precisa de utilizar para aceder a quaisquer APIs ams.

## <a name="test-the-assets-api-using-the-access-token"></a>Testar a API **dos Ativos** utilizando o token de acesso

Esta secção mostra como aceder à API **de Ativos** utilizando **o Carteiro**.

1. **Carteiro aberto.**
2. Selecione **OBTER**.
3. Colar o ponto final da API REST (por exemplo, https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Selecione o **separador Autorização.** 
5. Selecione **o token do portador**.
6. Cole o símbolo que foi criado na secção anterior.

    ![obter símbolo](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > O Carteiro UX pode ser diferente entre um Mac e um PC. Se a versão Mac não tiver a opção "Token portador" na secção de **autenticação,** deve adicionar manualmente o cabeçalho **de Autorização** ao cliente Mac.

   ![Cabeçalho de Auth](./media/connect-with-rest/auth-header.png)

7. **Selecione Cabeçalhos**.
5. Clique no link **de edição** a granel na janela direita do Carteiro.
6. Cole os seguintes cabeçalhos:

    ```javascript
    x-ms-version:2.19
    Accept:application/json
    Content-Type:application/json
    DataServiceVersion:3.0
    MaxDataServiceVersion:3.0
    ```

7. Prima **Enviar**.

A resposta devolvida contém os ativos que estão na sua conta.

## <a name="next-steps"></a>Passos seguintes

* Experimente este código de amostra na [autenticação AD Azure para acesso a serviços de mídia Azure: Ambos via REST API](https://github.com/willzhan/WAMSRESTSoln)
* [Faça upload de ficheiros com .NET](media-services-dotnet-upload-files.md)

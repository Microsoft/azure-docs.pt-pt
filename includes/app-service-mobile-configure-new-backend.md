---
title: incluir ficheiro
description: incluir ficheiro
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 04/15/2020
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: b5b6389491fd91b716a05e7c57c98addb6352bd9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461853"
---
1. Descarregue os quickstarts sdK do cliente para as seguintes plataformas:
    
    [iOS (Objectivo-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Janelas (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Se utilizar o projeto iOS, precisa de descarregar "azuresdk-iOS-\*.zip" a partir do mais recente lançamento do [GitHub](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Desaperte `MicrosoftAzureMobile.framework` e adicione o ficheiro à raiz do projeto.
    >

2. Terá de adicionar uma ligação à base de dados ou ligar-se a uma ligação existente. Primeiro, determine se vai criar uma loja de dados ou usar uma existente.

    - **Criar uma nova loja**de dados : Se vai criar uma loja de dados, utilize o seguinte arranque rápido:

        [Quickstart: Começar com bases de dados únicas na Base de Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Fonte de dados existente**: Siga as instruções abaixo se pretender utilizar uma ligação de base de dados existente

        1. Formato de string de ligação à base de dados SQL -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** Nome do servidor, este pode ser encontrado na página geral da sua base de dados e é geralmente na forma de "server_name.database.windows.net".
            **{porta}** normalmente 1433.
            **{your_catalogue}** Nome da base de dados.
            **{your_username}** Nome do utilizador para aceder à sua base de dados.
            **{your_password}** Senha para aceder à sua base de dados.

            [Saiba mais sobre o formato SQL Connection String](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Adicione a cadeia de ligação à sua **aplicação móvel** No Serviço de Aplicações, pode gerir as cordas de ligação para a sua aplicação utilizando a opção **Configuração** no menu.

            Para adicionar uma cadeia de ligação:

            1. Clique no separador de definições de **Aplicação.**

            2. Clique em **[+] Nova cadeia**de ligação .

            3. Terá de fornecer **Nome,** **Valor** e **Tipo** para a sua cadeia de ligação.

            4. Tipo **nome** como`MS_TableConnectionString`

            5. O valor deve ser a corda de ligação que formou no passo anterior.

            6. Se estiver a adicionar uma cadeia de ligação a uma base de dados SQL Azure, escolha **o SQLAzure** sob o **tipo**.

3. As Aplicações Móveis Azure têm SDKs para backends .NET e Node.js.

   - **Node.js backend**
    
     Se vai utilizar a aplicação Node.js quickstart, siga as instruções abaixo.
     
        1. Crie uma nova API - Pode fazer alterações no portal Azure ou modificar o código localmente no seu ambiente de desenvolvimento e depois publicar no Azure. Clique no `App Service Editor (Preview)` `Development Tools` menu abaixo que fornece uma experiência de edição no navegador para o seu código de aplicações.
        
        2. Clique `Go` e assim que o Editor de Serviço de Aplicações abrir, tem controlo total sobre o código fonte. Assumindo que já instalou pacote de aplicações expressas e azure-mobile com comando de instalação npm, clique na pasta API em WWWROOT para criar ou editar API personalizada. Faça as alterações no ficheiro de código e as alterações são guardadas automaticamente.
        
        3. Tem controlo total sobre a Base de Dados Azure SQL utilizada para armazenar os dados da aplicação. Pode criar facilmente novas tabelas na base de dados.
 
   - **.NET backend**
    
        Se vai utilizar a aplicação .NET quickstart, siga as instruções abaixo.

        1. Descarregue o projeto de servidor azure mobile Apps .NET a partir do [repositório azure-mobile-apps-quickstarts](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Construa o projeto de servidor .NET localmente no Estúdio Visual.

        3. No Estúdio Visual, abra o Solution `ZUMOAPPNAMEService` Explorer, clique à direita `Publish to App Service` no projeto, clique em **Publicar,** verá uma janela. Se estiver a trabalhar no Mac, confira outras formas de implementar a aplicação [aqui](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Publicação de estúdio visual](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Selecione **o Serviço de Aplicações** como alvo de publicação e, em seguida, clique em **Selecionar Existindo**e, em seguida, clique no botão **Publicar** na parte inferior da janela.

        5. Terá de entrar no Visual Studio com a sua assinatura Azure primeiro. Selecione o `Subscription`, e, em seguida, `Resource Group`selecione o nome da sua aplicação. Quando estiver pronto, clique em **OK,** isto irá implementar o projeto de servidor .NET que tem localmente no backend do Serviço de Aplicações. Quando a implementação terminar, será `http://{zumoappname}.azurewebsites.net/` redirecionado para o navegador.                   

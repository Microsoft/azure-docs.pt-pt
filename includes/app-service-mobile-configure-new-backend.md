---
title: incluir ficheiro
description: incluir ficheiro
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 99ca7e82a11687d25355589e7ea539a14cdb493b
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420745"
---
1. Transferir o cliente inícios rápidos do SDK para as seguintes plataformas:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Se utilizar o projeto iOS tem de transferir "azuresdk-iOS -\*. zip" partir [versão mais recente do GitHub](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Deszipe e adicione o `MicrosoftAzureMobile.framework` ficheiro para a raiz do projeto.
    >

2. Terá de adicionar uma ligação de base de dados ou ligar a uma ligação existente. Primeiro, determine se irá utilizar para criar um arquivo de dados ou utilize um já existente.

    - **Criar um novo arquivo de dados**: Se pretende criar um arquivo de dados, utilize o início rápido seguinte:

        [Quickstart: Guia de introdução bases de dados individuais na base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Origem de dados existente**: Siga as instruções abaixo se pretender utilizar uma ligação de base de dados existente

        1. Formato de cadeia de ligação de base de dados SQL: `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}**  Nome do servidor, isso pode ser encontrado na página Descrição geral da base de dados e é normalmente na forma de "server_name.database.windows.net".
            **{porta}**  normalmente 1433.
            **{your_catalogue}**  Nome da base de dados.
            **{your_username}**  Nome de utilizador para aceder à sua base de dados.
            **{your_password}**  Palavra-passe para aceder à sua base de dados.

            [Saiba mais sobre o formato de cadeia de ligação SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Adicione a cadeia de ligação para sua **aplicação móvel** no serviço de aplicações, pode gerir as cadeias de ligação para a sua aplicação utilizando o **configuração** opção no menu.

            Para adicionar uma cadeia de ligação:

            1. Clique nas **as definições da aplicação** separador.

            2. Clique em **[+] nova cadeia de ligação**.

            3. Terá de fornecer **Name**, **valor** e **tipo** para a cadeia de ligação.

            4. Tipo **nome** como `MS_TableConnectionString`

            5. Valor deve ser formada no passo antes da cadeia de ligação.

            6. Se estiver a adicionar uma cadeia de ligação para uma base de dados do SQL Azure escolha **SQL Azure** sob **tipo**.

3. Aplicações móveis do Azure tem SDKs para o back-ends .NET e node. js.

   - **Node.js backend**
    
     Se estiver a utilizar a aplicação de início rápido do node. js, siga as instruções abaixo.

     1. No portal do Azure, aceda a **tabelas simples**, verá este ecrã.
      
        ![Tabelas simples de nó](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Certifique-se a cadeia de ligação do SQL já está adicionada a **configuração** separador. Em seguida, selecione a caixa de **reconheço que esta ação substituirá todo o conteúdo do site** e clique nas **criar tabela TodoItem** botão.
     
        ![Configuração de tabelas simples de nós](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. Na **tabelas simples**, clique nas **+ adicionar** botão.
    
        ![Botão de adicionar tabelas simples de nó](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Criar um `TodoItem` tabela com o acesso anônimo.
      
        ![Nó de tabelas simples Adicionar tabela](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET backend**
    
        Se estiver a utilizar a aplicação de início rápido do .NET, siga as instruções abaixo.

        1. Transfira o projeto de servidor .NET de aplicações móveis do Azure da [repositório azure-mobile-apps-inícios Rápidos](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Crie o projeto de servidor .NET localmente no Visual Studio.

        3. No Visual Studio, abra o Explorador de soluções, faça duplo clique em `ZUMOAPPNAMEService` do projeto, clique em **Publish**, verá um `Publish to App Service` janela. Se estiver a trabalhar no Mac, verifique outras formas de implementar a aplicação [aqui](https://docs.microsoft.com/en-us/azure/app-service/deploy-local-git).
        
           ![Publicação do Visual studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Selecione **serviço de aplicações** como destino de publicar, em seguida, clique em **selecionar existente**, em seguida, clique no **publicar** na parte inferior da janela.

        5. Terá de iniciar sessão no Visual Studio com a sua subscrição do Azure pela primeira vez. Selecione o `Subscription`, `Resource Group`e, em seguida, selecione o nome da sua aplicação. Quando estiver pronto, clique em **OK**, isto irá implementar o projeto de servidor .NET que tem localmente para o back-end do serviço de aplicações. Quando a conclusão da implementação, será redirecionado para `http://{zumoappname}.azurewebsites.net/` no browser.
        
           ![Back-end está a funcionar](./media/app-service-mobile-configure-new-backend/backend-is-up.png)

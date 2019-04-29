---
title: Atualização dos serviços móveis para o serviço de aplicações do Azure - node. js
description: Saiba como atualizar facilmente a sua aplicação de serviços móveis para uma aplicação de Mobile do serviço de aplicações
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: yochayk
editor: ''
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: e54ed6c526182cea57e2d40f356ad9236510d82c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128071"
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Atualizar o seu serviço móvel do Azure de node. js existente para o serviço de aplicações
Serviço de aplicações móveis é uma nova forma de compilação de aplicações móveis com o Microsoft Azure. Para obter mais informações, consulte [O que são Aplicações Móveis?].

Este artigo descreve como atualizar uma aplicação de back-end de node. js existente de serviços móveis do Azure para um novo Mobile Apps do App Service. Enquanto executa esta atualização, a aplicação de serviços móveis existente pode continuar a operar.  Se precisar de atualizar uma aplicação de back-end de node. js, consulte [atualizar os seus serviços móveis .NET](app-service-mobile-net-upgrading-from-mobile-services.md).

Quando um back-end móvel é atualizado para o serviço de aplicações do Azure, tem acesso a todas as funcionalidades do serviço de aplicações e são faturados de acordo com a [preços do serviço de aplicações], não os serviços móveis preços.

## <a name="migrate-vs-upgrade"></a>Migrar vs. a atualização
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> É recomendável que [efetuar uma migração](app-service-mobile-migrating-from-mobile-services.md) antes de passar por uma atualização. Dessa forma, pode colocar as duas versões do seu aplicativo no mesmo plano de serviço de aplicações e incorrer sem custos adicionais.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Aprimoramentos no servidor de aplicações de Mobile node. js SDK
Atualizar para o novo [SDK de aplicações móveis](https://www.npmjs.com/package/azure-mobile-apps) fornece muitos aprimoramentos, incluindo:

* Com base na [Express framework](https://expressjs.com/en/index.html), o novo SDK do nó é leve e projetado para se manter atualizado com novas versões de nó que estes. Pode personalizar o comportamento da aplicação com o Express middleware.
* Melhorias de desempenho significativo em comparação comparadas o SDK dos Mobile Services.
* Agora, pode alojar um Web site em conjunto com o seu back-end móvel; da mesma forma, é fácil adicionar o SDK do Azure Mobile para qualquer aplicativo express.v4 existente.
* Criado para desenvolvimento para várias plataformas e local, o SDK de aplicações móveis podem ser desenvolvido e executar localmente em plataformas Windows, Linux e OSX. Agora, é fácil de usar técnicas de desenvolvimento nó comuns, como em execução [grande](https://mochajs.org/) testes antes da implantação.

## <a name="overview"></a>Descrição geral da atualização básica
Para ajudar a atualizar um back-end de node. js, serviço de aplicações do Azure fornece um pacote de compatibilidade.  Após a atualização, terá um novo site que pode ser implementado para um novo site do serviço de aplicações.

O cliente de serviços móveis os SDKs estão **não** compatível com o novo servidor de aplicações móveis do SDK. Para fornecer continuidade do serviço para a sua aplicação, não deve publicar as alterações a um site publicados clientes atualmente a funcionar. Em vez disso, deve criar uma nova aplicação móvel que serve como um duplicado. Pode colocar o aplicativo no mesmo plano de serviço de aplicações para evitar incorrer em custos financeiros adicionais.

Em seguida, terá duas versões da aplicação: aplicações que permanece o mesmo e serve publicadas no terreno e a outra que pode, em seguida, atualizar e de destino com uma nova versão de cliente. Pode mover e testar o seu código ao seu ritmo, mas deve certificar-se de que quaisquer correções de erros que fizer são aplicadas para ambos. Assim que se sentir que um número pretendido de aplicações no terreno tem atualizado para a versão mais recente do cliente, pode eliminar a aplicação migrada original se desejar. Ele não incorrer em custos monetários adicionais, se alojada no mesmo plano de serviço de aplicações que a sua aplicação móvel.

A estrutura completa para o processo de atualização é o seguinte:

1. Transfira o seu serviço de Mobile do Azure (migrados) existente.
2. Converta o projeto para uma aplicação móvel do Azure utilizando o pacote de compatibilidade.
3. Corrigir quaisquer diferenças (por exemplo, definições de autenticação).
4. Implemente o seu projeto de aplicação móvel do Azure convertido para um novo serviço de aplicações.
5. Lançar uma nova versão da sua aplicação de cliente que utiliza a nova aplicação móvel.
6. (Opcional) Elimine a aplicação de serviço móvel migrado original.

A eliminação pode ocorrer quando não vir qualquer tráfego no seu serviço móvel migrado original.

## <a name="install-npm-package"></a> Instalar os pré-requisitos
Deve instalar o [nó] no seu computador local.  Também deve instalar o pacote de compatibilidade.  Após instalação do nó, pode executar o comando seguinte numa cmd novo ou a linha de comandos PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Obter os Scripts de Mobile Services do Azure
* Inicie sessão no [Portal do Azure].
* Usando **todos os recursos** ou **dos serviços de aplicações**, encontrar o seu site de serviços móveis.
* Dentro do site, clique em **ferramentas** -> **Kudu** -> **ir** para abrir o site Kudu.
* Clique em **consola de depuração** -> **PowerShell** para abrir a consola de depuração.
* Navegue para `site/wwwroot/App_Data/config` ao clicar em cada diretório, por sua vez
* Clique no ícone de download junto aos `scripts` diretório.

Isto irá transferir os scripts no formato ZIP.  Criar um novo diretório no seu computador local e Descompacte o `scripts.ZIP` arquivo dentro do diretório.  Esta ação irá criar um `scripts` diretório.

## <a name="scaffold-app"></a> Estruturar o novo back-end de aplicações móveis do Azure
Execute o seguinte comando a partir do diretório que contém o diretório de scripts:

```scaffold-mobile-app scripts out```

Esta ação irá criar um estruturado back-end de aplicações móveis do Azure no `out` diretório.  Embora não seja necessário, é uma boa ideia verificar o `out` diretório num repositório de código fonte da sua preferência.

## <a name="deploy-ama-app"></a> Implementar o seu back-end de aplicações móveis do Azure
Durante a implementação, terá de fazer o seguinte:

1. Criar uma nova aplicação de Mobile no [Portal do Azure].
2. Execute o `createViews.sql` script na base de dados ligada.
3. Ligar a base de dados que está ligada ao seu serviço móvel para o novo serviço de aplicações.
4. Ligar a quaisquer outros recursos (por exemplo, os Hubs de notificação) para o novo serviço de aplicações.
5. Implemente o código gerado no seu novo site.

### <a name="create-a-new-mobile-app"></a>Criar uma nova aplicação móvel
1. Inicie sessão no [Portal do Azure].
2. Clique em **+NOVO** > **Web + Móvel** > **Aplicação Móvel** e indique um nome para o back-end da Aplicação Móvel.
3. Para o **Grupo de Recursos**, selecione um grupo de recursos existente ou crie um novo (utilizando o mesmo nome que a sua aplicação).

    Pode selecionar outro plano do Serviço de Aplicações ou criar um novo. Para obter mais informações sobre os planos dos Serviços de Aplicações e como criar um novo plano com um escalão de preço diferente na localização pretendida, veja [Azure App Service plans in-depth overview (Descrição geral aprofundada dos planos do Serviço de Aplicações do Azure)](../app-service/overview-hosting-plans.md).
4. Para o **Plano do Serviço de Aplicações**, encontra-se selecionado o plano predefinido (no [Escalão Standard](https://azure.microsoft.com/pricing/details/app-service/)). Pode ainda selecionar um plano diferente ou [criar um novo](../app-service/app-service-plan-manage.md#create-an-app-service-plan). Definições do plano de serviço de aplicações determinam a [localização, funcionalidades, custo e recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associados à aplicação.

    Depois de decidir o plano, clique em **Criar**. Esta ação cria o back-end da Aplicação Móvel.

### <a name="run-createviewssql"></a>Executar CreateViews.SQL
A aplicação estruturada contém um arquivo chamado `createViews.sql`.  Este script tem de ser executado na base de dados de destino.  A cadeia de ligação da base de dados de destino pode ser obtida a partir do seu serviço móvel migrado do **definições** página sob **cadeias de ligação**.  Ele é denominado `MS_TableConnectionString`.

Pode executar este script a partir do SQL Server Management Studio ou o Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Ligar a base de dados para o serviço de aplicações
Ligar a base de dados existente para o serviço de aplicações:

* Na [Portal do Azure], abra o serviço de aplicações.
* Selecione **todas as definições** -> **conexões de dados**.
* Clique em **+ adicionar**.
* Na lista pendente, selecione **base de dados SQL**
* Sob **base de dados SQL**, selecione a sua base de dados existente, em seguida, clique em **selecione**.
* Sob **cadeia de ligação**, introduza o nome de utilizador e palavra-passe para a base de dados, em seguida, clique em **OK**.
* Na **adicionar conexões de dados** página, clique em **OK**.

O nome de utilizador e palavra-passe podem ser encontrados visualizando a cadeia de ligação da base de dados de destino no seu serviço móvel migrados.

### <a name="set-up-authentication"></a>Configurar autenticação
Aplicações móveis do Azure permite-lhe configurar o Azure Active Directory, Facebook, Google, Microsoft e autenticação no serviço do Twitter.  Autenticação personalizada tem de ser desenvolvida separadamente.  Consulte a [conceitos de autenticação] documentação e [Início rápido de autenticação] documentação para obter mais informações.  

## <a name="updating-clients"></a>Atualizar clientes móveis
Depois de ter um back-end de aplicação móvel operacional, pode trabalhar numa versão nova da sua aplicação de cliente que consome-lo. Aplicações móveis também inclui uma nova versão dos SDKs do cliente e, assim como a atualização do servidor acima, terá de remover todas as referências para os SDKs de serviços móveis antes de instalar as versões de aplicações móveis.

Uma das principais alterações entre as versões é que os construtores já não necessitam de uma chave de aplicação.
Agora simplesmente passa a URL da aplicação móvel. Por exemplo, nos clientes .NET, o `MobileServiceClient` construtor é agora:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Pode ler sobre como instalar os novos SDKs e usando a nova estrutura por meio dos links a seguir:

* [Versão do Android 2.2 ou posterior](app-service-mobile-android-how-to-use-client-library.md)
* [versão 3.0.0 do iOS ou posterior](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) versão 2.0.0 ou posterior](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Versão 2.0 ou posterior do Apache Cordova](app-service-mobile-cordova-how-to-use-client-library.md)

Se a aplicação faz usar notificações push, anote as instruções de registro específicos para cada plataforma, como houve algumas alterações aqui também.

Quando tiver a nova versão de cliente pronta, faça um teste em relação a seu projeto de servidor atualizado. Depois de confirmar que funciona, pode lançar uma nova versão da sua aplicação para os clientes. Eventualmente, depois dos seus clientes tenham tido a oportunidade para receber estas atualizações, pode eliminar a versão de serviços móveis da sua aplicação. Neste momento, completamente tiver atualizado para uma aplicação móvel do serviço de aplicação com o servidor de aplicações móveis SDK mais recente.

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[O que são Aplicações Móveis?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Preços do serviço de aplicações]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Conceitos de autenticação]: ../app-service/overview-authentication-authorization.md
[Início rápido de autenticação]: app-service-mobile-auth.md

[Portal do Azure]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

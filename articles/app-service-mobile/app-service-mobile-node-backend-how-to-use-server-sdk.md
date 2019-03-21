---
title: Como trabalhar com o SDK de servidor de back-end de node. js para aplicações móveis | Documentos da Microsoft
description: Saiba como trabalhar com o SDK de servidor de back-end de node. js para aplicações de Mobile do serviço de aplicações do Azure.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: elamalani
editor: ''
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 831f6b4bdc99e63859b390f8a9bb88d74301284e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997541"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Como utilizar o SDK de node. js de aplicações móveis

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este artigo fornece informações detalhadas e exemplos que mostram como trabalhar com um node. js de back-end na funcionalidade de aplicações móveis do serviço de aplicações do Azure.

## <a name="Introduction"></a>Introdução

Aplicações móveis fornece a capacidade de adicionar um acesso de dados otimizadas para mobilidade Web API a uma aplicação web. O SDK de aplicações móveis é fornecido para aplicativos da web ASP.NET e node. js. O SDK fornece as seguintes operações:

* Operações de tabela (ler, inserir, atualizar, eliminar) para acesso a dados
* Operações de API personalizada

As duas operações oferecem para autenticação em todos os fornecedores de identidade que permite o serviço de aplicações do Azure. Esses provedores incluem fornecedores de identidade social, como o Facebook, Twitter, Google e Microsoft, bem como a Azure Active Directory para a identidade empresarial.

Pode encontrar exemplos para cada caso de utilização no [diretório de exemplos no GitHub].

## <a name="supported-platforms"></a>Plataformas suportadas

O Mobile Apps node. js SDK suporta a versão atual do LTS do nó e mais tarde. Atualmente, a versão mais recente do LTS é v4.5.0 de nó. Outras versões do nó podem funcionar, mas não são suportadas.

O Mobile Apps node. js SDK suporta dois controladores de base de dados: 

* O driver de nó mssql oferece suporte a SQL Database do Azure e instâncias do SQL Server locais.  
* O driver do sqlite3 suporta bases de dados do SQLite numa única instância apenas.

### <a name="howto-cmdline-basicapp"></a>Criar um back-end nas node. js básico através da linha de comando

Todas as aplicações de Mobile node. js back-end é iniciado como um aplicativo de ExpressJS. ExpressJS é a estrutura de serviços de web mais popular disponível para node. js. Pode criar um basic [Express] aplicação da seguinte forma:

1. Num comando ou janela do PowerShell, crie um diretório para o seu projeto:

        mkdir basicapp

1. Executar `npm init` para inicializar a estrutura do pacote:

        cd basicapp
        npm init

   O `npm init` comando pede-lhe um conjunto de perguntas para inicializar o projeto. Veja o resultado de exemplo:

   ![A saída de init do npm][0]

1. Instalar o `express` e `azure-mobile-apps` bibliotecas a partir do repositório do npm:

        npm install --save express azure-mobile-apps

1. Crie um ficheiro App. js para implementar o servidor móvel básico:

    ```javascript
    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define a TodoItem table.
    mobile.tables.add('TodoItem');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
    ```

Esta aplicação cria uma API da Web otimizadas para mobilidade com um único ponto final (`/tables/TodoItem`) que fornece acesso não autenticado para um arquivo de dados SQL subjacente ao utilizar um esquema dinâmico. É adequado para os inícios rápidos de biblioteca de cliente a seguir:

* [Início rápido de cliente Android]
* [Início rápido de cliente do Apache Cordova]
* [início rápido de cliente do iOS]
* [Início rápido de cliente do Windows Store]
* [Início rápido de cliente do xamarin. IOS]
* [Início rápido de cliente do xamarin. Android]
* [Início rápido de cliente do xamarin. Forms]

Pode encontrar o código para esta aplicação básica no [exemplo de basicapp no GitHub].

### <a name="howto-vs2015-basicapp"></a>Criar um back-end de node. js com o Visual Studio 2015

Visual Studio 2015 requer uma extensão para desenvolver aplicações node. js no IDE. Para começar, instale o [Ferramentas do node. js 1.1 para o Visual Studio]. Quando concluir a instalação, crie um aplicativo de 4.x expresso:

1. Abra o **novo projeto** caixa de diálogo (da **ficheiro** > **novo** > **projeto**).
1. Expanda **modelos** > **JavaScript** > **node. js**.
1. Selecione **aplicativo básico do Azure node. js Express 4**.
1. Preencha o nome do projeto. Selecione **OK**.

   ![Visual Studio 2015 novo projeto][1]
1. Com o botão direito a **npm** nó e selecione **instalar novos pacotes de npm**.
1. Poderá ter de atualizar o catálogo de npm, depois de criar a sua primeira aplicação node. js. Selecione **atualizar** se necessário.
1. Introduza **-aplicações móveis do azure** na caixa de pesquisa. Selecione o **-aplicações móveis do azure-2.0.0** do pacote e, em seguida, selecione **Instalar pacote**.

   ![Instalar novos pacotes de npm][2]
1. Selecione **fechar**.
1. Abra o ficheiro App. js para adicionar suporte para o SDK de aplicações móveis. Na linha 6 at na parte inferior da biblioteca `require` instruções, adicione o seguinte código:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Na linha aproximadamente 27 após o outro `app.use` instruções, adicione o seguinte código:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Guarde o ficheiro.

1. A executar a aplicação localmente (a API é atendida em `http://localhost:3000`) ou publicar no Azure.

### <a name="create-node-backend-portal"></a>Criar um back-end de node. js com o portal do Azure

Pode criar das aplicações móveis de back-end certo na [portal do Azure]. Pode concluir os passos seguintes ou criar um cliente e servidor em conjunto ao seguir a [criar uma aplicação móvel](app-service-mobile-ios-get-started.md) tutorial. O tutorial contém uma versão simplificada destas instruções e é melhor para projetos de prova de conceito.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

De volta a **começar** painel, em **criar uma API de tabela**, escolha **node. js** como linguagem de back-end.
Selecione a caixa **reconheço que esta ação substituirá todo o conteúdo do site**e, em seguida, selecione **criar tabela TodoItem**.

### <a name="download-quickstart"></a>Transferir o projeto de código de início rápido de back-end de node. js utilizando o Git

Ao criar um back-end de aplicações móveis do node. js com o portal **guia de introdução** painel, um projeto de node. js é criada para si e implementada em seu site. No portal, pode adicionar tabelas e as APIs e editar arquivos de código para o back-end de node. js. Também pode utilizar várias ferramentas de implantação para transferir o projeto de back-end, para que pode adicionar ou modificar tabelas e as APIs e, em seguida, voltar a publicar o projeto. Para obter mais informações, consulte a [Guia de implementação do serviço de aplicações do Azure].

O procedimento seguinte utiliza um repositório de Git para transferir o código de projeto de início rápido:

1. Instale o Git, se ainda não o tiver feito. Os passos necessários para instalar o Git variam entre sistemas operativos. Para as distribuições de sistema operativo específico e orientações de instalação, consulte [instalar o Git](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Ver [preparar o seu repositório](../app-service/deploy-local-git.md#prepare-your-repository) para ativar o repositório de Git para o seu site de back-end. Tome nota do nome de utilizador de implementação e a palavra-passe.
3. No painel para as suas aplicações de Mobile back-end, anote o **URL de clone de Git** definição.
4. Executar o `git clone` comando utilizando o URL de clone de Git. Introduza a palavra-passe quando for necessário, como no exemplo seguinte:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Navegue para o diretório de local (`/todolist` no exemplo anterior) e tenha em atenção que os arquivos do projeto tiverem sido transferidos. Localize o ficheiro de todoitem.json no `/tables` diretório. Esse arquivo define as permissões na tabela. Também encontre o ficheiro de todoitem.js no mesmo diretório. Define os scripts de operação CRUD para a tabela.
6. Depois de efetuar alterações aos ficheiros de projeto, executados os seguintes comandos para adicionar, consolidar e, em seguida, carregue as alterações para o site:

        $ git commit -m "updated the table script"
        $ git push origin master

   Quando adiciona novos ficheiros ao projeto, terá primeiro de executar o `git add .` comando.

O site será replicado sempre que um novo conjunto de consolidações é emitidos via push para o site.

### <a name="howto-publish-to-azure"></a>Publicar o seu back-end de node. js no Azure

O Microsoft Azure fornece vários mecanismos para publicar o node. js de aplicações do Mobile back-end para o serviço do Azure. Estes mecanismos incluem ferramentas de implantação integradas ao Visual Studio, as ferramentas da linha de comandos e opções de implementação contínua com base no controle de origem. Para obter mais informações, consulte a [Guia de implementação do serviço de aplicações do Azure].

Serviço de aplicações do Azure tem conselhos específico para aplicações node. js, que deve rever antes de publicar o back-end:

* Como [Especifique a versão de nó]
* Como [utilizar módulos do nó]

### <a name="howto-enable-homepage"></a>Ativar uma home page de seu aplicativo

Muitos aplicativos são uma combinação de aplicações web e móveis. Pode utilizar a estrutura de ExpressJS para combinar os dois lados. Às vezes, no entanto, pode querer implementar apenas uma interface móvel. É útil para fornecer uma home page para garantir que o serviço de aplicações está operacional e em execução. Pode fornecer sua própria página inicial ou ativar uma home page do temporária. Para ativar uma home page do temporária, utilize o seguinte código para criar uma instância de aplicações móveis:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Se quiser apenas esta opção disponível ao desenvolver localmente, pode adicionar esta definição para o ficheiro de azureMobile.js.

## <a name="TableOperations"></a>Operações de tabela

O SDK do azure-mobile-apps node. js Server fornece mecanismos para expor as tabelas de dados armazenadas na base de dados do Azure SQL como uma API Web. Ele fornece cinco operações:

| Operação | Descrição |
| --- | --- |
| GET /tables/*tablename* |Obter todos os registos na tabela. |
| GET /tables/*tablename*/:id |Obter um registo específico na tabela. |
| POST /tables/*tablename* |Crie um registo na tabela. |
| PATCH /tables/*tablename*/:id |Atualize um registo na tabela. |
| DELETE /tables/*tablename*/:id |Elimine um registo na tabela. |

Esta API Web suporta [OData] e expande o esquema de tabela para suportar [sincronização de dados offline].

### <a name="howto-dynamicschema"></a>Definir tabelas com um esquema dinâmico

Antes de poder utilizar uma tabela, tem de defini-lo. Pode definir tabelas usando um esquema estático (onde define as colunas no esquema) ou dinamicamente (onde o SDK controla o esquema com base nos pedidos de entrada). Além disso, pode controlar os aspectos específicos da Web API ao adicionar o código JavaScript para a definição.

Como melhor prática, deve definir cada tabela num arquivo JavaScript no `tables` directory e, em seguida, utilize o `tables.import()` método para importar as tabelas. Estendendo o exemplo de aplicação básico, teria ajustar o ficheiro App. js:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Define the database schema that is exposed.
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined.
mobile.tables.initialize().then(function () {
    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
});
```

Definir a tabela no. / tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Por predefinição, o tabelas utilizam um esquema dinâmico. Para desativar o esquema dinâmico globalmente, defina o `MS_DynamicSchema` definição de aplicação para false no portal do Azure.

Pode encontrar um exemplo completo no [lista de tarefas exemplo no GitHub].

### <a name="howto-staticschema"></a>Definir tabelas com um esquema estático

Pode definir explicitamente as colunas para expor através da API Web. O SDK de node. js-mobile apps do azure adiciona automaticamente quaisquer colunas adicionais necessárias para a sincronização de dados offline para a lista que fornecer. Por exemplo, as aplicações de cliente de início rápido requerem uma tabela com duas colunas: `text` (uma cadeia de caracteres) e `complete` (um valor booleano).  
A tabela pode ser definida no arquivo de JavaScript de definição de tabela (localizado no `tables` diretório) da seguinte forma:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

module.exports = table;
```

Se definir tabelas de forma estática, também deve chamar o `tables.initialize()` método para criar o esquema de banco de dados na inicialização. O `tables.initialize()` método devolve um [promessa] para que o serviço web não serve pedidos antes da base de dados é inicializado.

### <a name="howto-sqlexpress-setup"></a>Utilizar o SQL Server Express como um arquivo de dados de desenvolvimento no seu computador local

O Mobile Apps node. js SDK fornece três opções para satisfazer as necessidades dados prontos a utilizar:

* Utilize o **memória** driver para fornecer um arquivo de exemplo não persistentes.
* Utilize o **mssql** driver para fornecer um arquivo de dados SQL Server Express para o desenvolvimento.
* Utilize o **mssql** driver para fornecer um arquivo de dados de base de dados do Azure SQL de produção.

O Mobile Apps node. js SDK utiliza o [mssql Node.js package] para estabelecer e utilizar uma ligação ao SQL Server Express e base de dados SQL. Este pacote requer que ative as ligações de TCP na sua instância do SQL Server Express.

> [!TIP]
> O driver de memória não fornece um conjunto completo de recursos para fins de teste. Se quiser testar seu back-end localmente, recomendamos a utilização de um arquivo de dados SQL Server Express e o driver mssql.

1. Transfira e instale [Microsoft SQL Server 2014 Express]. Certifique-se de que instala o SQL Server 2014 Express com a edição de ferramentas. A menos que explicitamente necessitar de suporte de 64 bits, a versão de 32 bits consome menos memória quando em execução.
1. Execute o Gestor de configuração do SQL Server 2014:

   a. Expanda a **configuração de rede do SQL Server** nó no menu de árvore.

   b. Selecione **protocolos para o SQLEXPRESS**.

   c. Com o botão direito **TCP/IP** e selecione **ativar**. Selecione **OK** na caixa de diálogo pop-up.

   d. Com o botão direito **TCP/IP** e selecione **propriedades**.

   e. Selecione o **endereços IP** separador.

   f. Encontrar o **IPAll** nó. Na **porta TCP** , insira **1433**.

      ![Configurar o SQL Server Express para TCP/IP][3]

   g. Selecione **OK**. Selecione **OK** na caixa de diálogo pop-up.

   h. Selecione **SQL Server Services** no menu de árvore.

   i. Com o botão direito **SQL Server (SQLEXPRESS)** e selecione **reiniciar**.

   j. Feche o Gestor de configuração do SQL Server 2014.

1. Executar o SQL Server 2014 Management Studio e ligue-se à sua instância do SQL Server Express local:

   1. A instância no Object Explorer com o botão direito e selecione **propriedades**.
   1. Selecione o **segurança** página.
   1. Certifique-se de que **modo de SQL Server e a autenticação do Windows** está selecionada.
   1. Selecione **OK**.

      ![Configurar a autenticação do SQL Server Express][4]
   1. Expanda **Security** > **inícios de sessão** no Object Explorer.
   1. Com o botão direito **inícios de sessão** e selecione **novo início de sessão**.
   1. Introduza um nome de início de sessão. Selecione **Autenticação do SQL Server**. Introduza uma palavra-passe e, em seguida, introduza a mesma palavra-passe no **Confirmar palavra-passe**. A palavra-passe tem de cumprir os requisitos de complexidade do Windows.
   1. Selecione **OK**.

      ![Adicionar um novo utilizador para o SQL Server Express][5]
   1. Seu novo início de sessão com o botão direito e selecione **propriedades**.
   1. Selecione o **funções de servidor** página.
   1. Selecione a caixa de verificação para o **dbcreator** função de servidor.
   1. Selecione **OK**.
   1. Feche o SQL Server 2015 Management Studio.

Certifique-se de que registe o nome de utilizador e palavra-passe que selecionou. Poderá ter de atribuir funções de servidor adicionais ou permissões, dependendo dos requisitos da base de dados.

As leituras de aplicação do node. js a `SQLCONNSTR_MS_TableConnectionString` variável de ambiente para a cadeia de ligação para esta base de dados. Pode definir esta variável no seu ambiente. Por exemplo, pode utilizar o PowerShell para definir esta variável de ambiente:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Acesso a base de dados através de uma ligação de TCP/IP. Forneça um nome de utilizador e palavra-passe para a ligação.

### <a name="howto-config-localdev"></a>Configurar o seu projeto de desenvolvimento local

Aplicações móveis lê um arquivo JavaScript chamado *azureMobile.js* do sistema de arquivos local. Não utilize este ficheiro para configurar o SDK de aplicações móveis na produção. Em alternativa, utilize **as definições da aplicação** no [portal do Azure].

O ficheiro de azureMobile.js deve exportar de um objeto de configuração. As configurações mais comuns são:

* Definições da base de dados
* Definições de registo de diagnósticos
* Definições de CORS alternativas

Este exemplo **azureMobile.js** arquivo implementa as definições de base de dados anterior:

```javascript
module.exports = {
    cors: {
        origins: [ 'localhost' ]
    },
    data: {
        provider: 'mssql',
        server: '127.0.0.1',
        database: 'mytestdatabase',
        user: 'azuremobile',
        password: 'T3stPa55word'
    },
    logging: {
        level: 'verbose'
    }
};
```

Recomendamos que adicione **azureMobile.js** para sua **. gitignore** arquivo (ou outro controle do código de origem ignorar ficheiro) para impedir que as palavras-passe a ser armazenados na cloud. Sempre configurar definições de produção no **as definições da aplicação** dentro do [portal do Azure].

### <a name="howto-appsettings"></a>Configurar as definições de aplicações para a sua aplicação móvel

A maioria das definições no ficheiro azureMobile.js tem uma definição de aplicação equivalente no [portal do Azure]. Utilize a lista seguinte para configurar a sua aplicação no **as definições da aplicação**:

| Definição de aplicação | azureMobile.js setting | Descrição | Valores válidos |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |nome |Nome da aplicação |string |
| **MS_MobileLoggingLevel** |logging.level |Nível de registo mínimo de mensagens para iniciar sessão |erro, aviso, informações, verbosa, depuração, tolice |
| **MS_DebugMode** |depurar |Ativa ou desativa o modo de depuração |VERDADEIRO, FALSO |
| **MS_TableSchema** |data.schema |Nome do esquema padrão para tabelas SQL |cadeia de caracteres (predefinição: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Ativa ou desativa o modo de depuração |VERDADEIRO, FALSO |
| **MS_DisableVersionHeader** |versão (definido para não definido) |Desativa o cabeçalho X-ZUMO-Server-Version |VERDADEIRO, FALSO |
| **MS_SkipVersionCheck** |skipversioncheck |Desativa a verificação da versão de API do cliente |VERDADEIRO, FALSO |

Para definir uma definição de aplicação:

1. Inicie sessão no [portal do Azure].
1. Selecione **todos os recursos** ou **dos serviços de aplicações**e, em seguida, selecione o nome da sua aplicação móvel.
1. O **definições** painel abre-se por predefinição. Se não, selecione **definições**.
1. Sobre o **gerais** menu, selecione **configurações de aplicativo**.
1. Desloque-se para o **as definições da aplicação** secção.
1. Se a aplicação definir já existir, selecione o valor da definição de aplicação para editar o valor.
   Se a definição de aplicação não existe, introduza a definição de aplicação no **chave** caixa e o valor a **valor** caixa.
1. Selecione **Guardar**.

Alterar a maioria das definições de aplicação requer um reinício do serviço.

### <a name="howto-use-sqlazure"></a>Armazenar a base de dados de SQL de utilização como seus dados de produção

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Utilizar a SQL Database do Azure como um arquivo de dados é idêntica em todos os tipos de aplicação do serviço de aplicações do Azure. Se não o tiver feito isso já, siga estes passos para criar um back-end de aplicações móveis:

1. Inicie sessão no [portal do Azure].
1. No canto superior esquerdo da janela, selecione o **+ novo** botão > **Web + móvel** > **aplicação móvel**e, em seguida, forneça um nome para as suas aplicações de Mobile back-end.
1. Na **grupo de recursos** , introduza o mesmo nome que a sua aplicação.
1. A predefinição é selecionado o plano do serviço de aplicações. Se pretender alterar o seu plano do serviço de aplicações:

   a. Selecione **plano do App Service** > **+ criar novo**.

   b. Forneça um nome do novo plano de serviço de aplicações e selecione uma localização adequada.

   c. Selecione um escalão de preço adequado para o serviço. Selecione **ver tudo** para ver mais opções, de preços, tal como **gratuito** e **partilhado**.

   d. Clique nas **selecione** botão.

   e. De volta a **plano do App Service** painel, selecione **OK**.
1. Selecione **Criar**.

Aprovisionamento de aplicações de Mobile back-end pode demorar alguns minutos. Depois de fazer uma cópia das aplicações móveis final é aprovisionado, o portal abre o **definições** painel para o back-end de aplicações móveis.

É possível ligar uma base de dados existente do SQL ao seu back-end de aplicações móveis ou crie uma nova base de dados do SQL. Nesta secção, vamos criar uma base de dados SQL.

> [!NOTE]
> Se já tiver uma base de dados na mesma localização tal como as aplicações móveis de back-end, em vez disso, pode selecionar **utilizar uma base de dados existente** e, em seguida, selecione essa base de dados. Não é recomendada a utilização de uma base de dados numa localização diferente devido às latências maiores.

1. No novo Mobile Apps back-end, selecione **configurações** > **aplicação móvel** > **dados** > **+ adicionar**.
1. Na **adicionar ligação de dados** painel, selecione **base de dados do SQL - configurar definições necessárias** > **criar uma nova base de dados**. Introduza o nome da nova base de dados do **nome** caixa.
1. Selecione **servidor**. Na **novo servidor** painel, introduza um nome de servidor exclusivo na **nome do servidor** caixa e forneça um servidor adequado, início de sessão de administrador e a palavra-passe. Certifique-se de que **permitir que os serviços do azure ao servidor de acesso** está selecionada. Selecione **OK**.

   ![Criar uma SQL Database do Azure][6]
1. Na **nova base de dados** painel, selecione **OK**.
1. De volta a **adicionar ligação de dados** painel, selecione **cadeia de ligação**e introduza o início de sessão e palavra-passe que indicou quando criou a base de dados. Se utilizar uma base de dados existente, forneça as credenciais de início de sessão para essa base de dados. Selecione **OK**.
1. De volta a **adicionar ligação de dados** painel mais uma vez, selecione **OK** para criar a base de dados.

<!--- END OF ALTERNATE INCLUDE -->

Criação da base de dados pode demorar alguns minutos. Utilize o **notificações** área para monitorizar o progresso da implementação. Não progride até que a base de dados é implementado com êxito. Depois de implementar a base de dados, é criada uma cadeia de ligação para a instância de base de dados SQL nas definições da aplicação de back-end de aplicações móveis. Pode ver esta definição de aplicação na **configurações** > **configurações de aplicativo** > **cadeias de ligação**.

### <a name="howto-tables-auth"></a>Exigir autenticação para o acesso às tabelas

Se pretender utilizar a autenticação do serviço de aplicações com o `tables` ponto final, tem de configurar a autenticação do serviço de aplicações no [portal do Azure] primeiro. Para obter mais informações, consulte o guia de configuração para o fornecedor de identidade que pretende utilizar:

* [Configurar a autenticação do Azure Active Directory]
* [Configurar a autenticação do Facebook]
* [Configurar a autenticação do Google]
* [Configurar a autenticação da Microsoft]
* [Configurar a autenticação do Twitter]

Cada tabela tem uma propriedade de acesso que pode utilizar para controlar o acesso à tabela. O exemplo a seguir mostra uma tabela foram definida estaticamente com autenticação necessária.

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

A propriedade de acesso pode ter um de três valores:

* *anónimo* indica que a aplicação cliente tem permissão para ler os dados sem autenticação.
* *autenticado* indica que a aplicação cliente tem de enviar um token de autenticação válido com o pedido.
* *desativada* indica que esta tabela está atualmente desativada.

Se a propriedade de acesso não está definida, é permitido o acesso não autenticado.

### <a name="howto-tables-getidentity"></a>Utilizar afirmações de autenticação com as suas tabelas
Pode configurar várias afirmações que são pedidas quando é configurada a autenticação. Essas declarações não estão normalmente disponíveis através do `context.user` objeto. No entanto, pode recuperá-los utilizando o `context.user.getIdentity()` método. O `getIdentity()` método retorna uma promessa que é resolvido para um objeto. O objeto é codificado o método de autenticação (`facebook`, `google`, `twitter`, `microsoftaccount`, ou `aad`).

Por exemplo, se configurar a autenticação da conta Microsoft e os endereços de e-mail de afirmação de pedido, pode adicionar o endereço de e-mail para o registo com o controlador da tabela seguinte:

```javascript
var azureMobileApps = require('azure-mobile-apps');

// Create a new table definition.
var table = azureMobileApps.table();

table.columns = {
    "emailAddress": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;
table.access = 'authenticated';

/**
* Limit the context query to those records with the authenticated user email address
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function queryContextForEmail(context) {
    return context.user.getIdentity().then((data) => {
        context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
        return context.execute();
    });
}

/**
* Adds the email address from the claims to the context item - used for
* insert operations
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function addEmailToContext(context) {
    return context.user.getIdentity().then((data) => {
        context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
        return context.execute();
    });
}

// Configure specific code when the client does a request.
// READ: only return records that belong to the authenticated user.
table.read(queryContextForEmail);

// CREATE: add or overwrite the userId based on the authenticated user.
table.insert(addEmailToContext);

// UPDATE: only allow updating of records that belong to the authenticated user.
table.update(queryContextForEmail);

// DELETE: only allow deletion of records that belong to the authenticated user.
table.delete(queryContextForEmail);

module.exports = table;
```

Para ver quais as afirmações estão disponíveis, utilize um browser para ver o `/.auth/me` ponto final do seu site.

### <a name="howto-tables-disabled"></a>Desativar o acesso às operações de tabela específica

Para além de ser apresentados na tabela, pode servir-se a propriedade de acesso para controlar as operações individuais. Existem quatro operações:

* `read` é a operação obter RESTful na tabela.
* `insert` é a operação RESTful POST na tabela.
* `update` é a operação RESTful PATCH na tabela.
* `delete` é a operação eliminar RESTful na tabela.

Por exemplo, talvez queira fornecer uma tabela não autenticada só de leitura:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>Ajustar a consulta que é utilizada com operações de tabela

É um requisito comum para operações de tabela fornecer uma vista restrita dos dados. Por exemplo, pode fornecer uma tabela que está marcada com o ID de utilizador autenticado, de modo a que só pode ler ou atualizar os seus próprios registos. A definição da tabela seguinte fornece essa funcionalidade:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table.
table.columns = {
    "userId": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table.
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved.
table.read(function (context) {
    context.query.where({ userId: context.user.id });
    return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user.
table.insert(function (context) {
    context.item.userId = context.user.id;
    return context.execute();
});

module.exports = table;
```

Operações que normalmente são executados uma consulta de ter uma propriedade de consulta que pode ajustar com um `where` cláusula. A propriedade de consulta é uma [QueryJS] pode processar o objeto que é usado para converter uma consulta de OData para algo que os dados de back-end. Para casos de igualdade simples (como o anterior), pode utilizar um mapa. Também pode adicionar as cláusulas SQL específicas:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Configurar a eliminação de forma recuperável numa tabela

Eliminação de forma recuperável, na verdade, não a eliminar registos. Em vez disso, ele marca-los como eliminados na base de dados, definindo a coluna eliminada para true. O SDK de aplicações móveis remove automaticamente registos eliminados de forma recuperável, copiando-o de resultados, a menos que o SDK de cliente móvel utiliza `IncludeDeleted()`. Para configurar uma tabela para a eliminação de forma recuperável, defina o `softDelete` propriedade no arquivo de definição de tabela:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Turn on soft delete.
table.softDelete = true;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Deve estabelecer um mecanismo para a eliminação de registos: uma aplicação de cliente, um WebJob, uma função do Azure ou uma API personalizada.

### <a name="howto-tables-seeding"></a>Efetuar o seeding sua base de dados com dados

Quando estiver a criar uma nova aplicação, poderá querer efetuar o seeding uma tabela com dados. Pode fazê-lo dentro do arquivo de JavaScript de definição de tabela da seguinte forma:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};
table.seed = [
    { text: 'Example 1', complete: false },
    { text: 'Example 2', complete: true }
];

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Propagação de dados acontece apenas quando utilizou o SDK de aplicações móveis para criar a tabela. Se a tabela já existe na base de dados, não existem dados são inseridos na tabela. Se o esquema dinâmico estiver ativado, o esquema é inferido dos dados propagados.

É recomendável chamar explicitamente o `tables.initialize()` método para criar a tabela quando inicia o serviço em execução.

### <a name="Swagger"></a>Ativar o suporte de Swagger
Aplicações móveis vem com interna [Swagger] suportar. Para ativar o suporte de Swagger, instale primeiro o IU do swagger como uma dependência:

    npm install --save swagger-ui

Em seguida, pode ativar o suporte de Swagger no construtor de aplicações móveis:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Provavelmente só quer ativar o suporte de Swagger nas edições de desenvolvimento. Pode fazê-lo utilizando o `NODE_ENV` definição de aplicação:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

O `swagger` ponto final está localizado em http://*oseusite*.azurewebsites.net/swagger. Pode acessar a IU do Swagger através do `/swagger/ui` ponto final. Se optar por exigir a autenticação em toda o aplicativo, o Swagger produzirá um erro. Para obter melhores resultados, optar por permitir pedidos não autenticados nas definições de autenticação/autorização do serviço de aplicações do Azure e, em seguida, controlar autenticação com o `table.access` propriedade.

Também pode adicionar a opção de Swagger para o ficheiro de azureMobile.js, caso queira apenas suporte de Swagger para o desenvolvimento localmente.

## <a name="a-namepushpush-notifications"></a><a name="push"/>Notificações Push

Aplicações móveis integra-se com os Hubs de notificação do Azure para que possa enviar notificações push segmentadas para milhões de dispositivos em todas as plataformas principais. Ao utilizar os Hubs de notificação, pode enviar notificações push para iOS, Android e Windows dispositivos. Para saber mais sobre tudo o que pode fazer com os Hubs de notificação, consulte a [descrição geral dos Hubs de notificação](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Enviar notificações push

O código a seguir mostra como utilizar o `push` objeto para enviar uma notificação push de difusão para dispositivos iOS:

```javascript
// Create an APNS payload.
var payload = '{"aps": {"alert": "This is an APNS payload."}}';

// Only do the push if configured.
if (context.push) {
    // Send a push notification by using APNS.
    context.push.apns.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Ao criar um registo de modelo de push do cliente, em vez disso, pode enviar uma mensagem de push de modelo em dispositivos nas plataformas suportadas. O código seguinte mostra como enviar uma notificação de modelo:

```javascript
// Define the template payload.
var payload = '{"messageParam": "This is a template payload."}';

// Only do the push if configured.
if (context.push) {
    // Send a template notification.
    context.push.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

### <a name="push-user"></a>Enviar notificações push para um usuário autenticado através de etiquetas
Quando um usuário autenticado registra para notificações push, uma etiqueta de ID de utilizador é automaticamente adicionada ao registo. Ao utilizar esta etiqueta, pode enviar notificações push para todos os dispositivos registados por um utilizador específico. O código seguinte obtém o SID de utilizador que está fazendo a solicitação e envia uma notificação push de modelo para cada registo de dispositivos para que o utilizador:

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Quando estiver se registrar para notificações push a partir de um cliente autenticado, certifique-se de que a autenticação é concluída antes de tentar o registo.

## <a name="CustomAPI"></a> APIs personalizadas

### <a name="howto-customapi-basic"></a>Definir uma API personalizada

Além da API de acesso de dados via o `/tables` ponto de extremidade, aplicações móveis pode fornecer uma cobertura de API personalizada. As APIs personalizadas são definidos de forma semelhante para as definições de tabela e podem aceder a todos os mesmos recursos, incluindo a autenticação.

Se pretender utilizar a autenticação do serviço de aplicações com uma API personalizada, tem de configurar autenticação do serviço de aplicações no [portal do Azure] primeiro. Para obter mais informações, consulte o guia de configuração para o fornecedor de identidade que pretende utilizar:

* [Configurar a autenticação do Azure Active Directory]
* [Configurar a autenticação do Facebook]
* [Configurar a autenticação do Google]
* [Configurar a autenticação da Microsoft]
* [Configurar a autenticação do Twitter]

APIs personalizadas são definidas no quase da mesma forma como a API de tabelas:

1. Criar um `api` diretório.
1. Criar um ficheiro de JavaScript de definição de API no `api` diretório.
1. Utilize o método de importação para importar o `api` diretório.

Aqui está o protótipo de definição de API com base no exemplo de aplicação básico que utilizámos anteriormente:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

Vejamos um exemplo de API que devolve a data de servidor utilizando o `Date.now()` método. Este é o ficheiro de api/date.js:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Cada parâmetro é um dos verbos padrão RESTful: OBTER, publicar, aplicar um PATCH ou eliminar. O método é uma norma [ExpressJS middleware] função que envia a saída necessária.

### <a name="howto-customapi-auth"></a>Exigir a autenticação para acesso a uma API personalizada

O SDK de aplicações móveis implementa autenticação da mesma forma para ambos os `tables` ponto final e APIs personalizadas. Para adicionar autenticação para a API desenvolvida na secção anterior, adicione um `access` propriedade:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Também pode especificar a autenticação em operações específicas:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

O mesmo token que é utilizado para o `tables` ponto final tem de ser utilizada para APIs personalizadas que requerem autenticação.

### <a name="howto-customapi-auth"></a>Lidar com carregamentos de ficheiros grandes

O SDK de aplicações móveis utiliza a [middleware do analisador de corpo](https://github.com/expressjs/body-parser) para aceitar e decodificar o conteúdo do corpo na sua submissão. Pode pré-configurar o analisador de corpo para aceitar carrega o ficheiro de maior:

```javascript
var express = require('express'),
    bodyParser = require('body-parser'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Set up large body content handling.
app.use(bodyParser.json({ limit: '50mb' }));
app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP.
app.listen(process.env.PORT || 3000);
```

O ficheiro é base-64 codificada antes da transmissão. Essa codificação aumenta o tamanho do carregamento real (e o tamanho que tem em conta).

### <a name="howto-customapi-sql"></a>Executar instruções de SQL personalizadas

O SDK de aplicações móveis permite o acesso ao contexto inteiro por meio do objeto de solicitação. Pode executar facilmente instruções SQL parametrizadas para o fornecedor de dados definidos:

```javascript
var api = {
    get: function (request, response, next) {
        // Check for parameters. If not there, pass on to a later API call.
        if (typeof request.params.completed === 'undefined')
            return next();

        // Define the query. Anything that the mssql
        // driver can handle is allowed.
        var query = {
            sql: 'UPDATE TodoItem SET complete=@completed',
            parameters: [{
                completed: request.params.completed
            }]
        };

        // Execute the query. The context for Mobile Apps is available through
        // request.azureMobile. The data object contains the configured data provider.
        request.azureMobile.data.execute(query)
        .then(function (results) {
            response.json(results);
        });
    }
};

api.get.access = 'authenticated';
module.exports = api;
```

## <a name="Debugging"></a>Depuração, tabelas simples e APIs simples

### <a name="howto-diagnostic-logs"></a>Depurar, diagnosticar e resolver problemas de aplicações móveis

Serviço de aplicações do Azure fornece vários depuração e resolução de problemas de técnicas para aplicações node. js.
Para começar a utilizar no seu back-end de aplicações móveis do node. js de resolução de problemas, consulte os artigos seguintes:

* [Monitorização do serviço de aplicações do Azure]
* [Ativar o registo de diagnóstico no serviço de aplicações do Azure]
* [Resolver problemas relacionados com o serviço de aplicações do Azure no Visual Studio]

Aplicações node. js têm acesso a uma vasta gama de ferramentas de registo de diagnóstico. Internamente, o Mobile Apps node. js SDK usa [Winston] para registo de diagnósticos. O registo é ativado automaticamente ao ativar a depuração modo ou um conjunto a `MS_DebugMode` definição de aplicação como true no [portal do Azure]. Registos gerados aparecem nos registos de diagnóstico no [portal do Azure].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Trabalhar com tabelas simples no portal do Azure

Pode utilizar tabelas simples para criar e trabalhar com o direito de tabelas no portal. Pode carregar o conjunto de dados para tabelas simples no formato CSV. Tenha em atenção que não é possível utilizar nomes de propriedade (no seu conjunto de dados do CSV), que entram em conflito com nomes de propriedades do sistema de back-end das aplicações móveis. Os nomes de propriedade de sistema são:
* createdAt
* updatedAt
* eliminado
* versão

Ainda pode editar as operações de tabela com o Editor do serviço de aplicações. Quando seleciona **tabelas simples** nas definições do site de back-end, pode adicionar, modificar ou eliminar uma tabela. Também pode ver dados da tabela.

![Trabalhar com tabelas simples](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Os comandos seguintes estão disponíveis na barra de comandos para uma tabela:

* **Alterar permissões**: Modificar as permissões de leitura, inserir, atualizar e eliminar operações na tabela.
 As opções são para permitir o acesso anônimo, para exigir a autenticação, ou para desativar todos os acessos para a operação.
* **Editar script**: O ficheiro de script para a tabela é aberto no Editor de serviço de aplicações.
* **Gerir esquema**: Adicionar ou eliminar colunas ou alterar o índice de tabela.
* **Limpar tabela**: Truncar uma tabela existente, eliminando todas as linhas de dados, mas deixar o esquema inalterado.
* **Eliminar linhas**: Elimine linhas individuais de dados.
* **Ver registos de transmissão em fluxo**: Liga ao serviço de log de transmissão em fluxo para o seu site.

### <a name="work-easy-apis"></a>Trabalhar com APIs simples no portal do Azure

Pode utilizar APIs simples para criar e trabalhar com o direito APIs personalizado no portal. Pode editar scripts de API com o Editor do serviço de aplicações.

Quando seleciona **APIs simples** nas definições do site de back-end, pode adicionar, modificar ou eliminar um ponto final da API personalizado.

![Trabalhar com APIs simples](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

No portal, pode alterar as permissões de acesso de uma ação de HTTP, edite o ficheiro de script no Editor de serviço de aplicações de API ou ver os registos de transmissão em fluxo.

### <a name="online-editor"></a>Editar o código no Editor de serviço de aplicações

Ao utilizar o portal do Azure, pode editar os ficheiros de script de back-end de node. js no Editor de serviço de aplicações sem ter de transferir o projeto para o computador local. Para editar ficheiros de script no online editor:

1. No painel para as suas aplicações de Mobile back-end, selecione **todas as definições** > qualquer um dos **tabelas simples** ou **APIs simples**. Selecione uma tabela ou a API e, em seguida, selecione **Editar script**. O ficheiro de script é aberto no Editor de serviço de aplicações.

   ![Editor do Serviço de Aplicações](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
1. Faça as alterações para o arquivo de código no online editor. As alterações são guardadas automaticamente à medida que escreve.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Início rápido de cliente Android]: app-service-mobile-android-get-started.md
[Início rápido de cliente do Apache Cordova]: app-service-mobile-cordova-get-started.md
[início rápido de cliente do iOS]: app-service-mobile-ios-get-started.md
[Início rápido de cliente do xamarin. IOS]: app-service-mobile-xamarin-ios-get-started.md
[Início rápido de cliente do xamarin. Android]: app-service-mobile-xamarin-android-get-started.md
[Início rápido de cliente do xamarin. Forms]: app-service-mobile-xamarin-forms-get-started.md
[Início rápido de cliente do Windows Store]: app-service-mobile-windows-store-dotnet-get-started.md
[sincronização de dados offline]: app-service-mobile-offline-data-sync.md
[Configurar a autenticação do Azure Active Directory]: ../app-service/configure-authentication-provider-aad.md
[Configurar a autenticação do Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Configurar a autenticação do Google]: ../app-service/configure-authentication-provider-google.md
[Configurar a autenticação da Microsoft]: ../app-service/configure-authentication-provider-microsoft.md
[Configurar a autenticação do Twitter]: ../app-service/configure-authentication-provider-twitter.md
[Guia de implementação do serviço de aplicações do Azure]: ../app-service/deploy-local-git.md
[Monitorização do serviço de aplicações do Azure]: ../app-service/web-sites-monitor.md
[Ativar o registo de diagnóstico no serviço de aplicações do Azure]: ../app-service/troubleshoot-diagnostic-logs.md
[Resolver problemas relacionados com o serviço de aplicações do Azure no Visual Studio]: ../app-service/troubleshoot-dotnet-visual-studio.md
[Especifique a versão de nó]: ../nodejs-specify-node-version-azure-apps.md
[utilizar módulos do nó]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Portal do Azure]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promessa]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[exemplo de basicapp no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[lista de tarefas exemplo no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[diretório de exemplos no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Ferramentas do node. js 1.1 para o Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

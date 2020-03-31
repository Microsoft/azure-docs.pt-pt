---
title: Trabalhe com o Node.js Back-End Server SDK
description: Saiba como trabalhar com o Node.js Back-End Server SDK para aplicações móveis do Serviço de Aplicações Azure.
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 566b6db829c9694a7e5e83cc25695e71ada0bc8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249948"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Como utilizar as Aplicações Móveis Node.js SDK

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este artigo fornece informações detalhadas e exemplos que mostram como trabalhar com um node.js back end na funcionalidade de Aplicações Móveis do Azure App Service.

## <a name="introduction"></a><a name="Introduction"></a>Introdução

As Aplicações Móveis fornecem a capacidade de adicionar um API de acesso a dados otimizado para dispositivos móveis a uma aplicação web. O Mobile Apps SDK está previsto para aplicações web ASP.NET e Node.js. O SDK fornece as seguintes operações:

* Operações de mesa (ler, inserir, atualizar, excluir) para acesso a dados
* Operações API personalizadas

Ambas as operações prevêem a autenticação em todos os fornecedores de identidade que o Azure App Service permite. Estes fornecedores incluem fornecedores de identidade social como Facebook, Twitter, Google e Microsoft, bem como O Diretório Ativo Azure para identidade empresarial.

Pode encontrar amostras para cada caso de utilização no [diretório de amostras no GitHub].

## <a name="supported-platforms"></a>Plataformas suportadas

O Mobile Apps Node.js SDK suporta a versão atual lts do Nó e posteriormente. Atualmente, a versão mais recente lts é nonó v4.5.0. Outras versões do Nó podem funcionar, mas não são suportadas.

O Mobile Apps Node.js SDK suporta dois controladores de base de dados: 

* O controlador de nó-mssql suporta a Base de Dados Azure SQL e as instâncias locais do Servidor SQL.  
* O condutor sqlite3 suporta bases de dados SQLite numa única instância.

### <a name="create-a-basic-nodejs-back-end-by-using-the-command-line"></a><a name="howto-cmdline-basicapp"></a>Crie um node.js básico de volta usando a linha de comando

Todas as aplicações móveis Node.js back end começa como uma aplicação ExpressJS. ExpressJS é o quadro de serviço web mais popular disponível para Node.js. Pode criar uma [aplicação] expressa básica da seguinte forma:

1. Numa janela de comando ou PowerShell, crie um diretório para o seu projeto:

        mkdir basicapp

1. Correr `npm init` para inicializar a estrutura do pacote:

        cd basicapp
        npm init

   O `npm init` comando faz um conjunto de perguntas para inicializar o projeto. Veja a saída do exemplo:

   ![A saída de npm init][0]

1. Instale `express` `azure-mobile-apps` as e bibliotecas do repositório npm:

        npm install --save express azure-mobile-apps

1. Criar um ficheiro app.js para implementar o servidor móvel básico:

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

Esta aplicação cria uma Web API otimizada`/tables/TodoItem`para dispositivos móveis com um único ponto final que proporciona acesso não autenticado a uma loja de dados SQL subjacente utilizando um esquema dinâmico. É adequado para seguir os quickstarts da biblioteca do cliente:

* [Quickstart do cliente Android]
* [Cliente Apache Cordova quickstart]
* [iOS Cliente quickstart]
* [Cliente da Windows Store quickstart]
* [Xamarin.iOS cliente quickstart]
* [Xamarin.Cliente Android quickstart]
* [Xamarin.Forma cliente quickstart]

Pode encontrar o código para esta aplicação básica na [amostra basicapp no GitHub].

### <a name="create-a-nodejs-back-end-by-using-visual-studio-2015"></a><a name="howto-vs2015-basicapp"></a>Crie um Node.js back end usando Visual Studio 2015

O Visual Studio 2015 requer uma extensão para desenvolver aplicações nonóios dentro do IDE. Para iniciar, instale as [Ferramentas Node.js 1.1 para Estúdio Visual]. Quando terminar a instalação, crie uma aplicação Express 4.x:

1. Abra a caixa de diálogo **New Project** (a partir do **Arquivo** > **Novo** > **Projeto).**
1. Expandir **modelos** > **JavaScript** > **Node.js**.
1. Selecione **Basic Azure Node.js Express 4 Application**.
1. Preencha o nome do projeto. Selecione **OK**.

   ![Novo projeto do Visual Studio 2015][1]
1. Clique no nó **npm** e selecione **Instalar novos pacotes npm**.
1. Poderá ter de renovar o catálogo da NPM depois de criar a sua primeira aplicação Node.js. Selecione **Refresh** se necessário.
1. Introduza **aplicativos azure-mobile** na caixa de pesquisa. Selecione o pacote **2.0.0 de aplicações móveis e,** em seguida, selecione **Install Package**.

   ![Instale novos pacotes npm][2]
1. Selecione **Fechar**.
1. Abra o ficheiro app.js para adicionar suporte para o Mobile Apps SDK. Na linha 6, na `require` parte inferior das declarações da biblioteca, adicione o seguinte código:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Na linha 27, aproximadamente, após as outras `app.use` declarações, adicione o seguinte código:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Guarde o ficheiro.

1. Ou executa a aplicação localmente `http://localhost:3000`(a API é servida) ou publica para o Azure.

### <a name="create-a-nodejs-back-end-by-using-the-azure-portal"></a><a name="create-node-backend-portal"></a>Crie um Lado atrás do Nó usando o portal Azure

Pode criar uma aplicação móvel no [portal Azure.] Pode completar os seguintes passos ou criar um cliente e servidor em conjunto seguindo o tutorial de [aplicação create a mobile.](app-service-mobile-ios-get-started.md) O tutorial contém uma versão simplificada destas instruções e é o melhor para projetos de prova de conceito.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

De volta ao painel **Get started,** sob **Criar uma Tabela API,** escolha **Node.js** como a sua língua de back-end.
Selecione a caixa para **eu reconhecer que isto irá sobrepor todos os conteúdos**do site , e, em seguida, selecione tabela Create **TodoItem**.

### <a name="download-the-nodejs-back-end-quickstart-code-project-by-using-git"></a><a name="download-quickstart"></a>Descarregue o projeto de código quickstart back-end node.js usando Git

Quando cria um Node.js Mobile Apps no final, utilizando o painel **de arranque rápido** do portal, é criado um projeto Node.js para si e implantado no seu site. No portal, pode adicionar tabelas e APIs e editar ficheiros de código para a extremidade traseira do Nó.js. Também pode utilizar várias ferramentas de implementação para descarregar o projeto back-end para que possa adicionar ou modificar tabelas e APIs e, em seguida, reeditar o projeto. Para mais informações, consulte o guia de implementação do Serviço de [Aplicações Azure].

O seguinte procedimento utiliza um repositório Git para descarregar o código do projeto quickstart:

1. Instale Git, se ainda não o fez. Os passos necessários para instalar o Git variam entre os sistemas operativos. Para distribuição e orientação de instalação específicas do sistema operativo, consulte [instalação git](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Consulte [Prepare o seu repositório](../app-service/deploy-local-git.md#prepare-your-repository) para ativar o repositório Git para o seu site de back-end. Tome nota do nome de utilizador de implementação e palavra-passe.
3. No painel para as suas Aplicações Móveis, tome nota da definição de URL do **clone Git.**
4. Execute `git clone` o comando utilizando o URL do clone Git. Introduza a sua palavra-passe quando necessário, como no seguinte exemplo:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Navegue no diretório`/todolist` local (no exemplo anterior) e note que os ficheiros do projeto foram descarregados. Localize o ficheiro todoitem.json no `/tables` diretório. Este ficheiro define permissões na mesa. Também encontre o ficheiro todoitem.js no mesmo diretório. Define os scripts de operação CRUD para a mesa.
6. Depois de efazer alterações nos ficheiros do projeto, executar os seguintes comandos para adicionar, comprometer e, em seguida, fazer upload das alterações para o site:

        $ git commit -m "updated the table script"
        $ git push origin master

   Quando adiciona novos ficheiros ao projeto, primeiro `git add .` tem de executar o comando.

O site é republicado cada vez que um novo conjunto de compromissos é empurrado para o site.

### <a name="publish-your-nodejs-back-end-to-azure"></a><a name="howto-publish-to-azure"></a>Publique o seu Lado de Volta do Nó ao Azure

O Microsoft Azure fornece muitos mecanismos para a publicação das suas aplicações móveis Node.js no final do serviço Azure. Estes mecanismos incluem ferramentas de implementação integradas no Estúdio Visual, ferramentas de linha de comando e opções de implementação contínua seladas com base no controlo de fonte. Para mais informações, consulte o guia de implementação do Serviço de [Aplicações Azure].

O Azure App Service tem conselhos específicos para aplicações node.js que deve rever antes de publicar o back end:

* Como [especificar a versão nó]
* Como [usar módulos de nó]

### <a name="enable-a-home-page-for-your-application"></a><a name="howto-enable-homepage"></a>Ativar uma página inicial para a sua aplicação

Muitas aplicações são uma combinação de aplicações web e móveis. Pode usar a estrutura expressJS para combinar as duas facetas. Por vezes, no entanto, é melhor implementar apenas uma interface móvel. É útil fornecer uma página inicial para garantir que o serviço de aplicações está em funcionamento. Pode fornecer a sua própria página inicial ou ativar uma página inicial temporária. Para ativar uma página inicial temporária, utilize o seguinte código para instantaneamente aplicações móveis:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Se só quiser esta opção disponível no desenvolvimento local, pode adicionar esta definição ao seu ficheiro azureMobile.js.

## <a name="table-operations"></a><a name="TableOperations"></a>Operações de mesa

O SDK de aplicativos azure-mobile Node.js Server SDK fornece mecanismos para expor as tabelas de dados armazenadas na Base de Dados Azure SQL como uma API Web. Fornece cinco operações:

| Operação | Descrição |
| --- | --- |
| GET /mesas/*nome de mesa* |Pôr todos os registos na mesa. |
| GET /mesas/*tablename*/:id |Arranja um registo específico na mesa. |
| POST /tabelas/*nome de mesa* |Crie um recorde na mesa. |
| PATCH /tabelas/*tablename*/:id |Atualize um recorde na tabela. |
| DELETE /tabelas/*tablename*/:id |Apague um disco na mesa. |

Esta Web API suporta o [OData] e alarga o esquema de tabela para suportar a [sincronização de dados offline].

### <a name="define-tables-by-using-a-dynamic-schema"></a><a name="howto-dynamicschema"></a>Defina tabelas usando um esquema dinâmico

Antes de poder usar uma mesa, deve defini-la. Pode definir tabelas utilizando um esquema estático (onde define as colunas no esquema) ou dinamicamente (onde o SDK controla o esquema com base nos pedidos de entrada). Além disso, pode controlar aspetos específicos da API Web adicionando código JavaScript à definição.

Como uma boa prática, deve definir cada tabela `tables` num ficheiro JavaScript `tables.import()` no diretório e, em seguida, utilizar o método para importar as tabelas. Alargando a amostra de aplicação básica, ajustaria o ficheiro app.js:

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

Defina a tabela em ./tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

As tabelas usam um esquema dinâmico por defeito. Para desligar o esquema dinâmico a nível global, defina a definição de `MS_DynamicSchema` aplicação como falsa no portal Azure.

Pode encontrar um exemplo completo na [amostra de todo-o-dia no GitHub].

### <a name="define-tables-by-using-a-static-schema"></a><a name="howto-staticschema"></a>Definir tabelas utilizando um esquema estático

Pode definir explicitamente as colunas para expor através da API web. O Node.js SDK de aplicações azure-mobile adiciona automaticamente quaisquer colunas extra necessárias para sincronização de dados offline à lista que fornece. Por exemplo, as aplicações de cliente quickstart `text` requerem uma `complete` tabela com duas colunas: (uma corda) e (uma Boolean).  
A tabela pode ser definida na definição de `tables` tabela JavaScript (localizada no diretório) da seguinte forma:

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

Se definir as tabelas estáticamente, também deve ligar para o `tables.initialize()` método de criar o esquema de base de dados no arranque. O `tables.initialize()` método devolve uma [promessa] para que o serviço web não sirva pedidos antes da base de dados ser inicializada.

### <a name="use-sql-server-express-as-a-development-data-store-on-your-local-machine"></a><a name="howto-sqlexpress-setup"></a>Use o SQL Server Express como uma loja de dados de desenvolvimento na sua máquina local

O Mobile Apps Node.js SDK fornece três opções para servir dados fora da caixa:

* Utilize o controlador de **memória** para fornecer uma loja de exemplos não persistente.
* Utilize o controlador **mssql** para fornecer uma loja de dados SQL Server Express para desenvolvimento.
* Utilize o controlador **mssql** para fornecer uma loja de dados Azure SQL Database para produção.

O Mobile Apps Node.js SDK utiliza o [pacote Mssql Node.js] para estabelecer e utilizar uma ligação tanto ao SQL Server Express como à Base de Dados SQL Server. Este pacote requer que ative as ligações TCP na sua instância SQL Server Express.

> [!TIP]
> O controlador de memória não fornece um conjunto completo de instalações para testes. Se quiser testar a sua parte traseira localmente, recomendamos a utilização de uma loja de dados SQL Server Express e do controlador mssql.

1. Descarregue e instale [o Microsoft SQL Server 2014 Express]. Certifique-se de que instala o Expresso SQL Server 2014 com edição de Ferramentas. A menos que necessite explicitamente de suporte de 64 bits, a versão de 32 bits consome menos memória ao correr.
1. Executar o Gestor de Configuração do Servidor SQL 2014:

   a. Expanda o nó de configuração da configuração da **rede do servidor SQL** no menu de árvores.

   b. Selecione **Protocolos para SQLEXPRESS**.

   c. Clique à direita **TCP/IP** e selecione **Ativar**. Selecione **OK** na caixa de diálogo pop-up.

   d. Clique à direita **TCP/IP** e selecione **Propriedades**.

   e. Selecione o separador **endereços IP.**

   f. Encontre o nó **IPAll.** No campo **da Porta TCP,** insira **1433**.

      ![Configure O Expresso do Servidor SQL para TCP/IP][3]

   g. Selecione **OK**. Selecione **OK** na caixa de diálogo pop-up.

   h. Selecione **Serviços de Servidor Estivar SQL** no menu de árvores.

   i. Clique à direita **no Servidor SQL (SQLEXPRESS)** e selecione **Reiniciar**.

   j. Feche o Gestor de Configuração do Servidor SQL 2014.

1. Executar o Estúdio de Gestão SQL Server 2014 e ligar-se à sua instância local do SQL Server Express:

   1. Clique na sua instância no Object Explorer e selecione **Propriedades**.
   1. Selecione a página **de Segurança.**
   1. Certifique-se de que o **servidor SQL e** o modo de autenticação do Windows estão selecionados.
   1. Selecione **OK**.

      ![Configure a autenticação Expressa do Servidor SQL][4]
   1. Expandir**logins de** **segurança** > no Explorador de Objetos.
   1. Clique no **login sinos** e selecione **New Login**.
   1. Introduza um nome de login. Selecione **Autenticação do SQL Server**. Introduza uma palavra-passe e introduza a mesma palavra-passe em **Confirmar palavra-passe**. A palavra-passe deve satisfazer os requisitos de complexidade do Windows.
   1. Selecione **OK**.

      ![Adicione um novo utilizador ao SQL Server Express][5]
   1. Clique no seu novo login e selecione **Propriedades**.
   1. Selecione a página funções do **Servidor.**
   1. Selecione a caixa de verificação para a função do servidor **dbcreator.**
   1. Selecione **OK**.
   1. Feche o Estúdio de Gestão SQL Server 2015.

Certifique-se de gravar o nome de utilizador e a palavra-passe que selecionou. Poderá ser necessário atribuir funções ou permissões adicionais ao servidor, dependendo dos requisitos da sua base de dados.

A aplicação Node.js lê a `SQLCONNSTR_MS_TableConnectionString` variável ambiental para a cadeia de ligação para esta base de dados. Pode definir esta variável no seu ambiente. Por exemplo, pode usar o PowerShell para definir esta variável ambiental:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Aceda à base de dados através de uma ligação TCP/IP. Forneça um nome de utilizador e uma senha para a ligação.

### <a name="configure-your-project-for-local-development"></a><a name="howto-config-localdev"></a>Configure o seu projeto para o desenvolvimento local

As Aplicações Móveis lêum ficheiro JavaScript chamado *azureMobile.js* do sistema de ficheiros local. Não utilize este ficheiro para configurar as Aplicações Móveis SDK em produção. Em vez disso, utilize **as definições** da App no [portal Azure].

O ficheiro azureMobile.js deve exportar um objeto de configuração. As configurações mais comuns são:

* Definições de base de dados
* Definições de registo de diagnóstico
* Definições alternativas de CORS

Este exemplo de ficheiro **azureMobile.js** implementa as definições de base de dados anteriores:

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

Recomendamos que adicione o ficheiro **.gitignore** **(ou** outro controlo de código de origem) para evitar que as palavras-passe sejam armazenadas na nuvem. Configure sempre as **definições** de produção nas definições da App dentro do [portal Azure].

### <a name="configure-app-settings-for-your-mobile-app"></a><a name="howto-appsettings"></a>Configure as definições de aplicativos para a sua aplicação móvel

A maioria das definições no ficheiro azureMobile.js têm uma configuração de aplicação equivalente no [portal Azure]. Utilize a seguinte lista para configurar a sua aplicação nas definições da **App:**

| Definição de aplicativos | definição azureMobile.js | Descrição | Valores válidos |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |nome |Nome da app |string |
| **MS_MobileLoggingLevel** |login.nível |Nível mínimo de registo de mensagens para registar |erro, aviso, informação, verbosa, depuração, tolo |
| **MS_DebugMode** |depurar |Ativa ou desativa o modo de depuração |TRUE, false |
| **MS_TableSchema** |data.schema |Nome de esquema padrão para tabelas SQL |cadeia (padrão: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Ativa ou desativa o modo de depuração |TRUE, false |
| **MS_DisableVersionHeader** |versão (definida para indefinida) |Desativa o cabeçalho X-ZUMO-Server-Version |TRUE, false |
| **MS_SkipVersionCheck** |skipversioncheck |Desativa a verificação da versão API do cliente |TRUE, false |

Para definir uma definição de aplicação:

1. Inicie sessão no [Portal do Azure].
1. Selecione **Todos os recursos** ou **Serviços de Aplicações**e, em seguida, selecione o nome da sua aplicação móvel.
1. O painel **Definições** abre por defeito. Se não o fizer, selecione **Definições**.
1. No menu **GENERAL,** selecione **as definições**de Aplicação .
1. Percorra a secção de definições da **App.**
1. Se a definição da sua aplicação já existir, selecione o valor da definição da aplicação para editar o valor.
   Se a definição da sua aplicação não existir, introduza a definição da aplicação na caixa **Chave** e o valor na caixa **Value.**
1. Selecione **Guardar**.

Alterar a maioria das definições de aplicações requer um reinício do serviço.

### <a name="use-sql-database-as-your-production-data-store"></a><a name="howto-use-sqlazure"></a>Utilize a Base de Dados SQL como a sua loja de dados de produção

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

A utilização da Base de Dados Azure SQL como uma loja de dados é idêntica em todos os tipos de aplicações do Serviço de Aplicações Azure. Se ainda não o fez, siga estes passos para criar uma extremidade mobile Apps:

1. Inicie sessão no [Portal do Azure].
1. Na parte superior esquerda da janela, selecione o botão **+NEW** > **Web + Mobile** > **Mobile App,** e, em seguida, forneça um nome para as suas Aplicações Móveis na extremidade traseira.
1. Na caixa do **Grupo de Recursos,** introduza o mesmo nome que a sua aplicação.
1. O plano de serviço de aplicações padrão é selecionado. Se quiser alterar o seu plano de Serviço de Aplicações:

   a. Selecione **Plano** > de Serviço de Aplicações **+Criar Novo**.

   b. Forneça um nome do novo plano de Serviço de Aplicações e selecione uma localização apropriada.

   c. Selecione um nível de preços adequado para o serviço. Selecione **Ver tudo** para ver mais opções de preços, como **Grátis** e **Partilhados**.

   d. Clique no botão **Selecionar.**

   e. De volta ao painel do **plano do Serviço de Aplicações,** selecione **OK**.
1. Selecione **Criar**.

Fornecer uma aplicação móvel na parte de trás pode demorar alguns minutos. Depois de o back back end das Aplicações Móveis ser aprovisionado, o portal abre o painel **Definições** para as Aplicações Móveis de volta.

Pode optar por ligar uma base de dados SQL existente às suas Aplicações Móveis ou criar uma nova base de dados SQL. Nesta secção, criamos uma base de dados SQL.

> [!NOTE]
> Se já tiver uma base de dados no mesmo local que as Aplicações Móveis, pode, em vez disso, selecionar **Utilize uma base** de dados existente e, em seguida, selecione essa base de dados. Não recomendamos o uso de uma base de dados num local diferente por causa de tardios mais elevados.

1. Nas novas Aplicações Móveis,**Data** > selecione **Definições** > Dados de**Aplicações** > Móveis **+Adicionar**.
1. No painel de ligação de **dados Adicionar,** selecione Base de **Dados SQL - Configurar as definições necessárias** > **Crie uma nova base de dados**. Introduza o nome da nova base de dados na caixa **nome.**
1. Selecione **Server**. No painel do **novo servidor,** introduza um nome de servidor único na caixa de **nome** siny e forneça um login e senha de entrada de servidor adequados. Certifique-se de que **os serviços azure acederao servidor** são selecionados. Selecione **OK**.

   ![Criar uma Base de Dados SQL do Azure][6]
1. No painel de bases de **dados Novo,** selecione **OK**.
1. Volte ao painel de ligação de **dados Adicionar,** selecione a cadeia de **ligação**e introduza o login e a palavra-passe que forneceu quando criou a base de dados. Se utilizar uma base de dados existente, forneça as credenciais de login para essa base de dados. Selecione **OK**.
1. De volta ao painel de **ligação** adicionar dados novamente, selecione **OK** para criar a base de dados.

<!--- END OF ALTERNATE INCLUDE -->

A criação da base de dados pode demorar alguns minutos. Utilize a área de **Notificações** para monitorizar o progresso da implantação. Não progrida até que a base de dados seja implantada com sucesso. Após a implementação da base de dados, é criada uma cadeia de ligação para a instância de Base de Dados SQL nas definições de aplicações de back-end das aplicações mobile Apps. Pode ver esta definição de aplicação nas definições de **definições** > **de definições** > de definições de definições de**ligação**.

### <a name="require-authentication-for-access-to-tables"></a><a name="howto-tables-auth"></a>Exigir autenticação para acesso às tabelas

Se pretender utilizar a Autenticação `tables` do Serviço app com o ponto final, tem de configurar primeiro a Autenticação do Serviço de Aplicações no [portal Azure.] Para mais informações, consulte o guia de configuração do fornecedor de identidade que pretende utilizar:

* [Configurar a autenticação do Azure Active Directory]
* [Configure a autenticação do Facebook]
* [Configure a autenticação do Google]
* [Configure a autenticação da Microsoft]
* [Configure a autenticação do Twitter]

Cada mesa tem uma propriedade de acesso que pode usar para controlar o acesso à mesa. A amostra seguinte mostra uma tabela estática com autenticação necessária.

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

* *anónimo* indica que a aplicação do cliente é permitida a ler dados sem autenticação.
* *autenticado* indica que a aplicação do cliente deve enviar um símbolo de autenticação válido com o pedido.
* *desativado* indica que esta tabela está atualmente desativada.

Se a propriedade de acesso não for definida, é permitido um acesso indefinido e inautenticado.

### <a name="use-authentication-claims-with-your-tables"></a><a name="howto-tables-getidentity"></a>Use reclamações de autenticação com as suas tabelas
Pode configurar várias reclamações que são solicitadas quando a autenticação for configurada. Estas alegações não estão normalmente disponíveis através do `context.user` objeto. No entanto, pode recuperá-los utilizando o `context.user.getIdentity()` método. O `getIdentity()` método devolve uma promessa que resolve um objeto. O objeto é keyed pelo`facebook`método `google` `twitter`de `microsoftaccount`autenticação ( , , , ou `aad`).

Por exemplo, se configurar a autenticação da conta microsoft e solicitar a reclamação de endereços de e-mail, pode adicionar o endereço de e-mail ao registo com o seguinte controlador de tabela:

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

Para ver quais as reclamações disponíveis, `/.auth/me` utilize um navegador web para ver o ponto final do seu site.

### <a name="disable-access-to-specific-table-operations"></a><a name="howto-tables-disabled"></a>Desativar o acesso a operações específicas de tabela

Além de aparecer em cima da mesa, a propriedade de acesso pode ser usada para controlar operações individuais. Há quatro operações:

* `read`é a operação RESTful GET em cima da mesa.
* `insert`é a operação RESTful POST em cima da mesa.
* `update`é a operação RESTful PATCH em cima da mesa.
* `delete`é a operação RESTful DELETE sobre a mesa.

Por exemplo, é melhor fornecer uma mesa não autenticada de leitura:

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

### <a name="adjust-the-query-that-is-used-with-table-operations"></a><a name="howto-tables-query"></a>Ajuste a consulta que é usada com operações de mesa

Um requisito comum para as operações de mesa é fornecer uma visão restrita dos dados. Por exemplo, pode fornecer uma tabela que esteja marcada com o ID autenticado do utilizador de modo a que só possa ler ou atualizar os seus próprios registos. A seguinte definição de tabela fornece esta funcionalidade:

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

As operações que normalmente executam uma consulta têm `where` uma propriedade de consulta que você pode ajustar usando uma cláusula. A propriedade de consulta é um objeto [QueryJS] que é usado para converter uma consulta OData para algo que o final de dados pode processar. Para casos simples de igualdade (como o anterior), pode usar um mapa. Também pode adicionar cláusulas SQL específicas:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="configure-a-soft-delete-on-a-table"></a><a name="howto-tables-softdelete"></a>Configure um suave apague numa mesa

Um apague suavemente não apaga os registos. Em vez disso, marca-os como eliminados na base de dados, definindo a coluna eliminada como verdadeira. O Mobile Apps SDK remove automaticamente os registos eliminados dos `IncludeDeleted()`resultados a menos que o Mobile Client SDK utilize . Para configurar uma tabela para uma `softDelete` eliminação suave, coloque a propriedade no ficheiro de definição de tabela:

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

Deve estabelecer um mecanismo de abater registos: uma aplicação de cliente, um WebJob, uma função Azure ou uma API personalizada.

### <a name="seed-your-database-with-data"></a><a name="howto-tables-seeding"></a>Semente a sua base de dados com dados

Quando está a criar uma nova aplicação, é melhor semear uma tabela com dados. Pode fazê-lo dentro da definição de tabela JavaScript da seguinte forma:

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

A sementeir de dados só acontece quando se utilizou o SDK de Aplicações Móveis para criar a tabela. Se a tabela já existir na base de dados, não são injetados dados na tabela. Se o esquema dinâmico for ligado, o esquema é inferido a partir dos dados semeados.

Recomendamos que ligue explicitamente para o `tables.initialize()` método para criar a tabela quando o serviço começar a funcionar.

### <a name="enable-swagger-support"></a><a name="Swagger"></a>Ativar o suporte swagger
Mobile Apps vem com suporte [swagger] incorporado. Para permitir o suporte swagger, primeiro instale swagger-ui como uma dependência:

    npm install --save swagger-ui

Em seguida, pode ativar o suporte swagger no construtor de Aplicações Móveis:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Provavelmente só quer permitir o apoio da Swagger em edições de desenvolvimento. Pode fazê-lo `NODE_ENV` utilizando a definição da aplicação:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

O `swagger` ponto final está localizado em http://*seu site*.azurewebsites.net/swagger. Pode aceder à UI `/swagger/ui` Swagger através do ponto final. Se optar por exigir a autenticação em toda a sua aplicação, a Swagger produz um erro. Para obter os melhores resultados, opte por permitir pedidos não autenticados nas definições de `table.access` Autenticação/Autorização do Serviço de Aplicação Azure e, em seguida, controlar a autenticação utilizando a propriedade.

Também pode adicionar a opção Swagger ao seu ficheiro azureMobile.js se apenas quiser suporte Swagger para desenvolver localmente.

## <a name="push-notifications"></a><a name="push"/>Notificações push

As Aplicações Móveis integram-se com hubs de notificação do Azure para que possa enviar notificações push direcionadas a milhões de dispositivos em todas as grandes plataformas. Ao utilizar centros de notificação, pode enviar notificações push para dispositivos iOS, Android e Windows. Para saber mais sobre tudo o que pode fazer com Centros de Notificação, consulte a visão geral dos Centros de [Notificação.](../notification-hubs/notification-hubs-push-notification-overview.md)

### <a name="send-push-notifications"></a><a name="send-push"></a>Enviar notificações push

O seguinte código mostra `push` como utilizar o objeto para enviar uma notificação push de transmissão para dispositivos iOS registados:

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

Ao criar um registo de impulso de modelo do cliente, pode, em vez disso, enviar uma mensagem de push modelo para dispositivos em todas as plataformas suportadas. O seguinte código mostra como enviar uma notificação de modelo:

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

### <a name="send-push-notifications-to-an-authenticated-user-by-using-tags"></a><a name="push-user"></a>Envie notificações push a um utilizador autenticado utilizando tags
Quando um utilizador autenticado se regista para notificações push, uma etiqueta de identificação do utilizador é automaticamente adicionada ao registo. Ao utilizar esta etiqueta, pode enviar notificações push para todos os dispositivos registados por um utilizador específico. O seguinte código recebe o SID do utilizador que está a fazer o pedido e envia uma notificação de push modelo para cada registo do dispositivo para esse utilizador:

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

Ao registar-se para notificações push de um cliente autenticado, certifique-se de que a autenticação está completa antes de tentar o registo.

## <a name="custom-apis"></a><a name="CustomAPI"></a>APIs personalizados

### <a name="define-a-custom-api"></a><a name="howto-customapi-basic"></a>Defina uma API personalizada

Além da API de `/tables` acesso a dados através do ponto final, as Aplicações Móveis podem fornecer cobertura API personalizada. As APIs personalizadas são definidas de forma semelhante às definições de tabela e podem aceder a todas as mesmas instalações, incluindo a autenticação.

Se pretender utilizar a Autenticação do Serviço app com uma API personalizada, tem de configurar primeiro a Autenticação do Serviço de Aplicações no [portal Azure.] Para mais informações, consulte o guia de configuração do fornecedor de identidade que pretende utilizar:

* [Configurar a autenticação do Azure Active Directory]
* [Configure a autenticação do Facebook]
* [Configure a autenticação do Google]
* [Configure a autenticação da Microsoft]
* [Configure a autenticação do Twitter]

As APIs personalizadas são definidas da mesma forma que as Tabelas API:

1. Criar `api` um diretório.
1. Crie um ficheiro JavaScript `api` de definição API no diretório.
1. Utilize o método de `api` importação para importar o diretório.

Aqui está a definição de API do protótipo com base na amostra de aplicação básica que usamos anteriormente:

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

Vejamos um exemplo de API que devolve `Date.now()` a data do servidor utilizando o método. Aqui está o ficheiro api/date.js:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Cada parâmetro é um dos verbos RESTful padrão: GET, POST, PATCH ou DELETE. O método é uma função [de middleware expressJS] padrão que envia a saída necessária.

### <a name="require-authentication-for-access-to-a-custom-api"></a><a name="howto-customapi-auth"></a>Exigir autenticação para acesso a uma API personalizada

O Mobile Apps SDK implementa a autenticação `tables` da mesma forma tanto para o ponto final como para as APIs personalizadas. Para adicionar autenticação à API desenvolvida na secção `access` anterior, adicione um imóvel:

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

O mesmo símbolo que é `tables` usado para o ponto final deve ser utilizado para APIs personalizados que exijam autenticação.

### <a name="handle-large-file-uploads"></a><a name="howto-customapi-auth"></a>Manuseie grandes uploads de ficheiros

O Mobile Apps SDK utiliza o [body-parser middleware](https://github.com/expressjs/body-parser) para aceitar e descodificar conteúdo corporal na sua submissão. Pode configurar o body-parser para aceitar uploads de ficheiros maiores:

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

O ficheiro é codificado antes da transmissão. Esta codificação aumenta o tamanho do carregamento real (e o tamanho que deve prestar).

### <a name="execute-custom-sql-statements"></a><a name="howto-customapi-sql"></a>Executar declarações SQL personalizadas

O Mobile Apps SDK permite o acesso a todo o contexto através do objeto de pedido. Pode executar facilmente declarações SQL parametrizadas ao fornecedor de dados definidos:

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

## <a name="debugging"></a><a name="Debugging"></a>Depurar

### <a name="debug-diagnose-and-troubleshoot-mobile-apps"></a><a name="howto-diagnostic-logs"></a>Depuração, diagnóstico e resolução de problemas aplicativos móveis

O Azure App Service fornece várias técnicas de depuração e resolução de problemas para aplicações Node.js.
Para começar a resolver problemas na resolução das suas Aplicações Móveis Node.js, consulte os seguintes artigos:

* [Monitorização do Serviço de Aplicações Azure]
* [Ativar a exploração de diagnóstico no Serviço de Aplicações Azure]
* [Troubleshoot Azure App Service em Estúdio Visual]

As aplicações Node.js têm acesso a uma vasta gama de ferramentas de registo de diagnóstico. Internamente, o Mobile Apps Node.js SDK utiliza [winston] para diagnóstico de registo. O registo é ativado automaticamente quando ativa `MS_DebugMode` o modo de depuração ou se define a definição da aplicação verdadeiramente no [portal Azure]. Os registos gerados aparecem nos registos de diagnóstico no [portal Azure].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Quickstart do cliente Android]: app-service-mobile-android-get-started.md
[Apache Cordova Cliente quickstart]: app-service-mobile-cordova-get-started.md
[iOS Cliente quickstart]: app-service-mobile-ios-get-started.md
[Xamarin.iOS Cliente quickstart]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android Cliente quickstart]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms Cliente quickstart]: app-service-mobile-xamarin-forms-get-started.md
[Cliente da Loja windows]: app-service-mobile-windows-store-dotnet-get-started.md
[sincronização de dados offline]: app-service-mobile-offline-data-sync.md
[Configurar a autenticação do Azure Active Directory]: ../app-service/configure-authentication-provider-aad.md
[Configure a autenticação do Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Configure a autenticação do Google]: ../app-service/configure-authentication-provider-google.md
[Configure a autenticação da Microsoft]: ../app-service/configure-authentication-provider-microsoft.md
[Configure a autenticação do Twitter]: ../app-service/configure-authentication-provider-twitter.md
[Guia de implantação do Serviço de Aplicações Azure]: ../app-service/deploy-local-git.md
[Monitorização do Serviço de Aplicações Azure]: ../app-service/web-sites-monitor.md
[Ativar a exploração de diagnóstico no Serviço de Aplicações Azure]: ../app-service/troubleshoot-diagnostic-logs.md
[Troubleshoot Azure App Service em Estúdio Visual]: ../app-service/troubleshoot-dotnet-visual-studio.md
[especificar a versão nó]: ../nodejs-specify-node-version-azure-apps.md
[usar módulos de nó]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Portal Azure]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promessa]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[amostra de basicapp no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[amostra toda no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[diretório de amostras no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[ConsultaJS]: https://github.com/Azure/queryjs
[Node.js Ferramentas 1.1 para Estúdio Visual]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[pacote mssql Node.js]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[Utensílios de meio expressJS]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

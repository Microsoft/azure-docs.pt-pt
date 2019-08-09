---
title: Como trabalhar com o SDK do servidor back-end do node. js para aplicativos móveis | Microsoft Docs
description: Saiba como trabalhar com o SDK do servidor back-end do node. js para aplicativos móveis do serviço Azure App.
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
ms.openlocfilehash: 6eaaeba8a36bcba8134d605889185fb8827dd05c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851196"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Como usar o SDK do node. js de aplicativos móveis

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este artigo fornece informações detalhadas e exemplos que mostram como trabalhar com um back-end do node. js no recurso aplicativos móveis do serviço de Azure App.

## <a name="Introduction"></a>Introdução

Os aplicativos móveis fornecem a capacidade de adicionar uma API Web de acesso a dados otimizada para mobilidade a um aplicativo Web. O SDK de aplicativos móveis é fornecido para aplicativos Web ASP.NET e node. js. O SDK fornece as seguintes operações:

* Operações de tabela (leitura, inserção, atualização, exclusão) para acesso a dados
* Operações de API personalizadas

Ambas as operações fornecem autenticação em todos os provedores de identidade que Azure App serviço permite. Esses provedores incluem provedores de identidade social, como Facebook, Twitter, Google e Microsoft, bem como Azure Active Directory para identidade corporativa.

Você pode encontrar exemplos para cada caso de uso no [diretório de exemplos no GitHub].

## <a name="supported-platforms"></a>Plataformas suportadas

O SDK do node. js dos aplicativos móveis dá suporte à versão atual do LTS do nó e posterior. Atualmente, a versão mais recente do LTS é o node v 4.5.0. Outras versões do Node podem funcionar, mas não têm suporte.

O SDK do node. js dos aplicativos móveis dá suporte a dois drivers de banco de dados: 

* O driver node-MSSQL dá suporte ao banco de dados SQL do Azure e a instâncias de SQL Server locais.  
* O driver sqlite3 dá suporte a bancos de dados SQLite somente em uma única instância.

### <a name="howto-cmdline-basicapp"></a>Criar um back-end do node. js básico usando a linha de comando

Cada back-end node. js de aplicativos móveis é iniciado como um aplicativo ExpressJS. ExpressJS é a estrutura de serviço Web mais popular disponível para o Node. js. Você pode criar um aplicativo [Express] básico da seguinte maneira:

1. Em um comando ou janela do PowerShell, crie um diretório para seu projeto:

        mkdir basicapp

1. Execute `npm init` para inicializar a estrutura do pacote:

        cd basicapp
        npm init

   O `npm init` comando solicita um conjunto de perguntas para inicializar o projeto. Consulte o exemplo de saída:

   ![A saída de inicialização NPM][0]

1. Instale as `express` bibliotecas `azure-mobile-apps` e do repositório NPM:

        npm install --save express azure-mobile-apps

1. Crie um arquivo app. js para implementar o servidor móvel básico:

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

Esse aplicativo cria uma API Web otimizada para dispositivos móveis com um único`/tables/TodoItem`ponto de extremidade () que fornece acesso não autenticado a um armazenamento de dados SQL subjacente usando um esquema dinâmico. Ele é adequado para seguir os guias de início rápido da biblioteca do cliente:

* [Início rápido do cliente Android]
* [Início rápido do cliente Apache Cordova]
* [início rápido do cliente iOS]
* [Início rápido do cliente da Windows Store]
* [Início rápido do cliente Xamarin. iOS]
* [Início rápido do cliente Xamarin. Android]
* [Início rápido do cliente Xamarin. Forms]

Você pode encontrar o código para esse aplicativo básico no [exemplo de aplicativo básico no GitHub].

### <a name="howto-vs2015-basicapp"></a>Criar um back-end do node. js usando o Visual Studio 2015

O Visual Studio 2015 requer uma extensão para desenvolver aplicativos node. js dentro do IDE. Para começar, instale as [Ferramentas do node. js 1,1 para Visual Studio]. Quando você concluir a instalação, crie um aplicativo Express 4. x:

1. Abra a caixa de diálogo **novo projeto** (de **arquivo** > **novo** > **projeto**).
1. Expanda **modelos** > **JavaScript** > **node. js**.
1. Selecione o **aplicativo Azure node. js Express 4 básico**.
1. Preencha o nome do projeto. Selecione **OK**.

   ![Novo projeto do Visual Studio 2015][1]
1. Clique com o botão direito do mouse no nó **NPM** e selecione **instalar novos pacotes NPM**.
1. Talvez seja necessário atualizar o catálogo NPM depois de criar seu primeiro aplicativo node. js. Selecione **Atualizar** , se necessário.
1. Digite **Azure-Mobile-Apps** na caixa de pesquisa. Selecione o pacote **Azure-Mobile-Apps 2.0.0** e, em seguida, selecione **instalar pacote**.

   ![Instalar novos pacotes do NPM][2]
1. Selecione **fechar**.
1. Abra o arquivo app. js para adicionar suporte para o SDK de aplicativos móveis. Na linha 6 na parte inferior das instruções de `require` biblioteca, adicione o seguinte código:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Em aproximadamente a linha 27 após as `app.use` outras instruções, adicione o seguinte código:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Guarde o ficheiro.

1. Execute o aplicativo localmente (a API é servida `http://localhost:3000`) ou publique no Azure.

### <a name="create-node-backend-portal"></a>Criar um back-end do node. js usando o portal do Azure

Você pode criar um back-end de aplicativos móveis diretamente no [Azure portal]. Você pode concluir as etapas a seguir ou criar um cliente e um servidor juntos seguindo o tutorial [criar um aplicativo móvel](app-service-mobile-ios-get-started.md) . O tutorial contém uma versão simplificada dessas instruções e é melhor para projetos de prova de conceito.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

De volta ao painel introdução, em **criar uma API de tabela**, escolha **node. js** como seu idioma de back-end.
Selecione a caixa para **confirmar que isso substituirá todo o conteúdo do site**e, em seguida, selecione **criar tabela TodoItem**.

### <a name="download-quickstart"></a>Baixar o projeto de código de início rápido de back-end do node. js usando o Git

Quando você cria um back-end de aplicativos móveis do node. js usando o painel **início rápido** do portal, um projeto Node. js é criado para você e implantado em seu site. No portal, você pode adicionar tabelas e APIs e editar arquivos de código para o back-end do node. js. Você também pode usar várias ferramentas de implantação para baixar o projeto de back-end para que você possa adicionar ou modificar tabelas e APIs e, em seguida, publicar o projeto novamente. Para obter mais informações, consulte o [Guia de implantação do serviço Azure App].

O procedimento a seguir usa um repositório Git para baixar o código do projeto de início rápido:

1. Instale o Git, caso ainda não tenha feito isso. As etapas necessárias para instalar o Git variam de acordo com os sistemas operacionais. Para distribuições específicas do sistema operacional e diretrizes de instalação, consulte Instalando o [git](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Consulte [preparar seu repositório](../app-service/deploy-local-git.md#prepare-your-repository) para habilitar o repositório Git para seu site de back-end. Anote o nome de usuário e a senha da implantação.
3. No painel do back-end de seus aplicativos móveis, anote a configuração da **URL de clone do git** .
4. Execute o `git clone` comando usando a URL de clone do git. Insira sua senha quando necessário, como no exemplo a seguir:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Navegue até o diretório local (`/todolist` no exemplo anterior) e observe que os arquivos de projeto foram baixados. Localize o arquivo todoitem. JSON no `/tables` diretório. Esse arquivo define as permissões na tabela. Encontre também o arquivo todoitem. js no mesmo diretório. Ele define os scripts de operação CRUD para a tabela.
6. Depois de fazer alterações nos arquivos de projeto, execute os seguintes comandos para adicionar, confirmar e, em seguida, carregar as alterações no site:

        $ git commit -m "updated the table script"
        $ git push origin master

   Ao adicionar novos arquivos ao projeto, primeiro você precisa executar o `git add .` comando.

O site é republicado sempre que um novo conjunto de confirmações é enviado para o site.

### <a name="howto-publish-to-azure"></a>Publicar seu back-end do node. js no Azure

O Microsoft Azure fornece muitos mecanismos para publicar seu back-end do node. js de aplicativos móveis para o serviço do Azure. Esses mecanismos incluem ferramentas de implantação integradas ao Visual Studio, ferramentas de linha de comando e opções de implantação contínua com base no controle do código-fonte. Para obter mais informações, consulte o [Guia de implantação do serviço Azure App].

Azure App serviço tem conselhos específicos para aplicativos node. js que você deve examinar antes de publicar o back-end:

* Como [especificar a versão do nó]
* Como [usar módulos de nó]

### <a name="howto-enable-homepage"></a>Habilitar um home page para seu aplicativo

Muitos aplicativos são uma combinação de aplicativos Web e móveis. Você pode usar a estrutura ExpressJS para combinar as duas facetas. Às vezes, no entanto, talvez você queira implementar apenas uma interface móvel. É útil fornecer um home page para garantir que o serviço de aplicativo esteja em execução. Você pode fornecer seu próprio home page ou habilitar um home page temporário. Para habilitar um home page temporário, use o seguinte código para criar uma instância de aplicativos móveis:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Se você quiser apenas essa opção disponível ao desenvolver localmente, poderá adicionar essa configuração ao seu arquivo azureMobile. js.

## <a name="TableOperations"></a>Operações de tabela

O SDK do servidor do node. js do Azure-Mobile-Apps fornece mecanismos para expor tabelas de dados armazenadas no Azure SQL Database como uma API da Web. Ele fornece cinco operações:

| Operação | Descrição |
| --- | --- |
| OBTER/Tables/*TableName* |Obter todos os registros na tabela. |
| OBTER/Tables/*TableName*/: ID |Obter um registro específico na tabela. |
| POSTAR*TableName* /Tables/ |Crie um registro na tabela. |
| PATCH/Tables/*TableName*/: ID |Atualizar um registro na tabela. |
| EXCLUIR/Tables/*TableName*/: ID |Excluir um registro na tabela. |

Esta API Web dá suporte a [OData] e estende o esquema de tabela para dar suporte à [sincronização de dados offline].

### <a name="howto-dynamicschema"></a>Definir tabelas usando um esquema dinâmico

Para poder usar uma tabela, você deve defini-la. Você pode definir tabelas usando um esquema estático (no qual você define as colunas no esquema) ou dinamicamente (onde o SDK controla o esquema com base em solicitações de entrada). Além disso, você pode controlar aspectos específicos da API Web adicionando código JavaScript à definição.

Como prática recomendada, você deve definir cada tabela em um arquivo JavaScript no `tables` diretório e, em seguida, usar o `tables.import()` método para importar as tabelas. Estendendo o exemplo de aplicativo básico, você ajustaria o arquivo app. js:

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

Defina a tabela em./tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

As tabelas usam um esquema dinâmico por padrão. Para desativar o esquema dinâmico globalmente, defina a configuração `MS_DynamicSchema` do aplicativo como false no portal do Azure.

Você pode encontrar um exemplo completo no [exemplo de tarefas pendentes no GitHub].

### <a name="howto-staticschema"></a>Definir tabelas usando um esquema estático

Você pode definir explicitamente as colunas a serem expostas por meio da API da Web. O SDK do node. js do Azure-Mobile-Apps adiciona automaticamente todas as colunas extras necessárias para a sincronização de dados offline à lista que você fornece. Por exemplo, os aplicativos cliente de início rápido exigem uma tabela com duas `text` colunas: (uma cadeia `complete` de caracteres) e (um booliano).  
A tabela pode ser definida no arquivo JavaScript de definição de tabela (localizado no `tables` diretório) da seguinte maneira:

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

Se você definir tabelas estaticamente, também deverá chamar o `tables.initialize()` método para criar o esquema de banco de dados na inicialização. O `tables.initialize()` método retorna uma [promessa] para que o serviço Web não atenda solicitações antes que o banco de dados seja inicializado.

### <a name="howto-sqlexpress-setup"></a>Usar SQL Server Express como um armazenamento de dados de desenvolvimento em seu computador local

O SDK do node. js dos aplicativos móveis fornece três opções para o fornecimento de dados prontos para uso:

* Use o driver de **memória** para fornecer um armazenamento de exemplo não persistente.
* Use o driver **MSSQL** para fornecer um armazenamento de dados SQL Server Express para desenvolvimento.
* Use o driver **MSSQL** para fornecer um armazenamento de dados do banco de dados SQL do Azure para produção.

O SDK do node. js dos aplicativos móveis usa o [pacote do MSSQL node. js] para estabelecer e usar uma conexão com o SQL Server Express e o banco de dados SQL. Este pacote requer que você habilite conexões TCP em sua instância de SQL Server Express.

> [!TIP]
> O driver de memória não fornece um conjunto completo de recursos para teste. Se você quiser testar seu back-end localmente, recomendamos o uso de um armazenamento de dados SQL Server Express e do driver MSSQL.

1. Baixe e instale o [Microsoft SQL Server 2014 Express]. Certifique-se de instalar o SQL Server 2014 Express com o Tools Edition. A menos que você explicitamente exija suporte de 64 bits, a versão de 32 bits consome menos memória durante a execução.
1. Execute SQL Server Configuration Manager 2014:

   a. Expanda o nó **configuração de rede SQL Server** no menu de árvore.

   b. Selecione **protocolos para SQLExpress**.

   c. Clique com o botão direito do mouse em **TCP/IP** e selecione **habilitar**. Selecione **OK** na caixa de diálogo pop-up.

   d. Clique com o botão direito do mouse em **TCP/IP** e selecione **Propriedades**.

   e. Selecione a guia **endereços IP** .

   f. Localize o nó **IPAll** . No campo **porta TCP** , digite **1433**.

      ![Configurar SQL Server Express para TCP/IP][3]

   g. Selecione **OK**. Selecione **OK** na caixa de diálogo pop-up.

   h. Selecione **SQL Server serviços** no menu de árvore.

   i. Clique com o botão direito do mouse em **SQL Server (SQLExpress)** e selecione **reiniciar**.

   j. Feche SQL Server Configuration Manager 2014.

1. Execute SQL Server Management Studio 2014 e conecte-se à sua instância de SQL Server Express local:

   1. Clique com o botão direito do mouse em sua instância no Pesquisador de objetos e selecione **Propriedades**.
   1. Selecione a página **segurança** .
   1. Certifique-se de que **SQL Server e modo de autenticação do Windows** esteja selecionado.
   1. Selecione **OK**.

      ![Configurar a autenticação SQL Server Express][4]
   1. Expanda**logons** de **segurança** > no Pesquisador de objetos.
   1. Clique com o botão direito do mouse em logons e selecione **novo logon**.
   1. Insira um nome de logon. Selecione **Autenticação do SQL Server**. Insira uma senha e, em seguida, digite a mesma senha em **Confirmar senha**. A senha deve atender aos requisitos de complexidade do Windows.
   1. Selecione **OK**.

      ![Adicionar um novo usuário ao SQL Server Express][5]
   1. Clique com o botão direito do mouse no novo logon e selecione **Propriedades**.
   1. Selecione a página **funções de servidor** .
   1. Marque a caixa de seleção da função de servidor **dbcreator** .
   1. Selecione **OK**.
   1. Feche SQL Server Management Studio 2015.

Certifique-se de registrar o nome de usuário e a senha que você selecionou. Talvez seja necessário atribuir permissões ou funções de servidor adicionais, dependendo dos requisitos do banco de dados.

O aplicativo node. js lê a `SQLCONNSTR_MS_TableConnectionString` variável de ambiente para a cadeia de conexão para esse banco de dados. Você pode definir essa variável em seu ambiente. Por exemplo, você pode usar o PowerShell para definir essa variável de ambiente:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Acesse o banco de dados por meio de uma conexão TCP/IP. Forneça um nome de usuário e senha para a conexão.

### <a name="howto-config-localdev"></a>Configurar seu projeto para desenvolvimento local

Os aplicativos móveis lêem um arquivo JavaScript chamado *azureMobile. js* do sistema de arquivos local. Não use esse arquivo para configurar o SDK de aplicativos móveis em produção. Em vez disso, use **as configurações do aplicativo** no [Azure portal].

O arquivo azureMobile. js deve exportar um objeto de configuração. As configurações mais comuns são:

* Definições da base de dados
* Configurações de log de diagnóstico
* Configurações alternativas de CORS

Este arquivo de exemplo **azureMobile. js** implementa as configurações de banco de dados anteriores:

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

Recomendamos que você adicione **azureMobile. js** ao arquivo **. gitignore** (ou outro controle de código-fonte ignora o arquivo) para impedir que as senhas sejam armazenadas na nuvem. Sempre defina as configurações de produção nas **configurações do aplicativo** dentro do [Azure portal].

### <a name="howto-appsettings"></a>Definir configurações de aplicativo para seu aplicativo móvel

A maioria das configurações no arquivo azureMobile. js tem uma configuração de aplicativo equivalente no [Azure portal]. Use a lista a seguir para configurar seu aplicativo nas **configurações do aplicativo**:

| Configuração do aplicativo | configuração do azureMobile. js | Descrição | Valores válidos |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |Nome do aplicativo |Cadeia de caracteres |
| **MS_MobileLoggingLevel** |logging.level |Nível mínimo de log das mensagens a serem registradas |erro, aviso, informações, detalhado, depuração, insimples |
| **MS_DebugMode** |depurar |Habilita ou desabilita o modo de depuração |true, false |
| **MS_TableSchema** |data.schema |Nome de esquema padrão para tabelas SQL |Cadeia de caracteres (padrão: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Habilita ou desabilita o modo de depuração |true, false |
| **MS_DisableVersionHeader** |versão (definida como indefinida) |Desabilita o cabeçalho X-ZUMO-Server-Version |true, false |
| **MS_SkipVersionCheck** |skipversioncheck |Desabilita a verificação de versão da API do cliente |true, false |

Para definir uma configuração de aplicativo:

1. Inicie sessão no [Azure portal].
1. Selecione **todos os recursos** ou **serviços de aplicativos**e, em seguida, selecione o nome do seu aplicativo móvel.
1. O painel **configurações** é aberto por padrão. Se não estiver, selecione **configurações**.
1. No menu **geral** , selecione **configurações do aplicativo**.
1. Role até a seção **configurações do aplicativo** .
1. Se a configuração do aplicativo já existir, selecione o valor da configuração do aplicativo para editar o valor.
   Se a configuração do aplicativo não existir, insira a configuração do aplicativo na caixa **chave** e o valor na caixa **valor** .
1. Selecione **Guardar**.

Alterar a maioria das configurações de aplicativo requer uma reinicialização do serviço.

### <a name="howto-use-sqlazure"></a>Usar o banco de dados SQL como seu armazenamento de data de produção

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

O uso do banco de dados SQL do Azure como um repositório é idêntico em todos os tipos de aplicativos de serviço Azure App. Se você ainda não tiver feito isso, siga estas etapas para criar um back-end de aplicativos móveis:

1. Inicie sessão no [Azure portal].
1. No canto superior esquerdo da janela, selecione o botão **+ novo** > **Web + celular** > **aplicativo móvel**e forneça um nome para o back-end dos seus aplicativos móveis.
1. Na caixa **grupo de recursos** , digite o mesmo nome que o seu aplicativo.
1. O plano do serviço de aplicativo padrão é selecionado. Se você quiser alterar o plano do serviço de aplicativo:

   a. Selecione **plano** > do serviço de aplicativo **+ criar novo**.

   b. Forneça um nome para o novo plano do serviço de aplicativo e selecione um local apropriado.

   c. Selecione um tipo de preço apropriado para o serviço. Selecione **Exibir tudo** para exibir mais opções de preços, como **gratuito** e **compartilhado**.

   d. Clique no botão **selecionar** .

   e. De volta ao painel do **plano do serviço de aplicativo** , selecione **OK**.
1. Selecione **Criar**.

O provisionamento de um back-end de aplicativos móveis pode levar alguns minutos. Depois que o back-end dos aplicativos móveis é provisionado, o portal abre o painel **configurações** para o back-end dos aplicativos móveis.

Você pode optar por conectar um banco de dados SQL existente ao back-end de seus aplicativos móveis ou criar um novo banco de dados SQL. Nesta seção, criamos um banco de dados SQL.

> [!NOTE]
> Se você já tiver um banco de dados no mesmo local que o back-end dos aplicativos móveis, poderá selecionar **usar um banco de dados existente** e, em seguida, selecionar esse banco de dados. Não recomendamos o uso de um banco de dados em um local diferente devido a latências mais altas.

1. No novo back-end de aplicativos móveis, selecione **configurações** > **aplicativos** > móveis**dados** >  **+ Adicionar**.
1. No painel **Adicionar conexão de dados** , selecione **banco de dados SQL-definir configurações** > necessárias**criar um novo banco de**dados. Digite o nome do novo banco de dados na caixa **nome** .
1. Selecione **servidor**. No painel **novo servidor** , insira um nome de servidor exclusivo na caixa **nome do servidor** e forneça um logon de administrador do servidor e uma senha adequados. Verifique se a seleção de permitir que os **Serviços do Azure acessem o servidor** está selecionada. Selecione **OK**.

   ![Criar uma Base de Dados SQL do Azure][6]
1. No painel **novo banco de dados** , selecione **OK**.
1. De volta ao painel **Adicionar conexão de dados** , selecione **cadeia de conexão**e insira o logon e a senha que você forneceu ao criar o banco de dados. Se você usar um banco de dados existente, forneça as credenciais de logon para esse banco de dados. Selecione **OK**.
1. De volta ao painel **Adicionar conexão de dados** , selecione **OK** para criar o banco de dados.

<!--- END OF ALTERNATE INCLUDE -->

A criação do banco de dados pode levar alguns minutos. Use a área **notificações** para monitorar o progresso da implantação. Não progredi até que o banco de dados seja implantado com êxito. Depois que o banco de dados é implantado, uma cadeia de conexão é criada para a instância do banco de dados SQL em suas configurações de aplicativo de back-end de aplicativos móveis. Você pode ver essa configuração de aplicativo em **configurações** > **aplicativos configurações** > cadeias de**conexão**.

### <a name="howto-tables-auth"></a>Exigir autenticação para acesso a tabelas

Se você quiser usar a autenticação do serviço de aplicativo `tables` com o ponto de extremidade, deverá configurar a autenticação do serviço de aplicativo na [Azure portal] primeiro. Para obter mais informações, consulte o guia de configuração para o provedor de identidade que você pretende usar:

* [Configurar a autenticação Azure Active Directory]
* [Configurar a autenticação do Facebook]
* [Configurar a autenticação do Google]
* [Configurar a autenticação da Microsoft]
* [Configurar a autenticação do Twitter]

Cada tabela tem uma propriedade de acesso que você pode usar para controlar o acesso à tabela. O exemplo a seguir mostra uma tabela definida estaticamente com autenticação necessária.

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

A propriedade Access pode ter um destes três valores:

* *anônimo* indica que o aplicativo cliente tem permissão para ler dados sem autenticação.
* *autenticado* indica que o aplicativo cliente deve enviar um token de autenticação válido com a solicitação.
* *Disabled* indica que esta tabela está desabilitada no momento.

Se a propriedade de acesso for indefinida, o acesso não autenticado será permitido.

### <a name="howto-tables-getidentity"></a>Usar declarações de autenticação com suas tabelas
Você pode configurar várias declarações que são solicitadas quando a autenticação é configurada. Essas declarações não estão normalmente disponíveis por meio `context.user` do objeto. No entanto, você pode recuperá-los `context.user.getIdentity()` usando o método. O `getIdentity()` método retorna uma promessa que é resolvida para um objeto. O objeto é codificado pelo método de autenticação (`facebook`, `google`, `twitter` `microsoftaccount`, ou `aad`).

Por exemplo, se você configurar conta Microsoft autenticação e solicitar a declaração de endereços de email, poderá adicionar o endereço de email ao registro com o seguinte controlador de tabela:

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

Para ver quais declarações estão disponíveis, use um navegador da Web para exibir `/.auth/me` o ponto de extremidade do seu site.

### <a name="howto-tables-disabled"></a>Desabilitar o acesso a operações de tabela específicas

Além de aparecer na tabela, a propriedade Access pode ser usada para controlar operações individuais. Há quatro operações:

* `read`é a operação de obtenção de RESTful na tabela.
* `insert`é a operação de POSTAgem RESTful na tabela.
* `update`é a operação de PATCH RESTful na tabela.
* `delete`é a operação de exclusão RESTful na tabela.

Por exemplo, talvez você queira fornecer uma tabela não autenticada somente leitura:

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

### <a name="howto-tables-query"></a>Ajustar a consulta usada com operações de tabela

Um requisito comum para operações de tabela é fornecer uma exibição restrita dos dados. Por exemplo, você pode fornecer uma tabela que é marcada com a ID de usuário autenticado, de modo que você só possa ler ou atualizar seus próprios registros. A definição de tabela a seguir fornece essa funcionalidade:

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

As operações que normalmente executam uma consulta têm uma propriedade de consulta que você pode ajustar `where` usando uma cláusula. A propriedade de consulta é um objeto [QueryJS] que é usado para converter uma consulta OData em algo que o back-end de dados pode processar. Para casos de igualdade simples (como o anterior), você pode usar um mapa. Você também pode adicionar cláusulas SQL específicas:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Configurar uma exclusão reversível em uma tabela

Na verdade, uma exclusão reversível não exclui registros. Em vez disso, ele os marca como excluído no banco de dados, definindo a coluna excluída como true. O SDK de aplicativos móveis remove automaticamente os registros com exclusão reversível dos resultados, a menos `IncludeDeleted()`que o SDK do cliente móvel use o. Para configurar uma tabela para uma exclusão reversível, defina `softDelete` a propriedade no arquivo de definição de tabela:

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

Você deve estabelecer um mecanismo para excluir registros: um aplicativo cliente, um WebJob, uma função do Azure ou uma API personalizada.

### <a name="howto-tables-seeding"></a>Propague seu banco de dados

Ao criar um novo aplicativo, talvez você queira propagar uma tabela com dados. Você pode fazer isso no arquivo JavaScript de definição de tabela da seguinte maneira:

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

A propagação de dados ocorre somente quando você usou o SDK de aplicativos móveis para criar a tabela. Se a tabela já existir no banco de dados, nenhum dado será injetado na tabela. Se o esquema dinâmico estiver ativado, o esquema será inferido dos dados propagados.

É recomendável chamar explicitamente o `tables.initialize()` método para criar a tabela quando o serviço começar a ser executado.

### <a name="Swagger"></a>Habilitar suporte a Swagger
Os aplicativos móveis são fornecidos com suporte interno a [Swagger] . Para habilitar o suporte a Swagger, primeiro instale o Swagger-UI como uma dependência:

    npm install --save swagger-ui

Em seguida, você pode habilitar o suporte a Swagger no construtor de aplicativos móveis:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Provavelmente, você só desejará habilitar o suporte a Swagger em edições de desenvolvimento. Você pode fazer isso usando a configuração `NODE_ENV` do aplicativo:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

O `swagger` ponto de extremidade está localizado em http://*seusite*. azurewebsites.net/Swagger. Você pode acessar a interface do usuário do `/swagger/ui` Swagger por meio do ponto de extremidade. Se você optar por exigir autenticação em todo o aplicativo, o Swagger produzirá um erro. Para obter melhores resultados, opte por permitir solicitações não autenticadas nas configurações de autenticação/autorização do serviço de Azure app e, em seguida, `table.access` controle a autenticação usando a propriedade.

Você também pode adicionar a opção Swagger ao seu arquivo azureMobile. js se desejar apenas o suporte do Swagger para o desenvolvimento local.

## <a name="a-namepushpush-notifications"></a><a name="push"/>Notificações por push

Os aplicativos móveis se integram aos hubs de notificação do Azure para que você possa enviar notificações por push direcionadas para milhões de dispositivos em todas as principais plataformas. Usando os hubs de notificação, você pode enviar notificações por push para dispositivos iOS, Android e Windows. Para saber mais sobre tudo o que você pode fazer com os hubs de notificação, consulte [visão geral dos hubs de notificação](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Enviar notificações por push

O código a seguir mostra como usar o `push` objeto para enviar uma notificação por push de difusão para dispositivos IOS registrados:

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

Ao criar um registro de push de modelo do cliente, você pode enviar uma mensagem de push de modelo para dispositivos em todas as plataformas com suporte. O código a seguir mostra como enviar uma notificação de modelo:

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

### <a name="push-user"></a>Enviar notificações por push para um usuário autenticado usando marcas
Quando um usuário autenticado se registra para notificações por push, uma marca de ID de usuário é adicionada automaticamente ao registro. Usando essa marca, você pode enviar notificações por push para todos os dispositivos registrados por um usuário específico. O código a seguir obtém o SID do usuário que está fazendo a solicitação e envia uma notificação por push de modelo para cada registro de dispositivo para esse usuário:

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

Quando você estiver se registrando para notificações por push de um cliente autenticado, verifique se a autenticação foi concluída antes de tentar o registro.

## <a name="CustomAPI"></a>APIs personalizadas

### <a name="howto-customapi-basic"></a>Definir uma API personalizada

Além da API de acesso a dados por meio `/tables` do ponto de extremidade, os aplicativos móveis podem fornecer cobertura de API personalizada. As APIs personalizadas são definidas de forma semelhante às definições de tabela e podem acessar todos os mesmos recursos, incluindo a autenticação.

Se você quiser usar a autenticação do serviço de aplicativo com uma API personalizada, deverá configurar a autenticação do serviço de aplicativo na [Azure portal] primeiro. Para obter mais informações, consulte o guia de configuração para o provedor de identidade que você pretende usar:

* [Configurar a autenticação Azure Active Directory]
* [Configurar a autenticação do Facebook]
* [Configurar a autenticação do Google]
* [Configurar a autenticação da Microsoft]
* [Configurar a autenticação do Twitter]

As APIs personalizadas são definidas quase da mesma forma que a API de tabelas:

1. Crie um `api` diretório.
1. Crie um arquivo JavaScript de definição de API `api` no diretório.
1. Use o método de importação para importar `api` o diretório.

Aqui está a definição de API de protótipo baseada no exemplo de aplicativo básico que usamos anteriormente:

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

Vamos pegar um exemplo de API que retorna a data do servidor usando o `Date.now()` método. Este é o arquivo API/Date. js:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Cada parâmetro é um dos verbos padrão RESTful: GET, POST, PATCH ou DELETE. O método é uma função de [middleware ExpressJS] padrão que envia a saída necessária.

### <a name="howto-customapi-auth"></a>Exigir autenticação para acesso a uma API personalizada

O SDK de aplicativos móveis implementa a autenticação da mesma forma para o `tables` ponto de extremidade e APIs personalizadas. Para adicionar autenticação à API desenvolvida na seção anterior, adicione uma `access` Propriedade:

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

Você também pode especificar a autenticação em operações específicas:

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

O mesmo token que é usado para o `tables` ponto de extremidade deve ser usado para APIs personalizadas que exigem autenticação.

### <a name="howto-customapi-auth"></a>Lidar com carregamentos de arquivos grandes

O SDK de aplicativos móveis usa o middleware do analisador de [corpo](https://github.com/expressjs/body-parser) para aceitar e decodificar o conteúdo do corpo em seu envio. Você pode pré-configurar o analisador de corpo para aceitar carregamentos de arquivos maiores:

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

O arquivo é codificado em base-64 antes da transmissão. Essa codificação aumenta o tamanho do upload real (e o tamanho que você deve considerar).

### <a name="howto-customapi-sql"></a>Executar instruções SQL personalizadas

O SDK de aplicativos móveis permite o acesso ao contexto inteiro por meio do objeto de solicitação. Você pode executar facilmente instruções SQL parametrizadas para o provedor de dados definido:

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

## <a name="Debugging"></a>Pura

### <a name="howto-diagnostic-logs"></a>Depurar, diagnosticar e solucionar problemas de aplicativos móveis

O serviço de Azure App fornece várias técnicas de depuração e solução de problemas para aplicativos node. js.
Para começar a solucionar problemas de back-end de aplicativos móveis do node. js, consulte os seguintes artigos:

* [Monitorando Azure App serviço]
* [Habilitar o log de diagnóstico no serviço Azure App]
* [Solucionar problemas de serviço Azure App no Visual Studio]

Os aplicativos node. js têm acesso a uma ampla variedade de ferramentas de log de diagnóstico. Internamente, o SDK do node. js dos aplicativos móveis usa [Winston] para log de diagnóstico. O registro em log é habilitado automaticamente quando você habilita o modo `MS_DebugMode` de depuração ou define a configuração do aplicativo como true no [Azure portal]. Os logs gerados aparecem nos logs de diagnóstico no [Azure portal].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Início rápido do cliente Android]: app-service-mobile-android-get-started.md
[Início rápido do cliente Apache Cordova]: app-service-mobile-cordova-get-started.md
[início rápido do cliente iOS]: app-service-mobile-ios-get-started.md
[Início rápido do cliente Xamarin. iOS]: app-service-mobile-xamarin-ios-get-started.md
[Início rápido do cliente Xamarin. Android]: app-service-mobile-xamarin-android-get-started.md
[Início rápido do cliente Xamarin. Forms]: app-service-mobile-xamarin-forms-get-started.md
[Início rápido do cliente da Windows Store]: app-service-mobile-windows-store-dotnet-get-started.md
[sincronização de dados offline]: app-service-mobile-offline-data-sync.md
[Configurar a autenticação Azure Active Directory]: ../app-service/configure-authentication-provider-aad.md
[Configurar a autenticação do Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Configurar a autenticação do Google]: ../app-service/configure-authentication-provider-google.md
[Configurar a autenticação da Microsoft]: ../app-service/configure-authentication-provider-microsoft.md
[Configurar a autenticação do Twitter]: ../app-service/configure-authentication-provider-twitter.md
[Guia de implantação do serviço Azure App]: ../app-service/deploy-local-git.md
[Monitorando Azure App serviço]: ../app-service/web-sites-monitor.md
[Habilitar o log de diagnóstico no serviço Azure App]: ../app-service/troubleshoot-diagnostic-logs.md
[Solucionar problemas de serviço Azure App no Visual Studio]: ../app-service/troubleshoot-dotnet-visual-studio.md
[especificar a versão do nó]: ../nodejs-specify-node-version-azure-apps.md
[usar módulos de nó]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promessa]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[exemplo de aplicativo básico no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[exemplo de tarefas pendentes no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[diretório de exemplos no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Ferramentas do node. js 1,1 para Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[pacote do MSSQL node. js]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[Middleware ExpressJS]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

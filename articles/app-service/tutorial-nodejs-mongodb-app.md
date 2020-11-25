---
title: 'Tutorial: Node.js app com o MongoDB'
description: Saiba como obter uma aplicação Node.js a funcionar em Azure, com ligação a uma base de dados MongoDB em Azure (Cosmos DB). MEAN.js é usado no tutorial.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/16/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-js, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 9c204a07e3c5edff028342af1c88b15ebac0754b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012225"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>Tutorial: Construa uma aplicação Node.js e MongoDB em Azure

::: zone pivot="platform-windows"  

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra como criar uma aplicação Node.js no Serviço de Aplicações no Windows e conectá-la a uma base de dados MongoDB. Quando terminar, terá uma aplicação MEAN (MongoDB, Express, AngularJS e Node.js) em execução no [Serviço de Aplicações do Azure](overview.md). Para obter simplicidade, a aplicação de exemplo utiliza a [estrutura Web MEAN.js](https://meanjs.org/).

::: zone-end

::: zone pivot="platform-linux"


[O Azure App Service](overview.md) fornece um serviço de hospedagem web altamente escalável e auto-remendado utilizando o sistema operativo Linux. Este tutorial mostra como criar uma aplicação Node.js no Serviço de Aplicações em Linux, conectá-la localmente a uma base de dados mongoDB e, em seguida, implantá-la para uma base de dados na API da Azure Cosmos DB para o MongoDB. Quando terminar, terá uma aplicação MEAN (MongoDB, Express, AngularJS e Node.js) em execução no Serviço de Aplicações no Linux. Para obter simplicidade, a aplicação de exemplo utiliza a [estrutura Web MEAN.js](https://meanjs.org/).

::: zone-end

![Aplicação MEAN.js em execução no Serviço de Aplicações do Azure](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

O que irá aprender:

> [!div class="checklist"]
> * Criar uma base de dados MongoDB no Azure
> * Ligar uma aplicação Node.js ao MongoDB
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos de diagnóstico em fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
2. [Instale o Node.js e o NPM](https://nodejs.org/)
3. [Instalar Bower](https://bower.io/) (exigido pelo [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
4. [Instalar Gulp.js](https://gulpjs.com/) (exigido pelo [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
5. [Instalar e executar a Edição de Comunidade do MongoDB](https://docs.mongodb.com/manual/administration/install-community/) 

## <a name="test-local-mongodb"></a>Testar MongoDB local

Abra a janela do terminal e `cd` para o diretório `bin` da sua instalação do MongoDB. Pode utilizar esta janela de terminal para executar todos os comandos deste tutorial.

Execute `mongo` no terminal para ligar ao servidor MongoDB local.

```bash
mongo
```

Se a ligação for bem-sucedida, então a base de dados do MongoDB já está em execução. Caso contrário, certifique-se de que a base de dados do MongoDB local é iniciada através dos passos indicados em [Instalar Edição da Comunidade do MongoDB](https://docs.mongodb.com/manual/administration/install-community/). Muitas vezes, mesmo com o MongoDB instalado, continua a precisar de iniciá-lo ao executar o `mongod`. 

Quando tiver terminado de testar a sua base de dados do MongoDB, escreva `Ctrl+C` no terminal. 

## <a name="create-local-nodejs-app"></a>Criar aplicação do Node.js local

Neste passo, vai configurar o projeto Node.js local.

### <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Na janela de terminal, `cd` num diretório de trabalho.  

Execute o seguinte comando para clonar o repositório de exemplo. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Este repositório de exemplo inclui uma cópia do [repositório do MEAN.js](https://github.com/meanjs/mean). É modificado para ser executado no Serviço de Aplicações (para obter mais informações, veja o [ficheiro README](https://github.com/Azure-Samples/meanjs/blob/master/README.md) do repositório do MEAN.js).

### <a name="run-the-application"></a>Executar a aplicação

Execute os seguintes comandos para instalar os pacotes necessários e iniciar a aplicação.

```bash
cd meanjs
npm install
npm start
```

Ignore o aviso config.domain. Quando a aplicação estiver totalmente carregada, verá algo semelhante à mensagem seguinte:

<pre>
--
MEAN.JS - Ambiente de Desenvolvimento

Ambiente: servidor de desenvolvimento:          http://0.0.0.0:3000 Base de dados: versão mongodb://localhost/mean-dev App: 0.5.0 MEAN.JS versão: 0.5.0 --
</pre>

Navegue para `http://localhost:3000` num browser. Clique em **Inscrever** no menu superior e crie um utilizador de teste. 

A aplicação MEAN.js de exemplo armazena os dados do utilizador na base de dados. Se criar um utilizador e iniciar sessão com êxito, então a aplicação está a escrever dados na base de dados do MongoDB local.

![A aplicação MEAN.js estabelece ligação com êxito ao MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Selecione **Administrador > Gerir Artigos** para adicionar alguns artigos.

Para parar o Node.js em qualquer altura, prima `Ctrl+C` no terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Criar produção do MongoDB

Neste passo, vai criar uma Base de Dados MongoDB no Azure. Quando a aplicação for implementada no Azure, utiliza esta base de dados na cloud.

Para o MongoDB, este tutorial utiliza o [Azure Cosmos DB](/azure/documentdb/). O Cosmos DB suporta ligações de cliente do MongoDB.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Criar uma conta do Cosmos DB

> [!NOTE]
> A criação das bases de dados do Azure Cosmos DB na sua própria subscrição do Azure neste tutorial não acarreta custos. Para utilizar uma conta gratuita do Azure Cosmos DB durante sete dias, pode utilizar a experiência [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/). Basta clicar no botão **Criar** no mosaico MongoDB para criar uma base de dados do MongoDB gratuita no Azure. Após a criação da base de dados, navegue para a **cadeia de ligação** no portal e obtenha a cadeia de ligação do Azure Cosmos DB para utilizar mais à frente no tutorial.
>

Na Cloud Shell, crie uma conta Cosmos DB com o [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-create) comando.

No comando seguinte, substitua um nome exclusivo cosmos DB para o *\<cosmosdb-name>* espaço reservado. Este nome é utilizado como parte do ponto final do Cosmos DB, `https://<cosmosdb-name>.documents.azure.com/`, por isso, o nome tem de ser exclusivo em todas as contas Cosmos DB no Azure. O nome só pode conter letras minúsculas, números, o caráter hífen (-) e tem de ter entre três e 50 carateres.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

O parâmetro *--kind MongoDB* permite ligações de cliente da MongoDB.

Após criar a conta do Cosmos DB, a CLI do Azure mostra informações semelhantes ao exemplo seguinte:

<pre>
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://&lt;cosmosdb-name&gt;.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  &lt; Output truncated for readability &gt;
}
</pre>

## <a name="connect-app-to-production-mongodb"></a>Ligar aplicação ao MongoDB de produção

Neste passo, vai ligar a aplicação de exemplo MEAN.js a uma base de dados do Cosmos DB que acabou de criar, com uma cadeia de ligação do MongoDB. 

### <a name="retrieve-the-database-key"></a>Obter a chave de base de dados

Para ligar à base de dados do Cosmos DB, precisa da chave da base de dados. Na Cloud Shell, use o [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-keys) comando para recuperar a chave primária.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup
```

A CLI do Azure apresenta informações semelhantes ao exemplo seguinte:

<pre>
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
</pre>

Copie o valor de `primaryMasterKey`. Estas informações são necessárias no passo seguinte.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurar a cadeia de ligação na aplicação Node.js

No seu repositório do MEAN.js local, na pasta _config/env/_, crie um ficheiro denominado _local-production.js_. _.gitignore_ já está configurado para manter este ficheiro fora do repositório. 

Copie o código seguinte para o mesmo. Certifique-se de substituir os dois *\<cosmosdb-name>* espaços reservados pelo nome da base de dados Cosmos DB e substitua o *\<primary-master-key>* espaço reservado pela chave que copiou no passo anterior.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

A opção `ssl=true` é necessária porque o [Cosmos DB precisa do SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Guarde as alterações.

### <a name="test-the-application-in-production-mode"></a>Teste a aplicação no modo de produção 

Numa janela do terminal local, execute o seguinte comando para minimizar e agrupar scripts para o ambiente de produção. Este processo gera os ficheiros necessários para o ambiente de produção.

```bash
gulp prod
```

Numa janela do terminal local, execute o seguinte comando para utilizar a cadeia de ligação configurada em _config/env/local-production.js_. Ignore o erro de certificado e o aviso config.domain.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production` define a variável de ambiente que informa o Node.js para ser executado no ambiente de produção.  `node server.js` inicia o servidor do Node.js com `server.js` na raiz do repositório. Esta é a forma que a sua aplicação do Node.js é carregada no Azure. 

Assim que a aplicação é carregada, certifique-se de que está em execução no ambiente de produção:

<pre>
--
MEAN.JS

Ambiente: produção http://0.0.0.0:8443 Server: Base de &lt; dados: mongodb:// cosmosdb-name &gt; : &lt; primar-master-key &gt; @ &lt; cosmosdb-name &gt; .documents.azure.com:10250/mean?ssl=true&sslvercertificate=false App versão: 0.5.0 MEAN.JS versão: 0.5.0
</pre>

Navegue para `http://localhost:8443` num browser. Clique em **Inscrever** no menu superior e crie um utilizador de teste. Se criar um utilizador e iniciar sessão com êxito, então a aplicação está a escrever dados na base de dados do Cosmos DB no Azure. 

No terminal, pare o Node.js ao escrever `Ctrl+C`. 

## <a name="deploy-app-to-azure"></a>Implementar a aplicação no Azure

Neste passo, vai implementar a aplicação Node.js ligada ao MongoDB no Serviço de Aplicações do Azure.

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Criar uma aplicação Web

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

::: zone-end

### <a name="configure-an-environment-variable"></a>Configure uma variável de ambiente

Por predefinição, o projeto do MEAN.js mantém o _config/env/local-production.js_ fora do repositório do Git. Assim, para a sua aplicação Azure, utiliza as definições de aplicações para definir a sua cadeia de ligação MongoDB.

Para definir as definições de aplicações, utilize o [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando na Cloud Shell. 

O exemplo a seguir configura uma `MONGODB_URI` configuração de aplicação na sua aplicação Azure. Substitua o *\<app-name>* *\<cosmosdb-name>* , e espaços *\<primary-master-key>* reservados.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb-name>:<primary-master-key>@<cosmosdb-name>.documents.azure.com:10250/mean?ssl=true"
```

Em Node.js código, [aceda a esta configuração da aplicação](configure-language-nodejs.md#access-environment-variables) com `process.env.MONGODB_URI` , tal como acederia a qualquer variável ambiental. 

No seu repositório do MEAN.js local, abra _config/env/production.js_ (não _config/env/local-production.js_), que tem uma configuração específica de ambiente de produção. A aplicação do MEAN.js predefinida já está configurada para utilizar a variável de ambiente `MONGODB_URI` que criou.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

Poderá reparar que o processo de implementação executa o [Gulp](https://gulpjs.com/) depois de `npm install`. O Serviço de Aplicações não executa tarefas do Gulp ou do Grunt durante a implementação, pelo que este repositório de exemplo tem dois ficheiros adicionais no diretório raiz para a permitir: 

- _.implementação_ - este ficheiro diz ao Serviço de Aplicações para executar `bash deploy.sh` como o script de implementação personalizado.
- _deploy.sh_ - o script de implementação personalizado. Se vir o ficheiro, verá que executa `gulp prod` a seguir a `npm install` e `bower install`. 

Pode utilizar esta abordagem para adicionar qualquer passo à sua implementação baseada no Git. Se reiniciar a sua aplicação Azure em qualquer ponto, o Serviço de Aplicações não reenseja estas tarefas de automação. Para mais informações, consulte [Run Grunt/Bower/Gulp](configure-language-nodejs.md#run-gruntbowergulp).

### <a name="browse-to-the-azure-app"></a>Navegue pela app Azure 

Navegue pela aplicação implementada utilizando o seu navegador web. 

```bash 
http://<app-name>.azurewebsites.net 
``` 

Clique em **Inscrever** no menu superior e crie um utilizador de teste. 

Se concluir com êxito e a aplicação iniciar sessão automaticamente no utilizador criado, então a aplicação do MEAN.js no Azure tem conectividade à base de dados do MongoDB (Cosmos DB). 

![Aplicação MEAN.js em execução no Serviço de Aplicações do Azure](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Selecione **Administrador > Gerir Artigos** para adicionar alguns artigos. 

**Parabéns!** Está a executar uma aplicação Node.js condicionada por dados no Serviço de Aplicações do Azure.

## <a name="update-data-model-and-redeploy"></a>Atualizar o modelo de dados e voltar a implementar

Neste passo, altera o modelo de dados `article` e publica a sua alteração no Azure.

### <a name="update-the-data-model"></a>Atualizar o modelo de dados

No repositório MEAN.js local, abra _módules/artigos/servidor/modelos/article.server.model.js_.

No `ArticleSchema`, adicione um tipo `String` denominado `comment`. Quando tiver terminado, o código de esquema deverá este aspeto:

```javascript
const ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Atualizar o código de artigos

Atualize o resto do seu código `articles` para utilizar `comment`.

Existem cinco ficheiros que tem de modificar: o controlador de servidor e as vistas de quatro clientes. 

Abra _módulos/artigos/servidor/controladores/articles.server.controller.js_.

Na função `update`, adicione uma atribuição de `article.comment`. O código seguinte mostra a função `update` concluída:

```javascript
exports.update = function (req, res) {
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Abra _modules/artigos/cliente/vistas/view-article.client.view.html_.

Exatamente acima da etiqueta de fecho `</section>`, adicione a seguinte linha para apresentar `comment` juntamente com o resto dos dados do artigo:

```html
<p class="lead" ng-bind="vm.article.comment"></p>
```

Abra _modules/artigos/cliente/vistas/list-articles.client.view.html_.

Exatamente acima da etiqueta de fecho `</a>`, adicione a seguinte linha para apresentar `comment` juntamente com o resto dos dados do artigo:

```html
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Abra _módulos/artigos/cliente/vistas/administrador/list-articles.client.view.html_.

No interior do elemento `comment` e imediatamente acima da etiqueta de fecho `<div class="list-group">`, adicione a seguinte linha para apresentar `</a>` juntamente com o resto dos dados do artigo:

```html
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Abra _módulos/artigos/cliente/vistas/administrador/form-article.client.view.html_.

Encontre o elemento `<div class="form-group">` que contém o botão para submeter, semelhante ao seguinte:

```html
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Imediatamente acima desta etiqueta, adicione outro elemento `<div class="form-group">` que permite que as pessoas editem o campo `comment`. O novo elemento deve ter o seguinte aspeto:

```html
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Testar as alterações localmente

Guarde todas as alterações.

Na janela do terminal local, teste novamente as alterações no modo de produção.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Navegue para `http://localhost:8443` num browser e certifique-se de que tem sessão iniciada.

Selecione **Administrador > Gerir Artigos** e adicione um artigo ao selecionar o botão **+**.

Pode ver a nova caixa de texto `Comment` agora.

![Campo de comentário adicionado aos Artigos](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

No terminal, pare o Node.js ao escrever `Ctrl+C`. 

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

Na janela terminal local, consolide as suas alterações no Git e envie as alterações ao código para o Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Assim que a `git push` conclusão estiver concluída, navegue para a sua app Azure e experimente a nova funcionalidade.

![Alterações ao modelo e à base de dados publicadas no Azure](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Se tiver adicionado quaisquer artigos anteriormente, ainda pode vê-los. Os dados existentes no Cosmos DB não se perdem. Além disso, atualiza o esquema de dados e mantém os dados existentes intactos.

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo 

::: zone pivot="platform-windows"  

Enquanto executa a aplicação Node.js no Serviço de Aplicações do Azure, pode obter os registos de consola direcionados para o seu terminal. Dessa forma, pode obter as mesmas mensagens de diagnóstico para ajudar a depurar erros de aplicações.

Para iniciar o streaming de registos, utilize o [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) comando na Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
``` 

Assim que o streaming de registos tiver começado, refresque a sua app Azure no navegador para obter algum tráfego web. Verá então os registos da consola direcionados para o seu terminal.

Pare a transmissão em fluxo do registo em qualquer altura, ao escrever `Ctrl+C`. 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

## <a name="manage-your-azure-app"></a>Gerencie a sua app Azure

Vá ao [portal Azure](https://portal.azure.com) para ver a app que criou.

A partir do menu esquerdo, clique em **Serviços de Aplicação** e, em seguida, clique no nome da sua aplicação Azure.

![Navegação do portal para a aplicação do Azure](./media/tutorial-nodejs-mongodb-app/access-portal.png)

Por predefinição, o portal mostra a página **geral** da sua aplicação. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir.

![Página Serviço de Aplicações no portal do Azure](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Criar uma base de dados MongoDB no Azure
> * Ligar uma aplicação Node.js ao MongoDB
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos do Azure para o seu terminal
> * Gerir a aplicação no portal do Azure

Avance para o tutorial seguinte para aprender a mapear um nome DNS personalizado para a aplicação.

> [!div class="nextstepaction"] 
> [Mapeie um nome DNS personalizado existente para o Azure App Service](app-service-web-tutorial-custom-domain.md)

Ou, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar Node.js app](configure-language-nodejs.md)

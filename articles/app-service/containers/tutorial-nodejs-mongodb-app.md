---
title: Criar aplicação de node. js com MongoDB no Linux - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como colocar uma aplicação Node.js a funcionar no Serviço de Aplicações do Azure no Linux, com uma ligação à base de dados do Cosmos DB com uma cadeia de ligação do MongoDB.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: syntaxc4
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/10/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 73f810072fce9345208593342df597b72c522a73
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894517"
---
# <a name="build-a-nodejs-and-mongodb-app-in-azure-app-service-on-linux"></a>Criar uma aplicação de MongoDB no serviço de aplicações do Azure e o node. js no Linux

> [!NOTE]
> Este artigo implementa uma aplicação para o Serviço de Aplicações no Linux. Para implementar um serviço de aplicações no _Windows_, consulte [criar uma aplicação node. js e MongoDB no Azure](../app-service-web-tutorial-nodejs-mongodb-app.md).
>

O [Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática através do sistema operativo Linux. Este tutorial mostra como criar uma aplicação node. js, ligá-la localmente a uma base de dados do MongoDB, em seguida, implementá-la para uma base de dados API do Azure Cosmos DB para MongoDB. Quando terminar, terá uma aplicação MEAN (MongoDB, Express, AngularJS e Node.js) em execução no Serviço de Aplicações no Linux. Para obter simplicidade, a aplicação de exemplo utiliza a [estrutura Web MEAN.js](https://meanjs.org/).

![Aplicação MEAN.js em execução no Serviço de Aplicações do Azure](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

O que aprende com o saiba como:

> [!div class="checklist"]
> * Criar uma base de dados com a API do Azure Cosmos DB do MongoDB
> * Ligar uma aplicação Node.js ao MongoDB
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos de diagnóstico em fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar o Node.js v6.0 ou posterior e NPM](https://nodejs.org/)
3. [Instalar o Gulp.js](https://gulpjs.com/) (exigido pelo [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
4. [Instalar e executar a Edição de Comunidade do MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

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

```txt
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Navegue para `http://localhost:3000` num browser. Clique em **Inscrever** no menu superior e crie um utilizador de teste. 

A aplicação MEAN.js de exemplo armazena os dados do utilizador na base de dados. Se criar um utilizador e iniciar sessão com êxito, então a aplicação está a escrever dados na base de dados do MongoDB local.

![A aplicação MEAN.js estabelece ligação com êxito ao MongoDB](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Selecione **Administrador > Gerir Artigos** para adicionar alguns artigos.

Para parar o Node.js em qualquer altura, prima `Ctrl+C` no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Criar produção do MongoDB

Neste passo, vai criar uma conta de base de dados com a API do Azure Cosmos DB do MongoDB. Quando a aplicação for implementada no Azure, utiliza esta base de dados na cloud.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-cosmos-db-account"></a>Criar uma conta do Cosmos DB

No Cloud Shell, crie uma conta do Cosmos DB com o comando [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-create).

No seguinte comando, substitua um nome do Cosmos DB exclusivo pelo marcador de posição *\<cosmosdb_name>*. Este nome é utilizado como parte do ponto final do Cosmos DB, `https://<cosmosdb_name>.documents.azure.com/`, por isso, o nome tem de ser exclusivo em todas as contas Cosmos DB no Azure. O nome só pode conter letras minúsculas, números, o caráter hífen (-) e tem de ter entre três e 50 carateres.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

O parâmetro *--kind MongoDB* permite ligações de cliente da MongoDB.

Após criar a conta do Cosmos DB, a CLI do Azure mostra informações semelhantes ao exemplo seguinte:

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies":
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-configured-with-azure-cosmos-dbs-api-for-mongodb"></a>Ligar a aplicação para produção configurada com a API do Azure Cosmos DB para o MongoDB

Neste passo, vai ligar a aplicação de exemplo MEAN.js a uma base de dados do Cosmos DB que acabou de criar, com uma cadeia de ligação do MongoDB.

### <a name="retrieve-the-database-key"></a>Obter a chave de base de dados

Para ligar à base de dados do Cosmos DB, precisa da chave da base de dados. No Cloud Shell, utilize o comando [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-keys) para obter a chave primária.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

A CLI do Azure apresenta informações semelhantes ao exemplo seguinte:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Copie o valor de `primaryMasterKey`. Estas informações são necessárias no passo seguinte.

<a name="devconfig"></a>

### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurar a cadeia de ligação na aplicação Node.js

No seu repositório do MEAN.js local, na pasta _config/env/_, crie um ficheiro denominado _local-production.js_. O _.gitignore_ está configurado para manter este ficheiro fora do repositório.

Copie o código seguinte para o mesmo. Confirme que substitui os dois marcadores de posição *\<cosmosdb_name>* pelo nome da base de dados do Cosmos DB e substitua o marcador de posição *\<primary_master_key>* pela chave que copiou no passo anterior.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

A opção `ssl=true` é necessária porque o [Cosmos DB precisa do SSL](../../cosmos-db/connect-mongodb-account.md#connection-string-requirements).

Guarde as alterações.

### <a name="test-the-application-in-production-mode"></a>Teste a aplicação no modo de produção

Numa janela do terminal local, execute o seguinte comando para minimizar e agrupar scripts para o ambiente de produção. Este processo gera os ficheiros necessários para o ambiente de produção.

```bash
gulp prod
```

Numa janela do terminal local, execute o seguinte comando para utilizar a cadeia de ligação configurada em _config/env/local-production.js_. Ignore o erro de certificado e o aviso config.domain.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` define a variável de ambiente que informa o Node.js para ser executado no ambiente de produção.  `node server.js` inicia o servidor do Node.js com `server.js` na raiz do repositório. Esta é a forma que a sua aplicação do Node.js é carregada no Azure.

Assim que a aplicação é carregada, certifique-se de que está em execução no ambiente de produção:

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Navegue para `http://localhost:8443` num browser. Clique em **Inscrever** no menu superior e crie um utilizador de teste. Se criar um utilizador e iniciar sessão com êxito, então a aplicação está a escrever dados na base de dados do Cosmos DB no Azure.

No terminal, pare o Node.js ao escrever `Ctrl+C`.

## <a name="deploy-app-to-azure"></a>Implementar a aplicação no Azure

Neste passo, vai implementar a aplicação node. js no serviço de aplicações do Azure.

### <a name="configure-local-git-deployment"></a>Configurar a implementação do git local

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

<a name="create"></a>

### <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Configurar uma variável de ambiente

Por predefinição, o projeto do MEAN.js mantém o _config/env/local-production.js_ fora do repositório do Git. Portanto, para a sua aplicação do Azure, utilize as definições da aplicação para definir a cadeia de ligação do MongoDB.

Para configurar as definições da aplicação, utilize o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell.

O exemplo seguinte configura um `MONGODB_URI` definição de aplicação na sua aplicação do Azure. Substitua os marcadores de posição *\<app_name>*, *\<cosmosdb_name>* e *\<primary_master_key>*.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

No código do Node.js, aceda a esta definição de aplicação com `process.env.MONGODB_URI`, tal como faria em qualquer variável de ambiente. 

No seu repositório do MEAN.js local, abra _config/env/production.js_ (não _config/env/local-production.js_), que tem uma configuração específica de ambiente de produção. A aplicação do MEAN.js predefinida já está configurada para utilizar a variável de ambiente `MONGODB_URI` que criou.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
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
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

Poderá reparar que o processo de implementação executa o [Gulp](https://gulpjs.com/) depois de `npm install`. O Serviço de Aplicações não executa tarefas do Gulp ou do Grunt durante a implementação, pelo que este repositório de exemplo tem dois ficheiros adicionais no diretório raiz para a permitir:

- _.implementação_ - este ficheiro diz ao Serviço de Aplicações para executar `bash deploy.sh` como o script de implementação personalizado.
- _deploy.sh_ - o script de implementação personalizado. Se vir o ficheiro, verá que executa `gulp prod` a seguir a `npm install` e `bower install`.

Pode utilizar esta abordagem para adicionar qualquer passo à sua implementação baseada no Git. Se reiniciar a aplicação do Azure a qualquer momento, o serviço de aplicações não volta a executar estas tarefas de automatização.

### <a name="browse-to-the-azure-app"></a>Navegue para a aplicação do Azure

Navegue para a aplicação implementada com o browser.

```bash
http://<app_name>.azurewebsites.net
```

Clique em **Inscrever** no menu superior e crie um utilizador de teste.

Se concluir com êxito e a aplicação iniciar sessão automaticamente no utilizador criado, a sua aplicação do Mean. js no Azure tem conectividade à API do Azure Cosmos DB para o MongoDB.

![Aplicação MEAN.js em execução no Serviço de Aplicações do Azure](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Selecione **Administrador > Gerir Artigos** para adicionar alguns artigos.

**Parabéns!** Está a executar uma aplicação Node.js condicionada por dados no Serviço de Aplicações do Azure no Linux.

## <a name="update-data-model-and-redeploy"></a>Atualizar o modelo de dados e voltar a implementar

Neste passo, altera o modelo de dados `article` e publica a sua alteração no Azure.

### <a name="update-the-data-model"></a>Atualizar o modelo de dados

No repositório MEAN.js local, abra _módules/artigos/servidor/modelos/article.server.model.js_.

No `ArticleSchema`, adicione um tipo `String` denominado `comment`. Quando tiver terminado, o código de esquema deverá este aspeto:

```javascript
let ArticleSchema = new Schema({
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

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Abra _modules/artigos/cliente/vistas/list-articles.client.view.html_.

Exatamente acima da etiqueta de fecho `</a>`, adicione a seguinte linha para apresentar `comment` juntamente com o resto dos dados do artigo:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Abra _módulos/artigos/cliente/vistas/administrador/list-articles.client.view.html_.

No interior do elemento `comment` e imediatamente acima da etiqueta de fecho `<div class="list-group">`, adicione a seguinte linha para apresentar `</a>` juntamente com o resto dos dados do artigo:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Abra _módulos/artigos/cliente/vistas/administrador/form-article.client.view.html_.

Encontre o elemento `<div class="form-group">` que contém o botão para submeter, semelhante ao seguinte:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Imediatamente acima desta etiqueta, adicione outro elemento `<div class="form-group">` que permite que as pessoas editem o campo `comment`. O novo elemento deve ter o seguinte aspeto:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Testar as alterações localmente

Guarde todas as alterações.

Na janela do terminal local, teste novamente as alterações no modo de produção.

```bash
gulp prod
NODE_ENV=production node server.js
```

Navegue para `http://localhost:8443` num browser e certifique-se de que tem sessão iniciada.

Selecione **Administrador > Gerir Artigos** e adicione um artigo ao selecionar o botão **+**.

Pode ver a nova caixa de texto `Comment` agora.

![Campo de comentário adicionado aos Artigos](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

No terminal, pare o Node.js ao escrever `Ctrl+C`.

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

Consolide as suas alterações no Git e envie as alterações ao código para o Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Uma vez o `git push` estiver concluído, navegue para a aplicação do Azure e experimente a nova funcionalidade.

![Alterações ao modelo e à base de dados publicadas no Azure](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Se tiver adicionado quaisquer artigos anteriormente, ainda pode vê-los. Os dados existentes no Cosmos DB não se perdem. Além disso, atualiza o esquema de dados e mantém os dados existentes intactos.

## <a name="manage-your-azure-app"></a>Gerir a sua aplicação do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver a aplicação que criou.

No menu à esquerda, clique em **dos serviços de aplicações**, em seguida, clique no nome da sua aplicação do Azure.

![Navegação do portal para a aplicação do Azure](./media/tutorial-nodejs-mongodb-app/access-portal.png)

Por predefinição, o portal mostra a sua aplicação **descrição geral** página. Esta página proporciona-lhe uma vista do desempenho da aplicação. Aqui, também pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar. Os separadores no lado esquerdo da página mostram as várias páginas de configuração que pode abrir.

![Página Serviço de Aplicações no portal do Azure](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Criar uma base de dados com a API do Azure Cosmos DB do MongoDB
> * Ligar uma aplicação de node. js a uma base de dados
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos do Azure para o seu terminal
> * Gerir a aplicação no portal do Azure

Avance para o próximo tutorial para saber como mapear um nome DNS personalizado à sua aplicação.

> [!div class="nextstepaction"]
> [Mapear um nome DNS existente personalizado para o serviço de aplicações do Azure](../app-service-web-tutorial-custom-domain.md)

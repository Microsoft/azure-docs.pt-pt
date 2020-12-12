---
title: 'Tutorial: Aplicativo Linux Ruby com Postgres'
description: Saiba como obter uma aplicação Linux Ruby a funcionar no Azure App Service, com ligação a uma base de dados PostgreSQL em Azure. Os trilhos são usados no tutorial.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/18/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: f565fcef60b2cb4726b180eb67e6ac1fcaefc24b
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347851"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Construa uma app Ruby e Postgres no Azure App Service em Linux

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra como criar uma aplicação Ruby e conectá-la a uma base de dados PostgreSQL. Quando tiver terminado, terá uma aplicação [Ruby on Rails](https://rubyonrails.org/) em execução no Serviço de Aplicações no Linux.

:::image type="content" source="./media/tutorial-ruby-postgres-app/complete-checkbox-published.png" alt-text="Screenshot de um exemplo de aplicação Ruby on Rails intitulado Tasks.":::

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma base de dados PostgreSQL no Azure
> * Ligar uma aplicação Ruby on Rails ao PostgreSQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos de diagnóstico em fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Instalar o Git](https://git-scm.com/)
- [Instalar Ruby 2.6](https://www.ruby-lang.org/en/documentation/installation/)
- [Instalar o Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
- [Instale e execute PostgreSQL](https://www.postgresql.org/download/)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="prepare-local-postgres"></a>Preparar o Postgres local

Neste passo, vai criar uma base de dados no seu servidor Postgres local para utilizar neste tutorial.

### <a name="connect-to-local-postgres-server"></a>Ligar ao servidor Postgres local

Abra a janela do terminal e execute `psql` para ligar ao seu servidor do Postgres local.

```bash
sudo -u postgres psql
```

Se a ligação for bem-sucedida, a base de dados Postgres está em execução. Caso contrário, certifique-se de que a base de dados Postgres local é iniciada conforme os passos indicados em [Transferências - Distribuição do PostgreSQL Core](https://www.postgresql.org/download/).

Escreva `\q` para sair do cliente Postgres. 

Crie um utilizador de Postgres que possa criar bases de dados ao executar o seguinte comando, com o nome de utilizador com que iniciou sessão no Linux.

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Criar uma aplicação Ruby on Rails localmente
Neste passo, vai obter uma aplicação Ruby on Rails de exemplo, configurar a respetiva ligação à base de dados e executá-la localmente. 

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela de terminal, `cd` num diretório de trabalho.

Execute o seguinte comando para clonar o repositório de exemplo.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` para o diretório clonado. Instale os pacotes necessários.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>Executar o exemplo localmente

Execute as [migrações do Rails](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) para criar as tabelas de que a aplicação precisa. Para ver que tabelas são criadas nas migrações, veja o diretório _db/migrate_ no repositório Git.

```bash
rake db:create
rake db:migrate
```

Execute a aplicação.

```bash
rails server
```

Navegue para `http://localhost:3000` num browser. Adicione algumas tarefas à página.

![O Ruby on Rails liga-se com êxito ao Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Para parar o servidor do Rails, escreva `Ctrl + C` no terminal.

## <a name="create-postgres-in-azure"></a>Criar Postgres no Azure

Neste passo, vai criar uma base de dados Postgres na [Base de Dados do Azure para PostgreSQL](../postgresql/index.yml). Posteriormente, vai configurar a aplicação Ruby on Rails para se ligar a esta base de dados.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

## <a name="create-postgres-database-in-azure"></a>Criar base de dados postgres em Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

Nesta secção, cria-se uma Base de Dados Azure para servidor e base de dados PostgreSQL. Para iniciar, instale a `db-up` extensão com o seguinte comando:

```azurecli
az extension add --name db-up
```

Crie a base de dados Postgres em Azure com o [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) comando, como mostra o exemplo seguinte. *\<postgresql-name>* Substitua-o por um nome *único* (o ponto final do servidor é *https:// \<postgresql-name> .postgres.database.azure.com*). Para *\<admin-username>* e *\<admin-password>* , especificar credenciais para criar um utilizador de administrador para este servidor Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westeurope --server-name <postgresql-name> --database-name sampledb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Este comando pode demorar um pouco porque está a fazer o seguinte:

- Cria um [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) chamado , se não `myResourceGroup` existe. Todos os recursos do Azure precisam de estar num destes. `--resource-group` é opcional.
- Cria um servidor Postgres com o utilizador administrativo.
- Cria uma `sampledb` base de dados.
- Permite o acesso a partir do seu endereço IP local.
- Permite o acesso a partir dos serviços Azure.
- Criar um utilizador de base de dados com acesso à `sampledb` base de dados.

Podes fazer todos os passos separadamente com `az postgres` outros comandos `psql` e, mas `az postgres up` fá-los todos num só passo para ti.

Quando o comando terminar, encontre as linhas de saída com `Ran Database Query:` . Eles mostram o utilizador da base de dados que é criado para si, com o nome de utilizador `root` e a palavra-passe. `Sampledb1` Irá usá-los mais tarde para ligar a sua aplicação à base de dados.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`, pode ser definido para qualquer uma das [regiões de Azure](https://azure.microsoft.com/global-infrastructure/regions/). Pode disponibilizar as regiões à sua subscrição com o [`az account list-locations`](/cli/azure/account#az-account-list-locations) comando. Para aplicações de produção, coloque a sua base de dados e a sua aplicação no mesmo local.

## <a name="connect-app-to-azure-postgres"></a>Ligar a aplicação ao Azure Postgres

Neste passo, vai ligar a aplicação Ruby on Rails à base de dados Postgres que criou na Base de Dados do Azure para PostgreSQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurar a ligação à base de dados

No repositório, abra _config/database.yml_. Na parte inferior do ficheiro, substitua as variáveis de produção pelo código seguinte. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Guarde as alterações.

### <a name="test-the-application-locally"></a>Testar a aplicação localmente

Novamente no terminal local, defina as seguintes variáveis de ambiente:

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=root@<postgres-server-name>
export DB_PASSWORD=Sampledb1
```

Execute migrações de bases de dados do Rails com os valores de produção que acabou de configurar para criar as tabelas na sua base de dados Postgres na Base de Dados do Azure para PostgreSQL.

```bash
rake db:migrate RAILS_ENV=production
```

Quando estiver em execução no ambiente de produção, a aplicação Rails precisa de ativos pré-compilados. Gere os recursos necessários com o seguinte comando:

```bash
rake assets:precompile
```

O ambiente de produção do Rails também utiliza segredos para gerir a segurança. Gere uma chave secreta.

```bash
rails secret
```

Guarde a chave secreta para as respetivas variáveis utilizadas pelo ambiente de produção do Rails. Para sua comodidade, utilize a mesma chave para ambas as variáveis.

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

Ative o ambiente de produção do Rails para servir ficheiros JavaScript e CSS.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Execute a aplicação de exemplo no ambiente de produção.

```bash
rails server -e production
```

Navegue para `http://localhost:3000`. Se a página for carregada sem erros, a aplicação Ruby on Rails está a ligar-se à base de dados Postgres no Azure.

Adicione algumas tarefas à página.

![O Ruby on Rails liga-se com êxito à Base de Dados do Azure para PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Para parar o servidor do Rails, escreva `Ctrl + C` no terminal.

### <a name="commit-your-changes"></a>Consolidar as alterações

Execute os comandos Git seguintes para consolidar as alterações:

```bash
git add .
git commit -m "database.yml updates"
```

A sua aplicação está pronta para ser implementada.

## <a name="deploy-to-azure"></a>Implementar no Azure

Neste passo, vai implementar a aplicação Rails ligada ao Postgres no Serviço de Aplicações do Azure.

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Configurar as definições da base de dados

No Serviço de Aplicações, as variáveis de ambiente são definidas como _definições da aplicação_ com o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) no Cloud Shell.

O comando do Cloud Shell seguinte configura as definições da aplicação `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Substitua o _&lt; nome_ de>e _&lt; o nome do servidor pós-venda>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="root@<postgres-server-name>" DB_PASSWORD="Sampledb1"
```

### <a name="configure-rails-environment-variables"></a>Configurar as variáveis de ambiente do Rails

No terminal local, [gere um novo segredo](configure-language-ruby.md#set-secret_key_base-manually) para o ambiente de produção dos Trilhos em Azure.

```bash
rails secret
```

Configure as variáveis necessárias para o ambiente de produção do Rails.

No seguinte comando Cloud Shell, substitua os dois espaços _&lt; de>secretos de saída dos trilhos_ com a nova chave secreta que gerou no terminal local.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` indica o contentor Ruby predefinido para pré-compilar ativos em cada implementação do Git. Para obter mais informações, consulte [os ativos pré-comutáveis](configure-language-ruby.md#precompile-assets) e [sirva ativos estáticos.](configure-language-ruby.md#serve-static-assets)

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

No terminal local, adicione um Azure remoto ao seu repositório Git local.

```bash
git remote add azure <paste-copied-url-here>
```

Envie para o Azure remoto para implementar a aplicação Ruby on Rails. É-lhe pedida a palavra-passe que indicou anteriormente como parte da criação do utilizador de implementação.

```bash
git push azure master
```

Durante a implementação, o Serviço de Aplicações do Azure comunica o respetivo progresso com o Git.

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Navegue pela app Azure

Navegue para `http://<app-name>.azurewebsites.net` e adicione algumas tarefas à lista.

:::image type="content" source="./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png" alt-text="Screenshot do exemplo da aplicação Azure intitulado Tarefas que mostram tarefas adicionadas à lista.":::

Parabéns! Está agora a executar uma Aplicação Ruby on Rails condicionada por dados no Serviço de Aplicações do Azure.

## <a name="update-model-locally-and-redeploy"></a>Atualizar o modelo localmente e reimplementar

Neste passo, vai fazer uma pequena alteração ao modelo de dados `task` e à aplicação Web e, depois, vai publicá-la no Azure.

No cenário de tarefas, vai modificar a aplicação de modo a poder marcar uma tarefa como concluída.

### <a name="add-a-column"></a>Adicionar uma coluna

No terminal, navegue para a raiz do repositório Git.

Gere uma nova migração que adicione uma coluna booleana denominada `Done` à tabela `Tasks`:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

Este comando gera um novo ficheiro de migração no diretório _db/migrate_.


No terminal, execute migrações de bases de dados do Rails para fazer a alteração na base de dados local.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Atualizar a lógica da aplicação

Abra o ficheiro *app/controllers/tasks_controller.rb*. No final do ficheiro, localize a seguinte linha:

```rb
params.require(:task).permit(:Description)
```

Modifique esta linha para incluir o novo parâmetro `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Atualizar as vistas

Abra o ficheiro *app/views/tasks/_form.html.erb*, que é o Formulário de edição.

Localize a linha `<%=f.error_span(:Description) %>` e insira o seguinte código diretamente abaixo da mesma:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Abra o ficheiro *app/views/tasks/show.html.erb*, que é a Página de visualização de registo único. 

Localize a linha `<dd><%= @task.Description %></dd>` e insira o seguinte código diretamente abaixo da mesma:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Abra o ficheiro *app/views/tasks/index.html.erb*, que é a Página de índice de todos os registos.

Localize a linha `<th><%= model_class.human_attribute_name(:Description) %></th>` e insira o seguinte código diretamente abaixo da mesma:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

No mesmo ficheiro, localize a linha `<td><%= task.Description %></td>` e insira o seguinte código diretamente abaixo da mesma:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Testar as alterações localmente

No terminal local, execute o servidor do Rails.

```bash
rails server
```

Para ver a alteração do estado da tarefa, navegue para `http://localhost:3000` e adicione ou edite os itens.

![Caixa de verificação adicionada à tarefa](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Para parar o servidor do Rails, escreva `Ctrl + C` no terminal.

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

No terminal, execute migrações de bases de dados do Rails para o ambiente de produção para fazer a alteração na base de dados do Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Consolide todas as alterações no Git e envie as alterações ao código para o Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Uma vez `git push` concluída, navegue para a app Azure e teste a nova funcionalidade.

![Alterações ao modelo e à base de dados publicadas no Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Se tiver adicionado tarefas, estas são mantidas na base de dados. As atualizações ao esquema de dados não afetam os dados já existentes.

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Gerir a app Azure

Vá ao [portal Azure](https://portal.azure.com) para gerir a app que criou.

A partir do menu esquerdo, clique em **Serviços de Aplicação** e, em seguida, clique no nome da sua aplicação Azure.

![Navegação do portal para a aplicação do Azure](./media/tutorial-php-mysql-app/access-portal.png)

Veja a página geral da sua aplicação. Aqui, pode realizar tarefas de gestão básicas, como parar, iniciar, reiniciar, navegar e eliminar.

O menu do lado esquerdo disponibiliza páginas para configurar a aplicação.

![Página Serviço de Aplicações no portal do Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma base de dados Postgres no Azure
> * Ligar uma aplicação Ruby on Rails ao Postgres
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos de diagnóstico em fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

Avance para o próximo tutorial para aprender a mapear um nome DNS personalizado para a sua aplicação.

> [!div class="nextstepaction"]
> [Tutorial: Mapeie o nome DNS personalizado para a sua aplicação](app-service-web-tutorial-custom-domain.md)

Ou, confira outros recursos:

> [!div class="nextstepaction"]
> [Configure a app Ruby](configure-language-ruby.md)

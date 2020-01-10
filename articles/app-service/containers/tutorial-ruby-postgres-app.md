---
title: 'Tutorial: aplicativo Ruby do Linux com postgres'
description: Saiba como obter um aplicativo Ruby do Linux trabalhando no serviço Azure App, com conexão a um banco de dados PostgreSQL no Azure. Os trilhos são usados no tutorial.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 03/27/2019
ms.custom: seodec18
ms.openlocfilehash: 3238de3f1d3416964af05db031b5ecec20e4f140
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720196"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Compilar um aplicativo Ruby e postgres no serviço Azure App no Linux

O [Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática através do sistema operativo Linux. Este tutorial mostra como criar um aplicativo Ruby e conectá-lo a um banco de dados PostgreSQL. Quando tiver terminado, terá uma aplicação [Ruby on Rails](https://rubyonrails.org/) em execução no Serviço de Aplicações no Linux.

![Aplicação Ruby on Rails em execução no Serviço de Aplicações do Azure](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma base de dados PostgreSQL no Azure
> * Ligar uma aplicação Ruby on Rails ao PostgreSQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos de diagnóstico em fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Instale o Git](https://git-scm.com/)
* [Instalar o Ruby 2.3](https://www.ruby-lang.org/en/documentation/installation/)
* [Instalar o Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
* [Instalar e executar o PostgreSQL](https://www.postgresql.org/download/)

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

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Criar Postgres no Azure

Neste passo, vai criar uma base de dados Postgres na [Base de Dados do Azure para PostgreSQL](/azure/postgresql/). Posteriormente, vai configurar a aplicação Ruby on Rails para se ligar a esta base de dados.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Criar um servidor Postgres

Criar um servidor PostgreSQL com o comando [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create).

Execute o seguinte comando na Cloud Shell e substitua um nome de servidor exclusivo pelo espaço reservado *\<postgres-Server-name >* . O nome do servidor tem de ser exclusivo em todos os servidores no Azure. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres-server-name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

Após criar o servidor da Base de Dados do Azure para PostgreSQL, a CLI do Azure mostra informações semelhantes ao exemplo seguinte:

```json
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "<postgres-server-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgres-server-name>",
  "location": "westeurope",
  "name": "<postgres-server-name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Configurar a firewall do servidor

No Cloud Shell, crie uma regra de firewall para o servidor Postgres permitir ligações ao cliente com o comando [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az-postgres-server-firewall-rule-create). Quando os IPs inicial e final estão definidos como 0.0.0.0, a firewall apenas é aberta para outros recursos do Azure. Substitua um nome de servidor exclusivo para o *\<postgres-Server-name >* espaço reservado.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres-server-name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Pode ser ainda mais restritivo na sua regra de firewall ao [utilizar apenas os endereços IP de saída que a aplicação utiliza](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-postgres-server-locally"></a>Ligar ao servidor Postgres de produção localmente

No Cloud Shell, ligue ao servidor Postgres no Azure. Use o valor especificado anteriormente para o _&lt;postgres-Server-name >_ espaços reservados.

```bash
psql -U adminuser@<postgres-server-name> -h <postgres-server-name>.postgres.database.azure.com postgres
```

Quando lhe for pedida uma palavra-passe, utilize _My5up3r$tr0ngPa$w0rd!_ , que especificou quando criou o servidor de base de dados.

### <a name="create-a-production-database"></a>Criar uma base de dados de produção

No comando `postgres`, crie uma base de dados.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Criar um utilizador com permissões

Crie um utilizador de base de dados com o nome _railsappuser_ e conceda-lhe todos os privilégios da base de dados `sampledb`.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017';
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

Escreva `\q` para sair da ligação ao servidor.

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
export DB_USERNAME=railsappuser@<postgres-server-name>
export DB_PASSWORD=MyPostgresAzure2017
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

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Configurar as definições da base de dados

No Serviço de Aplicações, as variáveis de ambiente são definidas como _definições da aplicação_ com o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell.

O comando do Cloud Shell seguinte configura as definições da aplicação `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Substitua os espaços reservados _&lt;appname >_ e _&lt;postgres-server-name >_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres-server-name>" DB_PASSWORD="MyPostgresAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Configurar as variáveis de ambiente do Rails

No terminal local, [gere um novo segredo](configure-language-ruby.md#set-secret_key_base-manually) para o ambiente de produção do Rails no Azure.

```bash
rails secret
```

Configure as variáveis necessárias para o ambiente de produção do Rails.

No comando Cloud Shell a seguir, substitua os dois espaços reservados _&lt;saída-de-trilho >_ com a nova chave secreta gerada no terminal local.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` indica o contentor Ruby predefinido para pré-compilar ativos em cada implementação do Git. Para obter mais informações, consulte [pré-compilar ativos](configure-language-ruby.md#precompile-assets) e [atender ativos estáticos](configure-language-ruby.md#serve-static-assets).

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

```bash
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
< Output has been truncated for readability >
```

### <a name="browse-to-the-azure-app"></a>Navegue até o aplicativo do Azure

Navegue para `http://<app-name>.azurewebsites.net` e adicione algumas tarefas à lista.

![Aplicação Ruby on Rails em execução no Serviço de Aplicações do Azure](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

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

Quando o `git push` for concluído, navegue até o aplicativo do Azure e teste a nova funcionalidade.

![Alterações ao modelo e à base de dados publicadas no Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Se tiver adicionado tarefas, estas são mantidas na base de dados. As atualizações ao esquema de dados não afetam os dados já existentes.

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Gerenciar o aplicativo do Azure

Vá para o [portal do Azure](https://portal.azure.com) para gerenciar o aplicativo que você criou.

No menu à esquerda, clique em **serviços de aplicativos**e, em seguida, clique no nome do seu aplicativo do Azure.

![Navegação do portal para a aplicação do Azure](./media/tutorial-php-mysql-app/access-portal.png)

Você vê a página de visão geral do seu aplicativo. Aqui, pode realizar tarefas de gestão básicas, como parar, iniciar, reiniciar, navegar e eliminar.

O menu do lado esquerdo disponibiliza páginas para configurar a aplicação.

![Página Serviço de Aplicações no portal do Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

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

Avance para o próximo tutorial para saber como mapear um nome DNS personalizado para seu aplicativo.

> [!div class="nextstepaction"]
> [Tutorial: mapear o nome DNS personalizado para seu aplicativo](../app-service-web-tutorial-custom-domain.md)

Ou então, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar aplicativo Ruby](configure-language-ruby.md)

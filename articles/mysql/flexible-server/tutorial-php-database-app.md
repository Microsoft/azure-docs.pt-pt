---
title: 'Tutorial: Construa uma app PHP (Laravel) com Base de Dados Azure para MySQL Flexible Server'
description: Este tutorial explica como construir uma aplicação PHP com servidor flexível.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc
ms.openlocfilehash: 1bad9a7da6f0604f910ce1095b734043be8cf3c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90946717"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>Tutorial: Construa uma app PHP (Laravel) e MySQL Flexible Server (Preview) no Azure App Service


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="PhP Web App em Azure com Servidor Flexível":::

[O Azure App Service](https://docs.microsoft.com/azure/app-service/overview) fornece um serviço de hospedagem web altamente escalável e auto-remendado utilizando o sistema operativo Linux. Este tutorial mostra como criar uma aplicação PHP em Azure e conectá-la a uma base de dados MySQL. Quando terminar, terá uma aplicação [Laravel](https://laravel.com/) em execução no Azure App Service no Linux.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configurar uma aplicação PHP (Laravel) com o MySQL local
> * Criar um Servidor Flexível MySQL (Pré-visualização)
> * Conecte uma aplicação PHP ao MySQL Flexible Server (Pré-visualização)
> * Implementar a app para o Azure App Service
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Gerir a aplicação no portal do Azure

Se não tiver uma [subscrição do Azure,](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar o PHP 5.6.4 ou superior](https://php.net/downloads.php)
3. [Instalar o Composer](https://getcomposer.org/doc/00-intro.md)
4. Ative as extensões do PHP seguintes, de que o Laravel precisa: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
5. [Instalar e iniciar o MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

## <a name="prepare-local-mysql"></a>Preparar o MySQL local

Neste passo, vai criar uma base de dados no seu servidor MySQL local para utilizar neste tutorial.

### <a name="connect-to-local-mysql-server"></a>Ligar ao servidor MySQL local

Numa janela de terminal, ligue ao seu servidor MySQL local. Pode utilizar esta janela de terminal para executar todos os comandos deste tutorial.

```bash
mysql -u root -p
```

Se lhe for pedida uma palavra-passe, introduza a palavra-passe da conta `root`. Se não se lembrar da sua palavra-passe da conta de raiz, veja [MySQL: Como Repor a Palavra-passe de Raiz](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se o comando for executado com êxito, significa que o servidor MySQL está em execução. Se não, siga os [passos de pós-instalação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) para verificar se o servidor MySQL local é iniciado.

### <a name="create-a-database-locally"></a>Criar uma base de dados localmente

No comando `mysql`, crie uma base de dados.

```sql
CREATE DATABASE sampledb;
```

Escreva `quit` para sair da ligação ao servidor.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Criar uma aplicação PHP localmente
Neste passo, vai obter uma aplicação Laravel de exemplo, configurar a respetiva ligação à base de dados e executá-la localmente.

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela do terminal, navegue para um diretório vazio onde pode clonar a aplicação da amostra.  Execute o seguinte comando para clonar o repositório de exemplo.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` para o diretório clonado.
Instale os pacotes necessários.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Configurar a ligação ao MySQL

Na raiz do repositório, crie um ficheiro com o nome *.env*. Copie as variáveis seguintes para o ficheiro *.env*. Substitua o espaço reservado _ &lt; root_password>_ pela palavra-passe do utilizador raiz MySQL.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Para obter informações sobre como o Laravel utiliza o ficheiro _.env_, veja [Configuração do Ambiente do Laravel](https://laravel.com/docs/5.4/configuration#environment-configuration).

### <a name="run-the-sample-locally"></a>Executar o exemplo localmente

Execute [migrações de bases de dados Laravel](https://laravel.com/docs/5.4/migrations) para criar as tabelas de que a aplicação precisa. Para ver que tabelas são criadas nas migrações, veja o diretório _database/migrations_ no repositório Git.

```bash
php artisan migrate
```

Gere uma chave de aplicação do Laravel nova.

```bash
php artisan key:generate
```

Execute a aplicação.

```bash
php artisan serve
```

Navegue para `http://localhost:8000` num browser. Adicione algumas tarefas à página.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PhP Web App em Azure com Servidor Flexível":::

Para parar o PHP, escreva `Ctrl + C` no terminal.

## <a name="create-a-mysql-flexible-server-preview"></a>Criar um Servidor Flexível MySQL (Pré-visualização)
Neste passo, cria uma base de dados MySQL na [Base de Dados Azure para o MySQL Flexible Server](/azure/mysql) que está em pré-visualização pública. Posteriormente, vai configurar a aplicação PHP para se ligar a esta base de dados. No [Azure Cloud Shell,](https://docs.microsoft.com/azure/cloud-shell/overview)crie um servidor com o [`az flexible-server create`](/cli/azure/mysql/server#az-mysql-flexible-server-create) comando.

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - Tome nota do nome do **servidor** e da cadeia **de ligação** para usá-lo no passo seguinte para ligar e executar a migração de dados laravel.
> - Para o argumento **ip-endereço,**  forneça o IP da sua máquina cliente. O servidor está bloqueado quando criado e é necessário permitir o acesso à sua máquina cliente para gerir o servidor localmente.

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>Configure a firewall do servidor para permitir que a web app se conecte ao servidor

Na Cloud Shell, crie uma regra de firewall para o seu servidor MySQL para permitir ligações ao cliente utilizando o comando de firewall do servidor az mysql. Quando tanto o IP inicial como o IP final estão definidos para ```0.0.0.0``` , a firewall só é aberta para outros serviços Azure que não tenham um IP estático para ligar ao servidor.

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>Ligar ao servidor MySQL de produção localmente

Na janela de terminal local, ligue ao servidor MySQL no Azure. Utilize o valor especificado anteriormente para ```<admin-user>``` e ```<mysql-server-name>``` . Quando lhe for pedida uma palavra-passe, utilize a que especificou quando criou a base de dados no Azure.

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Criar uma base de dados de produção

No comando `mysql`, crie uma base de dados.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Criar um utilizador com permissões

Crie um utilizador de base de dados com o nome _phpappuser_ e conceda-lhe todos os privilégios da base de dados `sampledb`. Para simplificar o tutorial, use o _MySQLAzure2020_ como senha.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Escreva `quit` para sair da ligação ao servidor.

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>Conecte a aplicação ao servidor flexível MySQL

Neste passo, vai ligar a aplicação PHP à base de dados MySQL que criou na Base de Dados do Azure para MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurar a ligação à base de dados

Na raiz do repositório, crie um ficheiro _.env.production_ e copie as variáveis seguintes para o mesmo. Substitua o _ &lt;>de nome do meu servidor_ de espaço reservado em *DB_HOST* e *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Guarde as alterações.

> [!TIP]
> Para proteger as informações da ligação do MySQL, este ficheiro já está excluído do repositório Git (veja _.gitignore_ na raiz do repositório). Mais tarde, vai aprender a configurar variáveis de ambiente no Serviço de Aplicações para ligar à base de dados na Base de Dados do Azure para MySQL. Com as variáveis de ambiente, não precisa do ficheiro *.env* no Serviço de Aplicações.
>

### <a name="configure-tlsssl-certificate"></a>Configure certificado TLS/SSL

Por padrão, o MySQL Flexible Server aplica ligações TLS dos clientes. Para se ligar à sua base de dados MySQL em Azure, tem de utilizar o certificado [ _.pem_ fornecido pela Base de Dados Azure para o MySQL Flexible Server](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem). Faça o download [deste certificado](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)) e coloque-o na pasta **ssl** na cópia local do repositório de aplicações da amostra.

Abra _config/database.php_ e adicione os parâmetros `sslmode` e `options` a `connections.mysql`, conforme mostrado no código abaixo.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

### <a name="test-the-application-locally"></a>Testar a aplicação localmente

Execute migrações de bases de dados do Laravel tendo _.env.production_ como o ficheiro de ambiente para criar as tabelas na sua base de dados MySQL na Base de Dados do Azure para MySQL. Lembre-se de que _.env.production_ tem as informações da ligação à sua base de dados MySQL no Azure.

```bash
php artisan migrate --env=production --force
```

_.env.production_ ainda não tem uma chave de aplicação válida. Gere uma nova no terminal.

```bash
php artisan key:generate --env=production --force
```

Execute a aplicação de exemplo tendo _.env.production_ como o ficheiro de ambiente.

```bash
php artisan serve --env=production
```

Navegue para `http://localhost:8000`. Se a página for carregada sem erros, a aplicação PHP está a ligar-se à base de dados MySQL no Azure.

Adicione algumas tarefas à página.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PhP Web App em Azure com Servidor Flexível":::

Para parar o PHP, escreva `Ctrl + C` no terminal.

### <a name="commit-your-changes"></a>Consolidar as alterações

Execute os comandos Git seguintes para consolidar as alterações:

```bash
git add .
git commit -m "database.php updates"
```

A sua aplicação está pronta para ser implementada.

## <a name="deploy-to-azure"></a>Implementar no Azure

Neste passo, vai implementar a aplicação PHP ligada ao MySQL no Serviço de Aplicações do Azure.

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

A FTP e o Git local podem implementar para uma aplicação web Azure utilizando um utilizador de implementação. Uma vez configurar o utilizador de implementação, pode usá-lo para todas as suas implementações Azure. O nome de utilizador e palavra-passe de implementação ao nível da sua conta são diferentes das suas credenciais de subscrição Azure.

Para configurar o utilizador de implementação, executar o comando [de configuração de implementação az webapp](https://docs.microsoft.com/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) em Azure Cloud Shell. Substitua _ &lt; o nome de utilizador>_ e>de _ &lt; palavra-passe_ pelo nome de utilizador e palavra-passe do utilizador da sua implementação.

O nome de utilizador deve ser único dentro do Azure, e para os pushes git locais, não deve conter o símbolo '@'.
A palavra-passe deve ter pelo menos oito caracteres, com dois dos seguintes três elementos: letras, números e símbolos.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

A saída JSON mostra a palavra-passe como nula. Se tiver um "Conflito". Detalhes: erro 409, alterar o nome de utilizador. Se receber um "Mau Pedido". Detalhes: 400 erro, use uma palavra-passe mais forte. **Grave o seu nome de utilizador e palavra-passe para usar para implementar as suas aplicações web.**

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

Na Cloud Shell, crie um plano de Serviço de Aplicações no grupo de recursos com o [plano de appservice az criar](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) comando. O exemplo a seguir cria um plano de Serviço de Aplicações chamado myAppServicePlan no nível de preços gratuitos (-sku F1) e num recipiente Linux (--is-linux).

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux

<a name="create"></a>

### <a name="create-a-web-app"></a>Criar uma aplicação Web

Crie uma [aplicação web](https://docs.microsoft.com/azure/app-service/overview#app-service-on-linux) no plano de Serviço de Aplicações myAppServicePlan.

Na Cloud Shell, pode utilizar o comando [de criação de webapp az.](https://docs.microsoft.com/cli/azure/webapp#az-webapp-create) No exemplo seguinte, substitua o _ &lt; nome da aplicação>_ por um nome de aplicação globalmente único (caracteres válidos são , e `a-z` `0-9` `-` ). O runtime está definido como `PHP|7.0`. Para ver todos os tempos de execução suportados, executar [az webapp list-runtimes -- linux](https://docs.microsoft.com/cli/azure/webapp#az-webapp-list-runtimes).

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Criou uma nova aplicação Web vazia, com a implementação de git ativada.

> [!NOTE]
> O URL do comando Git é mostrado na propriedade de DeploymentLocalGitUrl, com o formato https:// <username> @<nome de aplicação>.scm.azurewebsites.net/<app-name>.git. Guarde este URL, uma vez que vai precisar dele mais tarde.

### <a name="configure-database-settings"></a>Configurar as definições da base de dados

No Serviço de Aplicações, as variáveis de ambiente são definidas como _definições da aplicação_ com o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

O comando seguinte configura as definições da aplicação `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Substitua o _ &lt; nome de aplicativos dos_ espaços reservados>e _ &lt; o nome do meu servidor mysql-server>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Pode utilizar o método [getenv](https://www.php.net/manual/en/function.getenv.php) do PHP para aceder às definições. O código do Laravel utiliza um wrapper [env](https://laravel.com/docs/5.4/helpers#method-env) no `getenv` do PHP. Por exemplo, a configuração do MySQL em _config/database.php_ é semelhante ao código abaixo:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Configurar variáveis de ambiente do Laravel

O Laravel precisa de uma chave de aplicação no Serviço de Aplicações. Pode configurá-la nas definições da aplicação.

Na janela de terminal local, utilize `php artisan` para gerar uma chave de aplicação nova sem a guardar em _.env_.

```bash
php artisan key:generate --show
```

Na Cloud Shell, desaprove a chave de aplicação na aplicação Do Serviço de Aplicações utilizando o [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) comando. Substitua o nome de>de _ &lt; aplicação_ dos espaços reservados e _ &lt; a saídaofphpartisankey:gere>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` diz ao Laravel para devolver informações de depuragem quando a aplicação implementada encontrar erros. Ao executar uma aplicação de produção, defina-o como `false`, que é mais seguro.

### <a name="set-the-virtual-application-path"></a>Definir o caminho de aplicação virtual

O ciclo de vida da [aplicação Laravel](https://laravel.com/docs/5.4/lifecycle) começa no diretório _público_ em vez do diretório de raiz da aplicação. A imagem PHP predefinida do Docker para o Serviço de Aplicações utiliza o Apache e não lhe permite personalizar `DocumentRoot` para o Laravel. No entanto, pode utilizar `.htaccess` para reescrever todos os pedidos para que apontem para _/public_ em vez do diretório de raiz. Na raiz do repositório, já é adicionado um `.htaccess` para este fim. Com o mesmo, a sua aplicação do Laravel está pronta para ser implementada.

Para obter mais informações, consulte [alterar a raiz do site.](https://docs.microsoft.com/azure/app-service/configure-language-php?pivots=platform-linux#change-site-root)

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

Regresse à janela de terminal local e adicione um remoto do Azure ao seu repositório Git local. Substitua _ &lt; a implementaçãoLocalGitUrl-from-create-step>_ pelo URL do comando Git que guardou a partir de Criar uma [aplicação web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando o Git Credential Manager lhe pedir credenciais, certifique-se de que introduz as credenciais criadas em **Configurar um utilizador de implementação**, e não as credenciais que utiliza para iniciar sessão no portal Azure.

```bash
git push azure master
```

Este comando pode demorar alguns minutos a ser executado. Ao executar, apresenta informações semelhantes ao exemplo seguinte:

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

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="PhP Web App em Azure com Servidor Flexível":::

Parabéns! Está agora a executar uma Aplicação PHP condicionada por dados no Serviço de Aplicações do Azure.

## <a name="update-model-locally-and-redeploy"></a>Atualizar o modelo localmente e reimplementar

Neste passo, vai fazer uma pequena alteração ao modelo de dados `task` e à aplicação Web e, depois, vai publicá-la no Azure.

No cenário de tarefas, vai modificar a aplicação de modo a poder marcar uma tarefa como concluída.

### <a name="add-a-column"></a>Adicionar uma coluna

Na janela de terminal local, navegue para a raiz do repositório Git.

Gere uma migração de base de dados nova para a tabela `tasks`:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Este comando mostra-lhe o nome do ficheiro de migração que foi gerado. Localize o ficheiro em _database/migrations_ e abra-o.

Substitua o método `up` pelo código abaixo:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

O código anterior adiciona uma coluna booleana na tabela `tasks`, com o nome `complete`.

Substitua o método `down` pelo código seguinte na ação de reversão:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Na janela de terminal local, execute migrações de bases de dados do Laravel para fazer a alteração na base de dados local.

```bash
php artisan migrate
```

Com base na [convenção de nomenclatura do Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), o modelo `Task` (veja _app/Task.php_) mapeia para a tabela `tasks` por predefinição.

### <a name="update-application-logic"></a>Atualizar a lógica da aplicação

Abra o ficheiro *routes/web.php*. A aplicação define as respetivas rotas e a lógica de negócio aqui.

No fim do ficheiro, adicione uma rota com o código abaixo:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

O código anterior faz uma simples atualização ao modelo de dados, ao alternar o valor de `complete`.

### <a name="update-the-view"></a>Atualizar a vista

Abra o ficheiro *resources/views/tasks.blade.php*. Localize o código de início `<tr>` e substitua-o por:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

O código anterior altera a cor da linha, dependendo de a tarefa estar concluída ou não.

Na linha seguinte, tem o código abaixo:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Substitua a linha anterior pelo código abaixo:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

O código anterior adiciona o botão Submeter que referencia a rota que definiu anteriormente.

### <a name="test-the-changes-locally"></a>Testar as alterações localmente

Na janela de terminal local, execute o servidor de programação a partir do diretório de raiz do repositório Git.

```bash
php artisan serve
```

Para ver a alteração do estado da tarefa, navegue para `http://localhost:8000` e selecione a caixa de verificação.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="PhP Web App em Azure com Servidor Flexível":::

Para parar o PHP, escreva `Ctrl + C` no terminal.

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

Na janela de terminal local, execute migrações de bases de dados do Laravel com a cadeia de ligação de produção para fazer a alteração na base de dados do Azure.

```bash
php artisan migrate --env=production --force
```

Consolide todas as alterações no Git e envie as alterações ao código para o Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Uma vez `git push` concluída, navegue para a app Azure e teste a nova funcionalidade.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="PhP Web App em Azure com Servidor Flexível":::

Se tiver adicionado tarefas, estas são mantidas na base de dados. As atualizações ao esquema de dados não afetam os dados já existentes.

## <a name="clean-up-resources"></a>Limpar os recursos
Nos passos anteriores, criou os recursos do Azure num grupo de recursos. Se achar que não vai precisar destes recursos no futuro, execute o seguinte comando no Cloud Shell para eliminar o grupo de recursos:

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como gerir os seus recursos no portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal) <br/>
> [!div class="nextstepaction"]
> [Como gerir o seu servidor](how-to-manage-server-cli.md)

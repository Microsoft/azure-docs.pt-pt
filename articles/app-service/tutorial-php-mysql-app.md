---
title: 'Tutorial: app PHP com MySQL'
description: Saiba como pôr aplicações PHP a funcionar no Azure, com ligação a uma base de dados MySQL no Azure. Laravel é usado no tutorial.
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.devlang: php
ms.topic: tutorial
ms.date: 06/15/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: b321985bf7920934193723b60abb7bfb28482e6d
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862246"
---
# <a name="tutorial-build-a-php-and-mysql-app-in-azure-app-service"></a>Tutorial: Construa uma app PHP e MySQL no Azure App Service

::: zone pivot="platform-windows"  

[O Azure App Service](overview.md) fornece um serviço de hospedagem web altamente escalável e auto-remendado utilizando o sistema operativo Windows. Este tutorial mostra como criar uma aplicação PHP em Azure e conectá-la a uma base de dados MySQL. Quando terminar, terá uma aplicação [Laravel](https://laravel.com/) em execução no Azure App Service no Windows.

::: zone-end

::: zone pivot="platform-linux"

[O Azure App Service](overview.md) fornece um serviço de hospedagem web altamente escalável e auto-remendado utilizando o sistema operativo Linux. Este tutorial mostra como criar uma aplicação PHP em Azure e conectá-la a uma base de dados MySQL. Quando terminar, terá uma aplicação [Laravel](https://laravel.com/) em execução no Azure App Service no Linux.

::: zone-end

:::image type="content" source="./media/tutorial-php-mysql-app/complete-checkbox-published.png" alt-text="Screenshot de um exemplo de aplicação PHP intitulado Lista de Tarefas.":::

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma base de dados MySQL no Azure
> * Ligar uma aplicação PHP ao MySQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos de diagnóstico em fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Instalar o Git](https://git-scm.com/)
- [Instalar o PHP 5.6.4 ou superior](https://php.net/downloads.php)
- [Instalar o Composer](https://getcomposer.org/doc/00-intro.md)
- Ative as extensões do PHP seguintes, de que o Laravel precisa: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
- [Instale e inicie o MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

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

Na janela de terminal, `cd` num diretório de trabalho.

Execute o seguinte comando para clonar o repositório de exemplo.

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

Na raiz do repositório, crie um ficheiro com o nome *.env*. Copie as variáveis seguintes para o ficheiro *.env*. Substitua o espaço reservado _&lt; root_password>_ pela palavra-passe do utilizador raiz MySQL.

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

![O PHP liga-se com êxito ao MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Para parar o PHP, escreva `Ctrl + C` no terminal.

## <a name="create-mysql-in-azure"></a>Criar o MySQL no Azure

Neste passo, vai criar uma base de dados MySQL na [Base de Dados do Azure para MySQL](../mysql/index.yml). Posteriormente, vai configurar a aplicação PHP para se ligar a esta base de dados.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Criar um servidor MySQL

Na Cloud Shell, crie um servidor na Base de Dados Azure para o MySQL com o [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest&preserve-view=true#az-mysql-server-create) comando.

No seguinte comando, substitua um nome de servidor exclusivo para o espaço reservado, um nome de *\<mysql-server-name>* utilizador para o , e uma *\<admin-user>* palavra-passe para o *\<admin-password>*  espaço reservado. O nome do servidor é utilizado como parte do ponto final do MySQL (`https://<mysql-server-name>.mysql.database.azure.com`), por isso, o nome tem de ser exclusivo em todos os servidores no Azure. Para obter mais informações sobre a seleção do MySQL DB SKU, consulte [Criar uma Base de Dados Azure para o servidor MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md#create-an-azure-database-for-mysql-server).

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name> --location "West Europe" --admin-user <admin-user> --admin-password <admin-password> --sku-name B_Gen5_1
```

Quando o servidor MySQL tiver sido criado, a CLI do Azure mostra informações semelhantes às do exemplo abaixo:

<pre>
{
  "administratorLogin": "&lt;admin-user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "westeurope",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
  -  &lt; Output has been truncated for readability &gt;
}
</pre>

### <a name="configure-server-firewall"></a>Configurar a firewall do servidor

Na Cloud Shell, crie uma regra de firewall para o seu servidor MySQL para permitir ligações ao cliente utilizando o [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest&preserve-view=true#az-mysql-server-firewall-rule-create) comando. Quando os IPs inicial e final estão definidos como 0.0.0.0, a firewall apenas é aberta para outros recursos do Azure. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Pode ser ainda mais restritivo na sua regra de firewall ao [utilizar apenas os endereços IP de saída que a aplicação utiliza](overview-inbound-outbound-ips.md#find-outbound-ips).
>

Na Cloud Shell, volte a executar o comando para permitir o acesso a partir do seu computador local, substituindo *\<your-ip-address>* o seu endereço IP [IPv4 local](https://www.whatsmyip.org/).

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Ligar ao servidor MySQL de produção localmente

Na janela de terminal local, ligue ao servidor MySQL no Azure. Utilize o valor especificado anteriormente para _&lt;>de utilizador de administração_ e _&lt;>de nome do servidor mysql_. Quando lhe for pedida uma palavra-passe, utilize a que especificou quando criou a base de dados no Azure.

```bash
mysql -u <admin-user>@<mysql-server-name> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Criar uma base de dados de produção

No comando `mysql`, crie uma base de dados.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Criar um utilizador com permissões

Crie um utilizador de base de dados com o nome _phpappuser_ e conceda-lhe todos os privilégios da base de dados `sampledb`. Para simplificar o tutorial, utilize o _MySQLAzure2017_ como senha.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Escreva `quit` para sair da ligação ao servidor.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Ligar a aplicação ao MySQL do Azure

Neste passo, vai ligar a aplicação PHP à base de dados MySQL que criou na Base de Dados do Azure para MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurar a ligação à base de dados

Na raiz do repositório, crie um ficheiro _.env.production_ e copie as variáveis seguintes para o mesmo. Substitua o>_ &lt; de nome do placeholder_ mysql-servidor em *DB_HOST* e *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql-server-name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Guarde as alterações.

> [!TIP]
> Para proteger as informações da ligação do MySQL, este ficheiro já está excluído do repositório Git (veja _.gitignore_ na raiz do repositório). Mais tarde, vai aprender a configurar variáveis de ambiente no Serviço de Aplicações para ligar à base de dados na Base de Dados do Azure para MySQL. Com as variáveis de ambiente, não precisa do ficheiro *.env* no Serviço de Aplicações.
>

### <a name="configure-tlsssl-certificate"></a>Configure certificado TLS/SSL

Por padrão, a Azure Database for MySQL aplica ligações TLS dos clientes. Para ligar à sua base de dados MySQL no Azure, tem de utilizar o certificado [_.pem_ fornecido pela Base de Dados do Azure para MySQL](../mysql/howto-configure-ssl.md).

Abra _config/database.php_ e adicione os parâmetros `sslmode` e `options` a `connections.mysql`, conforme mostrado no código abaixo.

::: zone pivot="platform-windows"  

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

::: zone-end

::: zone pivot="platform-linux"

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem',
    ] : []
],
```

::: zone-end

Neste tutorial, o certificado `BaltimoreCyberTrustRoot.crt.pem` é fornecido no repositório para sua conveniência. 

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

![O PHP liga-se com êxito à Base de Dados do Azure para MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

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

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Criar uma aplicação Web

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

::: zone-end

### <a name="configure-database-settings"></a>Configurar as definições da base de dados

No Serviço de Aplicações, as variáveis de ambiente são definidas como _definições da aplicação_ com o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set).

O comando seguinte configura as definições da aplicação `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Substitua o _&lt; nome de aplicativos dos_ espaços reservados>e _&lt; o nome do meu servidor mysql-server>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql-server-name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
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

Na Cloud Shell, desaprove a chave de aplicação na aplicação Do Serviço de Aplicações utilizando o [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) comando. Substitua o nome de>de _&lt; aplicação_ dos espaços reservados e _&lt; a saídaofphpartisankey:gere>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` diz ao Laravel para devolver informações de depuragem quando a aplicação implementada encontrar erros. Ao executar uma aplicação de produção, defina-o como `false`, que é mais seguro.

### <a name="set-the-virtual-application-path"></a>Definir o caminho de aplicação virtual

::: zone pivot="platform-windows"  

Desaprote o caminho da aplicação virtual para a aplicação. Este passo é necessário porque o [ciclo de vida da aplicação Laravel](https://laravel.com/docs/5.4/lifecycle) começa no diretório _public_ em vez do diretório de raiz da aplicação. Outras arquiteturas PHP cujo ciclo de vida tem início no diretório de raiz podem funcionar sem configuração manual do caminho de aplicação virtual.

Na Cloud Shell, desacorda o caminho da aplicação virtual utilizando o [`az resource update`](/cli/azure/resource#az-resource-update) comando. Substitua o _&lt; nome de aplicação>_ espaço reservado.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Por predefinição, o Azure App Service aponta o caminho de aplicação virtual raiz _/_ () para o diretório de raiz dos ficheiros de aplicações implantados _(sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

O ciclo de vida da [aplicação Laravel](https://laravel.com/docs/5.4/lifecycle) começa no diretório _público_ em vez do diretório de raiz da aplicação. A imagem PHP predefinida do Docker para o Serviço de Aplicações utiliza o Apache e não lhe permite personalizar `DocumentRoot` para o Laravel. No entanto, pode utilizar `.htaccess` para reescrever todos os pedidos para que apontem para _/public_ em vez do diretório de raiz. Na raiz do repositório, já é adicionado um `.htaccess` para este fim. Com o mesmo, a sua aplicação do Laravel está pronta para ser implementada.

Para obter mais informações, consulte [alterar a raiz do site.](configure-language-php.md#change-site-root)

::: zone-end

### <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

::: zone pivot="platform-windows"  

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

> [!NOTE]
> Poderá reparar que o processo de implementação instala pacotes do [Composer](https://getcomposer.org/) no fim. O Serviço de Aplicações não executa estas automatizações durante a implementação predefinida, pelo que este repositório de exemplo tem três ficheiros adicionais no diretório de raiz para a permitir:
>
> - `.deployment` – este ficheiro diz ao Serviço de Aplicações para executar `bash deploy.sh` como o script de implementação personalizado.
> - `deploy.sh` – o script de implementação personalizado. Se revir o ficheiro, verá que executa `php composer.phar install` a seguir a `npm install`.
> - `composer.phar` – o gestor de pacotes do Composer.
>
> Pode utilizar esta abordagem para adicionar qualquer passo à sua implementação baseada no Git no Serviço de Aplicações. Para obter mais informações, veja [Script de Implementação Personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Navegue pela app Azure

Navegue para `http://<app-name>.azurewebsites.net` e adicione algumas tarefas à lista.

:::image type="content" source="./media/tutorial-php-mysql-app/php-mysql-in-azure.png" alt-text="Screenshot da aplicação Azure exemplo intitulado Lista de Tarefas mostrando novas tarefas adicionadas.":::

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

![Caixa de verificação adicionada à tarefa](./media/tutorial-php-mysql-app/complete-checkbox.png)

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
git push azure main
```

Uma vez `git push` concluída, navegue para a app Azure e teste a nova funcionalidade.

![Alterações ao modelo e à base de dados publicadas no Azure](media/tutorial-php-mysql-app/complete-checkbox-published.png)

Se tiver adicionado tarefas, estas são mantidas na base de dados. As atualizações ao esquema de dados não afetam os dados já existentes.

## <a name="stream-diagnostic-logs"></a>Transmitir registos de diagnóstico em fluxo

::: zone pivot="platform-windows"  

Enquanto executa a aplicação PHP no Serviço de Aplicações do Azure, pode obter os registos de consola direcionados para o seu terminal. Dessa forma, pode obter as mesmas mensagens de diagnóstico para ajudar a depurar erros de aplicações.

Para iniciar o streaming de registos, utilize o [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest&preserve-view=true#az-webapp-log-tail) comando na Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Uma vez iniciado o streaming de registos, refresque a app Azure no navegador para obter algum tráfego web. Verá então os registos da consola direcionados para o terminal. Se não vir os registos da consola imediatamente, volte a consultar dentro de 30 segundos.

Para parar a transmissão de registos em fluxo em qualquer altura, escreva `Ctrl`+`C`.

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

> [!TIP]
> Uma aplicação PHP pode utilizar o padrão [error_log()](https://php.net/manual/function.error-log.php) como saída para a consola. A aplicação de exemplo utiliza esta abordagem em _app/Http/routes.php_.
>
> Como uma arquitetura Web [o Laravel utiliza o Monolog](https://laravel.com/docs/5.4/errors) como fornecedor de registo. Para ver como o Monolog envia mensagens para a consola, veja [PHP: como utilizar o Monolog para iniciar sessão na consola (php://out)](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>
>

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
> * Criar uma base de dados MySQL no Azure
> * Ligar uma aplicação PHP ao MySQL
> * Implementar a aplicação no Azure
> * Atualizar o modelo de dados e voltar a implementar a aplicação
> * Transmitir os registos de diagnóstico em fluxo a partir do Azure
> * Gerir a aplicação no portal do Azure

Avance para o tutorial seguinte para aprender a mapear um nome DNS personalizado para a aplicação.

> [!div class="nextstepaction"]
> [Tutorial: Mapeie o nome DNS personalizado para a sua aplicação](app-service-web-tutorial-custom-domain.md)

Ou, confira outros recursos:

> [!div class="nextstepaction"]
> [Configure app PHP](configure-language-php.md)

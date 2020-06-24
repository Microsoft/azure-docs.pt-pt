---
title: Configure aplicativos PHP
description: Saiba como configurar um recipiente PHP pré-construído para a sua aplicação. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e4237f1eecb9f6542aac946525ff4583e478c2e
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905702"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configure um aplicativo Linux PHP para o Azure App Service

Este guia mostra-lhe como configurar o tempo de execução PHP incorporado para aplicações web, extremidades traseiras móveis e aplicações API no Azure App Service.

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores de PHP que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [quickstart PHP](quickstart-php.md) e [o PHP com o tutorial MySQL.](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>Mostrar versão PHP

Para mostrar a versão PHP atual, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões PHP suportadas, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Definir versão PHP

Executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão PHP para 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Personalize a automatização de construção

Se implementar a sua aplicação utilizando pacotes Git ou zip com automatização de construção ligada, o Serviço de Aplicações constrói passos de automação através da seguinte sequência:

1. Executar script personalizado se especificado por `PRE_BUILD_SCRIPT_PATH` .
1. Execute `php composer.phar install`.
1. Executar script personalizado se especificado por `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND`e `POST_BUILD_COMMAND` são variáveis ambientais que estão vazias por defeito. Para executar comandos pré-construção, defina `PRE_BUILD_COMMAND` . Para executar comandos pós-construção, defina `POST_BUILD_COMMAND` .

O exemplo a seguir especifica as duas variáveis a uma série de comandos, separados por vírgulas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para variáveis ambientais adicionais para personalizar a automatização de construção, consulte [a configuração oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o App Service funciona e constrói aplicações PHP no Linux, consulte [a documentação do Oryx: Como as aplicações PHP são detetadas e construídas.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)

## <a name="customize-start-up"></a>Personalizar o arranque

Por predefinição, o recipiente PHP incorporado executa o servidor Apache. No arranque, `apache2ctl -D FOREGROUND"` funciona. Se quiser, pode executar um comando diferente no arranque, executando o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir definições](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) de aplicações fora do seu código de aplicação. Em seguida, pode acessá-los usando o padrão [getenv()](https://secure.php.net/manual/function.getenv.php) padrão. Por exemplo, para aceder a uma configuração de aplicação chamada `DB_HOST` , use o seguinte código:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Alterar raiz do site

A estrutura web da sua escolha pode usar um subdirecional como raiz do site. Por exemplo, [Laravel,](https://laravel.com/)usa o `public/` subdirecional como raiz do site.

A imagem PHP padrão para o Serviço de Aplicações utiliza o Apache, e não permite personalizar a raiz do site para a sua aplicação. Para contornar esta limitação, adicione um ficheiro *.htaccess* à sua raiz de repositório com o seguinte conteúdo:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Se preferir não utilizar a reescrita *.htaccess*, pode implementar a aplicação do Laravel com uma [imagem do Docker personalizada](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Detetar sessão HTTPS

No Serviço de Aplicações, a [rescisão de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos equilibristas de carga de rede, pelo que todos os pedidos HTTPS chegam à sua aplicação como pedidos HTTP não encriptados. Se a lógica da sua aplicação precisar de verificar se os pedidos do utilizador estão encriptados ou não, inspecione o `X-Forwarded-Proto` cabeçalho.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

As estruturas web populares permitem-lhe aceder à `X-Forwarded-*` informação no seu padrão de aplicação padrão. No [CodeIgniter,](https://codeigniter.com/)o [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) verifica o valor por `X_FORWARDED_PROTO` defeito.

## <a name="customize-phpini-settings"></a>Personalize as configurações php.ini

Se precisar de alterar a sua instalação PHP, pode alterar qualquer uma das [php.ini diretivas](https://www.php.net/manual/ini.list.php) seguindo estes passos.

> [!NOTE]
> A melhor maneira de ver a versão PHP e a configuração *atualphp.ini* é chamar [phpinfo na](https://php.net/manual/function.phpinfo.php) sua aplicação.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Personalizar diretivas não PHP_INI_SYSTEM

Para personalizar PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL diretivas (ver [php.ini diretivas),](https://www.php.net/manual/ini.list.php)adicione um ficheiro *.htaccess* ao diretório de raiz da sua aplicação.

No ficheiro *.htaccess,* adicione as diretivas utilizando a `php_value <directive-name> <value>` sintaxe. Por exemplo:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Reimplante a sua aplicação com as alterações e reinicie-a. Se o implementar com o Kudu (por exemplo, utilizando [o Git),](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)é reiniciado automaticamente após a sua implantação.

Como alternativa à utilização *de .htaccess,* pode utilizar [ini_set na](https://www.php.net/manual/function.ini-set.php) sua app para personalizar estas diretivas não PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Personalizar PHP_INI_SYSTEM diretivas

Para personalizar PHP_INI_SYSTEM diretivas (ver [php.ini diretivas),](https://www.php.net/manual/ini.list.php)não pode utilizar a abordagem *.htaccess.* O Serviço de Aplicações fornece um mecanismo separado utilizando a definição da `PHP_INI_SCAN_DIR` aplicação.

Primeiro, executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para adicionar uma definição de aplicação chamada `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`é o diretório padrão onde *existephp.ini.* `/home/site/ini`é o diretório personalizado no qual você vai adicionar um arquivo personalizado *.ini.* Separa-se os valores com um `:` .

Navegue para a sessão web SSH com o seu recipiente Linux `https://<app-name>.scm.azurewebsites.net/webssh/host` ().

Crie um diretório `/home/site` chamado `ini` , em seguida, crie um ficheiro *.ini* no `/home/site/ini` diretório (por exemplo, *settings.ini)* com as diretivas que pretende personalizar. Use a mesma sintaxe que usaria num ficheiro *php.ini.* 

> [!TIP]
> Nos recipientes Linux incorporados no Serviço de Aplicações, */home* é usado como armazenamento compartilhado persistido. 
>

Por exemplo, para alterar o valor de [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) executar os seguintes comandos:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Para que as alterações entrem em vigor, reinicie a aplicação.

## <a name="enable-php-extensions"></a>Ativar extensões PHP

As instalações PHP incorporadas contêm as extensões mais utilizadas. Pode permitir extensões adicionais da mesma forma que [personaliza php.ini diretivas](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor maneira de ver a versão PHP e a configuração *atualphp.ini* é chamar [phpinfo na](https://php.net/manual/function.phpinfo.php) sua aplicação.
>

Para permitir extensões adicionais, seguindo estes passos:

Adicione um `bin` diretório ao diretório de raiz da sua aplicação e coloque os `.so` ficheiros de extensão nela (por exemplo, *mongodb.so).* Certifique-se de que as extensões são compatíveis com a versão PHP em Azure e são compatíveis com VC9 e não-thread-safe (nts).

Desdobre as suas alterações.

Siga os passos nas [diretivas Personalizar PHP_INI_SYSTEM,](#customize-php_ini_system-directives)adicione as extensões no ficheiro *personalizado .ini* com as diretivas [de extensão](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Para que as alterações entrem em vigor, reinicie a aplicação.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH aberto no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Quando uma aplicação PHP funcionada se comporta de forma diferente no Serviço de Aplicações ou tem erros, experimente o seguinte:

- [Aceda ao fluxo de registo](#access-diagnostic-logs).
- Teste a aplicação localmente em modo de produção. O App Service executa a sua aplicação em modo de produção, pelo que tem de se certificar de que o seu projeto funciona como esperado no modo de produção local. Por exemplo:
    - Dependendo da sua *composer.js,* podem ser instaladas diferentes embalagens para o modo de produção `require` (vs. `require-dev` ).
    - Certas estruturas web podem implantar ficheiros estáticos de forma diferente no modo de produção.
    - Certas estruturas web podem usar scripts de arranque personalizados quando estão em modo de produção.
- Execute a sua aplicação no Serviço de Aplicações em modo de depuragem. Por exemplo, em [Laravel,](https://meanjs.org/)pode configurar a sua app para depurar mensagens de depuração de saída em [produção, definindo a `APP_DEBUG` `true` definição da app para ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: app PHP com MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Serviço de Aplicações Linux FAQ](app-service-linux-faq.md)

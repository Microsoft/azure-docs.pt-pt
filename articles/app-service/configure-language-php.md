---
title: Configure aplicativos PHP
description: Saiba como configurar uma aplicação PHP nas instâncias nativas do Windows, ou num recipiente PHP pré-construído, no Azure App Service. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: afac8273b5729bcf5470be471145214426dc7dab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90055304"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Configure um aplicativo PHP para o Azure App Service

Este guia mostra-lhe como configurar as suas aplicações web PHP, back ends móveis e aplicações API no Azure App Service.

Este guia fornece conceitos e instruções fundamentais para desenvolvedores de PHP que implementam aplicações para o Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [quickstart PHP](quickstart-php.md) e [o PHP com o tutorial MySQL.](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>Mostrar versão PHP

::: zone pivot="platform-windows"  

Para mostrar a versão PHP atual, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Para mostrar todas as versões PHP suportadas, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Para mostrar a versão PHP atual, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões PHP suportadas, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>Definir versão PHP

::: zone pivot="platform-windows"  

Executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão PHP para 7.4:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

Executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão PHP para 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Compositor de execução

Se quiser que o Serviço de Aplicações execute [o Compositor](https://getcomposer.org/) no momento da implementação, a maneira mais fácil é incluir o Compositor no seu repositório.

A partir de uma janela de terminal local, mude o diretório para a raiz do repositório e siga as instruções no [download](https://getcomposer.org/download/) *composer.phar* para a raiz do diretório.

Executar os seguintes comandos (precisa de [npm](https://www.npmjs.com/get-npm) instalado):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A sua raiz de repositório tem agora dois ficheiros adicionais: *.deployment* and *deploy.sh*.

Abra *deploy.sh* e encontre a `Deployment` secção, que se parece com esta:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Adicione a secção de código que necessita para executar a ferramenta necessária *no final* da `Deployment` secção:

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Comprometa todas as alterações e implemente o seu código utilizando o Git, ou o Zip implemente com automatização de construção ativada. O compositor deve agora funcionar como parte da automatização de implantação.

## <a name="run-gruntbowergulp"></a>Executar Grunt/Bower/Gulp

Se pretender que o Serviço de Aplicações execute ferramentas de automação populares no tempo de implementação, como Grunt, Bower ou Gulp, precisa de fornecer um [script de implementação personalizado.](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) O Serviço de Aplicações executa este script quando implementa com o Git, ou com [a implementação zip](deploy-zip.md) com automatização de construção ativada. 

Para ativar o seu repositório para executar estas ferramentas, precisa adicioná-las às dependências *empackage.js.* Por exemplo:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

A partir de uma janela de terminal local, mude o diretório para a raiz do repositório e execute os seguintes comandos (precisa de [npm](https://www.npmjs.com/get-npm) instalado):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A sua raiz de repositório tem agora dois ficheiros adicionais: *.deployment* and *deploy.sh*.

Abra *deploy.sh* e encontre a `Deployment` secção, que se parece com esta:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Esta secção termina com a `npm install --production` execução. Adicione a secção de código que necessita para executar a ferramenta necessária *no final* da `Deployment` secção:

- [Estação Bower](#bower)
- [Gole](#gulp)
- [Grunt](#grunt)

Veja um [exemplo na amostra MEAN.js,](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)onde o script de implementação também executa um `npm install` comando personalizado.

### <a name="bower"></a>Bower

Este corte `bower install` corre.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gole

Este corte `gulp imagemin` corre.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Este corte `grunt` corre.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Personalize a automatização de construção

Se implementar a sua aplicação utilizando pacotes Git ou zip com automatização de construção ligada, o Serviço de Aplicações constrói passos de automação através da seguinte sequência:

1. Executar script personalizado se especificado por `PRE_BUILD_SCRIPT_PATH` .
1. Execute `php composer.phar install`.
1. Executar script personalizado se especificado por `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND` e `POST_BUILD_COMMAND` são variáveis ambientais que estão vazias por defeito. Para executar comandos pré-construção, defina `PRE_BUILD_COMMAND` . Para executar comandos pós-construção, defina `POST_BUILD_COMMAND` .

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

::: zone-end

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [configurar as definições da aplicação](configure-common.md#configure-app-settings) fora do código da aplicação. Em seguida, pode acessá-los usando o padrão [getenv()](https://secure.php.net/manual/function.getenv.php) padrão. Por exemplo, para aceder a uma definição da aplicação chamada `DB_HOST`, utilize o seguinte código:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Alterar raiz do site

::: zone pivot="platform-windows"  

A estrutura web da sua escolha pode usar um subdirecional como raiz do site. Por exemplo, [Laravel,](https://laravel.com/)utiliza o público/subdireoriário como raiz do site. 

Para personalizar a raiz do site, desacorra o caminho de aplicação virtual para a aplicação utilizando o [`az resource update`](/cli/azure/resource#az-resource-update) comando. O exemplo a seguir define  a raiz do site para o público/subdireorito no seu repositório. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Por predefinição, o Azure App Service aponta o caminho de aplicação virtual raiz _/_ () para o diretório de raiz dos ficheiros de aplicações implantados _(sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

A estrutura web da sua escolha pode usar um subdirecional como raiz do site. Por exemplo, [Laravel,](https://laravel.com/)usa o `public/` subdirecional como raiz do site.

A imagem PHP padrão para o Serviço de Aplicações utiliza o Apache, e não permite personalizar a raiz do site para a sua aplicação. Para contornar esta limitação, adicione um ficheiro *.htaccess* à sua raiz de repositório com o seguinte conteúdo:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^(.*)
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Se preferir não utilizar a reescrita *.htaccess*, pode implementar a aplicação do Laravel com uma [imagem do Docker personalizada](quickstart-custom-container.md).

::: zone-end

## <a name="detect-https-session"></a>Detetar sessão HTTPS

No Serviço de Aplicações, a [rescisão de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos equilibristas de carga de rede, pelo que todos os pedidos HTTPS chegam à sua aplicação como pedidos HTTP não encriptados. Se a lógica da sua aplicação precisar de verificar se os pedidos do utilizador estão encriptados ou não, inspecione o `X-Forwarded-Proto` cabeçalho.

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
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

::: zone pivot="platform-windows"  

Para personalizar PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL diretivas (ver [php.ini diretivas),](https://www.php.net/manual/ini.list.php)adicione um `.user.ini` ficheiro ao diretório de raiz da sua aplicação.

Adicione as definições de configuração ao `.user.ini` ficheiro utilizando a mesma sintaxe que utilizaria num `php.ini` ficheiro. Por exemplo, se pretender ligar a `display_errors` definição e definir `upload_max_filesize` a definição para 10M, o seu `.user.ini` ficheiro conteria este texto:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Reimplante a sua aplicação com as alterações e reinicie-a.

Como alternativa à utilização de um `.user.ini` ficheiro, pode utilizar [ini_set na](https://www.php.net/manual/function.ini-set.php) sua app para personalizar estas diretivas não PHP_INI_SYSTEM.

::: zone-end

::: zone pivot="platform-linux"

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

Reimplante a sua aplicação com as alterações e reinicie-a. Se o implementar com o Kudu (por exemplo, utilizando [o Git),](deploy-local-git.md)é reiniciado automaticamente após a sua implantação.

Como alternativa à utilização *de .htaccess,* pode utilizar [ini_set na](https://www.php.net/manual/function.ini-set.php) sua app para personalizar estas diretivas não PHP_INI_SYSTEM.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Personalizar PHP_INI_SYSTEM diretivas

::: zone pivot="platform-windows"  

Para personalizar PHP_INI_SYSTEM diretivas (ver [php.ini diretivas),](https://www.php.net/manual/ini.list.php)não pode utilizar a abordagem *.htaccess.* O Serviço de Aplicações fornece um mecanismo separado utilizando a definição da `PHP_INI_SCAN_DIR` aplicação.

Primeiro, executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para adicionar uma definição de aplicação chamada `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Navegue até à consola Kudu `https://<app-name>.scm.azurewebsites.net/DebugConsole` e navegue até `d:\home\site` .

Crie um diretório `d:\home\site` chamado `ini` , em seguida, crie um ficheiro *.ini* no `d:\home\site\ini` diretório (por exemplo, *settings.ini)* com as diretivas que pretende personalizar. Use a mesma sintaxe que usaria num ficheiro *php.ini.* 

Por exemplo, para alterar o valor de [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) executar os seguintes comandos:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Para que as alterações entrem em vigor, reinicie a aplicação.

::: zone-end

::: zone pivot="platform-linux"

Para personalizar PHP_INI_SYSTEM diretivas (ver [php.ini diretivas),](https://www.php.net/manual/ini.list.php)não pode utilizar a abordagem *.htaccess.* O Serviço de Aplicações fornece um mecanismo separado utilizando a definição da `PHP_INI_SCAN_DIR` aplicação.

Primeiro, executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para adicionar uma definição de aplicação chamada `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`é o diretório padrão onde *existephp.ini.* `/home/site/ini`é o diretório personalizado no qual você vai adicionar um arquivo *personalizado .ini.* Separa-se os valores com um `:` .

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

::: zone-end

## <a name="enable-php-extensions"></a>Ativar extensões PHP

::: zone pivot="platform-windows"  

As instalações PHP incorporadas contêm as extensões mais utilizadas. Pode permitir extensões adicionais da mesma forma que [personaliza php.ini diretivas](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor maneira de ver a versão PHP e a configuração *atualphp.ini* é chamar [phpinfo na](https://php.net/manual/function.phpinfo.php) sua aplicação.
>

Para permitir extensões adicionais, seguindo estes passos:

Adicione um `bin` diretório ao diretório de raiz da sua aplicação e coloque os `.dll` ficheiros de extensão nela (por exemplo, *mongodb.dll).* Certifique-se de que as extensões são compatíveis com a versão PHP em Azure e são compatíveis com VC9 e não-thread-safe (nts).

Desdobre as suas alterações.

Siga os passos nas [diretivas Personalizar PHP_INI_SYSTEM](#customize-php_ini_system-directives), adicione as extensões no ficheiro *.ini* personalizado com as diretivas [de extensão](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

Para que as alterações entrem em vigor, reinicie a aplicação.

::: zone-end

::: zone pivot="platform-linux"

As instalações PHP incorporadas contêm as extensões mais utilizadas. Pode permitir extensões adicionais da mesma forma que [personaliza php.ini diretivas](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor maneira de ver a versão PHP e a configuração *atualphp.ini* é chamar [phpinfo na](https://php.net/manual/function.phpinfo.php) sua aplicação.
>

Para permitir extensões adicionais, seguindo estes passos:

Adicione um `bin` diretório ao diretório de raiz da sua aplicação e coloque os `.so` ficheiros de extensão nela (por exemplo, *mongodb.so).* Certifique-se de que as extensões são compatíveis com a versão PHP em Azure e são compatíveis com VC9 e não-thread-safe (nts).

Desdobre as suas alterações.

Siga os passos nas [diretivas Personalizar PHP_INI_SYSTEM](#customize-php_ini_system-directives), adicione as extensões no ficheiro *.ini* personalizado com as diretivas [de extensão](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Para que as alterações entrem em vigor, reinicie a aplicação.

::: zone-end

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

::: zone pivot="platform-windows"  

Utilize o utilitário [standard error_log()](https://php.net/manual/function.error-log.php) para fazer os seus registos de diagnóstico para aparecer no Azure App Service.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Resolução de problemas

Quando uma aplicação PHP funcionada se comporta de forma diferente no Serviço de Aplicações ou tem erros, experimente o seguinte:

- [Aceda ao fluxo de registo](#access-diagnostic-logs).
- Teste a aplicação localmente em modo de produção. O App Service executa a sua aplicação em modo de produção, pelo que tem de se certificar de que o seu projeto funciona como esperado no modo de produção local. Por exemplo:
    - Dependendo da sua *composer.js,* podem ser instaladas diferentes embalagens para o modo de produção `require` (vs. `require-dev` ).
    - Certas estruturas web podem implantar ficheiros estáticos de forma diferente no modo de produção.
    - Certas estruturas web podem usar scripts de arranque personalizados quando estão em modo de produção.
- Execute a sua aplicação no Serviço de Aplicações em modo de depuragem. Por exemplo, em [Laravel,](https://meanjs.org/)pode configurar a sua app para depurar mensagens de depuração de saída em [produção, definindo a `APP_DEBUG` `true` definição da app para ](configure-common.md#configure-app-settings).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: app PHP com MySQL](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [FAQ do Serviço de Aplicações no Linux](faq-app-service-linux.md)

::: zone-end


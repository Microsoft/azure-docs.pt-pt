---
title: Configurar aplicativos PHP do Windows
description: Saiba como configurar uma aplicação PHP nas instâncias nativas do Windows do Serviço de Aplicações. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1eb4e9d349fdd0097cbde4e4cef3d5c61a167193
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908142"
---
# <a name="configure-a-windows-php-app-for-azure-app-service"></a>Configure um aplicativo Windows PHP para o Azure App Service

Este guia mostra-lhe como configurar as suas aplicações web PHP, back ends móveis e aplicações API no Azure App Service. Este guia aplica-se a aplicações hospedadas na plataforma Windows. Para obter informações sobre aplicações linux, consulte [configurar uma aplicação Linux PHP para o Azure App Service](containers/configure-language-php.md).

Este guia fornece conceitos e instruções fundamentais para desenvolvedores de PHP que implementam aplicações para o Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [quickstart PHP](app-service-web-get-started-php.md) e [o PHP com o tutorial MySQL.](app-service-web-tutorial-php-mysql.md)

## <a name="show-php-version"></a>Mostrar versão PHP

Para mostrar a versão PHP atual, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Para mostrar todas as versões PHP suportadas, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep php
```

## <a name="set-php-version"></a>Definir versão PHP

Executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão PHP para 7.4:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

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

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir definições](configure-common.md#configure-app-settings) de aplicações fora do seu código de aplicação. Em seguida, pode acessá-los usando o padrão [getenv()](https://secure.php.net/manual/function.getenv.php) padrão. Por exemplo, para aceder a uma configuração de aplicação chamada `DB_HOST` , use o seguinte código:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Alterar raiz do site

A estrutura web da sua escolha pode usar um subdirecional como raiz do site. Por exemplo, [Laravel,](https://laravel.com/)utiliza o público/subdireoriário como raiz do site. *public/*

Para personalizar a raiz do site, desacorra o caminho de aplicação virtual para a aplicação utilizando o [`az resource update`](/cli/azure/resource#az-resource-update) comando. O exemplo a seguir define *public/* a raiz do site para o público/subdireorito no seu repositório. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Por predefinição, o Azure App Service aponta o caminho de aplicação virtual raiz _/_ () para o diretório de raiz dos ficheiros de aplicações implantados _(sites\wwwroot_).

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

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Personalizar PHP_INI_SYSTEM diretivas

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

## <a name="enable-php-extensions"></a>Ativar extensões PHP

As instalações PHP incorporadas contêm as extensões mais utilizadas. Pode permitir extensões adicionais da mesma forma que [personaliza php.ini diretivas](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor maneira de ver a versão PHP e a configuração *atualphp.ini* é chamar [phpinfo na](https://php.net/manual/function.phpinfo.php) sua aplicação.
>

Para permitir extensões adicionais, seguindo estes passos:

Adicione um `bin` diretório ao diretório de raiz da sua aplicação e coloque os `.so` ficheiros de extensão nela (por exemplo, *mongodb.so).* Certifique-se de que as extensões são compatíveis com a versão PHP em Azure e são compatíveis com VC9 e não-thread-safe (nts).

Desdobre as suas alterações.

Siga os passos nas [diretivas Personalizar PHP_INI_SYSTEM,](#customize-php_ini_system-directives)adicione as extensões no ficheiro *personalizado .ini* com as diretivas [de extensão](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```
extension=d:\home\site\wwwroot\bin\mongodb.so
zend_extension=d:\home\site\wwwroot\bin\xdebug.so
```

Para que as alterações entrem em vigor, reinicie a aplicação.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

Utilize o utilitário [standard error_log()](https://php.net/manual/function.error-log.php) para fazer os seus registos de diagnóstico para aparecer no Azure App Service.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Quando uma aplicação PHP funcionada se comporta de forma diferente no Serviço de Aplicações ou tem erros, experimente o seguinte:

- [Aceda ao fluxo de registo](#access-diagnostic-logs).
- Teste a aplicação localmente em modo de produção. O App Service executa a sua aplicação em modo de produção, pelo que tem de se certificar de que o seu projeto funciona como esperado no modo de produção local. Por exemplo:
    - Certas estruturas web podem implantar ficheiros estáticos de forma diferente no modo de produção.
    - Certas estruturas web podem usar scripts de arranque personalizados quando estão em modo de produção.
- Execute a sua aplicação no Serviço de Aplicações em modo de depuragem. Por exemplo, em [Laravel,](https://meanjs.org/)pode configurar a sua app para depurar mensagens de depuração de saída em [produção, definindo a `APP_DEBUG` `true` definição da app para ](configure-common.md#configure-app-settings).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Tutorial: app PHP com MySQL](app-service-web-tutorial-php-mysql.md)

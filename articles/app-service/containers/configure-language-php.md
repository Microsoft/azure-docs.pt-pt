---
title: Configure aplicativos PHP
description: Aprenda a configurar um recipiente PHP pré-construído para a sua aplicação. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e87466f810dc4ebf767c36ad74c358cbf6069e5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758871"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configure uma aplicação Linux PHP para o Serviço de Aplicações Azure

Este guia mostra-lhe como configurar o tempo de execução de PHP incorporado para aplicações web, back ends móveis e aplicações API no Azure App Service.

Este guia fornece conceitos e instruções fundamentais para desenvolvedores php que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [PHP quickstart](quickstart-php.md) e php com o [tutorial MySQL.](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>Mostrar versão PHP

Para mostrar a versão PHP atual, execute o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões PHP suportadas, execute o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Definir a versão PHP

Executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão PHP para 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Personalize a automatização de construção

Se implementar a sua aplicação utilizando pacotes Git ou zip com automatização de construção ligada, o Serviço de Aplicações constrói passos de automação através da seguinte sequência:

1. Executar script personalizado se `PRE_BUILD_SCRIPT_PATH`especificado por .
1. Execute `php composer.phar install`.
1. Executar script personalizado se `POST_BUILD_SCRIPT_PATH`especificado por .

`PRE_BUILD_COMMAND`e `POST_BUILD_COMMAND` são variáveis ambientais que são vazias por padrão. Para executar comandos pré-construção, defina `PRE_BUILD_COMMAND`. Para executar comandos pós-construção, defina `POST_BUILD_COMMAND`.

O exemplo seguinte especifica as duas variáveis a uma série de comandos, separados por vírgulas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para variáveis ambientais adicionais para personalizar a automatização de construção, consulte a [configuração oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o App Service executa e constrói aplicações PHP em Linux, consulte [documentação oryx: como as aplicações PHP são detetadas e construídas.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)

## <a name="customize-start-up"></a>Personalizar o arranque

Por padrão, o recipiente PHP incorporado executa o servidor Apache. No arranque, funciona. `apache2ctl -D FOREGROUND"` Se quiser, pode executar um comando diferente no arranque, executando o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir as definições](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) de aplicações fora do seu código de aplicações. Em seguida, pode acessá-los usando o padrão [getenv()](https://secure.php.net/manual/function.getenv.php) padrão. Por exemplo, para aceder `DB_HOST`a uma definição de aplicação chamada , use o seguinte código:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Alterar a raiz do site

O quadro web da sua escolha pode usar um subdiretório como raiz do site. Por exemplo, [Laravel,](https://laravel.com/)usa o `public/` subdiretório como raiz do site.

A imagem php padrão para App Service utiliza Apache, e não permite personalizar a raiz do site para a sua aplicação. Para contornar esta limitação, adicione um ficheiro *.htaccess* à sua raiz de repositório com o seguinte conteúdo:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Se preferir não utilizar a reescrita *.htaccess*, pode implementar a aplicação do Laravel com uma [imagem do Docker personalizada](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Detetar sessão HTTPS

No Serviço de Aplicações, a rescisão do [SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos equilibradores de carga da rede, pelo que todos os pedidos HTTPS chegam à sua aplicação como pedidos HTTP não encriptados. Se a lógica da sua aplicação necessitar de verificar se `X-Forwarded-Proto` os pedidos do utilizador estão encriptados ou não, inspecione o cabeçalho.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Os quadros web populares permitem-lhe aceder à `X-Forwarded-*` informação no padrão padrão da sua aplicação. No [CodeIgniter,](https://codeigniter.com/)o [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) `X_FORWARDED_PROTO` verifica o valor por defeito.

## <a name="customize-phpini-settings"></a>Personalize as definições de php.ini

Se precisar de fazer alterações na sua instalação PHP, pode alterar qualquer uma das [diretivas php.ini](https://www.php.net/manual/ini.list.php) seguindo estes passos.

> [!NOTE]
> A melhor maneira de ver a versão PHP e a configuração *php.ini* atual é ligar para [phpinfo()](https://php.net/manual/function.phpinfo.php) na sua aplicação.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Personalizar diretivas não PHP_INI_SYSTEM

Para personalizar as diretivas PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL (ver [diretivas php.ini),](https://www.php.net/manual/ini.list.php)adicione um ficheiro *.htaccess* ao diretório raiz da sua app.

No ficheiro *.htaccess,* adicione as `php_value <directive-name> <value>` diretivas utilizando a sintaxe. Por exemplo:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Recoloque a sua aplicação com as alterações e reinicie-a. Se o implementar com kudu (por exemplo, utilizando [git),](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)é automaticamente reiniciado após a implantação.

Como alternativa à utilização de *.htaccess,* pode utilizar [ini_set()](https://www.php.net/manual/function.ini-set.php) na sua aplicação para personalizar estas diretivas não PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Personalizar diretivas PHP_INI_SYSTEM

Para personalizar PHP_INI_SYSTEM diretivas (ver [diretivas php.ini),](https://www.php.net/manual/ini.list.php)não pode utilizar a abordagem *.htaccess.* O Serviço de Aplicações `PHP_INI_SCAN_DIR` fornece um mecanismo separado utilizando a definição da aplicação.

Primeiro, execute o seguinte comando na [Cloud](https://shell.azure.com) `PHP_INI_SCAN_DIR`Shell para adicionar uma definição de aplicação chamada :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`é o diretório padrão onde *php.ini* existe. `/home/site/ini`é o diretório personalizado no qual você adicionará um ficheiro personalizado *.ini.* Separa os valores com um `:`.

Navegue para a sessão Web SSH com o seu recipiente Linux ().`https://<app-name>.scm.azurewebsites.net/webssh/host`

Crie um `/home/site` diretório chamado `ini`, em seguida, `/home/site/ini` crie um ficheiro *.ini* no diretório (por exemplo, *configurações.ini)* com as diretivas que pretende personalizar. Use a mesma sintaxe que usaria num ficheiro *php.ini.* 

> [!TIP]
> Nos contentores Linux embutidos no App Service, *a /casa* é usada como armazenamento partilhado persistente. 
>

Por exemplo, para alterar o valor da [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) executar os seguintes comandos:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Para que as alterações entrem em vigor, reinicie a aplicação.

## <a name="enable-php-extensions"></a>Ativar extensões PHP

As instalações PHP incorporadas contêm as extensões mais utilizadas. Pode permitir extensões adicionais da mesma forma que personaliza as [diretivas php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor maneira de ver a versão PHP e a configuração *php.ini* atual é ligar para [phpinfo()](https://php.net/manual/function.phpinfo.php) na sua aplicação.
>

Para permitir extensões adicionais, seguindo estes passos:

Adicione `bin` um diretório ao diretório raiz da `.so` sua aplicação e coloque os ficheiros de extensão na sua aplicação (por exemplo, *mongodb.so).* Certifique-se de que as extensões são compatíveis com a versão PHP em Azure e são compatíveis com VC9 e não-thread-safe (nts).

Implemente as suas alterações.

Siga os passos em [Personalizar PHP_INI_SYSTEM diretivas,](#customize-php_ini_system-directives)adicione as extensões no ficheiro personalizado *.ini* com as diretivas de [extensão](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Para que as alterações entrem em vigor, reinicie a aplicação.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abra a sessão sSH no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Quando uma aplicação PHP em funcionamento se comportar de forma diferente no Serviço de Aplicações ou tiver erros, tente o seguinte:

- [Aceda ao fluxo de registos](#access-diagnostic-logs).
- Teste a aplicação localmente em modo de produção. O Serviço de Aplicações executa as suas aplicações Node.js em modo de produção, pelo que tem de se certificar de que o seu projeto funciona como esperado em modo de produção local. Por exemplo:
    - Dependendo do seu *compositor.json,* podem ser instalados`require` diferentes `require-dev`pacotes para o modo de produção (vs. ).
    - Certos quadros web podem implementar ficheiros estáticos de forma diferente no modo de produção.
    - Certos quadros web podem usar scripts de arranque personalizados quando estão em modo de produção.
- Execute a sua aplicação no Serviço de Aplicações em modo dedepura. Por exemplo, em [Laravel,](https://meanjs.org/)pode configurar a sua aplicação para produzir mensagens de depuração em [ `APP_DEBUG` produção, definindo a definição da aplicação para `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: app PHP com MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Serviço de aplicações Linux FAQ](app-service-linux-faq.md)

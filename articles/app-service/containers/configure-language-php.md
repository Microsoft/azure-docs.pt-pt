---
title: Configurar aplicações do PHP - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como configurar as aplicações PHP a funcionar no serviço de aplicações do Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 637feb855c7816dfb26229c5a65a069260a58cd3
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003100"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configurar uma aplicação PHP de Linux para o serviço de aplicações do Azure

Este guia mostra-lhe como configurar o runtime PHP incorporado para aplicações web, móveis back-ends e API apps no App Service do Azure.

Este guia fornece os conceitos chave e instruções para os desenvolvedores PHP que utilizam um contentor de Linux incorporado no serviço de aplicações. Se nunca tiver utilizado o serviço de aplicações do Azure, siga os [guia de introdução do PHP](quickstart-php.md) e [PHP com MySQL tutorial](tutorial-php-mysql-app.md) primeiro.

## <a name="show-php-version"></a>Mostrar a versão do PHP

Para mostrar a versão atual do PHP, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as PHP versões suportadas, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Definir a versão PHP

Execute o seguinte comando [Cloud Shell](https://shell.azure.com) para definir a versão do PHP para 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Execute o Composer

Por predefinição, não é executado Kudu [compositor](https://getcomposer.org/). Para ativar a automatização do compositor durante a implementação do Kudu, tem de fornecer um [script de implementação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

A partir de uma janela de terminal local, altere o diretório raiz do repositório. Siga os [passos de instalação da linha de comandos](https://getcomposer.org/download/) para transferir *composer.phar*.

Execute os seguintes comandos:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

Raiz do repositório tem agora dois novos ficheiros além *composer.phar*: *. Deployment* e *deploy.sh*. Estes ficheiros de trabalho para Windows e Linux tipos de serviço de aplicações.

Open *deploy.sh* e localize o `Deployment` secção. Substitua a secção de toda o código a seguir:

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

Consolide as suas alterações e implemente o seu código novamente. Compositor deve agora a ser executado como parte da automação da implantação.

## <a name="customize-start-up"></a>Personalizar o arranque

Por predefinição, o contentor PHP incorporado, execute o servidor Apache. Na inicialização, executa `apache2ctl -D FOREGROUND"`. Se quiser, pode executar um comando diferente na inicialização, executando o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No serviço de aplicações, pode [configurar definições de aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do seu código de aplicação. Em seguida, pode acessá-los a através da norma [getenv()](https://secure.php.net/manual/function.getenv.php) padrão. Por exemplo aceder a uma definição de aplicação denominada `DB_HOST`, utilize o seguinte código:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Raiz do site de alteração

A estrutura de web à sua escolha pode utilizar um subdiretório como a raiz do site. Por exemplo, [Laravel](https://laravel.com/), utiliza o `public/` subdiretório como a raiz do site.

A imagem PHP predefinida do serviço de aplicações utiliza o Apache e não lhe permite personalizar a raiz do site para a sua aplicação. Para contornar esta limitação, adicione uma *htaccess* ficheiro para a raiz do repositório com o seguinte conteúdo:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Se preferir não utilizar a reescrita *.htaccess*, pode implementar a aplicação do Laravel com uma [imagem do Docker personalizada](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Detetar a sessão HTTPS

No serviço de aplicações [terminação de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre aos balanceadores de carga de rede, para que todos os pedidos HTTPS à sua aplicação, como solicitações HTTP não criptografadas. Se as suas necessidades de lógica de aplicação para verificar se os pedidos de utilizador são encriptados ou não, Inspecione o `X-Forwarded-Proto` cabeçalho.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Web populares estruturas permitem-lhe acesso a `X-Forwarded-*` informações no seu padrão de aplicativo padrão. Na [CodeIgniter](https://codeigniter.com/), o [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) verifica o valor de `X_FORWARDED_PROTO` por predefinição.

## <a name="customize-phpini-settings"></a>Personalizar as definições do PHP. ini

Se precisar de efetuar alterações à sua instalação do PHP, pode alterar qualquer um da [diretivas de PHP. ini](https://www.php.net/manual/ini.list.php) seguindo estes passos.

> [!NOTE]
> A melhor forma de ver a versão do PHP e o atual *PHP. ini* configuração é chamar [phpinfo()](https://php.net/manual/function.phpinfo.php) na sua aplicação.
>

### <a name="Customize-non-PHP_INI_SYSTEM directives"></a>Personalizar-não-PHP_INI_SYSTEM diretivas

Para personalizar as diretivas PHP_INI_USER PHP_INI_PERDIR e PHP_INI_ALL (consulte [diretivas de PHP. ini](https://www.php.net/manual/ini.list.php)), adicione um *htaccess* ficheiro para o diretório de raiz da sua aplicação.

Na *htaccess* de ficheiros, adicione as diretivas usando o `php_value <directive-name> <value>` sintaxe. Por exemplo:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Voltar a implementar a sua aplicação com as alterações e reiniciá-lo. Se implementar com Kudu (por exemplo, utilizando [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), é reiniciado automaticamente após a implementação.

Como alternativa ao uso *htaccess*, pode utilizar [ini_set()](https://www.php.net/manual/function.ini-set.php) na sua aplicação para personalizar essas diretivas não PHP_INI_SYSTEM.

### <a name="customize-phpinisystem-directives"></a>Personalizar as diretivas PHP_INI_SYSTEM

Para personalizar as diretivas PHP_INI_SYSTEM (consulte [diretivas de PHP. ini](https://www.php.net/manual/ini.list.php)), não é possível utilizar o *htaccess* abordagem. Serviço de aplicações fornece um mecanismo separado usando o `PHP_INI_SCAN_DIR` definição de aplicação.

Em primeiro lugar, execute o seguinte comando [Cloud Shell](https://shell.azure.com) para adicionar uma aplicação chamada `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` é o diretório predefinido onde *PHP. ini* existe. `/home/site/ini` é o diretório personalizado no qual adicionará um personalizado *. ini* ficheiro. Separe os valores com um `:`.

Navegue para a sessão SSH da web com o seu contentor do Linux (`https://cephalin-container.scm.azurewebsites.net/webssh/host`).

Criar um diretório no `/home/site` chamado `ini`, em seguida, crie um *. ini* de ficheiros a `/home/site/ini` diretório (por exemplo, *settings.ini)* com as diretivas de que pretende personalizar. Utilizar a mesma sintaxe que usaria num *PHP. ini* ficheiro. 

> [!TIP]
> Dos contentores de Linux incorporada no serviço de aplicações */home* é utilizado como armazenamento partilhado persistente. 
>

Por exemplo, para alterar o valor de [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) execute os seguintes comandos:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Para que as alterações tenham efeito, reinicie a aplicação.

## <a name="enable-php-extensions"></a>Ativar extensões PHP

As instalações de PHP internas contêm as extensões utilizadas com mais frequência. Pode ativar extensões adicionais da mesma forma que [personalizar as diretivas de PHP. ini](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor forma de ver a versão do PHP e o atual *PHP. ini* configuração é chamar [phpinfo()](https://php.net/manual/function.phpinfo.php) na sua aplicação.
>

Para ativar extensões adicionais, seguindo estes passos:

Adicionar uma `bin` diretório para o diretório de raiz da sua aplicação e put a `.so` arquivos extensão (por exemplo, *mongodb.so*). Certifique-se de que as extensões são compatíveis com a versão do PHP no Azure e são VC9 e compatível com o não thread-safe (ualizar).

Implemente as suas alterações.

Siga os passos em [diretivas de personalizar PHP_INI_SYSTEM](#customize-php_ini_system-directives), adicionar as extensões para o personalizado *. ini* ficheiro com o [extensão](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension ](https://www.php.net/manual/ini.core.php#ini.zend-extension) diretivas.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Para que as alterações tenham efeito, reinicie a aplicação.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Quando uma aplicação PHP de trabalho tem um comportamento diferente no serviço de aplicações ou erros, experimente o seguinte:

- [O fluxo de registo de acesso](#access-diagnostic-logs).
- Teste a aplicação localmente no modo de produção. Serviço de aplicações executa as suas aplicações de node. js no modo de produção, por isso terá de certificar-se de que o seu projeto funciona conforme esperado no modo de produção localmente. Por exemplo:
    - Consoante os *Composer. JSON*, diferentes pacotes que podem ser instalados para o modo de produção (`require` versus `require-dev`).
    - Determinadas estruturas da web podem implementar ficheiros estáticos de forma diferente no modo de produção.
    - Determinadas estruturas da web podem utilizar scripts de inicialização personalizada quando executado no modo de produção.
- Execute a aplicação no serviço de aplicações no modo de depuração. Por exemplo, no [Laravel](https://meanjs.org/), pode configurar a sua aplicação para gerar mensagens de depuração em produção por [definição da `APP_DEBUG` definição de aplicação para `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

### <a name="robots933456"></a>robots933456

Pode ver a seguinte mensagem nos registos do contentor:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Pode ignorar esta mensagem. `/robots933456.txt` é um caminho de URL fictício que utiliza o serviço de aplicações para verificar se o contentor é capaz de servir pedidos. Uma resposta 404 indica simplesmente que o caminho não existir, mas permite que sabe que o contentor é bom estado de funcionamento e está pronto para responder a pedidos de serviço de aplicações.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Aplicação PHP com MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [FAQ do serviço de aplicações Linux](app-service-linux-faq.md)

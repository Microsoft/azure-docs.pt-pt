---
title: Configurar aplicativos PHP
description: Saiba como configurar um contêiner PHP pré-criado para seu aplicativo. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: a3de4769193d95a3ef483924c4d65c4fa1cc9f8d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671841"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configurar um aplicativo do PHP do Linux para o serviço Azure App

Este guia mostra como configurar o tempo de execução do PHP interno para aplicativos Web, back-ends móveis e aplicativos de API no serviço Azure App.

Este guia fornece os principais conceitos e instruções para desenvolvedores de PHP que usam um contêiner do Linux interno no serviço de aplicativo. Se você nunca usou Azure App serviço, siga o tutorial de [início rápido do PHP](quickstart-php.md) e [php com MySQL](tutorial-php-mysql-app.md) primeiro.

## <a name="show-php-version"></a>Mostrar versão do PHP

Para mostrar a versão atual do PHP, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do PHP com suporte, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Definir versão do PHP

Execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para definir a versão do PHP como 7,2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Executar o Composer

Por padrão, o kudu não executa o [Composer](https://getcomposer.org/). Para habilitar a automação do Composer durante a implantação do kudu, você precisa fornecer um [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

Em uma janela de terminal local, altere o diretório para a raiz do repositório. Siga as [etapas de instalação da linha de comando](https://getcomposer.org/download/) para baixar o *Composer. Phar*.

Execute os seguintes comandos:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

A raiz do repositório agora tem dois novos arquivos, além de *Composer. Phar*: *. Deployment* e *Deploy.sh*. Esses arquivos funcionam tanto para os tipos Windows e Linux do serviço de aplicativo.

Abra *Deploy.sh* e localize a seção `Deployment`. Substitua a seção inteira pelo código a seguir:

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

Confirme todas as suas alterações e implante seu código novamente. O Composer agora deve estar em execução como parte da automação da implantação.

## <a name="customize-start-up"></a>Personalizar o arranque

Por padrão, o contêiner PHP interno executa o servidor Apache. Na inicialização, ele é executado `apache2ctl -D FOREGROUND"`. Se desejar, você pode executar um comando diferente na inicialização, executando o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Variáveis de ambiente de acesso

No serviço de aplicativo, você pode [definir configurações de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do seu código do aplicativo. Em seguida, você pode acessá-los usando o padrão [getenv ()](https://secure.php.net/manual/function.getenv.php) padrão. Por exemplo, para acessar uma configuração de aplicativo chamada `DB_HOST`, use o seguinte código:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Alterar raiz do site

A estrutura da Web de sua escolha pode usar um subdiretório como a raiz do site. Por exemplo, [Laravel](https://laravel.com/), usa o subdiretório `public/` como a raiz do site.

A imagem PHP padrão para o serviço de aplicativo usa o Apache e não permite que você personalize a raiz do site para seu aplicativo. Para contornar essa limitação, adicione um arquivo *. htaccess* à raiz do repositório com o seguinte conteúdo:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Se preferir não utilizar a reescrita *.htaccess*, pode implementar a aplicação do Laravel com uma [imagem do Docker personalizada](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No serviço de aplicativo, a [terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede, portanto, todas as solicitações HTTPS atingem seu aplicativo como solicitações HTTP não criptografadas. Se a lógica do aplicativo precisar verificar se as solicitações do usuário estão criptografadas ou não, inspecione o cabeçalho `X-Forwarded-Proto`.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Estruturas da Web populares permitem que você acesse as informações de `X-Forwarded-*` em seu padrão de aplicativo padrão. No [CodeIgniter](https://codeigniter.com/), o [is_https ()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) verifica o valor de `X_FORWARDED_PROTO` por padrão.

## <a name="customize-phpini-settings"></a>Personalizar configurações do php. ini

Se você precisar fazer alterações na instalação do PHP, poderá alterar qualquer uma das diretivas do [php. ini](https://www.php.net/manual/ini.list.php) seguindo estas etapas.

> [!NOTE]
> A melhor maneira de ver a versão do PHP e a configuração do *php. ini* atual é chamar [phpinfo ()](https://php.net/manual/function.phpinfo.php) em seu aplicativo.
>

### <a name="Customize-non-PHP_INI_SYSTEM directives"></a>Personalizar-diretivas não PHP_INI_SYSTEM

Para personalizar as diretivas PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL (consulte [diretivas php. ini](https://www.php.net/manual/ini.list.php)), adicione um arquivo *. htaccess* ao diretório raiz do seu aplicativo.

No arquivo *. htaccess* , adicione as diretivas usando a sintaxe `php_value <directive-name> <value>`. Por exemplo:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Reimplante seu aplicativo com as alterações e reinicie-o. Se você implantá-lo com kudu (por exemplo, usando [git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), ele será reiniciado automaticamente após a implantação.

Como alternativa ao uso de *. htaccess*, você pode usar [ini_set ()](https://www.php.net/manual/function.ini-set.php) em seu aplicativo para personalizar essas diretivas não PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a>Personalizar diretivas de PHP_INI_SYSTEM

Para personalizar as diretivas de PHP_INI_SYSTEM (consulte as [diretivas php. ini](https://www.php.net/manual/ini.list.php)), você não pode usar a abordagem *. htaccess* . O serviço de aplicativo fornece um mecanismo separado usando a configuração de `PHP_INI_SCAN_DIR` aplicativo.

Primeiro, execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para adicionar uma configuração de aplicativo chamada `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` é o diretório padrão em que o *php. ini* existe. `/home/site/ini` é o diretório personalizado no qual você adicionará um arquivo *. ini* personalizado. Separe os valores com um `:`.

Navegue até a sessão SSH da Web com seu contêiner do Linux (`https://<app-name>.scm.azurewebsites.net/webssh/host`).

Crie um diretório em `/home/site` chamado `ini`e, em seguida, crie um arquivo *. ini* no diretório `/home/site/ini` (por exemplo, *Settings. ini)* com as diretivas que você deseja personalizar. Use a mesma sintaxe que você usaria em um arquivo *php. ini* . 

> [!TIP]
> Nos contêineres internos do Linux no serviço de aplicativo, */Home* é usado como armazenamento compartilhado persistente. 
>

Por exemplo, para alterar o valor de [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) execute os seguintes comandos:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Para que as alterações entrem em vigor, reinicie o aplicativo.

## <a name="enable-php-extensions"></a>Habilitar extensões PHP

As instalações internas do PHP contêm as extensões usadas com mais frequência. Você pode habilitar extensões adicionais da mesma maneira que personaliza as [diretivas php. ini](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor maneira de ver a versão do PHP e a configuração do *php. ini* atual é chamar [phpinfo ()](https://php.net/manual/function.phpinfo.php) em seu aplicativo.
>

Para habilitar extensões adicionais, seguindo estas etapas:

Adicione um diretório de `bin` ao diretório raiz do seu aplicativo e coloque os arquivos de extensão de `.so` nele (por exemplo, *MongoDB.so*). Verifique se as extensões são compatíveis com a versão do PHP no Azure e se são compatíveis com VC9 e não-thread-safe (NTS).

Implante suas alterações.

Siga as etapas em [personalizar PHP_INI_SYSTEM diretivas](#customize-php_ini_system-directives), adicione as extensões ao arquivo Custom *. ini* com a [extensão](https://www.php.net/manual/ini.core.php#ini.extension) ou as diretivas de [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) .

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Para que as alterações entrem em vigor, reinicie o aplicativo.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Quando um aplicativo PHP em funcionamento se comporta de forma diferente no serviço de aplicativo ou tem erros, tente o seguinte:

- [Acessar o fluxo de log](#access-diagnostic-logs).
- Teste o aplicativo localmente no modo de produção. O serviço de aplicativo executa seus aplicativos node. js no modo de produção, portanto, você precisa certificar-se de que seu projeto funciona como esperado no modo de produção localmente. Por exemplo:
    - Dependendo do *Composer. JSON*, pacotes diferentes podem ser instalados para o modo de produção (`require` versus `require-dev`).
    - Determinadas estruturas da Web podem implantar arquivos estáticos de forma diferente no modo de produção.
    - Determinadas estruturas da Web podem usar scripts de inicialização personalizados ao serem executados no modo de produção.
- Execute seu aplicativo no serviço de aplicativo no modo de depuração. Por exemplo, em [Laravel](https://meanjs.org/), você pode configurar seu aplicativo para gerar mensagens de depuração em produção [definindo a configuração de aplicativo `APP_DEBUG` como `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

### <a name="robots933456"></a>robots933456

Você pode ver a seguinte mensagem nos logs de contêiner:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Você pode ignorar essa mensagem com segurança. `/robots933456.txt` é um caminho de URL fictício que o serviço de aplicativo usa para verificar se o contêiner é capaz de atender solicitações. Uma resposta 404 indica simplesmente que o caminho não existe, mas permite que o serviço de aplicativo saiba que o contêiner está íntegro e pronto para responder às solicitações.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: aplicativo PHP com MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o serviço de aplicativo Linux](app-service-linux-faq.md)

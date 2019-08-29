---
title: Configurar aplicativos Ruby-serviço de Azure App
description: Este tutorial descreve as opções para criar e configurar aplicativos Ruby para Azure App serviço no Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: cephalin
ms.reviewer: astay; kraigb
ms.custom: seodec18
ms.openlocfilehash: 71734396e90987fb1e318f3d8bb01d957fc0fda1
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071294"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Configurar um aplicativo do Linux Ruby para o serviço Azure App

Este artigo descreve como [Azure app serviço](app-service-linux-intro.md) executa aplicativos Ruby e como você pode personalizar o comportamento do serviço de aplicativo quando necessário. Os aplicativos Ruby devem ser implantados com todos os módulos [Pip](https://pypi.org/project/pip/) necessários.

Este guia fornece os principais conceitos e instruções para desenvolvedores de Ruby que usam um contêiner do Linux interno no serviço de aplicativo. Se você nunca usou Azure App serviço, deverá seguir o tutorial [início rápido do Ruby](quickstart-ruby.md) e [Ruby com PostgreSQL](tutorial-ruby-postgres-app.md) primeiro.

## <a name="show-ruby-version"></a>Mostrar versão do Ruby

Para mostrar a versão atual do Ruby, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do Ruby com suporte, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Você pode executar uma versão sem suporte do Ruby criando sua própria imagem de contêiner em vez disso. Para obter mais informações, consulte [usar uma imagem personalizada](tutorial-custom-docker-image.md)do Docker.

## <a name="set-ruby-version"></a>Definir versão do Ruby

Execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para definir a versão do Ruby como 2,3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Se você encontrar erros semelhantes ao seguinte durante o tempo de implantação:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> ou
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Isso significa que a versão do Ruby configurada em seu projeto é diferente da versão instalada no contêiner que você está executando (`2.3.3` no exemplo acima). No exemplo acima, verifique *Gemfile* e *. Ruby-version* e verifique se a versão do Ruby não está definida ou se está definida para a versão instalada no contêiner que você está executando (`2.3.3` no exemplo acima).

## <a name="access-environment-variables"></a>Variáveis de ambiente de acesso

No serviço de aplicativo, você pode [definir configurações de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do seu código do aplicativo. Em seguida, você pode acessá-los usando o padrão [env ['\<caminho-nome > ']](https://ruby-doc.org/core-2.3.3/ENV.html) padrão. Por exemplo, para acessar uma configuração de aplicativo `WEBSITE_SITE_NAME`chamada, use o seguinte código:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Personalizar a implantação

Quando você implanta um [repositório git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)ou um [pacote zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de compilação ativados, o mecanismo de implantação (kudu) executa automaticamente as seguintes etapas após a implantação por padrão:

1. Verifique se existe um *Gemfile* .
1. Execute `bundle clean`. 
1. Execute `bundle install --path "vendor/bundle"`.
1. Execute `bundle package` para empacotar Gems na pasta de fornecedor/cache.

### <a name="use---without-flag"></a>Usar--sem sinalizador

Para executar `bundle install` com o sinalizador [--sem](https://bundler.io/man/bundle-install.1.html) Flag, defina `BUNDLE_WITHOUT` a [configuração do aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) como uma lista separada por vírgulas de grupos. Por exemplo, o comando a seguir o define `development,test`como.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Se essa configuração for definida, o mecanismo de implantação será `bundle install` executado `--without $BUNDLE_WITHOUT`com.

### <a name="precompile-assets"></a>Ativos de pré-compilação

As etapas pós-implantação não precompilam ativos por padrão. Para ativar a pré-compilação de ativos, `ASSETS_PRECOMPILE` defina a configuração `true`do [aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) como. Em seguida, `bundle exec rake --trace assets:precompile` o comando é executado no final das etapas após a implantação. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Para obter mais informações, consulte [fornecer ativos estáticos](#serve-static-assets).

## <a name="customize-start-up"></a>Personalizar o arranque

Por padrão, o contêiner do Ruby inicia o servidor do Rails na seguinte sequência (para obter mais informações, consulte o [script de inicialização](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Gerar um valor de [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) , se ainda não existir um. Esse valor é necessário para que o aplicativo seja executado no modo de produção.
1. Defina a `RAILS_ENV` variável de ambiente `production`como.
1. Exclua qualquer arquivo *. PID* no diretório *tmp/PIDs* que é deixado por um servidor do Rails em execução anteriormente.
1. Verifique se todas as dependências estão instaladas. Caso contrário, tente instalar o Gems do diretório local do *fornecedor/cache* .
1. Execute `rails server -e $RAILS_ENV`.

Você pode personalizar o processo de inicialização das seguintes maneiras:

- [Atender ativos estáticos](#serve-static-assets)
- [Executar em modo de não produção](#run-in-non-production-mode)
- [Definir secret_key_base manualmente](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Atender ativos estáticos

O servidor do Rails no contêiner Ruby é executado no modo de produção por padrão e [pressupõe que os ativos são pré-compilados e são servidos pelo servidor Web](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Para atender ativos estáticos do servidor do Rails, você precisa fazer duas coisas:

- **Pré-compilar os ativos** - [pré-compilar os ativos estáticos localmente](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) e implantá-los manualmente. Ou então, deixe que o mecanismo de implantação manipule-o em vez disso (consulte os [ativos de pré-compilação](#precompile-assets).
- **Habilitar o fornecimento de arquivos estáticos** – para atender ativos estáticos do contêiner do Ruby, [defina a configuração do `RAILS_SERVE_STATIC_FILES` aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) como. `true` Por exemplo:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Executar em modo de não produção

Por padrão, o servidor do Rails é executado no modo de produção. Para executar no modo de desenvolvimento, por exemplo, defina `RAILS_ENV` a [configuração](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) do `development`aplicativo como.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

No entanto, essa configuração sozinho faz com que o servidor do Rails inicie no modo de desenvolvimento, que aceita solicitações de localhost somente e não está acessível fora do contêiner. Para aceitar solicitações de cliente remoto, defina `APP_COMMAND_LINE` a [configuração](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) do `rails server -b 0.0.0.0`aplicativo como. Essa configuração de aplicativo permite executar um comando personalizado no contêiner Ruby. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a>Definir secret_key_base manualmente

Para usar seu próprio `secret_key_base` valor em vez de permitir que o serviço de aplicativo gere um para `SECRET_KEY_BASE` você, defina a [configuração do aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) com o valor desejado. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Rails com PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o serviço de aplicativo Linux](app-service-linux-faq.md)

---
title: Configurar aplicações Ruby - serviço de aplicações do Azure
description: Este tutorial descreve as opções de criação e configuração de aplicações Ruby para o serviço de aplicações do Azure no Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: aec85b16ab0357b28a564f75509e147e5555137b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205051"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Configurar uma aplicação Ruby do Linux para o serviço de aplicações do Azure

Este artigo descreve como [App Service do Azure](app-service-linux-intro.md) executa aplicações Ruby e como pode personalizar o comportamento do serviço de aplicações quando necessário. Aplicações Ruby tem de ser implementadas com todo o necessário [pip](https://pypi.org/project/pip/) módulos.

Este guia fornece os conceitos chave e instruções para programadores de Ruby que utilizam um contentor do Linux incorporado no serviço de aplicações. Se nunca tiver utilizado o serviço de aplicações do Azure, deve seguir a [guia de introdução Ruby](quickstart-ruby.md) e [Ruby com tutorial de PostgreSQL](tutorial-ruby-postgres-app.md) primeiro.

## <a name="show-ruby-version"></a>Mostrar a versão do Ruby

Para mostrar a versão atual do Ruby, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões suportadas do Ruby, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Pode executar uma versão não suportada do Ruby ao criar sua própria imagem de contentor em vez disso. Para obter mais informações, consulte [utilizar uma imagem personalizada do Docker](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Definir a versão do Ruby

Execute o seguinte comando [Cloud Shell](https://shell.azure.com) para definir a versão do Ruby para 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Se vir erros semelhantes ao seguinte durante o tempo de implementação:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> ou
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Isso significa que a versão do Ruby configurado no seu projeto é diferente do que a versão que está instalada no contentor que está a executar (`2.3.3` no exemplo acima). No exemplo acima, verifique os dois *Gemfile* e *.ruby versão* e certifique-se de que a versão do Ruby não está definido ou está definido para a versão que está instalada no contentor que está a executar (`2.3.3` no exemplo acima).

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No serviço de aplicações, pode [configurar definições de aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do seu código de aplicação. Em seguida, pode acessá-los a através da norma [ENV ['< caminho-name >"]](https://ruby-doc.org/core-2.3.3/ENV.html) padrão. Por exemplo aceder a uma definição de aplicação denominada `WEBSITE_SITE_NAME`, utilize o seguinte código:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Personalizar a implantação

Ao implementar um [repositório de Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), ou uma [pacote Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de criação ativados, o motor de implementação (Kudu) executa automaticamente os seguintes passos de pós-implementação por predefinição:

1. Verifique se uma *Gemfile* existe.
1. Execute `bundle clean`. 
1. Execute `bundle install --path "vendor/bundle"`.
1. Executar `bundle package` para pedras preciosas de pacote na pasta de cache/fornecedor.

### <a name="use---without-flag"></a>Utilize--sem o sinalizador

Para executar `bundle install` com o [– sem](https://bundler.io/man/bundle-install.1.html) sinalizador, defina o `BUNDLE_WITHOUT` [definição de aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) para uma lista separada por vírgulas de grupos. Por exemplo, o seguinte comando define-o como `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Se esta definição estiver definida, em seguida, o motor de implementação é executado `bundle install` com `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Pré-compilar ativos

Os passos de pós-implementação não pré-compilar ativos por predefinição. Para ativar a pré-compilação de recurso, defina o `ASSETS_PRECOMPILE` [definição de aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) para `true`. Em seguida, o comando `bundle exec rake --trace assets:precompile` é executado no final das etapas pós-implementação. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Para obter mais informações, consulte [servir elementos estáticos](#serve-static-assets).

## <a name="customize-start-up"></a>Personalizar o arranque

Por predefinição, o contentor Ruby inicia o servidor do Rails da seguinte sequência (para obter mais informações, consulte a [script de inicialização do](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Gerar um [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) valor, caso não exista já. Este valor é necessário para o aplicativo seja executado no modo de produção.
1. Definir o `RAILS_ENV` variável de ambiente para `production`.
1. Eliminar quaisquer *.pid* de ficheiros a *tmp/os pids* diretório é deixado por um servidor de Rails que estava em execução.
1. Verifique se todas as dependências estão instaladas. Se não, experimente instalar o pedras preciosas de local *fornecedor/cache* diretório.
1. Execute `rails server -e $RAILS_ENV`.

Pode personalizar o processo de inicialização das seguintes formas:

- [Servir elementos estáticos](#serve-static-assets)
- [Executar no modo de não produção](#run-in-non-production-mode)
- [Definir secret_key_base manualmente](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Servir elementos estáticos

O servidor do Rails no contentor Ruby é executado no modo de produção por predefinição, e [parte do princípio de que os ativos são pré-compilados e são servidos pelo seu servidor web](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Para fornecer recursos estáticos do servidor do Rails, terá de fazer duas coisas:

- **Pré-compilar os ativos** - [pré-compilar os recursos estáticos localmente](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) e implementá-las manualmente. Ou, permitir que o motor de implementação manipulá-lo em vez disso (veja [pré-compilar ativos](#precompile-assets).
- **Ativar a servir ficheiros estáticos** - para servir de elementos estáticos do contêiner Ruby, defina o `RAILS_SERVE_STATIC_FILES` [definir o `RAILS_SERVE_STATIC_FILES` definição de aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) para `true`. Por exemplo:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Executar no modo de não produção

O servidor do Rails é executado no modo de produção por predefinição. Para executar no modo de desenvolvimento, por exemplo, definir o `RAILS_ENV` [definição de aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) para `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

No entanto, esta definição apenas faz com que o servidor do Rails iniciar no modo de desenvolvimento, que aceita o localhost pede apenas e não está acessível fora do contentor. Para aceitar pedidos de cliente remoto, defina o `APP_COMMAND_LINE` [definição de aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) para `rails server -b 0.0.0.0`. Esta definição de aplicação permite-lhe executar um comando personalizado no contentor Ruby. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a> Definir secret_key_base manualmente

Para utilizar as suas próprias `secret_key_base` valor em vez de permitir que o serviço de aplicações, gerar uma por si, defina o `SECRET_KEY_BASE` [definição da aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) com o valor desejado. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Aplicação rails com PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [FAQ do serviço de aplicações Linux](app-service-linux-faq.md)

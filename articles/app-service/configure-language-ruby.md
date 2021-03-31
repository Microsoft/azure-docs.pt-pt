---
title: Configurar aplicações Ruby – Serviço de Aplicações do Azure
description: Saiba como configurar um contentor Ruby pré-compilado para a sua aplicação. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: quickstart
ms.date: 06/18/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 038d62573b491325adc60647debf17fa87e06cfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92743682"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Configurar uma aplicação Ruby do Linux para o Serviço de Aplicações do Azure

Este artigo descreve como o [Serviço de Aplicações do Azure ](overview.md) executa aplicações Ruby num contentor do Linux e como pode personalizar o comportamento do Serviço de Aplicações quando for necessário. As aplicações Ruby devem ser implementadas com todos os [gems](https://rubygems.org/gems) necessários.

Este guia disponibiliza os principais conceitos e instruções para programadores de Ruby que utilizam um contentor do Linux integrado no Serviço de Aplicações. Se nunca utilizou o Serviço de Aplicações do Azure, deve seguir primeiro o [Início rápido do Ruby](quickstart-ruby.md) e o [Tutorial de Ruby com PostgreSQL](tutorial-ruby-postgres-app.md).

## <a name="show-ruby-version"></a>Mostrar a versão do Ruby

Para mostrar a versão atual do Ruby, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões suportadas do Ruby, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Pode executar uma versão não suportada do Ruby ao compilar a sua própria imagem de contentor. Para obter mais informações, veja [Utilizar uma imagem personalizada do Docker](tutorial-custom-container.md?pivots=container-linux).

## <a name="set-ruby-version"></a>Definir a versão do Ruby

Execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para definir o Ruby para a versão 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Se vir erros semelhantes aos seguintes durante o tempo de implementação:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> ou
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Tal significa que a versão do Ruby configurada no projeto é diferente da versão instalada no contentor que está a executar (`2.3.3` no exemplo acima). No exemplo acima, marque *Gemfile* e *.ruby-version* e verifique se a versão do Ruby não está definida ou se está definida para a versão instalada no contentor que está a executar (`2.3.3` no exemplo acima).

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [configurar as definições da aplicação](configure-common.md#configure-app-settings) fora do código da aplicação. Em seguida, pode aceder às mesmas com o padrão [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html) predefinido. Por exemplo, para aceder a uma definição da aplicação chamada `WEBSITE_SITE_NAME`, utilize o seguinte código:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Personalizar a implementação

Por predefinição, quando implementa um [repositório Git](deploy-local-git.md) ou um [pacote Zip](deploy-zip.md) com os processos de compilação ativados, o motor de implementação (Kudu) executa automaticamente os seguintes passos pós-implementação:

1. Verifica se existe um *Gemfile*.
1. Execute `bundle clean`. 
1. Execute `bundle install --path "vendor/bundle"`.
1. Executa `bundle package` para empacotar gems na pasta fornecedor/cache.

### <a name="use---without-flag"></a>Utilizar o sinalizador --without

Para executar `bundle install` com o sinalizador [--without](https://bundler.io/man/bundle-install.1.html), configure a [definição da aplicação](configure-common.md#configure-app-settings) `BUNDLE_WITHOUT` para uma lista separada por vírgulas de grupos. Por exemplo, o comando seguinte define-a como `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Se esta definição for aplicada, o motor de implementação executa `bundle install` com `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Pré-compilar recursos

Por predefinição, os passos de pós-implementação não pré-compilam os recursos. Para ativar a pré-compilação de recursos, configure a [definição da aplicação](configure-common.md#configure-app-settings) `ASSETS_PRECOMPILE` para `true`. Em seguida, o comando `bundle exec rake --trace assets:precompile` é executado no final dos passos de pós-implementação. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Para obter mais informações, veja [Fornecer recursos estáticos](#serve-static-assets).

## <a name="customize-start-up"></a>Personalizar o arranque

Por predefinição, o contentor Ruby inicia o servidor do Rails na seguinte sequência (para obter mais informações, veja o [script de arranque](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Gera um valor [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security), se ainda não existir um. Este valor é necessário para que a aplicação seja executada no modo de produção.
1. Define a variável de ambiente `RAILS_ENV` para `production`.
1. Elimina qualquer ficheiro *.pid* no diretório *tmp/pids* deixado por um servidor do Rails em execução anteriormente.
1. Verifica se todas as dependências estão instaladas. Caso contrário, tenta instalar os gems a partir do diretório *fornecedor/cache* local.
1. Execute `rails server -e $RAILS_ENV`.

Pode personalizar o processo de arranque das seguintes formas:

- [Fornecer recursos estáticos](#serve-static-assets)
- [Executar no modo de não produção](#run-in-non-production-mode)
- [Definir secret_key_base manualmente](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Fornecer recursos estáticos

Por predefinição, o servidor do Rails no contentor Ruby é executado no modo de produção e [pressupõe que os recursos são pré-compilados e fornecidos pelo seu servidor Web](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Para fornecer recursos estáticos a partir do servidor do Rails, precisa de fazer duas coisas:

- **Pré-compilar os recursos** - [Pré-compilar os recursos estáticos localmente](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) e implementá-los manualmente. Em alternativa, deixe que o motor de implementação faça isso por si (veja [Pré-compilar recursos](#precompile-assets).
- **Ativar o fornecimento de ficheiros estáticos** – Para fornecer recursos estáticos a partir do contentor Ruby, [configure a definição da aplicação](configure-common.md#configure-app-settings) `RAILS_SERVE_STATIC_FILES` para `true`. Por exemplo:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Executar no modo de não produção

Por predefinição, o servidor do Rails é executado no modo de produção. Para executar no modo de desenvolvimento, por exemplo, configure a [definição da aplicação](configure-common.md#configure-app-settings) `RAILS_ENV` para `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

No entanto, apenas esta definição faz com que o servidor do Rails inicie no modo de desenvolvimento, que aceita apenas pedidos do localhost e não está acessível fora do contentor. Para aceitar pedidos de clientes remotos, configure a [definição da aplicação](configure-common.md#configure-app-settings) `APP_COMMAND_LINE` para `rails server -b 0.0.0.0`. Esta definição da aplicação permite-lhe executar um comando personalizado no contentor Ruby. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a> Definir secret_key_base manualmente

Para utilizar o seu próprio valor `secret_key_base` ao invés de permitir que o Serviço de Aplicações gere um por si, configure a [definição da aplicação](configure-common.md#configure-app-settings) `SECRET_KEY_BASE` com o valor pretendido. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no browser

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Aplicação do Rails com PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [FAQ do Serviço de Aplicações no Linux](faq-app-service-linux.md)

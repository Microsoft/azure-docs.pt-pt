---
title: Configure aplicativos Ruby - Azure App Service
description: Saiba como configurar um recipiente Ruby pré-construído para a sua aplicação. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: quickstart
ms.date: 06/18/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: c822dbdf9940db7b38d354fa32906c16977df0c0
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88085202"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Configure uma aplicação Linux Ruby para o Azure App Service

Este artigo descreve como o [Azure App Service](overview.md) executa aplicações Ruby num recipiente Linux, e como você pode personalizar o comportamento do App Service quando necessário. As aplicações ruby devem ser implantadas com todas as [joias necessárias.](https://rubygems.org/gems)

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores da Ruby que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, deve seguir primeiro o [quickstart Ruby](quickstart-ruby.md) e o [Ruby com o tutorial PostgreSQL.](tutorial-ruby-postgres-app.md)

## <a name="show-ruby-version"></a>Mostrar a versão Ruby

Para mostrar a versão Ruby atual, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões Ruby suportadas, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Em vez disso, pode executar uma versão não suportada da Ruby construindo a sua própria imagem de contentor. Para obter mais informações, consulte [a utilização de uma imagem personalizada do Docker.](tutorial-custom-container.md?pivots=container-linux)

## <a name="set-ruby-version"></a>Definir versão Ruby

Executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão Ruby para 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Se vir erros semelhantes aos seguintes durante o tempo de implantação:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> ou
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Significa que a versão Ruby configurada no seu projeto é diferente da versão instalada no recipiente que está a executar `2.3.3` (no exemplo acima). No exemplo acima, verifique tanto *a versão Gemfile* como *a versão .ruby* e verifique se a versão Ruby não está definida, ou está definida para a versão que está instalada no recipiente que está a executar `2.3.3` (no exemplo acima).

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir definições](configure-common.md#configure-app-settings) de aplicações fora do seu código de aplicação. Em seguida, pode acessá-los usando o padrão [ \<path-name> ENV[' padrão'.](https://ruby-doc.org/core-2.3.3/ENV.html) Por exemplo, para aceder a uma configuração de aplicação chamada `WEBSITE_SITE_NAME` , use o seguinte código:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Personalizar implantação

Quando implementa um [repositório Git](deploy-local-git.md), ou um [pacote Zip](deploy-zip.md) com processos de construção ligados, o motor de implantação (Kudu) executa automaticamente as seguintes etapas pós-implantação por predefinição:

1. Verifique se existe um *Gemfile.*
1. Execute `bundle clean`. 
1. Execute `bundle install --path "vendor/bundle"`.
1. Executar `bundle package` as joias em pacote na pasta fornecedor/cache.

### <a name="use---without-flag"></a>Uso -- sem bandeira

Para correr `bundle install` com a [-- sem](https://bundler.io/man/bundle-install.1.html) bandeira, coloque a definição da `BUNDLE_WITHOUT` [aplicação](configure-common.md#configure-app-settings) numa lista de grupos separados por vírgula. Por exemplo, o seguinte comando define-o para `development,test` .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Se esta definição for definida, o motor de implantação funciona `bundle install` com `--without $BUNDLE_WITHOUT` .

### <a name="precompile-assets"></a>Ativos pré-conformes

As etapas pós-implantação não precompletam os ativos por defeito. Para ligar a pré-compensação do ativo, defina a definição da `ASSETS_PRECOMPILE` [aplicação](configure-common.md#configure-app-settings) para `true` . Em seguida, o comando `bundle exec rake --trace assets:precompile` é executado no final das etapas pós-implantação. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Para obter mais informações, consulte [servir ativos estáticos.](#serve-static-assets)

## <a name="customize-start-up"></a>Personalizar o arranque

Por predefinição, o contentor Ruby inicia o servidor Rails na seguinte sequência (para mais informações, consulte o [script de arranque):](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)

1. Gere um [valor secret_key_base,](https://edgeguides.rubyonrails.org/security.html#environmental-security) se já não existir. Este valor é necessário para que a app seja executada em modo de produção.
1. Desaprote `RAILS_ENV` a variável ambiente para `production` .
1. Elimine qualquer ficheiro *.pid* no diretório *tmp/pids* que seja deixado por um servidor rails anteriormente em execução.
1. Verifique se todas as dependências estão instaladas. Caso contrário, tente instalar pedras preciosas no diretório local *de fornecedor/cache.*
1. Execute `rails server -e $RAILS_ENV`.

Pode personalizar o processo de arranque das seguintes formas:

- [Servir ativos estáticos](#serve-static-assets)
- [Executar em modo de não produção](#run-in-non-production-mode)
- [Desa ajuste secret_key_base manualmente](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Servir ativos estáticos

O servidor Rails no contentor Ruby funciona em modo de produção por padrão, e [assume que os ativos são pré-cumpridos e são servidos pelo seu servidor web](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Para servir ativos estáticos do servidor Rails, é preciso fazer duas coisas:

- **Pré-comutação dos ativos**  -  [Pré-comprovar os ativos estáticos localmente](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) e implementá-los manualmente. Ou, em vez disso, deixe o motor de implantação manuseá-lo (ver [ativos pré-comprelhados](#precompile-assets).
- **Ativar a colocação de ficheiros estáticos** - Para servir ativos estáticos a partir do recipiente Ruby, [defina a definição da `RAILS_SERVE_STATIC_FILES` aplicação](configure-common.md#configure-app-settings) para `true` . Por exemplo:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Executar em modo de não produção

O servidor Rails funciona em modo de produção por defeito. Para funcionar em modo de desenvolvimento, por exemplo, definir a definição da `RAILS_ENV` [aplicação](configure-common.md#configure-app-settings) para `development` .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

No entanto, esta definição faz com que o servidor Rails comece em modo de desenvolvimento, o que aceita apenas os pedidos locais e não é acessível fora do contentor. Para aceitar pedidos remotos de clientes, defina a definição da `APP_COMMAND_LINE` [aplicação](configure-common.md#configure-app-settings) para `rails server -b 0.0.0.0` . Esta definição de aplicação permite executar um comando personalizado no recipiente Ruby. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a>Desa ajuste secret_key_base manualmente

Para utilizar o seu próprio `secret_key_base` valor em vez de deixar o Serviço de Aplicações gerar um para si, defina a definição da `SECRET_KEY_BASE` [aplicação](configure-common.md#configure-app-settings) com o valor que pretende. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH aberto no navegador

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Rails com PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Serviço de Aplicações Linux FAQ](faq-app-service-linux.md)

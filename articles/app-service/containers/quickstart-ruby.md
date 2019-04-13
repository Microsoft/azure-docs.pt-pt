---
title: Criar aplicação Ruby web no Linux - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como criar uma aplicação Ruby on Rails com o Serviço de Aplicações no Linux.
keywords: serviço de aplicações do azure, linux, oss, ruby, rails
services: app-service
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 29126171a2d808153c7578d911e0725641ec39ff
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545150"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>Criar uma aplicação Ruby on Rails com o Serviço de Aplicações no Linux

[O Serviço de Aplicações do Azure no Linux](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este início rápido mostra como criar uma aplicação básica no [Ruby on Rails](https://rubyonrails.org/) que possa ser implementada no Azure como uma Aplicação Web no Linux.

> [!NOTE]
> A pilha de desenvolvimento Ruby só suporta o Ruby on Rails neste momento. Se pretender utilizar uma plataforma diferente, como Sinatra, ou se pretender utilizar um [versão não suportada do Ruby](app-service-linux-intro.md), precisa [executá-lo num contentor personalizado](quickstart-docker-go.md).

![Hello-world](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Instalar Ruby 2.3 ou superior</a>
* <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela do terminal, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente

Execute a aplicação localmente, para ver que aspeto deveria ter quando a implemente no Azure. Abra uma janela de terminal, mude para o diretório `hello-world` e utilize o comando `rails server` para iniciar o servidor.

O primeiro passo é instalar os gems necessários. Existe um `Gemfile` incluído no exemplo, pelo que não tem de especificar os gems a instalar. Para isto, iremos utilizar o bundler:

```bash
bundle install
```

Quando os gems estiverem instalados, vamos utilizar o bundler para iniciar a aplicação:

```bash
bundle exec rails server
```

Com o seu browser, navegue para `http://localhost:3000` para testar a aplicação localmente.

![Hello World configurado](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Navegue para o site para ver a sua aplicação Web criada recentemente com imagem incorporada. Substitua _&lt;nome da aplicação>_ pelo nome da sua aplicação Web.

```bash
http://<app_name>.azurewebsites.net
```

A aplicação Web deve ter o seguinte aspeto:

![Página inicial](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Implementar a sua aplicação

Execute os seguintes comandos para implementar a aplicação local no seu site do Azure:

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

Certifique-se de que as operações de implementação remota são bem-sucedidas. Os comandos produzem um resultado semelhante ao seguinte texto:

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

Assim que a implementação é concluída, reinicie a aplicação Web para a implementação ter efeito, ao utilizar o comando [`az webapp restart`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-restart), conforme mostrado aqui:

```azurecli-interactive
az webapp restart --name <app name> --resource-group myResourceGroup
```

Navegue para o site e verifique os resultados.

```bash
http://<app name>.azurewebsites.net
```

![aplicação Web atualizada](./media/quickstart-ruby/hello-world-updated.png)

> [!NOTE]
> Enquanto a aplicação está a reiniciar, tente procurar os resultados do site num código de estado HTTP `Error 503 Server unavailable`. Pode demorar alguns minutos a reiniciar completamente.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Ruby on Rails com Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Configurar a aplicação Ruby](configure-language-ruby.md)

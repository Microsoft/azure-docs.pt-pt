---
title: 'Quickstart: Criar uma aplicação Ruby'
description: Começa com o Azure App Service implementando a sua primeira app Ruby num contentor Linux no Serviço de Aplicações.
keywords: serviço de aplicações do azure, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: dc64dfa277e97fe7487ce91a140e73b9ec44e086
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483542"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>Criar uma App Ruby on Rails no Serviço de Aplicações

[O Azure App Service no Linux](overview.md#app-service-on-linux) fornece um serviço de hospedagem web altamente escalável e auto-remendado utilizando o sistema operativo Linux. Este tutorial de arranque rápido mostra como implementar uma aplicação Ruby on Rails para o Serviço de Aplicações em Linux usando a [Cloud Shell](../cloud-shell/overview.md).

> [!NOTE]
> A pilha de desenvolvimento Ruby só suporta o Ruby on Rails neste momento. Se quiser utilizar uma plataforma diferente, como o Sinatra, ou se pretender utilizar uma versão Ruby não suportada, tem de [a executar num recipiente personalizado.](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux)

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Instalar Ruby 2.6 ou superior</a>
* <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela do terminal, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente

Execute a aplicação localmente, para ver que aspeto deveria ter quando a implemente no Azure. Abra uma janela de terminal, mude para o diretório `hello-world` e utilize o comando `rails server` para iniciar o servidor.

O primeiro passo é instalar os gems necessários. Há um `Gemfile` incluído na amostra, então basta executar o seguinte comando:

```bash
bundle install
```

Quando os gems estiverem instalados, vamos utilizar o bundler para iniciar a aplicação:

```bash
bundle exec rails server
```

Com o seu browser, navegue para `http://localhost:3000` para testar a aplicação localmente.

![Hello World configurado](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Navegue pela aplicação para ver a sua aplicação web recém-criada com imagem incorporada. Substitua _&lt; o nome da aplicação>_ pelo nome da sua aplicação web.

```bash
http://<app_name>.azurewebsites.net
```

A aplicação Web deve ter o seguinte aspeto:

![Página inicial](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Implementar a sua aplicação

Execute os seguintes comandos para implementar a aplicação local na sua aplicação web Azure:

```bash
git remote add azure <Git deployment URL from above>
git push azure main
```

Certifique-se de que as operações de implementação remota são bem-sucedidas. Os comandos produzem um resultado semelhante ao seguinte texto:

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  main -> main
```

Uma vez concluída a implementação, aguarde cerca de 10 segundos para que a aplicação web reinicie e, em seguida, navegue para a aplicação web e verifique os resultados.

```bash
http://<app-name>.azurewebsites.net
```

![aplicação Web atualizada](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> Enquanto a aplicação estiver a reiniciar, pode observar o código de estado HTTP `Error 503 Server unavailable` no navegador ou a página `Hey, Ruby developers!` padrão. Pode levar alguns minutos para a aplicação reiniciar totalmente.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Ruby on Rails com Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Configure a app Ruby](configure-language-ruby.md)

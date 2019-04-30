---
title: Configurar aplicações node. js - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como configurar aplicações node. js a funcionar no serviço de aplicações do Azure
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
ms.openlocfilehash: 43dc76e6d1e1ec2a6167f1d3e3cc7b8780f843db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850236"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Configurar uma aplicação node. js do Linux para o serviço de aplicações do Azure

Aplicações node. js tem de ser implementadas com todas as dependências necessárias do NPM. O motor de implementação do serviço de aplicações (Kudu) executa automaticamente `npm install --production` para quando implanta um [repositório de Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), ou um [pacote Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de criação ativados. Se implementar os ficheiros com o [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), no entanto, terá de carregar manualmente os pacotes necessários.

Este guia fornece os conceitos chave e instruções para os programadores de node. js que utilizam um contentor de Linux incorporado no serviço de aplicações. Se nunca tiver utilizado o serviço de aplicações do Azure, siga os [guia de introdução do node. js](quickstart-nodejs.md) e [node. js com MongoDB tutorial](tutorial-nodejs-mongodb-app.md) primeiro.

## <a name="show-nodejs-version"></a>Mostrar a versão do node. js

Para mostrar a versão atual do node. js, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões suportadas do node. js, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Definir a versão do node. js

Para definir a sua aplicação para um [versão do node. js suportada](#show-nodejs-version), execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Esta definição especifica a versão do node. js para utilizar, tanto em tempo de execução durante o restauro do pacote automatizado de Kudu.

> [!NOTE]
> Deve definir a versão do node. js no seu projeto `package.json`. O motor de implementação é executado num contentor separado que contém todas as versões suportadas do node. js.

## <a name="configure-nodejs-server"></a>Configurar o servidor node. js

Os contentores de node. js acompanham [PM2](http://pm2.keymetrics.io/), um Gestor de processos de produção. Pode configurar a sua aplicação para iniciar PM2, ou com NPM ou com um comando personalizado.

- [Executar comandos personalizados](#run-custom-command)
- [Execute o início de npm](#run-npm-start)
- [Executar com PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Executar comandos personalizados

Serviço de aplicações pode iniciar a sua aplicação com um comando personalizado, como gosto de um executável *run.sh*. Por exemplo, para executar `npm run start:prod`, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Execute o início de npm

Para começar a sua aplicação utilizar `npm start`, basta certificar-se um `start` script estiver no *Package. JSON* ficheiro. Por exemplo:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Para utilizar um personalizado *Package. JSON* no seu projeto, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Executar com PM2

O contentor começa automaticamente a aplicação com PM2 quando um dos ficheiros de node. js comuns é encontrado em seu projeto:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Um dos seguintes [ficheiros PM2](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* e *ecosystem.config.js*

Também pode configurar um ficheiro de arranque personalizada com as seguintes extensões:

- R *. js* ficheiro
- R [ficheiro PM2](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) com a extensão *. JSON*, *. config. js*, *.yaml*, ou *. yml*

Para adicionar um ficheiro de arranque personalizadas, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Depurar remotamente

> [!NOTE]
> Depuração remota está atualmente em pré-visualização.

Pode depurar a sua aplicação node. js remotamente em [Visual Studio Code](https://code.visualstudio.com/) se o configurar para [executar com PM2](#run-with-pm2), exceto quando executá-la com um *. config. js, *.yml, ou *.yaml*.

Na maioria dos casos, nenhuma configuração adicional é necessária para a sua aplicação. Se a aplicação é executada com uma *process.json* ficheiro (padrão ou personalizado), tem de ter um `script` propriedade na raiz do JSON. Por exemplo:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Para configurar o Visual Studio Code para depuração remota, instale o [extensão de serviço de aplicações](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Siga as instruções na página de extensão e inicie sessão no Azure no Visual Studio Code.

No Explorador do Azure, localize a aplicação que pretende depurar, faça duplo clique nele e selecione **iniciar a depuração remota**. Clique em **Sim** para ativá-la para a sua aplicação. Serviço de aplicações é iniciado um proxy de túnel para e anexa o depurador. Em seguida, pode fazer pedidos à aplicação e ver o depurador colocar em pausa em pontos de interrupção.

Depois de terminar com a depuração, para o depurador selecionando **desligar**. Quando lhe for pedido, deve clicar **Sim** para desativar a depuração remota. Para desativá-la mais tarde, a aplicação novamente no Explorador do Azure com o botão direito e selecione **desativar a depuração remota**.

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No serviço de aplicações, pode [configurar definições de aplicação](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) fora do seu código de aplicação. Em seguida, pode acessá-los usando o padrão de node. js. Por exemplo aceder a uma definição de aplicação denominada `NODE_ENV`, utilize o seguinte código:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Executar pesado/Bower/vez

Por predefinição, é executado o Kudu `npm install --production` quando ele reconhece uma aplicação node. js é implementada. Se a sua aplicação precisar de qualquer uma das ferramentas de automatização populares, como pesado, Bower ou vez, tem de fornecer um [script de implementação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) executá-lo.

Para ativar o seu repositório executar estas ferramentas, tem de adicioná-los para as dependências no *Package. JSON.* Por exemplo:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

A partir de uma janela de terminal local, altere o diretório raiz do repositório e execute os seguintes comandos:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Raiz do repositório agora tem dois ficheiros adicionais: *. Deployment* e *deploy.sh*.

Open *deploy.sh* e localize o `Deployment` seção, semelhante ao seguinte:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Esta secção termina com execução `npm install --production`. Adicione a secção de código que necessita para executar a ferramenta necessária *no final* do `Deployment` secção:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Veja uma [exemplo de exemplo Mean. js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), em que o script de implementação também é executado um personalizado `npm install` comando.

### <a name="bower"></a>Bower

Este trecho de código é executado `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Este trecho de código é executado `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Este trecho de código é executado `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Detetar a sessão HTTPS

No serviço de aplicações [terminação de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre aos balanceadores de carga de rede, para que todos os pedidos HTTPS à sua aplicação, como solicitações HTTP não criptografadas. Se as suas necessidades de lógica de aplicação para verificar se os pedidos de utilizador são encriptados ou não, Inspecione o `X-Forwarded-Proto` cabeçalho.

Web populares estruturas permitem-lhe acesso a `X-Forwarded-*` informações no seu padrão de aplicativo padrão. Na [Express](https://expressjs.com/), pode utilizar [confiar proxies](http://expressjs.com/guide/behind-proxies.html). Por exemplo:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Quando um trabalho de aplicação node. js tem um comportamento diferente no serviço de aplicações ou erros, experimente o seguinte:

- [O fluxo de registo de acesso](#access-diagnostic-logs).
- Teste a aplicação localmente no modo de produção. Serviço de aplicações executa as suas aplicações de node. js no modo de produção, por isso terá de certificar-se de que o seu projeto funciona conforme esperado no modo de produção localmente. Por exemplo:
    - Consoante os *Package. JSON*, diferentes pacotes que podem ser instalados para o modo de produção (`dependencies` versus `devDependencies`).
    - Determinadas estruturas da web podem implementar ficheiros estáticos de forma diferente no modo de produção.
    - Determinadas estruturas da web podem utilizar scripts de inicialização personalizada quando executado no modo de produção.
- Execute a aplicação no serviço de aplicações no modo de desenvolvimento. Por exemplo, no [Mean. js](http://meanjs.org/), pode definir a aplicação para o modo de desenvolvimento no tempo de execução por [definição da `NODE_ENV` definição de aplicação](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Aplicação de node. js com MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [FAQ do serviço de aplicações Linux](app-service-linux-faq.md)
---
title: Configurar aplicativos node. js
description: Saiba como configurar um contêiner node. js predefinido para seu aplicativo. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6cf60472307a378d2fd4258a9777152344a11ded
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670277"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Configurar um aplicativo node. js do Linux para o serviço Azure App

Os aplicativos node. js devem ser implantados com todas as dependências de NPM necessárias. O kudu (mecanismo de implantação do serviço de aplicativo) executa automaticamente `npm install --production` para você quando você implanta um [repositório git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)ou um [pacote zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de compilação ativados. No entanto, se você implantar seus arquivos usando [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), será necessário carregar os pacotes necessários manualmente.

Este guia fornece os principais conceitos e instruções para desenvolvedores do node. js que usam um contêiner do Linux interno no serviço de aplicativo. Se você nunca usou Azure App serviço, siga o tutorial do [início rápido do node. js](quickstart-nodejs.md) e do [node. js com o MongoDB](tutorial-nodejs-mongodb-app.md) primeiro.

## <a name="show-nodejs-version"></a>Mostrar versão do node. js

Para mostrar a versão atual do node. js, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do node. js com suporte, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Definir versão do node. js

Para definir seu aplicativo para uma [versão do node. js com suporte](#show-nodejs-version), execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Essa configuração especifica a versão do node. js a ser usada, em tempo de execução e durante a restauração automatizada de pacotes em kudu.

> [!NOTE]
> Você deve definir a versão do node. js no `package.json`do seu projeto. O mecanismo de implantação é executado em um contêiner separado que contém todas as versões do node. js com suporte.

## <a name="configure-nodejs-server"></a>Configurar o servidor node. js

Os contêineres do node. js vêm com o [PM2](https://pm2.keymetrics.io/), um gerente de processo de produção. Você pode configurar seu aplicativo para iniciar com PM2, ou com NPM, ou com um comando personalizado.

- [Executar comando personalizado](#run-custom-command)
- [Executar NPM iniciar](#run-npm-start)
- [Executar com PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Executar comando personalizado

O serviço de aplicativo pode iniciar seu aplicativo usando um comando personalizado, como um executável como *Run.sh*. Por exemplo, para executar `npm run start:prod`, execute o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Executar NPM iniciar

Para iniciar seu aplicativo usando `npm start`, apenas verifique se um script de `start` está no arquivo *Package. JSON* . Por exemplo:

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

Para usar um *Package. JSON* personalizado em seu projeto, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Executar com PM2

O contêiner inicia automaticamente seu aplicativo com PM2 quando um dos arquivos node. js comuns é encontrado em seu projeto:

- *bin/www*
- *Server. js*
- *App. js*
- *index. js*
- *hostingstart. js*
- Um dos seguintes [arquivos PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *Process. JSON* e *ecossistema. config. js*

Você também pode configurar um arquivo de inicialização personalizado com as seguintes extensões:

- Um arquivo *. js*
- Um [arquivo PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) com a extensão *. JSON*, *. config. js*, *. YAML*ou *. yml*

Para adicionar um arquivo de inicialização personalizado, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Depurar remotamente

> [!NOTE]
> A depuração remota está atualmente em visualização.

Você pode depurar seu aplicativo node. js remotamente no [Visual Studio Code](https://code.visualstudio.com/) se configurá-lo para [ser executado com PM2](#run-with-pm2), exceto quando você executá-lo usando um *. config. js, *. yml ou *. YAML*.

Na maioria dos casos, nenhuma configuração adicional é necessária para seu aplicativo. Se seu aplicativo for executado com um arquivo *Process. JSON* (padrão ou personalizado), ele deverá ter uma propriedade `script` na raiz JSON. Por exemplo:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Para configurar Visual Studio Code para depuração remota, instale a [extensão do serviço de aplicativo](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Siga as instruções na página extensão e entre no Azure no Visual Studio Code.

No Azure Explorer, localize o aplicativo que você deseja depurar, clique nele com o botão direito do mouse e selecione **Iniciar Depuração remota**. Clique em **Sim** para habilitá-lo para seu aplicativo. O serviço de aplicativo inicia um proxy de túnel para você e anexa o depurador. Em seguida, você pode fazer solicitações para o aplicativo e ver o depurador pausando em pontos de interrupção.

Depois de concluir a depuração, pare o depurador selecionando **Desconectar**. Quando solicitado, você deve clicar em **Sim** para desabilitar a depuração remota. Para desabilitá-lo mais tarde, clique com o botão direito do mouse no seu aplicativo novamente no Azure Explorer e selecione **Desabilitar depuração remota**.

## <a name="access-environment-variables"></a>Variáveis de ambiente de acesso

No serviço de aplicativo, você pode [definir configurações de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do seu código do aplicativo. Em seguida, você pode acessá-los usando o padrão node. js padrão. Por exemplo, para acessar uma configuração de aplicativo chamada `NODE_ENV`, use o seguinte código:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Executar Grunt/Bower/Gulp

Por padrão, o kudu é executado `npm install --production` quando reconhece que um aplicativo node. js é implantado. Se seu aplicativo exigir qualquer uma das ferramentas de automação populares, como Grunt, Bower ou Gulp, você precisará fornecer um [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) para executá-lo.

Para habilitar seu repositório para executar essas ferramentas, você precisa adicioná-las às dependências em *Package. JSON.* Por exemplo:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Em uma janela de terminal local, altere o diretório para a raiz do repositório e execute os seguintes comandos:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A raiz do repositório agora tem dois arquivos adicionais: *. Deployment* e *Deploy.sh*.

Abra *Deploy.sh* e localize a seção `Deployment`, que tem a seguinte aparência:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Esta seção termina com a execução de `npm install --production`. Adicione a seção de código de que você precisa para executar a ferramenta necessária *no final* da seção `Deployment`:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Veja um [exemplo no exemplo Mean. js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), em que o script de implantação também executa um comando de `npm install` personalizado.

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

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No serviço de aplicativo, a [terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede, portanto, todas as solicitações HTTPS atingem seu aplicativo como solicitações HTTP não criptografadas. Se a lógica do aplicativo precisar verificar se as solicitações do usuário estão criptografadas ou não, inspecione o cabeçalho `X-Forwarded-Proto`.

Estruturas da Web populares permitem que você acesse as informações de `X-Forwarded-*` em seu padrão de aplicativo padrão. No [Express](https://expressjs.com/), você pode usar [proxies de confiança](https://expressjs.com/guide/behind-proxies.html). Por exemplo:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Quando um aplicativo node. js de trabalho se comporta de forma diferente no serviço de aplicativo ou tem erros, tente o seguinte:

- [Acessar o fluxo de log](#access-diagnostic-logs).
- Teste o aplicativo localmente no modo de produção. O serviço de aplicativo executa seus aplicativos node. js no modo de produção, portanto, você precisa certificar-se de que seu projeto funciona como esperado no modo de produção localmente. Por exemplo:
    - Dependendo do *Package. JSON*, pacotes diferentes podem ser instalados para o modo de produção (`dependencies` versus `devDependencies`).
    - Determinadas estruturas da Web podem implantar arquivos estáticos de forma diferente no modo de produção.
    - Determinadas estruturas da Web podem usar scripts de inicialização personalizados ao serem executados no modo de produção.
- Execute seu aplicativo no serviço de aplicativo no modo de desenvolvimento. Por exemplo, em [Mean. js](https://meanjs.org/), você pode definir seu aplicativo para o modo de desenvolvimento em tempo de execução [definindo a configuração do aplicativo `NODE_ENV`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: aplicativo node. js com o MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o serviço de aplicativo Linux](app-service-linux-faq.md)

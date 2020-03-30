---
title: Configure Aplicativos Node.js
description: Aprenda a configurar um recipiente node.js pré-construído para a sua aplicação. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252730"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Configure uma aplicação Linux Node.js para o Serviço de Aplicações Azure

As aplicações Node.js devem ser implementadas com todas as dependências npm necessárias. O motor de implantação do Serviço `npm install --production` de Aplicações (Kudu) funciona automaticamente para si quando implementa um [repositório Git,](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)ou um [pacote Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de construção ligados. No entanto, se implementar os seus ficheiros utilizando [FTP/S,](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)terá de carregar manualmente as embalagens necessárias.

Este guia fornece conceitos e instruções fundamentais para os desenvolvedores da Node.js que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, siga o [Node.js quickstart](quickstart-nodejs.md) e [node.js com tutorial MongoDB](tutorial-nodejs-mongodb-app.md) primeiro.

## <a name="show-nodejs-version"></a>Ver versão Node.js

Para mostrar a versão atual do Node.js, execute o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões node.js suportadas, execute o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Definir versão Node.js

Para definir a sua aplicação para uma [versão Node.js suportada,](#show-nodejs-version)execute o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Esta definição especifica a versão Node.js a utilizar, tanto no tempo de execução como durante a restauração automática do pacote em Kudu.

> [!NOTE]
> Deve definir a versão Node.js no `package.json`seu projeto. O motor de implantação funciona num recipiente separado que contém todas as versões nonóias suportadas.

## <a name="customize-build-automation"></a>Personalize a automatização de construção

Se implementar a sua aplicação utilizando pacotes Git ou zip com automatização de construção ligada, o Serviço de Aplicações constrói passos de automação através da seguinte sequência:

1. Executar script personalizado se `PRE_BUILD_SCRIPT_PATH`especificado por .
1. Correr `npm install` sem bandeiras, `preinstall` que `postinstall` incluam npm `devDependencies`e scripts e também instala.
1. Faça `npm run build` a execução se um script de construção for especificado no seu *pacote.json*.
1. Executar `npm run build:azure` se um script build:azure for especificado no seu *pacote.json*.
1. Executar script personalizado se `POST_BUILD_SCRIPT_PATH`especificado por .

> [!NOTE]
> Conforme descrito em [docs npm,](https://docs.npmjs.com/misc/scripts)scripts nomeados `prebuild` e `postbuild` executados antes e depois, `build`respectivamente, se especificado. `preinstall`e `postinstall` correr antes `install`e depois, respectivamente.

`PRE_BUILD_COMMAND`e `POST_BUILD_COMMAND` são variáveis ambientais que são vazias por padrão. Para executar comandos pré-construção, defina `PRE_BUILD_COMMAND`. Para executar comandos pós-construção, defina `POST_BUILD_COMMAND`.

O exemplo seguinte especifica as duas variáveis a uma série de comandos, separados por vírgulas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para variáveis ambientais adicionais para personalizar a automatização de construção, consulte a [configuração oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o App Service funciona e constrói aplicações Node.js em Linux, consulte [documentação oryx: Como as aplicações noNode.js são detetadas e construídas](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Configure Servidor Node.js

Os contentores node.js vêm com [PM2](https://pm2.keymetrics.io/), um gestor de processos de produção. Pode configurar a sua aplicação para começar com PM2, ou com NPM, ou com um comando personalizado.

- [Executar comando personalizado](#run-custom-command)
- [Executar início npm](#run-npm-start)
- [Corra com PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Executar comando personalizado

O Serviço de Aplicações pode iniciar a sua aplicação usando um comando personalizado, como um executável como *run.sh*. Por exemplo, `npm run start:prod`para executar, executar o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Executar início npm

Para iniciar a `npm start`sua aplicação `start` utilizando , certifique-se apenas de que um script está no ficheiro *package.json.* Por exemplo:

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

Para utilizar um *pacote personalizado.json* no seu projeto, execute o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Corra com PM2

O recipiente inicia automaticamente a sua aplicação com PM2 quando um dos ficheiros node.js comuns é encontrado no seu projeto:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Um dos [seguintes ficheiros PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* e *ecosystem.config.js*

Também pode configurar um ficheiro de início personalizado com as seguintes extensões:

- Um ficheiro *.js*
- Um [ficheiro PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) com a extensão *.json*, *.config.js,* *.yaml,* ou *.yml*

Para adicionar um ficheiro de início personalizado, execute o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Depurar remotamente

> [!NOTE]
> A depuração remota está atualmente em Pré-visualização.

Pode desincomodá-la remotamente no Código do [Estúdio Visual](https://code.visualstudio.com/) se a configurar para funcionar com [PM2](#run-with-pm2), exceto quando a executa utilizando um *.config.js, *.yml, ou *.yaml*.

Na maioria dos casos, não é necessária nenhuma configuração extra para a sua aplicação. Se a sua aplicação for executada com um ficheiro *process.json* (padrão ou personalizado), deve ter uma `script` propriedade na raiz jSON. Por exemplo:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Para configurar o Código do Estúdio Visual para depuração remota, instale a extensão do [Serviço de Aplicações](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Siga as instruções na página de extensão e inscreva-se no Azure no Código do Estúdio Visual.

No explorador Azure, encontre a aplicação que pretende depurar, clique à direita e selecione **Start Remote Debugging**. Clique em **Sim** para ativar para a sua aplicação. O Serviço de Aplicações inicia um proxy de túnel para si e anexa o debugger. Em seguida, pode fazer pedidos para a app e ver o desbugger fazer uma pausa nos pontos de rutura.

Uma vez terminada a depuração, pare o desbugger selecionando **Desligar**. Quando solicitado, deve clicar **em Sim** para desativar a depuração remota. Para desativá-lo mais tarde, clique novamente na sua aplicação no explorador Azure e selecione **Depuração Remota de desativação**.

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir as definições](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) de aplicações fora do seu código de aplicações. Depois pode acessá-los usando o padrão node.js padrão. Por exemplo, para aceder `NODE_ENV`a uma definição de aplicação chamada , use o seguinte código:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Run Grunt/Bower/Gulp

Por padrão, kudu executa `npm install --production` quando reconhece que uma aplicação Node.js é implementada. Se a sua aplicação necessitar de alguma das ferramentas de automação populares, como grunt, Bower ou Gulp, você precisa fornecer um script de [implementação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) para executá-lo.

Para permitir que o seu repositório execute estas ferramentas, precisa adicioná-las às dependências do *pacote.json.* Por exemplo:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

A partir de uma janela de terminal local, mude o diretório para a sua raiz de repositório e execute os seguintes comandos:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A sua raiz de repositório tem agora dois ficheiros adicionais: *.deployment* e *deploy.sh*.

Abra *deploy.sh* e `Deployment` encontre a secção, que se parece com isto:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Esta secção termina `npm install --production`com a execução . Adicione a secção de código que precisa para executar `Deployment` a ferramenta necessária no *final* da secção:

- [Bower](#bower)
- [Rio Gulp](#gulp)
- [Grunt](#grunt)

Veja um [exemplo na amostra MEAN.js,](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)onde o `npm install` script de implantação também executa um comando personalizado.

### <a name="bower"></a>Bower

Este corte corre. `bower install`

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Rio Gulp

Este corte corre. `gulp imagemin`

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Este corte corre. `grunt`

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Detetar sessão HTTPS

No Serviço de Aplicações, a rescisão do [SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos equilibradores de carga da rede, pelo que todos os pedidos HTTPS chegam à sua aplicação como pedidos HTTP não encriptados. Se a lógica da sua aplicação necessitar de verificar se `X-Forwarded-Proto` os pedidos do utilizador estão encriptados ou não, inspecione o cabeçalho.

Os quadros web populares permitem-lhe aceder à `X-Forwarded-*` informação no padrão padrão da sua aplicação. No [Express,](https://expressjs.com/)pode usar [proxies de confiança.](https://expressjs.com/guide/behind-proxies.html) Por exemplo:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abra a sessão sSH no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Quando uma aplicação node.js funcionando se comportar de forma diferente no Serviço de Aplicações ou tiver erros, tente o seguinte:

- [Aceda ao fluxo de registos](#access-diagnostic-logs).
- Teste a aplicação localmente em modo de produção. O Serviço de Aplicações executa as suas aplicações Node.js em modo de produção, pelo que tem de se certificar de que o seu projeto funciona como esperado em modo de produção local. Por exemplo:
    - Dependendo do seu *pacote.json,* podem ser instalados`dependencies` diferentes `devDependencies`pacotes para o modo de produção (vs. ).
    - Certos quadros web podem implementar ficheiros estáticos de forma diferente no modo de produção.
    - Certos quadros web podem usar scripts de arranque personalizados quando estão em modo de produção.
- Execute a sua aplicação no App Service em modo de desenvolvimento. Por exemplo, em [MEAN.js,](https://meanjs.org/)pode definir a sua aplicação para o modo de desenvolvimento em tempo de [execução, definindo a definição `NODE_ENV` da aplicação](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: App Node.js com MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Serviço de aplicações Linux FAQ](app-service-linux-faq.md)

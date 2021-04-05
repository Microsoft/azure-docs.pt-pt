---
title: Configurar aplicativos de Node.js
description: Saiba como configurar uma aplicação Node.js nas instâncias nativas do Windows, ou num recipiente Linux pré-construído, no Azure App Service. Este artigo mostra as tarefas de configuração mais comuns.
ms.custom: devx-track-js, devx-track-azurecli
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 6a6f782768db12c2ce75f5cf1e66100222f24446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095212"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>Configurar um aplicativo de Node.js para o Azure App Service

Node.js aplicações devem ser implementadas com todas as dependências de NPM necessárias. O motor de implementação do Serviço de Aplicações funciona automaticamente `npm install --production` para si quando implementa um [repositório Git](deploy-local-git.md), ou um pacote [Zip](deploy-zip.md) com automatização de construção ativada. Se implementar os seus ficheiros utilizando [FTP/S,](deploy-ftp.md)no entanto, tem de carregar manualmente as embalagens necessárias.

Este guia fornece conceitos e instruções fundamentais para Node.js desenvolvedores que implementam o Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [Node.js quickstart](quickstart-nodejs.md) e [Node.js com o tutorial mongoDB.](tutorial-nodejs-mongodb-app.md)

## <a name="show-nodejs-version"></a>Mostrar versão Node.js

::: zone pivot="platform-windows"  

Para mostrar a versão atual Node.js, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Para mostrar todas as versões Node.js suportadas, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

Para mostrar a versão atual Node.js, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões Node.js suportadas, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>Definir versão Node.js

::: zone pivot="platform-windows"  

Para definir a sua aplicação para uma [versão Node.js suportada,](#show-nodejs-version)executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir para uma `WEBSITE_NODE_DEFAULT_VERSION` versão suportada:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Esta definição especifica a versão Node.js a utilizar, tanto em tempo de execução como durante a restauração automática do pacote durante a automatização de construção do Serviço de Aplicações.

> [!NOTE]
> Deve definir a versão Node.js no seu `package.json` projeto. O motor de implantação funciona num processo separado que contém todas as versões Node.js suportadas.

::: zone-end

::: zone pivot="platform-linux"

Para definir a sua aplicação para uma [versão Node.js suportada,](#show-nodejs-version)executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Esta definição especifica a versão Node.js a utilizar, tanto no tempo de execução como durante a restauração automática do pacote em Kudu.

> [!NOTE]
> Deve definir a versão Node.js no seu `package.json` projeto. O motor de implantação funciona num recipiente separado que contém todas as versões Node.js suportadas.

::: zone-end

## <a name="get-port-number"></a>Obtenha o número da porta

Você Node.js app precisa ouvir a porta certa para receber pedidos de entrada.

::: zone pivot="platform-windows"  

No Serviço de Aplicações no Windows, Node.js aplicações estão hospedadas com [iISNode](https://github.com/Azure/iisnode), e a sua aplicação Node.js deve ouvir a porta especificada na `process.env.PORT` variável. O exemplo a seguir mostra como o faz numa simples aplicação Express:

::: zone-end

::: zone pivot="platform-linux"  

O Serviço de Aplicações define a variável `PORT` ambiente no recipiente Node.js e remete os pedidos de entrada para o seu contentor nesse número de porta. Para receber os pedidos, a sua aplicação deve ouvir a porta `process.env.PORT` usando. O exemplo a seguir mostra como o faz numa simples aplicação Express:

::: zone-end

```javascript
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Personalize a automatização de construção

Se implementar a sua aplicação utilizando pacotes Git ou zip com automatização de construção ligada, o Serviço de Aplicações constrói passos de automação através da seguinte sequência:

1. Executar script personalizado se especificado por `PRE_BUILD_SCRIPT_PATH` .
1. Corra `npm install` sem bandeiras, que inclua npm `preinstall` e `postinstall` scripts e também `devDependencies` instala.
1. Executar `npm run build` se um script de construção for especificado no seupackage.jsligado *.*
1. Corra `npm run build:azure` se uma escritura de construção:azure for especificada no seupackage.js *em*.
1. Executar script personalizado se especificado por `POST_BUILD_SCRIPT_PATH` .

> [!NOTE]
> Conforme descrito nos [npm docs](https://docs.npmjs.com/misc/scripts), scripts nomeados `prebuild` e `postbuild` executados antes e depois `build` , respectivamente, se especificado. `preinstall` e `postinstall` correr antes e `install` depois, respectivamente.

`PRE_BUILD_COMMAND` e `POST_BUILD_COMMAND` são variáveis ambientais que estão vazias por defeito. Para executar comandos pré-construção, defina `PRE_BUILD_COMMAND` . Para executar comandos pós-construção, defina `POST_BUILD_COMMAND` .

O exemplo a seguir especifica as duas variáveis a uma série de comandos, separados por vírgulas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para variáveis ambientais adicionais para personalizar a automatização de construção, consulte [a configuração oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o App Service funciona e constrói Node.js aplicações no Linux, consulte [a documentação do Oryx: Como Node.js aplicações são detetadas e construídas.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)

## <a name="configure-nodejs-server"></a>Configurar Node.js servidor

Os contentores Node.js vêm com [PM2,](https://pm2.keymetrics.io/)um gestor de processos de produção. Pode configurar a sua aplicação para começar com PM2, ou com NPM, ou com um comando personalizado.

- [Executar comando personalizado](#run-custom-command)
- [Executar npm start](#run-npm-start)
- [Corra com PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Executar comando personalizado

O Serviço de Aplicações pode iniciar a sua aplicação utilizando um comando personalizado, como um executável como *run.sh*. Por exemplo, para executar `npm run start:prod` , executar o seguinte comando na Cloud [Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Executar npm start

Para iniciar a utilização da sua `npm start` aplicação, certifique-se apenas de que `start` um script está napackage.js *no* ficheiro. Por exemplo:

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

Para utilizar umpackage.jspersonalizado *no* seu projeto, execute o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Corra com PM2

O recipiente inicia automaticamente a sua aplicação com PM2 quando um dos ficheiros de Node.js comum é encontrado no seu projeto:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Um dos [seguintes ficheiros PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.jse* *ecosystem.config.js*

Também pode configurar um ficheiro de início personalizado com as seguintes extensões:

- Um ficheiro *de .js*
- Um [ficheiro PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) com a extensão *.json*, *.config.js*, *.yaml,* ou *.yml*

Para adicionar um ficheiro de arranque personalizado, execute o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Depurar remotamente

> [!NOTE]
> A depuragem remota está atualmente em Pré-Visualização.

Pode desordificado a sua aplicação de Node.js remotamente no [Código do Estúdio Visual](https://code.visualstudio.com/) se a configurar para funcionar com [PM2,](#run-with-pm2)exceto quando a executar utilizando um *.config.js, *.yml, ou *.yaml*.

Na maioria dos casos, não é necessária nenhuma configuração extra para a sua aplicação. Se a sua aplicação for executada com um *process.jsno* ficheiro (padrão ou personalizado), deve ter uma `script` propriedade na raiz JSON. Por exemplo:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Para configurar o Código do Estúdio Visual para depurar remotamente, instale a [extensão do Serviço de Aplicações](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Siga as instruções na página de extensão e inscreva-se no Azure no Código do Estúdio Visual.

No explorador Azure, encontre a aplicação que pretende depurar, clique com o botão direito e selecione **Iniciar Depuração Remota**. Clique **em Sim** para ativar a sua aplicação. O Serviço de Aplicações inicia um proxy de túnel para si e anexa o depurar. Em seguida, pode fazer pedidos à app e ver o depurante a fazer uma pausa nos pontos de rutura.

Uma vez terminado com a depuragem, pare o depurante selecionando **Disconnect**. Quando solicitado, deve clicar em **Sim** para desativar a depuração remota. Para desativá-la mais tarde, clique com o botão direito na sua aplicação novamente no explorador Azure e selecione **Depuração Remota desativada**.

::: zone-end

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [configurar as definições da aplicação](configure-common.md) fora do código da aplicação. Depois pode aceder-lhes usando o padrão de Node.js. Por exemplo, para aceder a uma definição da aplicação chamada `NODE_ENV`, utilize o seguinte código:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Executar Grunt/Bower/Gulp

Por predefinição, o Serviço de Aplicações constrói automatização `npm install --production` quando reconhece que uma aplicação Node.js é implementada através da implementação de Git ou Zip com automatização de construção ativada. Se a sua aplicação necessitar de alguma das ferramentas populares de automação, como Grunt, Bower ou Gulp, precisa de fornecer um [script de implementação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) para executá-lo.

Para ativar o seu repositório para executar estas ferramentas, precisa adicioná-las às dependências *empackage.js.* Por exemplo:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

A partir de uma janela de terminal local, mude o diretório para a raiz do repositório e execute os seguintes comandos:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A sua raiz de repositório tem agora dois ficheiros adicionais: *.deployment* and *deploy.sh*.

Abra *deploy.sh* e encontre a `Deployment` secção, que se parece com esta:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Esta secção termina com a `npm install --production` execução. Adicione a secção de código que necessita para executar a ferramenta necessária *no final* da `Deployment` secção:

- [Estação Bower](#bower)
- [Gole](#gulp)
- [Grunt](#grunt)

Veja um [exemplo na amostra MEAN.js,](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)onde o script de implementação também executa um `npm install` comando personalizado.

### <a name="bower"></a>Bower

Este corte `bower install` corre.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gole

Este corte `gulp imagemin` corre.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Este corte `grunt` corre.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Detetar sessão HTTPS

No Serviço de Aplicações, a [rescisão de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos equilibristas de carga de rede, pelo que todos os pedidos HTTPS chegam à sua aplicação como pedidos HTTP não encriptados. Se a lógica da sua aplicação precisar de verificar se os pedidos do utilizador estão encriptados ou não, inspecione o `X-Forwarded-Proto` cabeçalho.

As estruturas web populares permitem-lhe aceder à `X-Forwarded-*` informação no seu padrão de aplicação padrão. No [Expresso,](https://expressjs.com/)pode utilizar [proxies de confiança.](https://expressjs.com/guide/behind-proxies.html) Por exemplo:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end


::: zone pivot="platform-linux"

## <a name="monitor-with-application-insights"></a>Monitorizar com o Application Insights

O Application Insights permite-lhe monitorizar o desempenho, exceções e utilização da sua aplicação sem escoar quaisquer alterações de código. Para anexar o agente App Insights, vá à sua aplicação web no Portal e selecione **Insights de Aplicação** em **Definições,** em seguida, selecione **Ligue os Insights de Aplicações**. Em seguida, selecione um recurso app Insights existente ou crie um novo. Por fim, **selecione Aplicar** na parte inferior. Para instrumentar a sua aplicação web usando o PowerShell, consulte [estas instruções](../azure-monitor/app/azure-web-apps.md?tabs=netcore#enabling-through-powershell)

Este agente monitorizará a aplicação Node.js do lado do servidor. Para monitorizar o JavaScript do seu lado do cliente, [adicione o SDK JavaScript ao seu projeto.](../azure-monitor/app/javascript.md) 

Para obter mais informações, consulte as notas de libertação da [extensão Application Insights](../azure-monitor/app/web-app-extension-release-notes.md).

::: zone-end

## <a name="troubleshooting"></a>Resolução de problemas

Quando uma aplicação de Node.js de trabalho se comporta de forma diferente no Serviço de Aplicações ou tem erros, experimente o seguinte:

- [Aceda ao fluxo de registo](#access-diagnostic-logs).
- Teste a aplicação localmente em modo de produção. O App Service executa as suas aplicações Node.js em modo de produção, pelo que tem de se certificar de que o seu projeto funciona como esperado no modo de produção local. Por exemplo:
    - Dependendo da sua *package.js,* podem ser instaladas diferentes embalagens para o modo de produção `dependencies` (vs. `devDependencies` ).
    - Certas estruturas web podem implantar ficheiros estáticos de forma diferente no modo de produção.
    - Certas estruturas web podem usar scripts de arranque personalizados quando estão em modo de produção.
- Execute a sua aplicação no Serviço de Aplicações em modo de desenvolvimento. Por exemplo, em [MEAN.js, ](https://meanjs.org/)pode definir a sua aplicação para o modo de desenvolvimento em tempo de [execução, definindo a `NODE_ENV` definição da aplicação](configure-common.md).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Node.js app com o MongoDB](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [FAQ do Serviço de Aplicações no Linux](faq-app-service-linux.md)

::: zone-end

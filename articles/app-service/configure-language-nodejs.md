---
title: Configurar apps Node.js windows
description: Saiba como configurar uma aplicação Node.js nas instâncias nativas do Windows do Serviço de Aplicações. Este artigo mostra as tarefas de configuração mais comuns.
ms.custom: devx-track-javascript
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 0fc6ed5cb090653e381d82f484d355a514520c62
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170904"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Configurar uma aplicação windows Node.js para o Azure App Service

Node.js aplicações devem ser implementadas com todas as dependências de NPM necessárias. O motor de implementação do Serviço de Aplicações funciona automaticamente `npm install --production` para si quando implementa um [repositório Git](deploy-local-git.md), ou um pacote [Zip](deploy-zip.md) com automatização de construção ativada. Se implementar os seus ficheiros utilizando [FTP/S,](deploy-ftp.md)no entanto, tem de carregar manualmente as embalagens necessárias. Para obter informações sobre aplicações Linux, consulte [configurar uma aplicação Linux PHP para o Azure App Service](containers/configure-language-nodejs.md).

Este guia fornece conceitos e instruções fundamentais para Node.js desenvolvedores que implementam o Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [Node.js quickstart](app-service-web-get-started-nodejs.md) e [Node.js com o tutorial mongoDB.](app-service-web-tutorial-nodejs-mongodb-app.md)

## <a name="show-nodejs-version"></a>Mostrar versão Node.js

Para mostrar a versão atual Node.js, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Para mostrar todas as versões Node.js suportadas, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Definir versão Node.js

Para definir a sua aplicação para uma [versão Node.js suportada,](#show-nodejs-version)executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir para uma `WEBSITE_NODE_DEFAULT_VERSION` versão suportada:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Esta definição especifica a versão Node.js a utilizar, tanto em tempo de execução como durante a restauração automática do pacote durante a automatização de construção do Serviço de Aplicações.

> [!NOTE]
> Deve definir a versão Node.js no seu `package.json` projeto. O motor de implantação funciona num processo separado que contém todas as versões Node.js suportadas.

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir definições](configure-common.md) de aplicações fora do seu código de aplicação. Depois pode aceder-lhes usando o padrão de Node.js. Por exemplo, para aceder a uma configuração de aplicação chamada `NODE_ENV` , use o seguinte código:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Executar Grunt/Bower/Gulp

Por predefinição, o Serviço de Aplicações constrói automatização `npm install --production` quando reconhece que uma aplicação Node.js é implementada através do Git (ou implementação zip com automatização de construção ativada). Se a sua aplicação necessitar de alguma das ferramentas populares de automação, como Grunt, Bower ou Gulp, precisa de fornecer um [script de implementação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) para executá-lo.

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

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Quando uma aplicação de Node.js de trabalho se comporta de forma diferente no Serviço de Aplicações ou tem erros, experimente o seguinte:

- [Aceda ao fluxo de registo](#access-diagnostic-logs).
- Teste a aplicação localmente em modo de produção. O App Service executa as suas aplicações Node.js em modo de produção, pelo que tem de se certificar de que o seu projeto funciona como esperado no modo de produção local. Por exemplo:
    - Dependendo da sua *package.js,* podem ser instaladas diferentes embalagens para o modo de produção `dependencies` (vs. `devDependencies` ).
    - Certas estruturas web podem implantar ficheiros estáticos de forma diferente no modo de produção.
    - Certas estruturas web podem usar scripts de arranque personalizados quando estão em modo de produção.
- Execute a sua aplicação no Serviço de Aplicações em modo de desenvolvimento. Por exemplo, em [MEAN.js, ](https://meanjs.org/)pode definir a sua aplicação para o modo de desenvolvimento em tempo de [execução, definindo a `NODE_ENV` definição da aplicação](configure-common.md).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Tutorial: Node.js app com o MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)


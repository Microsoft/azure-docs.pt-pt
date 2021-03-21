---
title: Tutorial - Preparar uma aplicação web para serviços de comunicação Azure (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Saiba como criar uma aplicação web de base que suporte serviços de comunicação Azure
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7cfc8fc24aea938b997fead4ca762ce7178e3386
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490624"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Tutorial: Preparar uma aplicação web para serviços de comunicação Azure (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Pode utilizar os Serviços de Comunicação Azure para adicionar comunicações em tempo real às suas aplicações. Neste tutorial, você vai aprender a configurar uma aplicação web que suporta os Serviços de Comunicação Azure. Este é um tutorial introdutório para novos desenvolvedores que querem começar com comunicações em tempo real.

No final deste tutorial, terá uma aplicação web de base que está configurada com bibliotecas de clientes do Azure Communication Services. Em seguida, pode utilizar essa aplicação para começar a construir a sua solução de comunicações em tempo real.

Sinta-se livre para visitar a [página gitHub dos Serviços de Comunicação Azure](https://github.com/Azure/communication) para fornecer feedback.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configure o seu ambiente de desenvolvimento.
> * Crie um servidor web local.
> * Adicione os pacotes Azure Communication Services ao seu website.
> * Publique o seu website em websites estáticos Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) A conta gratuita dá-lhe 200 dólares em créditos Azure para experimentar qualquer combinação de serviços.
- [Código de estúdio visual](https://code.visualstudio.com/) para edição de código no seu ambiente de desenvolvimento local.
- [webpack](https://webpack.js.org/) para embalar e localmente hospedar o seu código.
- [Node.js](https://nodejs.org/en/) instalar e gerir dependências como bibliotecas de clientes da Azure Communication Services e webpack.
- [nvm e npm](/windows/nodejs/setup-on-windows) para lidar com o controlo da versão.
- A [extensão de armazenamento Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) para Código de Estúdio Visual. Precisa desta extensão para publicar a sua aplicação no Azure Storage. [Leia mais sobre hospedagem de websites estáticos no Azure Storage](../../storage/blobs/storage-blob-static-website.md).
- [A extensão do Serviço de Aplicações Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). A extensão permite implantar websites com a opção de configurar integração contínua totalmente gerida e entrega contínua (CI/CD).
- [A extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para construir as suas próprias aplicações sem servidor. Por exemplo, pode hospedar a sua aplicação de autenticação em Funções Azure.
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Saiba como criar um recurso de Serviços de Comunicação.](../quickstarts/create-communication-resource.md)
- Um sinal de acesso ao utilizador. Para obter instruções, consulte o [quickstart para criar e gerir fichas de acesso](../quickstarts/access-tokens.md?pivots=programming-language-javascript) ou o [tutorial para a construção de um serviço de autenticação de confiança.](./trusted-service-tutorial.md)


## <a name="configure-your-development-environment"></a>Configure o seu ambiente de desenvolvimento

O seu ambiente de desenvolvimento local será configurado assim:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Diagrama que ilustra a arquitetura do ambiente de desenvolvimento.":::


### <a name="install-nodejs-nvm-and-npm"></a>Instale Node.js, nvm e npm

Usaremos Node.js para descarregar e instalar várias dependências que precisamos para a nossa aplicação do lado do cliente. Vamos usá-lo para gerar ficheiros estáticos que vamos hospedar no Azure, para que não tenhas de te preocupar em configurar o mesmo no teu servidor.

Os desenvolvedores windows podem seguir [este tutorial Node.js](/windows/nodejs/setup-on-windows) para configurar o Nó, nvm e npm. 

Este tutorial baseia-se na versão LTS 12.20.0. Depois de instalar o nvm, utilize o seguinte comando PowerShell para implementar a versão que pretende utilizar:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Screenshot que mostra os comandos para a implementação de uma versão Nó.":::

### <a name="configure-visual-studio-code"></a>Configurar o Visual Studio Code

Você pode baixar [Visual Studio Code](https://code.visualstudio.com/) em uma das plataformas [suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Crie um espaço de trabalho para os seus projetos de Serviços de Comunicação Azure

Crie uma pasta para armazenar os seus ficheiros de projeto, como este: `C:\Users\Documents\ACS\CallingApp` . No Código do Estúdio Visual, selecione  >  **File Add Folder para Workspace** e adicione a pasta ao seu espaço de trabalho.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Screenshot que mostra seleções para adicionar um ficheiro a um espaço de trabalho.":::

Vá ao **EXPLORER** no painel esquerdo e verá a sua `CallingApp` pasta no espaço de trabalho SEM **TÍTULO.**

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Screenshot que mostra Explorer e o espaço de trabalho sem título.":::

Sinta-se livre para atualizar o nome do seu espaço de trabalho. Pode validar a sua versão Node.js clicando à direita na sua `CallingApp` pasta e selecionando **Abrir no Terminal Integrado**.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Screenshot que mostra a seleção para abrir uma pasta num terminal integrado.":::

No terminal, introduza o seguinte comando para validar a versão Node.js instalada no passo anterior:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Screenshot que mostra validar a versão Nó.":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Instale extensões Azure para Código do Estúdio Visual

Instale a [extensão de armazenamento Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) através do mercado do Estúdio Visual ou através do Código do Estúdio Visual **(Ver**  >  **Extensões**  >  **Azure Storage**).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Screenshot que mostra o botão para instalar a extensão de Armazenamento Azure.":::

Siga os mesmos passos para as extensões do [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) e [do Azure App Service.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)


## <a name="set-up-a-local-web-server"></a>Configurar um servidor web local

### <a name="create-an-npm-package"></a>Criar um pacote npm

No seu terminal, a partir do caminho da sua pasta de espaço de trabalho, insira:

``` console
npm init -y
```

Este comando inicializa um novo pacote npm e adiciona `package.json` na pasta raiz do seu projeto.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Screenshot que mostra o pacote J S O N.":::

Para obter mais documentação sobre a `npm init` página , consulte a página [npm Docs para este comando](https://docs.npmjs.com/cli/v6/commands/npm-init).

### <a name="install-webpack"></a>Instalar webpack

Pode utilizar [o webpack](https://webpack.js.org/) para agregar código em ficheiros estáticos que pode implementar no Azure. Também tem um servidor de desenvolvimento, que irá configurar para usar com a amostra de chamada.

No seu terminal, insira o seguinte comando para instalar webpack:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Este tutorial foi testado com as versões especificadas no comando anterior. Especificar `-dev` indica ao gestor do pacote que esta dependência é para fins de desenvolvimento e não deve ser incluída no código que implementa para o Azure.

Verá dois novos pacotes adicionados ao seu `package.json` ficheiro como `devDependencies` . Os pacotes serão instalados no `./CallingApp/node_modules/` diretório.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Screenshot que mostra a configuração da webpack.":::

### <a name="configure-the-development-server"></a>Configurar o servidor de desenvolvimento

Executar uma aplicação estática (como o seu `index.html` ficheiro) a partir do seu navegador utiliza o `file://` protocolo. Para que os seus módulos npm funcionem corretamente, necessitará do protocolo HTTP utilizando o webpack como servidor de desenvolvimento local.

Criará duas configurações: uma para desenvolvimento e outra para produção. Os ficheiros preparados para a produção serão minificados, o que significa que removerás o espaço e os caracteres não reutilizados. Esta configuração é adequada para cenários de produção em que a latência deve ser minimizada ou onde o código deve ser obfuscado.

Utilizará a `webpack-merge` ferramenta para trabalhar com [diferentes ficheiros de configuração para webpack](https://webpack.js.org/guides/production/).

Vamos começar com o ambiente de desenvolvimento. Primeiro, tem de `webpack merge` instalar. No seu terminal, executar o seguinte comando:

```Console
npm install --save-dev webpack-merge
```

No seu `package.json` ficheiro, pode ver mais uma dependência adicionada a `devDependencies` .

Em seguida, crie um ficheiro chamado `webpack.common.js` e adicione o seguinte código:

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }
}
```

Em seguida, adicione mais dois ficheiros, um para cada configuração:

* `webpack.dev.js`
* `webpack.prod.js`

Agora modificar o `webpack.dev.js` ficheiro adicionando-lhe o seguinte código:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
Nesta configuração, importa parâmetros comuns `webpack.common.js` de, fundir os dois ficheiros, definir o modo para `development` , e configurar o mapa de origem como `inline-source-map` .

O modo de desenvolvimento diz à webpack para não minificar os ficheiros e não produzir ficheiros de produção otimizados. Pode encontrar documentação detalhada nos modos de webpack na [página webpack Mode](https://webpack.js.org/configuration/mode/).

As opções de mapa de origem estão listadas na [página webpack Devtool](https://webpack.js.org/configuration/devtool/#root). A definição do mapa de origem facilita a depurar através do seu navegador.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Screenshot que mostra o código para configurar a webpack.":::

Para executar o servidor de desenvolvimento, vá `package.json` e adicione o seguinte código em `scripts` :

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

O seu ficheiro agora deve ser assim:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

Acrescentou o comando que pode ser usado a partir das npm.

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Screenshot que mostra a modificação de package.jsligado.":::

### <a name="test-the-development-server"></a>Teste o servidor de desenvolvimento

 No Código do Estúdio Visual, crie três ficheiros no âmbito do seu projeto:

* `index.html`
* `app.js`
* `app.css` (opcional, para estilizar a sua app)

Cole este código `index.html` em:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Screenshot que mostra o ficheiro H T M L.":::

Adicione o seguinte código a `app.js`:

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Adicione o seguinte código a `app.css`:

```CSS
html {
    font-family: sans-serif;
  }
```
Não se esqueça de guardar! O ficheiro não-salvo é indicado por pontos brancos ao lado de nomes de ficheiros no Explorer.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="Screenshot que mostra o ficheiro App.js com o código JavaScript.":::

Quando abrir esta página, deverá ver a sua mensagem exibida com um alerta na consola do seu navegador.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Screenshot que mostra o ficheiro .css App.":::

Utilize o seguinte comando terminal para testar a sua configuração de desenvolvimento:

```Console
npm run build:dev
```

A consola mostra-lhe onde o servidor está a funcionar. Por defeito, `http://localhost:8080` é. O `build:dev` comando é o comando a que acrescentou `package.json` anteriormente.

:::image type="content" source="./media/step-one-pic-16.png" alt-text="Screenshot que mostra iniciar um servidor de desenvolvimento.":::
 
Vá ao endereço no seu browser e deverá ver a página e o alerta configurado em etapas anteriores.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="Screenshot da página H T M L.":::
  
 
Enquanto o servidor está em funcionamento, pode alterar o código e o servidor. A página HTML recarregará automaticamente. 

Em seguida, vá ao `app.js` ficheiro no Código do Estúdio Visual e apague `alert('Hello world alert!');` . Guarde o seu ficheiro e verifique se o alerta desaparece do seu navegador.

Para parar o seu servidor, pode funcionar `Ctrl+C` no seu terminal. Para iniciar o seu servidor, `npm run build:dev` insira a qualquer momento.

## <a name="add-the-azure-communication-services-packages"></a>Adicione os pacotes Azure Communication Services

Utilize o `npm install` comando para instalar os Serviços de Comunicação Azure chamando a biblioteca do cliente para o JavaScript.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Esta ação adiciona os serviços de comunicação Azure comuns e os pacotes de chamadas como dependências do seu pacote. Verá dois novos pacotes adicionados ao `package.json` ficheiro. Pode encontrar mais informações `npm install` sobre a [página npm Docs para esse comando](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Screenshot que mostra código para instalar pacotes de Serviços de Comunicação Azure.":::

Estes pacotes são fornecidos pela equipa dos Serviços de Comunicação Azure e incluem as bibliotecas de autenticação e chamada. O `--save` comando indica que a aplicação depende destas embalagens para uso de produção e será incluída no `devDependencies` `package.json` ficheiro. Quando construir o pedido de produção, os pacotes serão incluídos no seu código de produção.


## <a name="publish-your-website-to-azure-static-websites"></a>Publique o seu website em websites estáticos Azure

### <a name="create-a-configuration-for-production-deployment"></a>Criar uma configuração para implantação de produção

Adicione o seguinte código a `webpack.prod.js`:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Esta configuração será fundida `webpack.common.js` (onde especificou o ficheiro de entrada e onde armazenar os resultados). A configuração também definirá o modo para `production` .
 
In `package.json` , adicione o seguinte código:

```JavaScript
"build:prod": "webpack --config webpack.prod.js"
```

O seu ficheiro deverá ter o seguinte aspeto:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

:::image type="content" source="./media/step-one-pic-20.png" alt-text="Screenshot que mostra ficheiros configurados.":::


No terminal, corra:

```Console
npm run build:prod
```

O comando cria uma `dist` pasta e um ficheiro estático pronto para a produção. `app.js` 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Screenshot que mostra a construção da produção.":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Implemente a sua app para o Azure Storage

Copiar `index.html` e para a `app.css` `dist` pasta.

Na `dist` pasta, crie um ficheiro e dê-lhe `404.html` nomes . Copie a seguinte marcação nesse ficheiro:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Guarde o ficheiro (Ctrl+S).

Clique com o botão direito na `dist` pasta e selecione **Implementar para o Website Estático através do Armazenamento Azure**.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Screenshot das seleções para começar a implantar para Azure.":::
 
Na **subscrição Select**, selecione **Iniciar sessão no Azure** (ou **Crie uma Conta Azure Gratuita** se ainda não tiver criado uma subscrição antes).
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Screenshot que mostra seleções para iniciar sessão no Azure.":::
 
Selecione **Criar nova conta de armazenamento**  >  **Avançada.**

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Screenshot que mostra seleções para criar o grupo de conta de armazenamento.":::
 
Forneça o nome do grupo de armazenamento.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="Screenshot que mostra adicionar um nome para a conta.":::
 
Criar um novo grupo de recursos, se necessário.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Screenshot que mostra a seleção para criar um novo grupo de recursos.":::
  
Pois **pretende ativar o alojamento estático do site?** 

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Screenshot que mostra selecionar a opção para permitir hospedagem estática no site.":::
  
Para **Introduzir o nome do documento de índice,** aceite o nome do ficheiro predefinido. Já criou o `index.html` ficheiro.

Para **introduzir o caminho do documento de erro 404**, **introduza404.html**.  
  
Selecione a localização da aplicação. A localização que selecionar definirá qual processador de mídia será utilizado na sua futura aplicação de chamada em chamadas de grupo. 

A Azure Communication Services seleciona o processador de mídia com base na localização da aplicação.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Screenshot que mostra uma lista de locais.":::
  
Aguarde até que o recurso e o seu website sejam criados. 
 
**Selecione Procurar no site.**

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Screenshot que mostra uma mensagem de que a implementação está completa, com o botão para navegar para um site.":::
 
A partir das ferramentas de desenvolvimento do seu navegador, pode inspecionar a fonte e ver o ficheiro que está preparado para a produção.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Screenshot da fonte do site com arquivo.":::

Vá ao [portal Azure,](https://portal.azure.com/#home)selecione o seu grupo de recursos e selecione a aplicação que criou. Em seguida, selecione **Settings**  >  **Static website**. Pode ver que os sites estáticos estão ativados. Note o ponto final primário, o nome do documento do índice e o caminho do documento de erro.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Screenshot que mostra seleção estática do site.":::

Em **Serviço Blob**, selecione **Contentores**. A lista mostra dois contentores criados, um para registos `$logs` e outro para o conteúdo do seu website `$web` .

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Screenshot que mostra a configuração do recipiente.":::

Se abrir o `$web` contentor, verá os ficheiros que criou no Visual Studio e foi implantado para a Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Screenshot que mostra ficheiros implantados no Azure.":::

Pode recolocar a aplicação a partir do Código do Estúdio Visual a qualquer momento.

Está agora pronto para construir a sua primeira aplicação web Azure Communication Services.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar chamadas de voz à sua aplicação](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Também pode querer:

- [Adicionar o chat à aplicação](../quickstarts/chat/get-started.md)
- [Criar fichas de acesso ao utilizador](../quickstarts/access-tokens.md)
- [Conheça a arquitetura do cliente e do servidor](../concepts/client-and-server-architecture.md)
- [Saiba mais sobre a autenticação](../concepts/authentication.md)

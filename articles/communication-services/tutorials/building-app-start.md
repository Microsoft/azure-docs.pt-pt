---
title: Tutorial - Preparar uma aplicação web para serviços de comunicação Azure (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Saiba como criar uma aplicação web de base que suporte serviços de comunicação Azure
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 01/03/2012
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: db59a9e7693190582736b9460658f629f4f1e555
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369633"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Tutorial: Preparar uma aplicação web para serviços de comunicação Azure (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Os Serviços de Comunicação Azure permitem-lhe adicionar comunicações em tempo real às suas aplicações. Neste tutorial, você vai aprender a configurar uma aplicação web que suporta os Serviços de Comunicação Azure. Este é um tutorial introdutório destinado a novos desenvolvedores que querem começar com comunicações em tempo real.

No final deste tutorial, terá uma aplicação web de base configurada com bibliotecas de clientes Azure Communication Services que pode usar para começar a construir a sua solução de comunicações em tempo real.

Sinta-se livre para visitar a página [gitHub dos Serviços de Comunicação Azure](https://github.com/Azure/communication) para fornecer feedback.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configure o seu ambiente de desenvolvimento
> * Criar um webserver local
> * Adicione os pacotes Azure Communication Services ao seu website
> * Publique o seu website em Websites Estáticos Azure

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Note que a conta gratuita lhe dá $200 em créditos Azure para experimentar qualquer combinação de serviços.
- [Código do Estúdio Visual](https://code.visualstudio.com/): Vamos usar isto para editar código no seu ambiente de desenvolvimento local.
- [webpack](https://webpack.js.org/): Isto será usado para embalar e hospedar localmente o seu código.
- [Node.js](https://nodejs.org/en/): Isto será usado para instalar e gerir dependências como bibliotecas de clientes Azure Communication Services e webpack.
- [nvm e npm](https://docs.microsoft.com/windows/nodejs/setup-on-windows) para lidar com o controlo da versão.
- A [extensão de armazenamento Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) para Código de Estúdio Visual. Esta extensão é necessária para publicar a sua aplicação no Azure Storage. [Leia mais sobre hospedar sites estáticos no Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)
- [A extensão do Serviço de Aplicações Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). A extensão permite a implementação de websites (semelhantes aos anteriores), mas com a opção de configurar a integração contínua e a entrega contínua totalmente geridas (CI/CD).
- A [extensão Azure Function](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para construir as suas próprias aplicações sem servidor. Por exemplo, pode hospedar a sua aplicação de autenticação em funções Azure.
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../quickstarts/create-communication-resource.md)
- Um sinal de acesso ao utilizador. Consulte o [acesso rápido ou](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-javascript) o tutorial de serviço [fidedigno](https://docs.microsoft.com/azure/communication-services/tutorials/trusted-service-tutorial) para obter instruções.


## <a name="configure-your-development-environment"></a>Configure o seu ambiente de desenvolvimento

O seu ambiente de desenvolvimento local será configurado assim:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Arquitetura ambiental de desenvolvimento":::


### <a name="install-nodejs-nvm-and-npm"></a>Instale Node.js, nvm e npm

Usaremos Node.js para descarregar e instalar várias dependências que precisamos para a nossa aplicação do lado do cliente. Vamos usá-lo para gerar ficheiros estáticos que vamos hospedar no Azure, para que não tenhas de te preocupar em configurar o mesmo no teu servidor.

Os desenvolvedores windows podem seguir [este tutorial NodeJS](https://docs.microsoft.com/windows/nodejs/setup-on-windows) para configurar o Nó, nvm e npm. 

Testámos este tutorial usando a versão LTS 12.20.0. Depois de instalar o nvm, utilize o seguinte comando PowerShell para implementar a versão que pretende utilizar:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Trabalhar com a NVM para implantar Node.js":::

### <a name="configure-visual-studio-code"></a>Configurar o Visual Studio Code

Você pode baixar [Visual Studio Code](https://code.visualstudio.com/) em uma das plataformas [suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Crie um espaço de trabalho para os seus projetos de Serviços de Comunicação Azure

Crie uma nova pasta para armazenar os seus ficheiros de projeto, como este: `C:\Users\Documents\ACS\CallingApp` . No Código do Estúdio Visual, clique em "File", "Add Folder to Workspace" e adicione a pasta ao seu espaço de trabalho.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Criação de um novo local de trabalho":::

Vá ao "Explorer" no Código do Estúdio Visual no painel esquerdo e verá a sua pasta "CallingApp" no espaço de trabalho "Sem título".

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Explorador":::

Sinta-se livre para atualizar o nome do seu espaço de trabalho. Pode validar a sua versão Node.js clicando à direita na sua pasta "CallingApp" e selecionando "Abrir em Terminal Integrado".

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Abrir um terminal":::

No terminal, escreva o seguinte comando para validar a versão node.js instalada no passo anterior:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Validação da versão Node.js":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Instalar extensões Azure para código de estúdio visual

Instale a [extensão de armazenamento Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) através do mercado do Estúdio Visual ou com o Código do Estúdio Visual (Ver extensões > > Azure Storage).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Instalação da extensão de armazenamento Azure 1":::

Siga os mesmos passos para as extensões do [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) e [do Azure App Service.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)


## <a name="set-up-a-local-webserver"></a>Criar um webserver local

### <a name="create-a-new-npm-package"></a>Criar um novo pacote npm

No seu terminal, a partir do caminho da sua pasta de espaço de trabalho, escreva:

``` console
npm init -y
```

Este comando inicializa um novo pacote npm e adiciona `package.json` na pasta raiz do seu projeto.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Pacote JSON":::

Documentação adicional sobre o comando npm init pode ser encontrada [aqui](https://docs.npmjs.com/cli/v6/commands/npm-init)

### <a name="install-webpack"></a>Instalar webpack

[webpack](https://webpack.js.org/) permite-lhe embalar código em ficheiros estáticos que pode implementar para Azure. Também tem um servidor de desenvolvimento, que vamos configurar para usar com a amostra de chamada.

No seu tipo de terminal, o seguinte para instalar webpack:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Este tutorial foi testado utilizando as versões acima especificadas. Especificar `-dev` diz ao gestor do pacote que esta dependência é para fins de desenvolvimento e não deve ser incluída no código que implementamos para a Azure.

Verá dois novos pacotes adicionados ao seu `package.json` ficheiro como "devDependencies". Os pacotes serão instalados no `./CallingApp/node_modules/` diretório.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="configuração de webpack":::

### <a name="configure-the-development-server"></a>Configurar o servidor de desenvolvimento

Executar uma aplicação estática (como o seu `index.html` ficheiro) a partir do seu navegador utiliza o `file://` protocolo. Para que os seus módulos npm funcionem corretamente, precisaremos do protocolo HTTP usando webpack como um servidor de desenvolvimento local.

Vamos criar duas configurações: uma para desenvolvimento e outra para produção. Os ficheiros preparados para a produção serão minificados, o que significa que removeremos o espaço e os caracteres não reutilizados. Isto é adequado para cenários de produção em que a latência deve ser minimizada ou onde o código deve ser obfuscado.

Vamos usar a `webpack-merge` ferramenta para trabalhar com [diferentes ficheiros de configuração para webpack](https://webpack.js.org/guides/production/)

Vamos começar com o ambiente de desenvolvimento. Primeiro, temos de `webpack merge` instalar. No seu terminal, execute o seguinte:

```Console
npm install --save-dev webpack-merge
```

No seu `package.json` ficheiro, pode ver mais uma dependência adicionada às "devDependencies".

No próximo passo, precisamos de criar um novo ficheiro `webpack.common.js` e adicionar o seguinte código:

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

Em seguida, adicionaremos mais dois ficheiros, um para cada configuração:

* webpack.dev.js
* webpack.prod.js

No próximo passo, precisamos modificar o `webpack.dev.js` ficheiro. Adicione o seguinte código a este ficheiro:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
Nesta configuração, importamos parâmetros comuns de `webpack.common.js` , fundir os dois ficheiros, definir o modo para "desenvolvimento", e configurar o SourceMap como "mapa de fontes inline".

O modo de desenvolvimento diz à webpack para não minificar os ficheiros e não produzir ficheiros de produção otimizados. Documentação detalhada sobre os modos de webpack pode ser encontrada [aqui.](https://webpack.js.org/configuration/mode/)

As opções de mapa de origem estão listadas [aqui.](https://webpack.js.org/configuration/devtool/#root) A definição do mapa de origem facilita a depurar através do seu navegador.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Mochila web configurar":::

Para executar o servidor de desenvolvimento, vá `package.json` e adicione o seguinte código nos scripts:

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

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Modificação package.jsem":::

### <a name="testing-the-development-server"></a>Testar o servidor de desenvolvimento

 No Código do Estúdio Visual, crie três ficheiros no âmbito do seu projeto:

* `index.html`
* `app.js`
* `app.css` (opcional, isto permite-lhe modelar a sua app)

Cole isto `index.html` em:

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
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Arquivo HTML":::

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

 :::image type="content" source="./media/step-one-pic-14.png" alt-text="App.js ficheiro com código JS":::

Quando abrir esta página, deverá ver a sua mensagem exibida com um alerta e dentro da consola do seu navegador.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="App.css arquivo":::

Utilize o seguinte comando terminal para testar a sua configuração de desenvolvimento:

```Console
npm run build:dev
```

A consola irá mostrar-lhe onde o servidor está a funcionar. Por defeito, `http://localhost:8080` é. O comando build:dev é o comando que adicionámos ao nosso `package.json` anterior.

 :::image type="content" source="./media/step-one-pic-16.png" alt-text="Iniciar um servidor de desenvolvimento":::
 
 Navegue para o endereço no seu navegador e deverá ver a página e o alerta, configurados em passos anteriores.
 
  :::image type="content" source="./media/step-one-pic-17.png" alt-text="Página html":::
  
 
Enquanto o servidor estiver em funcionamento, pode alterar o código e o servidor e a página HTML recarregam automaticamente. 

Em seguida, vá ao `app.js` ficheiro no Código do Estúdio Visual e apague `alert('Hello world alert!');` . Guarde o seu ficheiro e verifique se o alerta desaparece do seu navegador.

Para parar o seu servidor, pode funcionar `Ctrl+C` no seu terminal. Para iniciar o seu servidor, escreva `npm run build:dev` a qualquer momento.

## <a name="add-the-azure-communication-services-packages"></a>Adicione os pacotes Azure Communication Services

Utilize o `npm install` comando para instalar a biblioteca de clientes Azure Communication Services Call para JavaScript.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Esta ação irá adicionar os serviços de comunicação Azure comuns e os pacotes de chamadas como dependências do seu pacote. Verá dois novos pacotes adicionados ao `package.json` ficheiro. Mais informações sobre o `npm install` comando podem ser [encontradas aqui.](https://docs.npmjs.com/cli/v6/commands/npm-install)

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Instalação de pacotes de Serviços de Comunicação Azure":::

Estes pacotes são fornecidos pela equipa dos Serviços de Comunicação Azure e incluem as bibliotecas de autenticação e chamada. O comando "-save" indica que a nossa aplicação depende destes pacotes para uso de produção e será incluído no `dependencies` nosso `package.json` ficheiro. Quando construirmos a aplicação para produção, os pacotes serão incluídos no nosso código de produção.


## <a name="publish-your-website-to-azure-static-websites"></a>Publique o seu website em Websites Estáticos Azure

### <a name="create-a-configuration-for-production-deployment"></a>Criar uma configuração para implantação de produção

Adicione o seguinte código `webpack.prod.js` ao:

```JavaScript
const { merge } = require('webpack-merge');
 const common = require('./webpack.common.js');

 module.exports = merge(common, {
   mode: 'production',
 });
 ```

Note que esta configuração será fundida com o webpack.common.js (onde especificamos o ficheiro de entrada e onde armazenar os resultados) e definirá o modo de "produção".
 
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
    "@azure/communication-calling": "^1.0.0-beta.3",
    "@azure/communication-common": "^1.0.0-beta.3"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

 :::image type="content" source="./media/step-one-pic-20.png" alt-text="Ficheiros configurados":::


Na execução do terminal:

```Console
npm run build:prod
```

O comando criará uma `dist` pasta e um ficheiro estático pronto para a produção. `app.js` 

 :::image type="content" source="./media/step-one-pic-21.png" alt-text="Construção de produção":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Implemente a sua app para o Azure Storage
 
Copiar `index.html` e para a `app.css` `dist` pasta.

Na `dist` pasta, crie um novo ficheiro e nomeie-o `404.html` . Copie a seguinte marcação nesse ficheiro:

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

Guarde o ficheiro (Ctrl + S).

Clique com o botão direito e selecione implementar para o Site Estático via Azure Storage.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Comece a implantar para Azure":::
 
No `Select subscription` campo, selecione "Iniciar sessão no Azure "ou "Criar uma Conta Azure Gratuita" se ainda não tiver criado uma subscrição antes)
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Iniciar sessão no Azure":::
 
`Create new Storage Account`  >  `Advanced` Selecione:

 :::image type="content" source="./media/step-one-pic-24.png" alt-text="Criação do Grupo de Conta de Armazenamento":::
 
 Fornecer o nome do grupo de armazenamento:
 
 :::image type="content" source="./media/step-one-pic-25.png" alt-text="Adicionar um nome para a conta":::
 
Criar um novo grupo de recursos, se necessário:
 
  :::image type="content" source="./media/step-one-pic-26.png" alt-text="Criação de um novo grupo":::
  
  Responda "Sim" a gostaria de ativar o alojamento estático do site?"
  
  :::image type="content" source="./media/step-one-pic-27.png" alt-text="Selecionando opção para permitir hospedagem estática do site":::
  
Aceite o nome de ficheiro predefinido em "Insira o nome do documento de índice", tal como criamos o ficheiro `index.html` .

Digite o `404.html` "Insira o caminho do documento de erro 404".  
  
Selecione a localização da aplicação. A localização selecionada definirá qual processador de mídia será utilizado na sua futura aplicação de chamada em chamadas de grupo. 

A Azure Communication Services seleciona o Processador de Mídia com base na localização da aplicação.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Selecione localização":::
  
Aguarde até que o recurso e o seu website sejam criados. 
 
Clique em "Procurar no site":

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Implantação concluída":::
 
A partir das ferramentas de desenvolvimento do seu navegador, pode inspecionar a fonte e ver o nosso ficheiro, preparado para a produção.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Site":::

Vá ao [portal Azure,](https://portal.azure.com/#home)selecione o seu grupo de recursos, selecione a aplicação que criou e navegue para `Settings`  >  `Static website` . Pode ver que os websites estáticos estão ativados e anotar os ficheiros de documentos de fim primário, documento de índice e de trajetória de erro.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Seleção estática do site":::

Em "Serviço Blob" selecione os "Contentores" e verá dois contentores criados, um para registos ($logs) e conteúdo do seu website ($web)

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Configuração do recipiente":::

Se for a `$web` casa verá os seus ficheiros que criou no Visual Studio e foi implantado para a Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Implementação":::

Pode recolocar a aplicação a partir do Código do Estúdio Visual a qualquer momento.

Está agora pronto para construir a sua primeira aplicação web Azure Communication Services.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar chamadas de voz à sua aplicação](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Também pode querer:

- [Adicionar o chat à aplicação](../quickstarts/chat/get-started.md)
- [Criação de fichas de acesso ao utilizador](../quickstarts/access-tokens.md)
- [Conheça a arquitetura do cliente e do servidor](../concepts/client-and-server-architecture.md)
- [Saiba mais sobre a autenticação](../concepts/authentication.md)

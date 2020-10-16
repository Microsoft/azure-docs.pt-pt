---
title: Construa e implemente uma aplicação Node.js Express para a Azure Cloud Services
titleSuffix: Azure Cloud Services
description: Utilize este tutorial para criar uma nova aplicação utilizando o módulo Express, que fornece uma estrutura MVC para criar aplicações web Node.js.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.custom: devx-track-js
ms.openlocfilehash: 7ecaccf36988e94d0cb1114bd04c5d571d4fe86d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071868"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Construa e implemente uma aplicação web Node.js utilizando o Express em um Azure Cloud Services

Node.js inclui um conjunto mínimo de funcionalidades no tempo de execução do núcleo.
Os desenvolvedores usam frequentemente módulos de terceiros para fornecer funcionalidades adicionais ao desenvolver uma aplicação Node.js. Neste tutorial irá criar uma nova aplicação utilizando o módulo [Express,](https://github.com/expressjs/express) que fornece uma estrutura MVC para a criação de aplicações web Node.js.

Uma imagem da aplicação completa é a seguinte:

![Um navegador web exibindo Welcome to Express em Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Criar um projeto de serviço em nuvem
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Execute os seguintes passos para criar um novo projeto de serviço em nuvem chamado 'expressapp':

1. A partir do **Menu Iniciar** ou do Ecrã **De Início,** procure por **Windows PowerShell**. Por fim, clique com o botão direito **do Windows PowerShell** e selecione **Executar Como Administrador**.

    ![Ícone Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Alterar diretórios para **c: \\ diretório de nó** e, em seguida, introduzir os seguintes comandos para criar uma nova solução chamada **expressapp** e uma função web chamada **WebRole1**:

   ```powershell
   PS C:\node> New-AzureServiceProject expressapp
   PS C:\Node\expressapp> Add-AzureNodeWebRole
   PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   ```

   > [!NOTE]
   > Por padrão, **o Add-AzureNodeWebRole** utiliza uma versão mais antiga de Node.js. A declaração **do Set-AzureServiceProjectRole** acima instrui a Azure a utilizar v0.10.21 de Nó.  Note que os parâmetros são sensíveis a maiôs.  Pode verificar se a versão correta do Node.js foi selecionada verificando a propriedade dos **motores** em **WebRole1\package.js.**
>
>

## <a name="install-express"></a>Instalar o Express
1. Instale o gerador Express emitindo o seguinte comando:

    ```powershell
    PS C:\node\expressapp> npm install express-generator -g
    ```

    A saída do comando npm deve ser semelhante ao resultado abaixo.

    ![Windows PowerShell exibindo a saída do comando expresso de instalação npm.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Altere os diretórios para o **diretório WebRole1** e utilize o comando expresso para gerar uma nova aplicação:

    ```powershell
    PS C:\node\expressapp\WebRole1> express
    ```

    Será solicitado para substituir a sua aplicação anterior. **Insira-o** ou **sim** para continuar. O Express gerará o ficheiro app.js e uma estrutura de pasta para a construção da sua aplicação.

    ![A saída do comando expresso](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Para instalar dependências adicionais definidas no package.jsno ficheiro, insira o seguinte comando:

    ```powershell
    PS C:\node\expressapp\WebRole1> npm install
    ```

   ![A saída do comando de instalação npm](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Utilize o seguinte comando para copiar o **ficheiro bin/www** para **server.js**. Isto é para que o serviço de nuvem possa encontrar o ponto de entrada para esta aplicação.

    ```powershell
    PS C:\node\expressapp\WebRole1> copy bin/www server.js
    ```

   Após o preeno de um comando concluído, deverá ter um ficheiro **server.js** no diretório WebRole1.
5. Modifique o **server.js** para remover um dos caracteres '.' da seguinte linha.

    ```js
    var app = require('../app');
    ```

   Depois de escoar esta modificação, a linha deve aparecer da seguinte forma.

    ```js
    var app = require('./app');
    ```

   Esta alteração é necessária uma vez que mudamos o ficheiro (anteriormente **bin/www**,) para o mesmo diretório que o ficheiro da aplicação que é necessário. Depois de fazer esta alteração, guarde o ficheiro **server.js.**
6. Utilize o seguinte comando para executar a aplicação no emulador Azure:

    ```powershell
    PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
    ```

    ![Uma página web contendo bem-vindo para expressar.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modificar a Vista
Agora modifique a vista para exibir a mensagem "Bem-vindo ao Expresso em Azure".

1. Introduza o seguinte comando para abrir o ficheiro index.jade:

    ```powershell
    PS C:\node\expressapp\WebRole1> notepad views/index.jade
    ```

   ![O conteúdo do ficheiro index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

   Jade é o motor de vista padrão usado pelas aplicações Express. Para obter mais informações sobre o motor de vista de Jade, [http://jade-lang.com][http://jade-lang.com] consulte.
2. Modifique a última linha de texto por **apedência em Azure**.

   ![O ficheiro index.jade, a última linha diz: p Bem-vindo a \# {título} em Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Guarde o ficheiro e saia do Bloco de Notas.
4. Refresque o seu navegador e verá as suas alterações.

   ![Uma janela de navegador, a página contém Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Depois de testar a aplicação, utilize o **cmdlet Stop-AzureEmulator** para parar o emulador.

## <a name="publishing-the-application-to-azure"></a>Publicação da Aplicação à Azure
Na janela Azure PowerShell, utilize o **cmdlet Publish-AzureServiceProject** para implantar a aplicação num serviço de nuvem

```powershell
PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch
```

Assim que a operação de implementação estiver concluída, o seu navegador abrirá e exibirá a página web.

![Um navegador web exibindo a página Express. O URL indica que está agora hospedado no Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Passos seguintes
Para mais informações, consulte o [Centro de Programadores de Node.js](/azure/developer/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com
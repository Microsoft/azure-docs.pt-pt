---
title: Construa e implemente uma app Node.js Express para a Azure Cloud Services
titleSuffix: Azure Cloud Services
description: Construa e implemente uma aplicação Express.js no Node.js para os Serviços De Nuvem Azure
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 79a998930a384420b22add8825ee4b2269eb4539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360757"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Construa e implemente uma aplicação web Node.js usando o Express num Azure Cloud Services

Node.js inclui um conjunto mínimo de funcionalidade no tempo de funcionamento do núcleo.
Os desenvolvedores usam frequentemente módulos de terceiros para fornecer funcionalidadeadicional ao desenvolver uma aplicação Node.js. Neste tutorial irá criar uma nova aplicação utilizando o módulo [Express,](https://github.com/expressjs/express) que fornece um quadro de MVC para a criação de aplicações web Node.js.

Uma imagem da aplicação concluída é a seguir:

![Um navegador web exibindo Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Criar um Projeto de Serviço na Nuvem
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Execute os seguintes passos para criar um novo projeto de serviço na nuvem chamado 'expressapp':

1. A partir do **Menu Iniciar** ou Ecrã **de Início,** procure o **Windows PowerShell**. Finalmente, clique no **Windows PowerShell** e selecione **Executar Como Administrador**.
   
    ![Ícone Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Alterne os diretórios para o **\\c:** diretório do nó e, em seguida, insira os seguintes comandos para criar uma nova solução chamada **expressapp** e um papel web chamado **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Por padrão, **add-AzureNodeWebRole** usa uma versão mais antiga do Node.js. A declaração **set-AzureServiceProjectRole** acima instrui o Azure a utilizar v0.10.21 do Nó.  Note que os parâmetros são sensíveis ao caso.  Pode verificar se a versão correta do Node.js foi selecionada verificando a propriedade dos **motores** em **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Instalar o Express
1. Instale o gerador Express emitindo o seguinte comando:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    A saída do comando npm deve ser semelhante ao resultado abaixo. 
   
    ![O Windows PowerShell exibe a saída do comando expresso npm.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Altere os diretórios para o diretório **WebRole1** e use o comando expresso para gerar uma nova aplicação:
   
        PS C:\node\expressapp\WebRole1> express
   
    Ser-lhe-á pedido que sobreponha a sua candidatura anterior. Insira **y** ou **sim** para continuar. O Express gerará o ficheiro app.js e uma estrutura de pasta para a construção da sua aplicação.
   
    ![A saída do comando expresso](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Para instalar dependências adicionais definidas no ficheiro package.json, introduza o seguinte comando:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![A saída do comando de instalação npm](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Utilize o seguinte comando para copiar o ficheiro **bin/www** para **server.js**. Isto é para que o serviço de nuvem possa encontrar o ponto de entrada para esta aplicação.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Após a conclusão deste comando, deverá ter um ficheiro **server.js** no diretório WebRole1.
5. Modifique o **servidor.js** para remover um dos caracteres '.' da seguinte linha.
   
       var app = require('../app');
   
   Depois de efazer esta modificação, a linha deve aparecer da seguinte forma.
   
       var app = require('./app');
   
   Esta alteração é necessária desde que mudámos o ficheiro (anteriormente **bin/www,)** para o mesmo diretório que o ficheiro da aplicação sendo exigido. Depois de efazer esta alteração, guarde o ficheiro **server.js.**
6. Utilize o seguinte comando para executar a aplicação no emulador Azure:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Uma página web contendo bem-vindo sondar.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modificação da Vista
Agora modifique a vista para exibir a mensagem "Bem-vindo ao Expresso em Azure".

1. Introduza o seguinte comando para abrir o ficheiro index.jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![O conteúdo do ficheiro index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade é o motor de visualização padrão usado pelas aplicações Express. Para mais informações sobre o [http://jade-lang.com][http://jade-lang.com]motor de visão de Jade, consulte .
2. Modificar a última linha de texto através de despesas **em Azure**.
   
   ![O ficheiro index.jade, a última linha \#diz: p Bem-vindo a {title} em Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Guarde o ficheiro e saia O Bloco de Notas.
4. Atualize o seu navegador e verá as suas alterações.
   
   ![Uma janela de navegador, a página contém Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Depois de testar a aplicação, utilize o cmdlet **Stop-AzureEmulator** para parar o emulador.

## <a name="publishing-the-application-to-azure"></a>Publicação da Candidatura ao Azure
Na janela Azure PowerShell, utilize o cmdlet **Publish-AzureServiceProject** para implementar a aplicação num serviço na nuvem

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Assim que a operação de implementação estiver concluída, o seu navegador abrirá e exibirá a página web.

![Um navegador web que exibe a página Express. O URL indica que está agora hospedado no Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Passos seguintes
Para mais informações, consulte o [Centro de Programadores de Node.js](https://docs.microsoft.com/azure/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com






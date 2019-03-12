---
title: Criar e implementar uma aplicação node. js Express para serviços Cloud do Azure
description: Criar e implementar uma aplicação Express. js em node. js para serviços Cloud do Azure
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 24f8e7ef-e90d-4554-9b1e-a9b31d5824e5
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: b212eaffb977846d40270a5f2abc76192aee4c0d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550656"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Criar e implementar uma aplicação web de node. js com o Express num serviços Cloud do Azure

NODE. js inclui um conjunto mínimo de funcionalidade no tempo de execução de núcleo.
Os desenvolvedores normalmente usam 3º módulos de terceiros para fornecer funcionalidade adicional ao desenvolver uma aplicação node. js. Neste tutorial irá criar uma nova aplicação com o [Express](https://github.com/expressjs/express) módulo, que oferece uma estrutura MVC para criar aplicativos da web de node. js.

Uma captura de ecrã da aplicação concluída está ilustrado abaixo:

![Um browser a apresentar de boas-vindas do Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Criar um projeto de serviço em nuvem
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Execute os seguintes passos para criar um novo projeto de serviço de nuvem com o nome "expressapp":

1. Partir do **Menu Iniciar** ou **ecrã inicial**, procure **Windows PowerShell**. Por fim, com o botão direito **Windows PowerShell** e selecione **executar como administrador**.
   
    ![Ícone de PowerShell do Azure](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Altere os diretórios para o **c:\\nó** diretório e, em seguida, introduza os seguintes comandos para criar uma nova solução com o nome **expressapp** e uma função da web com o nome **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Por predefinição, **Add-AzureNodeWebRole** utiliza uma versão mais antiga do node. js. O **Set-AzureServiceProjectRole** acima da declaração instrui o Azure para utilizar v0.10.21 do nó.  Tenha em atenção de que os parâmetros diferenciam maiúsculas de minúsculas.  Pode verificar a versão correta do node. js foi selecionada, verificando a **mecanismos** propriedade na **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Instalar o Express
1. Instale o gerador do Express emitindo o comando seguinte:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    A saída do comando npm deve ter um aspeto semelhante ao resultado abaixo. 
   
    ![Comando de express de instalação do PowerShell de Windows exibir a saída do npm.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Altere os diretórios para o **WebRole1** diretório e utilize o comando express para gerar uma nova aplicação:
   
        PS C:\node\expressapp\WebRole1> express
   
    Será solicitado a substituir a sua aplicação anterior. Introduza **y** ou **Sim** para continuar. Express irá gerar o ficheiro App. js e uma estrutura de pastas para a criação de seu aplicativo.
   
    ![A saída do comando express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Para instalar dependências adicionais definidas no ficheiro Package. JSON, introduza o seguinte comando:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Comando de instalação de saída do npm](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Utilize o comando seguinte para copiar o **bin/www** do ficheiro para **Server. js**. Isso é para que o serviço em nuvem possa encontrar o ponto de entrada para esta aplicação.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Depois de concluída este comando, deve ter uma **Server. js** ficheiro no diretório WebRole1.
5. Modificar a **Server. js** para remover um do '.' carateres da linha a seguir.
   
       var app = require('../app');
   
   Depois de fazer essa modificação, a linha deve aparecer da seguinte forma.
   
       var app = require('./app');
   
   Esta alteração é necessária uma vez que passamos o ficheiro (anteriormente **bin/www**,) no mesmo diretório como o ficheiro de aplicação que está a ser necessário. Depois de fazer esta alteração, guarde a **Server. js** ficheiro.
6. Utilize o seguinte comando para executar a aplicação no emulador do Azure:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Uma página da web que contém bem-vindo ao express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modificar a vista
Agora, modifique a vista para apresentar a mensagem "Bem-vindo ao Express no Azure".

1. Introduza o seguinte comando para abrir o ficheiro Index jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![O conteúdo do ficheiro Index. jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade é o mecanismo de exibição padrão usado por aplicativos de Express. Para obter mais informações sobre o mecanismo de exibição Jade, consulte [ http://jade-lang.com ] [ http://jade-lang.com].
2. Modificar a última linha de texto, acrescentando **no Azure**.
   
   ![Lê a última linha do ficheiro Index jade,: p bem-vindo ao \#{title} no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Guarde o ficheiro e sair do bloco de notas.
4. Atualize o browser e verá as suas alterações.
   
   ![Uma janela do browser, a página contém bem-vindo ao Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Depois de testar a aplicação, utilize o **Stop-AzureEmulator** cmdlet para parar o emulador.

## <a name="publishing-the-application-to-azure"></a>Publicar a aplicação no Azure
Na janela do PowerShell do Azure, utilize o **Publish-AzureServiceProject** cmdlet para implementar a aplicação para um serviço em nuvem

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Depois de concluída a operação de implementação, o seu browser abrir e exibir a página da web.

![Um browser a apresentar a página de Express. O URL indica que agora está alojado no Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte o [Centro para Programadores do Node.js](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com



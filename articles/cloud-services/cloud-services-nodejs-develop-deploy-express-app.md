---
title: Criar e implantar um aplicativo do node. js Express nos serviços de nuvem do Azure
description: Criar e implantar um aplicativo Express. js no node. js nos serviços de nuvem do Azure
services: cloud-services
documentationcenter: nodejs
author: georgewallace
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: gwallace
ms.openlocfilehash: b3d8e364a982f85ad9df6e48dc4d28da1e8efb40
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359061"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Criar e implantar um aplicativo Web node. js usando o Express em um serviço de nuvem do Azure

O Node. js inclui um conjunto mínimo de funcionalidades no tempo de execução principal.
Os desenvolvedores geralmente usam módulos de terceiros para fornecer funcionalidade adicional ao desenvolver um aplicativo node. js. Neste tutorial, você criará um novo aplicativo usando o módulo [Express](https://github.com/expressjs/express) , que fornece uma estrutura MVC para criar aplicativos Web node. js.

Uma captura de tela do aplicativo concluído está abaixo:

![Um navegador da Web exibindo bem-vindo ao Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Criar um projeto de serviço de nuvem
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Execute as seguintes etapas para criar um novo projeto de serviço de nuvem chamado ' eXpressApp ':

1. No **menu iniciar** ou na **tela iniciar**, pesquise pelo **Windows PowerShell**. Por fim, clique com o botão direito do mouse em **Windows PowerShell** e selecione **Executar como administrador**.
   
    ![Ícone de Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Altere os diretórios para o diretório **c:\\node** e, em seguida, insira os seguintes comandos para criar uma nova solução chamada **eXpressApp** e uma função Web chamada **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Por padrão, o **Add-AzureNodeWebRole** usa uma versão mais antiga do node. js. A instrução **set-AzureServiceProjectRole** acima instrui o Azure a usar v 0.10.21 do nó.  Observe que os parâmetros diferenciam maiúsculas de minúsculas.  Você pode verificar se a versão correta do node. js foi selecionada verificando a  Propriedade Engines em **WebRole1\package.JSON**.
    > 
    > 

## <a name="install-express"></a>Instalar o Express
1. Instale o gerador expresso emitindo o seguinte comando:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    A saída do comando NPM deve ser semelhante ao resultado abaixo. 
   
    ![O Windows PowerShell exibe a saída do comando NPM install Express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Altere os diretórios para o diretório **WebRole1** e use o comando Express para gerar um novo aplicativo:
   
        PS C:\node\expressapp\WebRole1> express
   
    Você será solicitado a substituir o aplicativo anterior. Digite **y** ou **Sim** para continuar. O Express irá gerar o arquivo app. js e uma estrutura de pastas para criar seu aplicativo.
   
    ![A saída do comando expresso](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Para instalar dependências adicionais definidas no arquivo Package. JSON, digite o seguinte comando:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![A saída do comando de instalação NPM](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Use o comando a seguir para copiar o arquivo **bin/www** para **Server. js**. Isso é para que o serviço de nuvem possa encontrar o ponto de entrada para este aplicativo.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Depois que esse comando for concluído, você deverá ter um arquivo **Server. js** no diretório WebRole1
5. Modifique o **Server. js** para remover um dos caracteres '. ' da linha a seguir.
   
       var app = require('../app');
   
   Depois de fazer essa modificação, a linha deve aparecer da seguinte maneira.
   
       var app = require('./app');
   
   Essa alteração é necessária, já que movemos o arquivo (anteriormente **bin/www**) para o mesmo diretório que o arquivo de aplicativo que está sendo necessário. Depois de fazer essa alteração, salve o arquivo **Server. js** .
6. Use o seguinte comando para executar o aplicativo no emulador do Azure:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Uma página da Web que contém bem-vindo ao Express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modificando a exibição
Agora, modifique a exibição para exibir a mensagem "bem-vindo ao Express no Azure".

1. Digite o seguinte comando para abrir o arquivo index. jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![O conteúdo do arquivo index. Jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade é o mecanismo de exibição padrão usado por aplicativos Express. Para obter mais informações sobre o mecanismo de exibição do [http://jade-lang.com][http://jade-lang.com]Jade, consulte.
2. Modifique a última linha de texto acrescentando **no Azure**.
   
   ![O arquivo index. Jade, a última linha lê: p bem- \#vindo ao {title} no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Salve o arquivo e saia do bloco de notas.
4. Atualize seu navegador e você verá suas alterações.
   
   ![Uma janela do navegador, a página contém bem-vindo ao Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Depois de testar o aplicativo, use o cmdlet **Stop-AzureEmulator** para interromper o emulador.

## <a name="publishing-the-application-to-azure"></a>Publicando o aplicativo no Azure
Na janela Azure PowerShell, use o cmdlet **Publish-AzureServiceProject** para implantar o aplicativo em um serviço de nuvem

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Depois que a operação de implantação for concluída, o navegador será aberto e exibirá a página da Web.

![Um navegador da Web exibindo a página Express. A URL indica que agora ele está hospedado no Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte o [Centro para Programadores do Node.js](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com



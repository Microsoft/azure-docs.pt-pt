---
title: Aplicação de node. js com Socket.io - Azure
description: Saiba como utilizar o socket.io numa aplicação node. js alojada no Azure.
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7f9435e0-7732-4aa1-a4df-ea0e894b847f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: cd0bceae770182e778410d8065d34dfeed055acc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57993259"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Criar uma aplicação de chat node. js com Socket.IO num serviço Cloud do Azure

O Socket.IO disponibiliza comunicação em tempo real entre o servidor node. js e clientes. Este tutorial explica-lhe alojar um soquete. Aplicação de chat no Azure com base em e/s. Para obter mais informações sobre o Socket.IO, consulte [socket.io](https://socket.io).

Uma captura de ecrã da aplicação concluída está ilustrado abaixo:

![Uma janela do browser apresenta o serviço alojado no Azure][completed-app]  

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que os seguintes produtos e versões estão instaladas para concluir com êxito o exemplo neste artigo:

* Instalar o [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Instalar o [Node. js](https://nodejs.org/download/)
* Instalar [Python versão 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Criar um projeto de serviço em nuvem
Os passos seguintes criam o projeto de serviço em nuvem que irá alojar a aplicação de Socket.IO.

1. Partir do **Menu Iniciar** ou **ecrã inicial**, procure **Windows PowerShell**. Por fim, com o botão direito **Windows PowerShell** e selecione **executar como administrador**.
   
    ![Ícone de PowerShell do Azure][powershell-menu]
2. Crie um diretório denominado **c:\\nó**. 
   
        PS C:\> md node
3. Altere os diretórios para o **c:\\nó** diretório
   
        PS C:\> cd node
4. Introduza os seguintes comandos para criar uma nova solução com o nome **chatapp** e uma função de trabalho com o nome **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Verá a seguinte resposta:
   
    ![A saída do novo-azureservice e azurenodeworkerrolecmdlets adicionar](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Transferir o exemplo de bate-papo
Para este projeto, utilizamos o exemplo de bate-papo do [repositório do GitHub de Socket.IO]. Execute os seguintes passos para transferir o exemplo e adicioná-lo ao projeto que criou anteriormente.

1. Criar uma cópia local do repositório utilizando o **Clone** botão. Também pode utilizar o **ZIP** botão para transferir o projeto.
   
   ![Uma visualização da janela de browser https://github.com/LearnBoost/socket.io/tree/master/examples/chat, com o ícone de download do ZIP realçado](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Navegue até a estrutura de diretório do repositório local, até que atinja os **exemplos\\bate-papo** diretório. Copie o conteúdo deste diretório para o **c:\\nó\\chatapp\\WorkerRole1** diretório criado anteriormente.
   
   ![Explorer, exibindo o conteúdo de exemplos\\extraído do arquivo de diretório de bate-papo][chat-contents]
   
   Os itens realçados na captura de ecrã acima são os arquivos copiados a partir da **exemplos\\bate-papo** diretório
3. No **c:\\nó\\chatapp\\WorkerRole1** diretório, eliminar a **Server. js** de ficheiros e, em seguida, renomeie o **App. js** o ficheiro para **Server. js**. Esta ação remove a predefinição **Server. js** arquivo criado anteriormente pela **Add-AzureNodeWorkerRole** cmdlet e substitui-lo com o ficheiro de aplicação de exemplo de chat.

### <a name="modify-serverjs-and-install-modules"></a>Modificar Server. js e instalar os módulos
Antes de testar a aplicação no emulador do Azure, é preciso fazer algumas pequenas modificações. Execute os seguintes passos para o ficheiro de Server. js:

1. Abra o **Server. js** ficheiro no Visual Studio ou de qualquer editor de texto.
2. Encontrar o **dependências do módulo** secção no início do Server. js e altere a linha que contém **sio = require('.. //.. lib//socket.IO ")** para **sio = require('socket.io')** conforme mostrado abaixo:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Para garantir que a aplicação escuta na porta correta, abra o Server. js no bloco de notas ou o seu editor favorito e, em seguida, altere a linha seguinte, substituindo **3000** com **process.env.port** conforme mostrado abaixo:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Depois de guardar as alterações ao **Server. js**, utilize os seguintes passos para instalar os módulos necessários e, em seguida, testar o aplicativo no emulador do Azure:

1. Usando **do Azure PowerShell**, altere os diretórios para o **c:\\nó\\chatapp\\WorkerRole1** diretório e utilize o seguinte comando para instalar os módulos necessário para esta aplicação:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Esta ação irá instalar os módulos listados no arquivo Package. JSON. Depois do comando for concluído, deverá ver um resultado semelhante ao seguinte:
   
   ![Comando de instalação de saída do npm][The-output-of-the-npm-install-command]
2. Como este exemplo foi originalmente uma parte do repositório do GitHub de Socket.IO e referência direta a biblioteca de Socket.IO pelo caminho relativo, Socket.IO não foi referenciado no arquivo Package. JSON, pelo que podemos deve instalá-lo ao emitir o comando seguinte:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testar e implantar
1. Acione o emulador emitindo o comando seguinte:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Se tiver problemas em iniciar o emulador, por exemplo,.: Start-AzureEmulator: Ocorreu uma falha inesperada.  Detalhes: Foi encontrado um erro inesperado ao objeto de comunicação, System.ServiceModel.Channels.ServiceChannel, não pode ser utilizado para comunicação, porque está no Estado Faulted.
   > 
   > reinstalar AzureAuthoringTools v 2.7.1 e AzureComputeEmulator v 2.7 - Certifique-se de que a versão corresponde.

2. Abra um browser e navegue para **http://127.0.0.1**.
3. Quando abre a janela do browser, introduza uma alcunha e, em seguida, prima enter.
   Isso permitirá que publicar mensagens como uma alcunha específica. Para testar a funcionalidade de vários utilizador, abra janelas de navegador adicionais com o mesmo URL e introduza nicknames diferentes.
   
   ![Duas janelas de navegador exibindo mensagens de bate-papo de Usuário1 e usuário2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Após a aplicação de teste, pare o emulador emitindo o comando seguinte:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Para implementar a aplicação no Azure, utilize o **Publish-AzureServiceProject** cmdlet. Por exemplo:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Certifique-se de que utilize um nome exclusivo, caso contrário, o processo de publicação falhará. Depois de concluída a implementação, o navegador abra e navegue para o serviço implementado.
   > 
   > Se receber um erro a indicar que o nome de subscrição fornecido não existe no perfil de publicação importados, tem de transferir e importar o perfil de publicação para a sua subscrição antes de implementar para o Azure. Consulte a **implementar a aplicação no Azure** secção [compilar e implementar uma aplicação node. js num serviço Cloud do Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Uma janela do browser apresenta o serviço alojado no Azure][completed-app]
   
   > [!NOTE]
   > Se receber um erro a indicar que o nome de subscrição fornecido não existe no perfil de publicação importados, tem de transferir e importar o perfil de publicação para a sua subscrição antes de implementar para o Azure. Consulte a **implementar a aplicação no Azure** secção [compilar e implementar uma aplicação node. js num serviço Cloud do Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

A aplicação está agora em execução no Azure e pode retransmitir mensagens de bate-papo entre diferentes clientes a utilizar o Socket.IO.

> [!NOTE]
> Para simplificar, este exemplo está limitado a bate-papo entre os usuários conectados à mesma instância. Isso significa que se o serviço em nuvem cria duas instâncias de função de trabalho, os utilizadores apenas poderão conversar com outras pessoas ligado à mesma instância de função de trabalho. Para dimensionar a aplicação funcione com várias instâncias de função, poderia usar uma tecnologia como o Service Bus para partilhar o estado do arquivo Socket.IO entre instâncias. Para obter exemplos, veja os exemplos de utilização de tópicos e filas do Service Bus no [SDK do Azure para o repositório do GitHub de node. js](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu como criar uma aplicação de chat básica alojada num serviço Cloud do Azure. Para saber como alojar esta aplicação num Web site do Azure, veja [criar uma aplicação de Chat node. js com Socket.IO num Web Site no Azure][chatwebsite].

Para obter mais informações, consulte também os [Centro de programadores de node. js](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Repositório do GitHub de Socket.IO]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png



---
title: Aplicativo node. js usando Socket.io-Azure
description: Saiba como usar o socket.io em um aplicativo node. js hospedado no Azure.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 0b515c630d8a3539cdab1df64b1925e9fcaf206e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75360774"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Criar um aplicativo de chat do node. js com Socket.IO em um serviço de nuvem do Azure

O Socket.IO fornece comunicação em tempo real entre o servidor node. js e os clientes. Este tutorial orienta você pela hospedagem de um soquete. Aplicativo de chat baseado em e/s no Azure. Para obter mais informações sobre o Socket.IO, consulte [Socket.Io](https://socket.io).

Uma captura de tela do aplicativo concluído está abaixo:

![Uma janela do navegador exibindo o serviço hospedado no Azure][completed-app]  

## <a name="prerequisites"></a>Pré-requisitos
Verifique se os seguintes produtos e versões estão instalados para concluir com êxito o exemplo neste artigo:

* Instalar o [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Instalar o [Node. js](https://nodejs.org/download/)
* Instalar o [Python versão 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Criar um projeto de serviço de nuvem
As etapas a seguir criam o projeto de serviço de nuvem que hospedará o aplicativo Socket.IO.

1. No **menu iniciar** ou na **tela iniciar**, pesquise pelo **Windows PowerShell**. Por fim, clique com o botão direito do mouse em **Windows PowerShell** e selecione **Executar como administrador**.
   
    ![Ícone de Azure PowerShell][powershell-menu]
2. Crie um diretório chamado **nó c:\\** . 
   
        PS C:\> md node
3. Alterar os diretórios para o diretório do **nó c:\\**
   
        PS C:\> cd node
4. Insira os seguintes comandos para criar uma nova solução chamada **chatapp** e uma função de trabalho denominada **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Você verá a seguinte resposta:
   
    ![A saída de New-azureservice e Add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Baixar o exemplo de chat
Para este projeto, usaremos o exemplo de chat do [repositório GitHub do Socket.Io]. Execute as etapas a seguir para baixar o exemplo e adicioná-lo ao projeto que você criou anteriormente.

1. Crie uma cópia local do repositório usando o botão **clonar** . Você também pode usar o botão **zip** para baixar o projeto.
   
   ![Uma janela do navegador exibindo https://github.com/LearnBoost/socket.io/tree/master/examples/chat, com o ícone de download do ZIP realçado](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Navegue pela estrutura de diretório do repositório local até chegar aos **exemplos\\** diretório de chat. Copie o conteúdo desse diretório para o **nó C:\\\\chatapp\\diretório WorkerRole1** criado anteriormente.
   
   ![Explorer, exibindo o conteúdo dos exemplos\\diretório de chat extraído do arquivo morto][chat-contents]
   
   Os itens realçados na captura de tela acima são os arquivos copiados dos **exemplos\\** diretório de chat
3. No **nó C:\\\\chatapp\\diretório WorkerRole1** , exclua o arquivo **Server. js** e renomeie o arquivo **app. js** para **Server. js**. Isso remove o arquivo **Server. js** padrão criado anteriormente pelo cmdlet **Add-AzureNodeWorkerRole** e o substitui pelo arquivo de aplicativo do exemplo de chat.

### <a name="modify-serverjs-and-install-modules"></a>Modificar Server. js e instalar módulos
Antes de testar o aplicativo no emulador do Azure, devemos fazer algumas pequenas modificações. Execute as seguintes etapas para o arquivo Server. js:

1. Abra o arquivo **Server. js** no Visual Studio ou em qualquer editor de texto.
2. Localize a seção de **dependências do módulo** no início do Server. js e altere a linha que contém **sio = require ('.. //.. lib//Socket. Io ')** para **sio = require (' Socket. Io ')** , conforme mostrado abaixo:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Para garantir que o aplicativo escute na porta correta, abra Server. js no bloco de notas ou seu editor favorito e, em seguida, altere a linha a seguir, substituindo **3000** por **Process. env. Port** , conforme mostrado abaixo:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Depois de salvar as alterações no **Server. js**, use as seguintes etapas para instalar os módulos necessários e, em seguida, teste o aplicativo no emulador do Azure:

1. Usando **Azure PowerShell**, altere os diretórios para o **nó C:\\\\Chatapp\\diretório WorkerRole1** e use o seguinte comando para instalar os módulos exigidos por este aplicativo:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Isso instalará os módulos listados no arquivo Package. JSON. Após o comando ser concluído, deverá ver um resultado semelhante ao seguinte:
   
   ![A saída do comando de instalação NPM][The-output-of-the-npm-install-command]
2. Como esse exemplo era originalmente uma parte do repositório GitHub Socket.IO e referenciou diretamente a biblioteca Socket.IO por caminho relativo, o Socket.IO não foi referenciado no arquivo Package. JSON, portanto, devemos instalá-lo emitindo o seguinte comando:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Teste e implantação
1. Inicie o emulador emitindo o seguinte comando:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Se você encontrar problemas ao iniciar o emulador, por exemplo: Start-AzureEmulator: ocorreu uma falha inesperada.  Detalhes: encontrado um erro inesperado o objeto de comunicação, System. ServiceModel. Channels. antichannel, não pode ser usado para comunicação porque está no estado com falha.
   > 
   > Reinstale o AzureAuthoringTools v 2.7.1 e o AzureComputeEmulator v 2,7-Verifique se a versão corresponde.

2. Abra um navegador e navegue até **http://127.0.0.1** .
3. Quando a janela do navegador for aberta, insira um apelido e pressione Enter.
   Isso permitirá que você poste mensagens como um apelido específico. Para testar a funcionalidade de vários usuários, abra janelas de navegador adicionais usando a mesma URL e insira apelidos diferentes.
   
   ![Duas janelas de navegador exibindo mensagens de chat do Usuário1 e do Usuário2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Depois de testar o aplicativo, interrompa o emulador emitindo o seguinte comando:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Para implantar o aplicativo no Azure, use o cmdlet **Publish-AzureServiceProject** . Por exemplo:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Certifique-se de usar um nome exclusivo, caso contrário, o processo de publicação falhará. Após a conclusão da implantação, o navegador será aberto e navegará até o serviço implantado.
   > 
   > Se você receber um erro informando que o nome de assinatura fornecido não existe no perfil de publicação importado, você deverá baixar e importar o perfil de publicação para sua assinatura antes de implantar no Azure. Consulte a seção **implantando o aplicativo no Azure** de [Compilar e implantar um aplicativo node. js em um serviço de nuvem do Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Uma janela do navegador exibindo o serviço hospedado no Azure][completed-app]
   
   > [!NOTE]
   > Se você receber um erro informando que o nome de assinatura fornecido não existe no perfil de publicação importado, você deverá baixar e importar o perfil de publicação para sua assinatura antes de implantar no Azure. Consulte a seção **implantando o aplicativo no Azure** de [Compilar e implantar um aplicativo node. js em um serviço de nuvem do Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Seu aplicativo agora está em execução no Azure e pode retransmitir mensagens de chat entre diferentes clientes usando o Socket.IO.

> [!NOTE]
> Para simplificar, este exemplo é limitado ao bate-papo entre os usuários conectados à mesma instância. Isso significa que, se o serviço de nuvem Criar duas instâncias de função de trabalho, os usuários poderão apenas bater papo com outras pessoas conectadas à mesma instância de função de trabalho. Para dimensionar o aplicativo para trabalhar com várias instâncias de função, você pode usar uma tecnologia como o barramento de serviço para compartilhar o estado da Socket.IO Store entre instâncias. Para obter exemplos, consulte os exemplos de uso de tópicos e filas do barramento de serviço no [SDK do Azure para o repositório GitHub do node. js](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você aprendeu a criar um aplicativo de chat básico hospedado em um serviço de nuvem do Azure. Para saber como hospedar esse aplicativo em um site do Azure, consulte [criar um aplicativo de chat do node. js com o Socket.Io em um site do Azure][chatwebsite].

Para obter mais informações, consulte também o [centro de desenvolvedores do node. js](https://docs.microsoft.com/azure/javascript/).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Repositório GitHub do Socket.IO]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png






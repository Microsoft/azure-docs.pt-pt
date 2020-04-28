---
title: Aplicação Node.js usando Socket.io - Azure
description: Aprenda a usar socket.io numa aplicação nó.js alojada no Azure.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 0b515c630d8a3539cdab1df64b1925e9fcaf206e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75360774"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Construa uma aplicação de chat Node.js com Socket.IO num Serviço Azure Cloud

Socket.IO fornece comunicação em tempo real entre o seu servidor nó.js e clientes. Este tutorial leva-o através de uma tomada. Aplicação de chat baseada em IO no Azure. Para mais informações sobre Socket.IO, consulte [socket.io](https://socket.io).

Uma imagem da aplicação concluída é a seguir:

![Uma janela de navegador que exibe o serviço hospedado no Azure][completed-app]  

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que os seguintes produtos e versões estão instalados para completar com sucesso o exemplo neste artigo:

* Instalar [Estúdio Visual](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Instalar o [Node. js](https://nodejs.org/download/)
* Instalar [a versão Python 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Criar um Projeto de Serviço na Nuvem
Os seguintes passos criam o projeto de serviço na nuvem que acolherá a aplicação Socket.IO.

1. A partir do **Menu Iniciar** ou Ecrã **de Início,** procure o **Windows PowerShell**. Finalmente, clique no **Windows PowerShell** e selecione **Executar Como Administrador**.
   
    ![Ícone Azure PowerShell][powershell-menu]
2. Crie um diretório chamado **c:\\nó**. 
   
        PS C:\> md node
3. Mudar diretórios para o **c:\\** diretório de nó
   
        PS C:\> cd node
4. Insira os seguintes comandos para criar uma nova solução chamada **chatapp** e um papel de trabalhador chamado **WorkerRole1**:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Verá a seguinte resposta:
   
    ![A saída do serviço de novos azureservices e add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Descarregue o Exemplo do Chat
Para este projeto, usaremos o exemplo de chat do [repositório Socket.IO GitHub.] Execute os seguintes passos para descarregar o exemplo e adicioná-lo ao projeto que criou anteriormente.

1. Crie uma cópia local do repositório utilizando o botão **Clone.** Também pode utilizar o botão **ZIP** para descarregar o projeto.
   
   ![Uma visualização https://github.com/LearnBoost/socket.io/tree/master/examples/chatda janela do navegador, com o ícone de descarregamento ZIP em destaque](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Navegue na estrutura diretório do repositório local até chegar ao diretório de **exemplos\\** de chat. Copie o conteúdo deste diretório para o diretório **\\C: diretório de\\chatapp\\de nós WorkerRole1** criado anteriormente.
   
   ![Explorer, exibindo o conteúdo\\dos exemplos de diretório de chat extraídos do arquivo][chat-contents]
   
   Os itens destacados na imagem acima são os ficheiros copiados do diretório de **chat exemplos\\**
3. No **c:\\\\diretório\\do chatapp do nó WorkerRole1,** elimine o ficheiro **server.js** e, em seguida, mude o nome do ficheiro **app.js** para **server.js**. Isto remove o ficheiro predefinido **do servidor.js** criado anteriormente pelo **Add-AzureNodeWorkerRole** cmdlet e substitui-o pelo ficheiro de aplicação do exemplo do chat.

### <a name="modify-serverjs-and-install-modules"></a>Modificar server.js e instalar módulos
Antes de testar a aplicação no emulador Azure, temos de fazer algumas pequenas modificações. Execute os seguintes passos no ficheiro server.js:

1. Abra o ficheiro **server.js** no Visual Studio ou em qualquer editor de texto.
2. Encontre a secção de **dependências** do Módulo no início do server.js e altere a linha que contém **sio = exija('.. //.. lib//socket.io')** para **sio = exigir ('socket.io')** como mostrado abaixo:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Para garantir que a aplicação ouve a porta correta, abra o servidor.js no Notepad ou no seu editor favorito e, em seguida, altere a seguinte linha substituindo **3000** por **process.env.port,** conforme mostrado abaixo:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Depois de guardar as alterações para **server.js,** utilize os seguintes passos para instalar os módulos necessários e, em seguida, teste a aplicação no emulador Azure:

1. Utilizando o **Azure PowerShell**, altere os diretórios para o diretório **\\C:\\diretório de chatapp\\de nós WorkerRole1** e utilize o seguinte comando para instalar os módulos exigidos por esta aplicação:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Isto irá instalar os módulos listados no ficheiro package.json. Após o comando ser concluído, deverá ver um resultado semelhante ao seguinte:
   
   ![A saída do comando de instalação npm][The-output-of-the-npm-install-command]
2. Uma vez que este exemplo fazia originalmente parte do repositório Socket.IO GitHub, e referenciado diretamente a biblioteca Socket.IO por caminho relativo, Socket.IO não foi referenciado no ficheiro package.json, pelo que devemos instalá-lo emitindo o seguinte comando:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Teste e Implantação
1. Lançar o emulador emitindo o seguinte comando:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Se encontrar problemas com o emulador de lançamento, por exemplo: Start-AzureEmulator : Ocorreu uma falha inesperada.  Detalhes: Encontrou um erro inesperado O objeto de comunicação, System.ServiceModel.Channels.ServiceChannel, não pode ser utilizado para comunicação porque se encontra no estado defeituoso.
   > 
   > Reinstalar as Ferramentas de AutoreTa v 2.7.1 e AzureComputeEmulator v 2.7 - certifique-se de que a versão corresponde.

2. Abra um navegador **http://127.0.0.1**e navegue para .
3. Quando a janela do navegador abrir, introduza uma alcunha e, em seguida, entre.
   Isto permitir-lhe-á publicar mensagens como um apelido específico. Para testar a funcionalidade multiutilizador, abra janelas adicionais de navegador utilizando o mesmo URL e introduza diferentes apelidos.
   
   ![Duas janelas do navegador que exibem mensagens de chat do User1 e do User2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Depois de testar a aplicação, pare o emulador emitindo o seguinte comando:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Para implementar a aplicação para o Azure, utilize o cmdlet **Publish-AzureServiceProject.** Por exemplo:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Certifique-se de que utiliza um nome único, caso contrário o processo de publicação falhará. Após a implementação concluída, o navegador será aberto e navegará para o serviço implementado.
   > 
   > Se receber um erro indicando que o nome de subscrição fornecido não existe no perfil de publicação importado, deve transferir e importar o perfil de publicação para a sua subscrição antes de ser lançado para o Azure. Consulte a **implementação da secção de Implementação de** Construção e [implemente uma aplicação Node.js para um Serviço Azure Cloud](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Uma janela de navegador que exibe o serviço hospedado no Azure][completed-app]
   
   > [!NOTE]
   > Se receber um erro indicando que o nome de subscrição fornecido não existe no perfil de publicação importado, deve transferir e importar o perfil de publicação para a sua subscrição antes de ser lançado para o Azure. Consulte a **implementação da secção de Implementação de** Construção e [implemente uma aplicação Node.js para um Serviço Azure Cloud](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

A sua aplicação está agora a ser recorrida no Azure, e pode transmitir mensagens de chat entre diferentes clientes usando Socket.IO.

> [!NOTE]
> Para a simplicidade, esta amostra limita-se a conversas entre utilizadores ligados à mesma instância. Isto significa que se o serviço na nuvem criar duas instâncias de função de trabalhador, os utilizadores só poderão conversar com outros ligados à mesma instância de função do trabalhador. Para escalar a aplicação para trabalhar com múltiplas instâncias de papel, você poderia usar uma tecnologia como Service Bus para compartilhar o estado de loja Socket.IO em instâncias. Por exemplo, consulte as filas de ônibus de serviço e as amostras de utilização de tópicos no [SDK Azure para o repositório Node.js GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial aprendeu a criar uma aplicação básica de chat hospedada num Serviço Azure Cloud. Para aprender a hospedar esta aplicação num Site Azure, consulte [Build a Node.js Chat Application com Socket.IO num Web Site Azure][chatwebsite].

Para mais informações, consulte também o Centro de Desenvolvimento do [Nó.js.](https://docs.microsoft.com/azure/javascript/)

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[repositório Socket.IO GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png






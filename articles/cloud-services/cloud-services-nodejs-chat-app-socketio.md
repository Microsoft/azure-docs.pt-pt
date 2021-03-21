---
title: Node.js aplicação utilizando Socket.io - Azure
description: Utilize este tutorial para aprender a hospedar uma tomada. Aplicação de chat baseada em IO no Azure. Socket.IO fornece comunicação em tempo real para um servidor e clientes node.js.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: abc02769d7d978e14975d90ae0f98547bdc4faf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743326"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service-classic"></a>Construa uma aplicação de chat Node.js com Socket.IO num Azure Cloud Service (clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Socket.IO fornece comunicação em tempo real entre o seu servidor node.js e os clientes. Este tutorial leva-o a hospedar uma tomada. IO aplicação de chat baseada em Azure. Para mais informações sobre Socket.IO, consulte [socket.io](https://socket.io).

Uma imagem da aplicação completa é a seguinte:

![Uma janela de navegador exibindo o serviço hospedado no Azure][completed-app]

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que os seguintes produtos e versões são instalados para completar com sucesso o exemplo neste artigo:

* Instalar [Estúdio Visual](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Instalar [Node.js](https://nodejs.org/download/)
* Instalar [a versão Python 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Criar um projeto de serviço em nuvem
Os passos seguintes criam o projeto de serviço em nuvem que irá acolher a aplicação Socket.IO.

1. A partir do **Menu Iniciar** ou do Ecrã **De Início,** procure por **Windows PowerShell**. Por fim, clique com o botão direito **do Windows PowerShell** e selecione **Executar Como Administrador**.

    ![Ícone Azure PowerShell][powershell-menu]
2. Criar um diretório chamado **c: \\ nó**.

    ```powershell
    PS C:\> md node
    ```

3. Mudar diretórios para **c: \\ diretório** de nó

    ```powershell
    PS C:\> cd node
    ```

4. Insira os seguintes comandos para criar uma nova solução nomeada `chatapp` e uma função de trabalhador chamada `WorkerRole1` :

    ```powershell
    PS C:\node> New-AzureServiceProject chatapp
    PS C:\Node> Add-AzureNodeWorkerRole
    ```

    Verá a seguinte resposta:

    ![A saída do novo serviço de azure e do add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Baixe o Exemplo do Chat
Para este projeto, usaremos o exemplo de chat do [repositório Socket.IO GitHub.] Execute os seguintes passos para descarregar o exemplo e adicioná-lo ao projeto que criou anteriormente.

1. Crie uma cópia local do repositório utilizando o botão **Clone.** Também pode utilizar o botão **ZIP** para descarregar o projeto.

   ![Uma visualização da janela https://github.com/LearnBoost/socket.io/tree/master/examples/chat do navegador, com o ícone de descarregamento ZIP em destaque](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Navegue pela estrutura do diretório do repositório local até chegar aos exemplos do diretório de **\\ chat.** Copie o conteúdo deste diretório para o **C: \\ nó \\ chatapp \\ WorkerRole1** diretório criado anteriormente.

   ![Explorador, exibindo o conteúdo dos exemplos \\ de diretório de chat extraído do arquivo][chat-contents]

   Os itens destacados na imagem acima são os ficheiros copiados do diretório de **\\ chat exemplos**
3. No **C: \\ \\ node chatapp \\ WorkerRole1,** apagar o ficheiro **server.js** e, em seguida, mudar o nome do ficheiro **app.js** para **server.js**. Isto remove o ficheiro **deserver.jspredefinido** criado anteriormente pelo **cmdlet Add-AzureNodeWorkerRole** e substitui-o pelo ficheiro de aplicação do exemplo de chat.

### <a name="modify-serverjs-and-install-modules"></a>Modificar Server.js e instalar módulos
Antes de testar a aplicação no emulador Azure, temos de fazer algumas pequenas modificações. Execute os seguintes passos no ficheiro server.js:

1. Abra o ficheiro **server.js** no Visual Studio ou em qualquer editor de texto.
2. Encontre a secção **de dependências** do Módulo no início da server.js e altere a linha que contém **sio = requere('. //.. lib//socket.io')** para **sio = exigir ('socket.io')** como mostrado abaixo:

    ```js
    var express = require('express')
      , stylus = require('stylus')
      , nib = require('nib')
    //, sio = require('..//..//lib//socket.io'); //Original
      , sio = require('socket.io');                //Updated
      var port = process.env.PORT || 3000;         //Updated
    ```

3. Para garantir que a aplicação ouve na porta correta, abra server.js no Bloco de Notas ou no seu editor favorito e, em seguida, altere a seguinte linha substituindo **3000** por **process.env.port** como mostrado abaixo:

    ```js
    //app.listen(3000, function () {            //Original
    app.listen(process.env.port, function () {  //Updated
      var addr = app.address();
      console.log('   app listening on http://' + addr.address + ':' + addr.port);
    });
    ```

Depois de guardar as alterações para **server.js,** utilize os seguintes passos para instalar os módulos necessários e, em seguida, teste a aplicação no emulador Azure:

1. Utilizando **o Azure PowerShell,** altere os diretórios para o **C: \\ \\ node chatapp \\ WorkerRole1** e utilize o seguinte comando para instalar os módulos exigidos por esta aplicação:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> npm install
    ```

   Isto instalará os módulos listados no package.jsem ficheiro. Após o comando ser concluído, deverá ver um resultado semelhante ao seguinte:

   ![A saída do comando de instalação npm][The-output-of-the-npm-install-command]
2. Uma vez que este exemplo era originalmente uma parte do repositório Socket.IO GitHub, e referenciado diretamente a biblioteca Socket.IO por caminho relativo, Socket.IO não foi referenciado no package.jsem arquivo, por isso devemos instalá-lo emitindo o seguinte comando:

   ```powershell
   PS C:\node\chatapp\WorkerRole1> npm install socket.io --save
    ```

### <a name="test-and-deploy"></a>Testar e implementar
1. Lançar o emulador emitindo o seguinte comando:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
    ```

   > [!NOTE]
   > Se encontrar problemas com o emulador de lançamento, por exemplo: Start-AzureEmulator: Ocorreu uma falha inesperada.  Detalhes: Encontrou um erro inesperado O objeto de comunicação, System.ServiceModel.Channels.ServiceChannel, não pode ser utilizado para comunicação por se encontrar no estado de Falha.
   >
   > Reinstalar AzureAuthoringToóis v 2.7.1 e AzureComputeEmulator v 2.7 - certifique-se de que a versão corresponde.

2. Abra um navegador e navegue para **http://127.0.0.1** .
3. Quando a janela do navegador abrir, introduza um apelido e, em seguida, acerte entrar.
   Isto permitir-lhe-á publicar mensagens como um apelido específico. Para testar a funcionalidade multiutilizador, abra janelas adicionais do navegador usando o mesmo URL e introduza diferentes apelidos.

   ![Dois windows de navegador exibindo mensagens de chat do Utilizador1 e Utilizador2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Depois de testar a aplicação, pare o emulador emitindo o seguinte comando:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
    ```

5. Para implementar a aplicação no Azure, utilize o **cmdlet Publish-AzureServiceProject.** Por exemplo:

    ```powershell
    PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
    ```

   > [!IMPORTANT]
   > Certifique-se de que usa um nome único, caso contrário o processo de publicação falhará. Após a implementação concluída, o navegador abrirá e navegará para o serviço implantado.
   >
   > Se receber um erro indicando que o nome de subscrição fornecido não existe no perfil de publicação importado, deve descarregar e importar o perfil de publicação para a sua subscrição antes de ser implantado no Azure. Consulte **a secção de Implementação da Implementação para Azure** de [Construir e implemente uma aplicação Node.js para um Serviço de Nuvem Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

   ![Uma janela de navegador exibindo o serviço hospedado no Azure][completed-app]

   > [!NOTE]
   > Se receber um erro indicando que o nome de subscrição fornecido não existe no perfil de publicação importado, deve descarregar e importar o perfil de publicação para a sua subscrição antes de ser implantado no Azure. Consulte **a secção de Implementação da Implementação para Azure** de [Construir e implemente uma aplicação Node.js para um Serviço de Nuvem Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   >
   >

A sua aplicação está agora em execução no Azure, e pode transmitir mensagens de chat entre diferentes clientes usando Socket.IO.

> [!NOTE]
> Para simplificar, esta amostra limita-se a conversar entre utilizadores ligados à mesma instância. Isto significa que, se o serviço de nuvem criar duas instâncias de função dos trabalhadores, os utilizadores só poderão conversar com outros ligados à mesma instância de função dos trabalhadores. Para escalar a aplicação para trabalhar com múltiplas instâncias de papel, você poderia usar uma tecnologia como a Service Bus para compartilhar o estado de loja Socket.IO em todos os casos. Por exemplo, consulte as amostras de utilização de buss de serviço e tópicos no [Azure SDK para Node.js repositório GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).
>
>

## <a name="next-steps"></a>Passos seguintes
Neste tutorial aprendeu a criar uma aplicação básica de chat hospedada num Azure Cloud Service. Para aprender a hospedar esta aplicação num Website Azure, consulte [a Build a Node.js Chat Application com Socket.IO num Web Site Azure][chatwebsite].

Para mais informações, consulte também o [Node.js Developer Center](/azure/developer/javascript/).

[chatwebsite]: ./cloud-services-nodejs-develop-deploy-app.md

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO repositório gitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
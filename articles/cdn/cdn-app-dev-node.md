---
title: Começar com o Azure CDN SDK para Node.js [ Microsoft Docs
description: Saiba como escrever aplicações Node.js para gerir o Azure CDN.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 18dbcbf93947306334ccc2c156d9266884198e19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67594139"
---
# <a name="get-started-with-azure-cdn-development"></a>Introdução à programação do CDN do Azure
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Pode utilizar o [Azure CDN SDK para Node.js](https://www.npmjs.com/package/azure-arm-cdn) para automatizar a criação e gestão de perfis e pontos finais da CDN.  Este tutorial atravessa a criação de uma simples aplicação de consola Node.js que demonstra várias das operações disponíveis.  Este tutorial não se destina a descrever todos os aspetos do Azure CDN SDK para Node.js em detalhe.

Para completar este tutorial, já deve ter [node.js](https://www.nodejs.org) **4.x.x** ou superior instalado e configurado.  Pode utilizar qualquer editor de texto que queira criar a sua aplicação Node.js.  Para escrever este tutorial, usei o [Código do Estúdio Visual.](https://code.visualstudio.com)  

> [!TIP]
> O [projeto concluído deste tutorial](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) está disponível para download na MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Crie o seu projeto e adicione dependências de NPM
Agora que criámos um grupo de recursos para os nossos perfis de CDN e damos permissão à nossa aplicação Azure AD para gerir perfis de CDN e pontos finais dentro desse grupo, podemos começar a criar a nossa aplicação.

Crie uma pasta para armazenar a sua aplicação.  A partir de uma consola com as ferramentas Node.js no seu percurso atual, detete a sua localização atual para esta nova pasta e inicialize o seu projeto executando:

    npm init

Em seguida, ser-lhe-ão apresentadas uma série de perguntas para inicializar o seu projeto.  Para **o ponto de entrada,** este tutorial utiliza *app.js*.  Pode ver as minhas outras escolhas no seguinte exemplo.

![NPM init output](./media/cdn-app-dev-node/cdn-npm-init.png)

O nosso projeto está agora inicializado com um ficheiro *packages.json.*  O nosso projeto vai utilizar algumas bibliotecas Azure contidas em pacotes NPM.  Usaremos o Tempo de Execução do Cliente Azure para Node.js (ms-rest-azure) e a Biblioteca de Clientes Azure CDN para Node.js (azure-arm-cd).  Vamos adicioná-los ao projeto como dependências.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Após a instalação dos pacotes, o ficheiro *package.json* deve parecer semelhante a este exemplo (os números da versão podem diferir):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Finalmente, utilizando o seu editor de texto, crie um ficheiro de texto em branco e guarde-o na raiz da nossa pasta de projeto como *app.js*.  Estamos prontos para começar a escrever código.

## <a name="requires-constants-authentication-and-structure"></a>Requer, constantes, autenticação e estrutura
Com *o app.js* aberto no nosso editor, vamos escrever a estrutura básica do nosso programa.

1. Adicione os "requisitos" para os nossos pacotes NPM no topo com o seguinte:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Temos de definir algumas constantes que os nossos métodos irão utilizar.  Adicione o seguinte.  Certifique-se de que substitui os espaços reservados, incluindo os ** &lt;suportes&gt;angulares,** com os seus próprios valores, conforme necessário.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Em seguida, vamos instantaneamente o cliente de gestão da CDN e dar-lhe as nossas credenciais.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Se estiver a utilizar a autenticação individual do utilizador, estas duas linhas serão ligeiramente diferentes.
   
   > [!IMPORTANT]
   > Utilize apenas esta amostra de código se optar por ter a autenticação individual do utilizador em vez de um diretor de serviço.  Tenha cuidado para guardar as suas credenciais individuais de utilizador e mantê-las em segredo.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Certifique-se de que substitui os itens em ** &lt;suportes angulares&gt; ** com a informação correta.  Para `<redirect URI>`utilizar o URI redirecionado que inseriu quando registou o pedido em Azure AD.
4. A nossa aplicação de consola Node.js vai ter alguns parâmetros de linha de comando.  Vamos validar que pelo menos um parâmetro foi passado.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Isso leva-nos à parte principal do nosso programa, onde nos ramificamos para outras funções com base nos parâmetros que foram passados.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. Em vários locais do nosso programa, precisamos de ter a certeza que o número certo de parâmetros foi passado e mostrar alguma ajuda se não estiverem corretos.  Vamos criar funções para fazer isso.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Finalmente, as funções que vamos usar no cliente de gestão da CDN são assíncronas, por isso precisam de um método para ligar de volta quando terminarem.  Vamos fazer um que possa exibir a saída do cliente de gestão da CDN (se houver) e sair do programa graciosamente.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Agora que a estrutura básica do nosso programa está escrita, devemos criar as funções chamadas com base nos nossos parâmetros.

## <a name="list-cdn-profiles-and-endpoints"></a>Listar perfis e pontos finais da CDN
Vamos começar com o código para listar os nossos perfis e pontos finais existentes.  Os meus comentários de código fornecem a sintaxe esperada para sabermos para onde vai cada parâmetro.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Criar perfis e pontos finais da CDN
Em seguida, escreveremos as funções para criar perfis e pontos finais.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Purgue um ponto final
Assumindo que o ponto final foi criado, uma tarefa comum que podemos querer realizar no nosso programa é purgar conteúdo no nosso ponto final.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Eliminar perfis e pontos finais da CDN
A última função que incluiremos elimina pontos finais e perfis.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Executar o programa
Agora podemos executar o nosso programa Node.js usando o nosso debugger favorito ou na consola.

> [!TIP]
> Se estáa a usar o Código do Estúdio Visual como seu desordeiro, terá de configurar o seu ambiente para passar nos parâmetros da linha de comando.  O Visual Studio Code faz isto no ficheiro **launch.json.**  Procure uma propriedade chamada **args** e adicione uma variedade de valores de cordas `"args": ["list", "profiles"]`para os seus parâmetros, de modo que se pareça com este: .
> 
> 

Vamos começar por listar os nossos perfis.

![Perfis de lista](./media/cdn-app-dev-node/cdn-list-profiles.png)

Temos de volta uma matriz vazia.  Como não temos perfis no nosso grupo de recursos, isso é esperado.  Vamos criar um perfil agora.

![Criar perfil](./media/cdn-app-dev-node/cdn-create-profile.png)

Agora, vamos adicionar um ponto final.

![Criar ponto final](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Finalmente, vamos apagar o nosso perfil.

![Eliminar perfil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Passos Seguintes
Para ver o projeto concluído a partir desta passagem, [descarregue a amostra](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Para ver a referência para o Azure CDN SDK para Node.js, consulte a [referência](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Para encontrar documentação adicional sobre o SDK Azure para Node.js, veja a [referência completa](https://azure.github.io/azure-sdk-for-node/).

Gerencie os seus recursos CDN com [a PowerShell](cdn-manage-powershell.md).


---
title: Introdução ao SDK da CDN do Azure para node. js | Documentos da Microsoft
description: Saiba como escrever aplicações node. js para gerir a CDN do Azure.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 48a84520a61d19968b467091871459e21898dd5e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548648"
---
# <a name="get-started-with-azure-cdn-development"></a>Introdução à programação do CDN do Azure
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Pode utilizar o [SDK do CDN do Azure para node. js](https://www.npmjs.com/package/azure-arm-cdn) para automatizar a criação e gestão de perfis da CDN e os pontos finais.  Este tutorial explica a criação de uma aplicação de consola node. js simple que demonstra vários das operações disponíveis.  Este tutorial não se destina para descrever todos os aspetos do SDK da CDN do Azure para node. js em detalhes.

Para concluir este tutorial, já deverá ter [node. js](https://www.nodejs.org) **4.x.x** ou superior instalado e configurado.  Pode utilizar qualquer editor de texto que pretende criar a sua aplicação node. js.  Para escrever neste tutorial, usei [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> O [projeto concluído este tutorial](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) está disponível para download no MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Criar o seu projeto e adicione as dependências NPM
Agora que já criou um grupo de recursos para nosso perfis CDN e nosso permissão de aplicação do Azure AD para gerir perfis de CDN e os pontos finais dentro desse grupo, podemos começar a criação de nosso aplicativo.

Crie uma pasta para armazenar seu aplicativo.  A partir de um console com as ferramentas do node. js no seu caminho atual, defina a sua localização atual para esta nova pasta e inicializar o seu projeto, executando:

    npm init

Em seguida, será apresentada uma série de perguntas para inicializar o seu projeto.  Para **ponto de entrada**, este tutorial utiliza *js*.  Pode ver minhas outras opções no exemplo a seguir.

![Saída de init do NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Nosso projeto agora é inicializado com um *packages.json* ficheiro.  Nosso projeto vai usar algumas bibliotecas do Azure contidas nos pacotes NPM.  Vamos utilizar o tempo de execução do cliente do Azure para node. js (ms-rest-azure) e a biblioteca de cliente de CDN do Azure para node. js (azure-arm-cd).  Vamos adicioná-los ao projeto como dependências.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Depois de terminar os pacotes de instalação, o *Package. JSON* ficheiro deve ter um aspeto semelhante a este exemplo (versão números podem ser diferentes):

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

Por fim, com o seu editor de texto, crie um ficheiro de texto em branco e guardá-lo na raiz da nossa pasta de projeto que *App. js*.  Agora estamos prontos para começar a escrever código.

## <a name="requires-constants-authentication-and-structure"></a>Requer, constantes, autenticação e estrutura
Com o *App. js* abrir no nosso editor, vamos obter a estrutura básica do programa escrito.

1. Adicione o "requer" para nossos pacotes NPM na parte superior com o seguinte:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Precisamos definir algumas constantes, que nossos métodos irão utilizar.  Adicione o seguinte.  Certifique-se de que substitua os marcadores de posição, incluindo o  **&lt;colchetes angulares&gt;**, com seus próprios valores, conforme necessário.
   
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
3. Em seguida, vamos criar uma instância do cliente de gestão de CDN e dê a ele nosso credenciais.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Se estiver a utilizar a autenticação de utilizador individuais, estas duas linhas terá uma aparência um pouco diferentes.
   
   > [!IMPORTANT]
   > Só utilize este exemplo de código se de que está optando por ter a autenticação de utilizador individuais em vez de um principal de serviço.  Tenha cuidado para proteger as suas credenciais de utilizador individuais e mantê-los secreta.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Não se esqueça de substituir os itens na **&lt;colchetes angulares&gt;** com as informações corretas.  Para `<redirect URI>`, utilize o URI que introduziu quando registou a aplicação no Azure AD de redirecionamento.
4. Nossa aplicação de consola node. js vai levar alguns parâmetros da linha de comandos.  Vamos confirmar que pelo menos um parâmetro foi transmitido.
   
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
5. Isso nos traz para a parte principal do programa, onde podemos desmembrá-la para outras funções com base nos quais os parâmetros foram transmitidos.
   
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
6. Em vários locais no nosso programa, precisamos de certificar-se de que o número certo de parâmetros foram transmitido e exibir alguma ajuda se não parecem corretas.  Vamos criar funções para fazer isso.
   
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
7. Por fim, as funções que usaremos no cliente de gestão da CDN são assíncronas, pelo que precisam de um método a ser chamado quando elas são feitas.  Vamos fazer um que pode exibir a saída do cliente de gestão CDN (se aplicável) e sair do programa com elegância.
   
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

Agora que a estrutura básica do programa é escrita, podemos deve criar as funções de chamada com base em nossos parâmetros.

## <a name="list-cdn-profiles-and-endpoints"></a>Perfis de CDN da lista e os pontos finais
Vamos começar com o código para listar os nossos perfis existentes e pontos de extremidade.  Meus comentários de código fornecem a sintaxe esperada para sabermos onde vai a cada parâmetro.

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

## <a name="create-cdn-profiles-and-endpoints"></a>Criar perfis de CDN e os pontos finais
Em seguida, vamos escrever as funções para criar perfis e pontos finais.

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

## <a name="purge-an-endpoint"></a>Remover um ponto final
Partindo do princípio de que o ponto final ter sido criado, uma tarefa comum que quisermos para executar no nosso programa é remover os conteúdos no nosso ponto final.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Eliminar perfis da CDN e os pontos finais
A última função, que iremos também incluir elimina os pontos finais e perfis.

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
Agora podemos executar nosso programa de node. js usando nosso depurador favorito ou no console.

> [!TIP]
> Se estiver a utilizar o Visual Studio Code como seu depurador, terá de configurar o ambiente para passar os parâmetros da linha de comandos.  Código do Visual Studio faz isso **Launch** ficheiro.  Procurar uma propriedade chamada **args** e adicione uma matriz de valores de cadeia de caracteres para seus parâmetros, para que ele é semelhante a este: `"args": ["list", "profiles"]`.
> 
> 

Vamos começar listando nosso perfis.

![Lista de perfis](./media/cdn-app-dev-node/cdn-list-profiles.png)

Obtivemos de volta uma matriz vazia.  Uma vez que não temos todos os perfis no nosso grupo de recursos, que é esperado.  Vamos criar um perfil de agora.

![Criar perfil](./media/cdn-app-dev-node/cdn-create-profile.png)

Agora, vamos adicionar um ponto de extremidade.

![Criar ponto final](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Por fim, vamos eliminar nosso perfil.

![Eliminar perfil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Próximos Passos
Para ver o projeto concluído nestas instruções [transferir o exemplo](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Para ver a referência do SDK da CDN do Azure para node. js, veja a [referência](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Para encontrar a documentação adicional sobre o Azure SDK para node. js, veja a [total referência](https://azure.github.io/azure-sdk-for-node/).

Gerir recursos do CDN com [PowerShell](cdn-manage-powershell.md).


---
title: Começa com o Azure CDN SDK para Node.js | Microsoft Docs
description: Aprenda a criar uma aplicação simples Node.js consola que demonstre como automatizar a criação e gestão de perfis e pontos finais da Azure CDN.
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
ms.topic: how-to
ms.date: 04/02/2021
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: 386a424e45d1b718b68cbbf53322fd704317a06b
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285229"
---
# <a name="get-started-with-azure-cdn-development"></a>Introdução à programação do CDN do Azure
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Pode utilizar o [SDK Azure CDN para o JavaScript](https://www.npmjs.com/package/@azure/arm-cdn) para automatizar a criação e gestão de perfis e pontos finais da CDN.  Este tutorial percorre a criação de uma aplicação simples Node.js consola que demonstra várias das operações disponíveis.  Este tutorial não se destina a descrever todos os aspetos do Azure CDN SDK para JavaScript em detalhe.

Para completar este tutorial, já deve ter [Node.js](https://www.nodejs.org) **6.x.x** ou superior instalado e configurado.  Pode utilizar qualquer editor de texto que pretenda criar a sua aplicação Node.js.  Para escrever este tutorial, usei [o Código do Estúdio Visual.](https://code.visualstudio.com)  


[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Crie o seu projeto e adicione dependências de NPM
Agora que criámos um grupo de recursos para os nossos perfis CDN e demos a nossa permissão à nossa aplicação AZure AD para gerir perfis e pontos finais da CDN dentro desse grupo, podemos começar a criar a nossa aplicação.

Crie uma pasta para armazenar a sua aplicação.  A partir de uma consola com as ferramentas Node.js no seu caminho atual, desacorda a sua localização atual para esta nova pasta e inicialize o seu projeto executando:

```console
npm init
```

Em seguida, ser-lhe-á apresentada uma série de perguntas para rubricar o seu projeto.  Para **o ponto de entrada,** este tutorial utiliza *app.js*.  Podem ver as minhas outras escolhas no exemplo seguinte.

![Saída init NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

O nosso projeto está agora rubricado com uma *packages.jsarquivada.*  O nosso projeto vai usar algumas bibliotecas Azure contidas em pacotes NPM.  Utilizaremos a biblioteca para autenticação do Azure Ative Directory em Node.js ( @azure/ms-rest-nodeauth ) e a Biblioteca de Clientes Azure CDN para JavaScript ( @azure/arm-cdn ).  Vamos adicioná-los ao projeto como dependências.

```console
npm install --save @azure/ms-rest-nodeauth
npm install --save @azure/arm-cdn
```

Depois de as embalagens serem feitas para instalar, o *package.jsno* ficheiro deve parecer semelhante a este exemplo (os números das versões podem diferir):

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
    "@azure/arm-cdn": "^5.2.0",
    "@azure/ms-rest-nodeauth": "^3.0.0"
  }
}
```

Finalmente, utilizando o seu editor de texto, crie um ficheiro de texto em branco e guarde-o na raiz da nossa pasta de projeto *à medida queapp.js*.  Estamos prontos para começar a escrever código.

## <a name="requires-constants-authentication-and-structure"></a>Requer, constantes, autenticação e estrutura
Com *app.js* aberto no nosso editor, vamos escrever a estrutura básica do nosso programa.

1. Adicione os "requer" para os nossos pacotes NPM no topo com o seguinte:
   
    ``` javascript
    var msRestAzure = require('@azure/ms-rest-nodeauth');
    const { CdnManagementClient } = require('@azure/arm-cdn');
    ```
2. Precisamos definir algumas constantes que os nossos métodos usarão.  Adicione o seguinte.  Certifique-se de que substitui os espaços reservados, incluindo os **&lt; suportes angulares, &gt;** com os seus próprios valores, se necessário.
   
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
3. Em seguida, vamos instantaneaizar o cliente de gestão da CDN e dar-lhe as nossas credenciais.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new CdnManagementClient(credentials, subscriptionId);
    ```

4. A nossa aplicação de consola Node.js vai levar alguns parâmetros de linha de comando.  Vamos validar que pelo menos um parâmetro foi passado.
   
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
6. Em vários lugares do nosso programa, precisamos de ter certeza de que o número certo de parâmetros foram passados e mostrar alguma ajuda se eles não parecerem corretos.  Vamos criar funções para fazer isso.
   
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
7. Finalmente, as funções que vamos usar no cliente de gestão da CDN são assíncronos, por isso precisam de um método para ligar de volta quando terminarem.  Vamos fazer um que possa exibir a saída do cliente de gestão CDN (se houver) e sair do programa graciosamente.
   
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
            cdnClient.endpoints.listByProfile(resourceGroupName, parms[2], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Criar perfis e pontos finais de CDN
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

    cdnClient.profiles.create( resourceGroupName, parms[2], standardCreateParameters, callback);
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

    cdnClient.endpoints.create(resourceGroupName, parms[2], parms[3], endpointProperties, callback);
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
    cdnClient.endpoints.purgeContent(resourceGroupName, parms[2], parms[3], purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Eliminar perfis e pontos finais da CDN
A última função que iremos incluir elimina pontos finais e perfis.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteMethod(resourceGroupName, parms[2], callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteMethod(resourceGroupName, parms[2], parms[3], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Executar o programa
Agora podemos executar o nosso programa de Node.js usando o nosso depuração favorito ou na consola.

> [!TIP]
> Se estiver a usar o Código do Estúdio Visual como seu depurado, terá de configurar o seu ambiente para passar nos parâmetros da linha de comando.  O Código do Estúdio Visual faz isto no **launch.jsem** ficheiro.  Procure uma propriedade chamada **Args** e adicione uma variedade de valores de corda para os seus parâmetros, de modo que se pareça com este:  `"args": ["list", "profiles"]` .
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

Para ver a referência para o Azure CDN SDK para JavaScript, consulte a [referência](https://docs.microsoft.com/javascript/api/@azure/arm-cdn).

Para encontrar documentação adicional sobre o Azure SDK para JavaScript, consulte a [referência completa](https://docs.microsoft.com/javascript/api/?view=azure-node-latest).

Gerencie os seus recursos CDN com [o PowerShell](cdn-manage-powershell.md).

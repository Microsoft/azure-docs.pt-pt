---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: da2477b19327273fe922ac81f909233cb4ef8f06
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59503137"
---
## <a name="setting-up-your-web-server-or-project"></a>Como configurar seu servidor web ou projeto

> Prefere baixar de projeto este exemplo em vez disso?
> - [Transferir os ficheiros de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para um servidor local web, como o nó
>
> ou
> - [Transfira o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)
>
> E, em seguida, avance para o [passo de configuração](#register-your-application) para configurar o exemplo de código antes de executá-la.

## <a name="prerequisites"></a>Pré-requisitos
Um servidor local web, como [node. js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core), ou a integração de IIS Express com [Visual Studio 2017](https://www.visualstudio.com/downloads/) é necessário para executar este tutorial.

Se utilizar o node. js para executar o projeto, instale um IDE, como [Visual Studio Code](https://code.visualstudio.com/download) para editar os arquivos do projeto.

As instruções neste guia se baseiam em node. js e o Visual Studio 2017, mas pode utilizar qualquer outro ambiente de desenvolvimento ou servidor Web.

## <a name="create-your-project"></a>Criar o seu projeto

> ### <a name="option-1-node-other-web-servers"></a>Opção 1: Nó / outros servidores web
> Certifique-se de que instalou [node. js](https://nodejs.org/en/download/), em seguida, siga o passo abaixo:
> - Crie uma pasta para alojar a sua aplicação.

<p><!-- -->

> ### <a name="option-2-visual-studio"></a>Opção 2: Visual Studio
> Se estiver a utilizar o Visual Studio e criar um novo projeto, siga os passos abaixo para criar uma nova solução do Visual Studio:
> 1.    No Visual Studio:  **Ficheiro > novo > projeto**
> 2.    Sob **Visual C# \Web**, selecione **aplicação Web ASP.NET (.NET Framework)**
> 3.    Introduza um nome para a sua aplicação e selecione **OK**
> 4.    Sob **nova aplicação Web ASP.NET**, selecione **vazio**

## <a name="create-your-single-page-applications-ui"></a>Criar a IU da sua aplicação de página única
1. Criar um `index.html` ficheiro para o SPA do JavaScript. Se estiver a utilizar o Visual Studio, selecione o projeto (pasta raiz do projeto), clique com botão direito e selecione: **Adicionar > Novo Item > página HTML** e dê-lhe o nome Index. HTML.

2. Adicione o seguinte código à sua página:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.4/js/msal.js"></script>
       <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

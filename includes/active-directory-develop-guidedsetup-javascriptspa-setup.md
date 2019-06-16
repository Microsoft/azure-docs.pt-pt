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
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133410"
---
## <a name="set-up-your-web-server-or-project"></a>Configurar seu servidor web ou projeto

> Prefere baixar de projeto este exemplo em vez disso? Efetue um dos seguintes procedimentos:
> 
> - Para executar o projeto com um servidor local web, como o node. js, [transferir os ficheiros de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - (Opcional) Para executar o projeto com o servidor IIS [transfira o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> E, em seguida, para configurar o exemplo de código antes de a execute, avance para o [passo de configuração](#register-your-application).

## <a name="prerequisites"></a>Pré-requisitos

* Para executar este tutorial, precisa de um servidor local web, como [node. js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core), ou a integração de IIS Express com [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Se estiver a utilizar o node. js para executar o projeto, instalar um ambiente de desenvolvimento integrado (IDE), como [Visual Studio Code](https://code.visualstudio.com/download), para editar os arquivos do projeto.

* As instruções neste guia se baseiam em node. js e o Visual Studio 2017, mas pode utilizar qualquer outro ambiente de desenvolvimento ou servidor web.

## <a name="create-your-project"></a>Criar o seu projeto

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Opção 1: NODE. js ou outros servidores web
> Certifique-se de que instalou [node. js](https://nodejs.org/en/download/), e, em seguida, faça o seguinte:
> - Crie uma pasta para alojar a sua aplicação.
>
> ### <a name="option-2-visual-studio"></a>Opção 2: Visual Studio
> Se estiver a utilizar o Visual Studio e criar um novo projeto, faça o seguinte:
> 1. No Visual Studio, selecione **arquivo** > **New** > **projeto**.
> 1. Em **Visual C#\Web**,selecione **Aplicação Web ASP.NET (.NET Framework)** .
> 1. Introduza um nome para a sua aplicação e, em seguida, selecione **OK**.
> 1. Sob **nova aplicação Web ASP.NET**, selecione **vazia**.

## <a name="create-the-spa-ui"></a>Criar a interface do Usuário do SPA
1. Criar uma *Index* ficheiro para o SPA do JavaScript. Se estiver a utilizar o Visual Studio, selecione o projeto (pasta raiz do projeto), o botão direito do mouse e selecione **Add** > **Novo Item** > **página HTML**, e nomeie o arquivo *Index*.

1. Na *Index* de ficheiros, adicione o seguinte código:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
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

   > [!TIP]
   > Pode substituir a versão de msal no script anterior com a versão mais recente lançada sob [msal libera](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

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
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133107"
---
## <a name="register-your-application"></a>Registar a sua aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Se a sua conta dá-lhe acesso a mais do que um inquilino, selecione a conta no canto superior direito e, em seguida, defina a sua sessão do portal para o inquilino do Azure AD que pretende utilizar.
1. Vá para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) página.
1. Quando o **registar uma aplicação** é apresentada a página, introduza um nome para a sua aplicação.
1. Sob **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais**.
1. Sob o **URI de redirecionamento** secção, na lista pendente, selecione a **Web** plataforma e, em seguida, defina o valor para o URL da aplicação com base no seu servidor web. 

   Para obter informações sobre como e obter o URL de redirecionamento no Visual Studio e o node. js, consulte as duas secções seguintes.

1. Selecione **Registar**.
1. Na aplicação **descrição geral** página, tenha em atenção a **ID de aplicação (cliente)** valor para utilização posterior.
1. Este início rápido requer a [fluxo de concessão implícita](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) seja ativado. No painel esquerdo do aplicativo registrado, selecione **autenticação**.
1. Na **definições avançadas**, em **concessão implícita**, selecione o **tokens de ID** e **tokens de acesso** caixas de verificação. Tokens de ID e tokens de acesso são necessários, uma vez que esta aplicação tem de iniciar sessão dos utilizadores e chamar uma API.
1. Selecione **Guardar**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Definir um URL de redirecionamento para node. js
> Para node. js, pode definir a web porta do servidor no *Server. js* ficheiro. Este tutorial utiliza a porta 30662 para referência, mas pode usar qualquer outra porta disponível. 
>
> Para configurar um URL de redirecionamento as informações de registo de aplicação, mude novamente para o **registo de aplicação** painel, e fazer o seguinte:
>
> - Definir *`http://localhost:30662/`* como a **URL de redirecionamento**.
> - Se estiver a utilizar uma porta TCP personalizada, utilize *`http://localhost:<port>/`* (onde  *\<porta >* é o número de porta TCP personalizado).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Definir um URL de redirecionamento para o Visual Studio
> Para obter o URL de redirecionamento para o Visual Studio, faça o seguinte:
> 1. Na **Explorador de soluções**, selecione o projeto.
>
>    O **propriedades** é aberta a janela. Se não abrir, prima **F4**.
>
>    ![A janela de propriedades do projeto JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Copiar o **URL** valor.
 
> 1. Mude novamente para o **registo de aplicação** painel e cole o valor copiado como o **URL de redirecionamento**.

#### <a name="configure-your-javascript-spa"></a>Configurar seu SPA do JavaScript

1. Na *Index* ficheiro que criou durante a configuração do projeto, adicionar as informações de registo de aplicação. Na parte superior do ficheiro, dentro do `<script></script>` etiquetas, adicione o seguinte código:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Em que:
    - *\<Enter_the_Application_Id_here >* é o **ID da aplicação (cliente)** para a aplicação que registou.
    - *\<Enter_the_Tenant_info_here >* está definido para uma das seguintes opções:
       - Se a sua aplicação suportar *contas neste diretório organizacional*, substitua este valor com o **ID do inquilino** ou **nome do inquilino** (por exemplo,  *contoso.microsoft.com*).
       - Se a sua aplicação suportar *contas em qualquer diretório organizacional*, substitua este valor com **organizações**.
       - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas Microsoft pessoais*, substitua este valor com **comuns**. Para restringir o suporte para *contas Microsoft pessoais de apenas*, substitua este valor com **consumidores**.

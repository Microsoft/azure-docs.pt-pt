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
ms.openlocfilehash: c0dcfc4ad7edf4d9203b807aa799eb047c753bed
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551522"
---
## <a name="register-your-application"></a>Registar a sua aplicação

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) registar uma aplicação.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. Navegue para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) página.
1. Quando o **registar uma aplicação** é apresentada a página, introduza um nome para a sua aplicação.
1. Sob **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais**.
1. Sob o **URI de redirecionamento** secção, selecione a **Web** plataforma e defina o valor para o URL da aplicação com base no seu servidor web. Consulte as secções abaixo para obter instruções sobre como definir e obter o URL de redirecionamento no nó e o Visual Studio.
1. Quando terminar, selecione **Registar**.
1. Na aplicação **descrição geral** página, tome nota da **ID de aplicação (cliente)** valor.
1. Este início rápido requer a [fluxo de concessão implícita](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) seja ativado. No painel de navegação do lado esquerdo do aplicativo registrado, selecione **autenticação**.
1. Na **definições avançadas**, em **concessão implícita**, permita as duas opções **tokens de ID** e **tokens de acesso** caixas de verificação. Tokens de ID e tokens de acesso são necessários, uma vez que esta aplicação tem de iniciar sessão dos utilizadores e chamar uma API.
1. Selecione **Guardar**.

> #### <a name="setting-the-redirect-url-for-node"></a>Definir o URL de redirecionamento para o nó
> Para node. js, pode definir a web porta do servidor no *Server. js* ficheiro. Este tutorial utiliza a porta 30662 para referência, mas pode usar qualquer outra porta disponível. Siga as instruções abaixo para configurar um URL de redirecionamento as informações de registo de aplicação:<br/>
> - Mude novamente para o *registo de aplicação* e defina `http://localhost:30662/` como uma `Redirect URL`, ou utilize `http://localhost:[port]/` se estiver a utilizar uma porta TCP personalizada (em que *[porta]* é o número de porta TCP personalizado).

<p>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio instruções para obter o URL de redirecionamento
> Siga estes passos para obter o URL de redirecionamento:
> 1. Na **Explorador de soluções**, selecione o projeto e descobrir a **propriedades** janela. Se não vir um **propriedades** janela, prima **F4**.
> 2. Copie o valor de **URL** na área de transferência:<br/> ![Propriedades do projeto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Mude novamente para o *registo de aplicação* e defina o valor como um **URL de redirecionamento**.

#### <a name="configure-your-javascript-spa"></a>Configurar seu SPA do JavaScript

1. Na `index.html` ficheiro criado durante a configuração do projeto, adicione as informações de registo de aplicação. Adicione o seguinte código na parte superior dentro do `<script></script>` etiquetas no corpo do seu `index.html` ficheiro:

    ```javascript
    var applicationConfig = {
        clientID: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

    Em que:
    - `Enter_the_Application_Id_here` - é o **ID da Aplicação (cliente)** que registou.
    - `Enter_the_Tenant_Info_Here` - está definido para uma das seguintes opções:
       - Se a sua aplicação suportar **Contas neste diretório organizacional**, substitua este valor pelo **Id do Inquilino** ou pelo **Nome do inquilino** (por exemplo, contoso.microsoft.com)
       - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
       - Se a sua aplicação suportar **Contas em quaisquer contas da Microsoft de diretório organizacional e pessoais**, substitua este valor por `common`

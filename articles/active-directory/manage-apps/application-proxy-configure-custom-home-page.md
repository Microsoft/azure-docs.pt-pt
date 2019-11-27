---
title: Home page personalizadas para aplicativos publicados-Azure Proxy de Aplicativo do AD
description: Abrange as noções básicas sobre os conectores de Proxy de aplicações do Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1621b273f617955a374ed46d9c215ba99e5b2913
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275603"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Definir uma página inicial personalizada para aplicações publicadas com o Proxy de aplicações do Azure AD

Este artigo discute como configurar um aplicativo para direcionar um usuário para um home page personalizado. Ao publicar um aplicativo com o proxy de aplicativo, você define uma URL interna, mas às vezes essa não é a página que um usuário deve ver primeiro. Defina um home page personalizado para que um usuário obtenha a página correta quando acessar o aplicativo. Um usuário verá o home page personalizado que você definiu, independentemente de eles acessarem o aplicativo do painel de acesso Azure Active Directory ou do iniciador de aplicativos do Office 365.

Quando um usuário inicia o aplicativo, ele é direcionado por padrão para a URL do domínio raiz para o aplicativo publicado. Normalmente, a página de destino está definida como o URL da home page. Use o módulo do PowerShell do Azure AD para definir uma URL de home page personalizada quando você quiser que um usuário de aplicativo pouse em uma página específica dentro do aplicativo.

Veja um cenário que explica por que sua empresa definiria um home page personalizado:

- Dentro de sua rede corporativa, um usuário vai para `https://ExpenseApp/login/login.aspx` para entrar e acessar seu aplicativo.
- Como você tem outros ativos (como imagens) que o proxy de aplicativo precisa acessar no nível superior da estrutura de pastas, você publica o aplicativo com `https://ExpenseApp` como a URL interna.
- A URL externa padrão é `https://ExpenseApp-contoso.msappproxy.net`, que não leva um usuário externo à página de entrada.
- Você deseja definir `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` como a URL de home page em vez disso, para que um usuário externo Veja a página de entrada primeiro.

> [!NOTE]
> Quando você concede aos usuários acesso a aplicativos publicados, os aplicativos são exibidos no [painel de acesso do Azure ad](../user-help/my-apps-portal-end-user-access.md) e no [iniciador de aplicativos do Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Antes de começar

Antes de definir o URL da home page, tenha em atenção os seguintes requisitos:

- O caminho especificado deve ser um caminho de subdomínio da URL do domínio raiz.

  Por exemplo, se a URL do domínio raiz for `https://apps.contoso.com/app1/`, a URL home page configurada deverá começar com `https://apps.contoso.com/app1/`.

- Se fizer uma alteração para a aplicação publicada, a alteração poderá repor o valor de URL da home page. Quando atualizar a aplicação no futuro, deve verificar novamente e, se necessário, atualize o URL da home page.

Você pode definir a URL de home page por meio do portal do Azure ou usando o PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Alterar a home page no portal do Azure

Para alterar a URL de home page do seu aplicativo por meio do portal do AD do Azure, siga estas etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador.
1. Selecione **Azure Active Directory**e, em seguida, **registros de aplicativo**. A lista de aplicativos registrados é exibida.
1. Escolha seu aplicativo na lista. É exibida uma página mostrando os detalhes do aplicativo registrado.
1. Em **gerenciar**, selecione **identidade visual**.
1. Atualize a **URL da Home Page** com o novo caminho.

   ![Página de identidade visual para um aplicativo registrado mostrando o campo URL da Home Page](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Selecione **Guardar**.

## <a name="change-the-home-page-with-powershell"></a>Alterar a home page com o PowerShell

Para configurar o home page de um aplicativo usando o PowerShell, você precisa:

1. Instale o módulo do PowerShell do Azure AD.
1. Localize o valor ObjectId do aplicativo.
1. Atualize a URL de home page do aplicativo usando comandos do PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Instalar o módulo do PowerShell do Azure AD

Antes de definir um URL de página inicial personalizada com o PowerShell, instale o módulo do PowerShell do Azure AD. Você pode baixar o pacote do [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), que usa o ponto de extremidade API do Graph.

Para instalar o pacote, siga estes passos:

1. Abra uma janela do PowerShell padrão e, em seguida, execute o seguinte comando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Se você estiver executando o comando como um não administrador, use a opção `-scope currentuser`.

1. Durante a instalação, selecione **Y** para instalar dois pacotes do NuGet.org. Ambos os pacotes são necessários.

### <a name="find-the-objectid-of-the-app"></a>Localizar o ObjectId do aplicativo

Você Obtém o ObjectId do aplicativo procurando pelo aplicativo por seu nome de exibição ou home page.

1. Na mesma janela do PowerShell, importe o módulo do Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Inicie sessão no módulo do Azure AD como o administrador de inquilino.

   ```powershell
   Connect-AzureAD
   ```

1. Localize o aplicativo. Este exemplo usa o PowerShell para localizar o ObjectId pesquisando o aplicativo com um nome de exibição de `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Deve obter um resultado semelhante à mostrada aqui. Copie o GUID do ObjectId a ser usado na próxima seção.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Como alternativa, você pode apenas efetuar pull da lista de todos os aplicativos, Pesquisar na lista o aplicativo com um nome de exibição ou home page específico e copiar o ObjectId do aplicativo quando o aplicativo for encontrado.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Atualizar o URL da home page

Crie a URL de home page e atualize seu aplicativo com esse valor. Continue usando a mesma janela do PowerShell ou, se você estiver usando uma nova janela do PowerShell, entre no módulo do Azure AD novamente usando `Connect-AzureAD`. Em seguida, siga estas etapas:

1. Crie uma variável para conter o valor de ObjectId que você copiou na seção anterior. (Substitua o valor ObjectId usado para neste exemplo do SharePoint pelo valor ObjectId do seu aplicativo.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Confirme se você tem o aplicativo correto executando o comando a seguir. A saída deve ser idêntica à saída que você viu na seção anterior ([localize o ObjectID do aplicativo](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Crie um objeto de aplicativo em branco para manter as alterações que deseja fazer.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Defina o URL da home page para o valor que pretende. O valor tem de ser um caminho de subdomínio da aplicação publicada. Por exemplo, se você alterar a URL do home page de `https://sharepoint-iddemo.msappproxy.net/` para `https://sharepoint-iddemo.msappproxy.net/hybrid/`, os usuários do aplicativo irão diretamente para o home page personalizado.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Faça a atualização do home page.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Para confirmar que a alteração foi bem-sucedida, execute o comando a seguir da etapa 2 novamente.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Para nosso exemplo, a saída agora deve aparecer da seguinte maneira:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Reinicie o aplicativo para confirmar se o home page aparece como a primeira tela, conforme esperado.

> [!NOTE]
> Quaisquer alterações que fizer para a aplicação poderão repor o URL da home page. Se repõe o seu URL da home page, repita os passos nesta secção para configurá-lo novamente.

## <a name="next-steps"></a>Passos seguintes

- [Habilitar o acesso remoto ao SharePoint com o Azure Proxy de Aplicativo do AD](application-proxy-integrate-with-sharepoint-server.md)
- [Tutorial: adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md)
---
title: Definir uma página inicial personalizada para aplicações publicadas com o Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Abrange as noções básicas sobre os conectores de Proxy de aplicações do Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8017049218bed5a1b1bd86b68dc4342b4044723
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109785"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Definir uma página inicial personalizada para aplicações publicadas com o Proxy de aplicações do Azure AD

Este artigo descreve como configurar as aplicações para direcionar os utilizadores para uma página inicial personalizada. Quando publica um aplicativo com o Proxy de aplicações, definir um URL interno de uma só, mas, às vezes, o que não é a página de que seus usuários deverá ver primeiro. Defina uma página inicial personalizada para que os utilizadores Ir para a página correta quando acedem as aplicações. Os utilizadores verão a página inicial personalizada que definir, se que aceder à aplicação a partir do painel de acesso de diretório Active Directory do Azure ou no iniciador de aplicações do Office 365.

Quando os utilizadores iniciarem o, este estão direcionado por predefinição para o URL de domínio de raiz para a aplicação publicada. Normalmente, a página de destino está definida como o URL da home page. Utilize o módulo Azure AD PowerShell para definir URLs de página inicial personalizada quando pretender que os utilizadores da aplicação aterrar numa página específica no aplicativo. 

Eis um exemplo de motivo pelo qual uma empresa definiria uma página inicial personalizada:
- No interior da rede empresarial, os utilizadores aceder a `https://ExpenseApp/login/login.aspx` para iniciar sessão e aceder à sua aplicação.
- Como tem outros recursos, como imagens que precisa de Proxy de aplicações de acesso no nível superior da estrutura de pasta, publicar a aplicação com `https://ExpenseApp` como o URL interno.
- O URL externo de predefinido é `https://ExpenseApp-contoso.msappproxy.net`, que não usa os seus utilizadores para a página de início de sessão.  
- Definir `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` como o URL da home page. 

>[!NOTE]
>Quando concede aos utilizadores acesso a aplicações publicadas, as aplicações são apresentadas na [painel de acesso do Azure AD](../user-help/active-directory-saas-access-panel-introduction.md) e o [iniciador de aplicações do Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Antes de começar

Antes de definir o URL da home page, tenha em atenção os seguintes requisitos:

* Certifique-se de que o caminho especificado é um caminho de subdomínio do URL de domínio de raiz.

  Se, por exemplo, o URL de domínio de raiz for https://apps.contoso.com/app1/, o URL da home page que configurar tem de começar com https://apps.contoso.com/app1/.

* Se fizer uma alteração para a aplicação publicada, a alteração poderá repor o valor de URL da home page. Quando atualizar a aplicação no futuro, deve verificar novamente e, se necessário, atualize o URL da home page.

## <a name="change-the-home-page-in-the-azure-portal"></a>Alterar a home page no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue para **do Azure Active Directory** > **registos das aplicações** e escolha a sua aplicação na lista. 
3. Selecione **propriedades** das configurações.
4. Atualização do **URL da Home page** campo com o novo caminho. 

   ![Fornecer o novo URL da home page](./media/application-proxy-configure-custom-home-page/homepage.png)

5. Selecione **guardar**

## <a name="change-the-home-page-with-powershell"></a>Alterar a home page com o PowerShell

### <a name="install-the-azure-ad-powershell-module"></a>Instalar o módulo do PowerShell do Azure AD

Antes de definir um URL de página inicial personalizada com o PowerShell, instale o módulo do PowerShell do Azure AD. Pode transferir o pacote a partir da [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), que utiliza o ponto de extremidade do Graph API. 

Para instalar o pacote, siga estes passos:

1. Abra uma janela do PowerShell padrão e, em seguida, execute o seguinte comando:

    ```
     Install-Module -Name AzureAD
    ```
    Se estiver a executar o comando como um não-administrador, utilize o `-scope currentuser` opção.
2. Durante a instalação, selecione **Y** para instalar dois pacotes de Nuget.org. Ambos os pacotes são necessários. 

### <a name="find-the-objectid-of-the-app"></a>Localizar o ObjectID da aplicação

Obtenha o ObjectID da aplicação e, em seguida, procure a aplicação pela sua página inicial.

1. Na mesma janela do PowerShell, importe o módulo do Azure AD.

    ```
    Import-Module AzureAD
    ```

2. Inicie sessão no módulo do Azure AD como o administrador de inquilino.

    ```
    Connect-AzureAD
    ```
3. Localize a aplicação com base no seu URL da home page. Pode encontrar o URL no portal, acedendo a **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações**. Este exemplo utiliza *sharepoint iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like "sharepoint-iddemo" } | fl DisplayName, Homepage, ObjectID
    ```
4. Deve obter um resultado semelhante à mostrada aqui. Copie o GUID de ObjectID para utilizar na secção seguinte.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>Atualizar o URL da home page

Crie o URL da home page e atualizar a sua aplicação com esse valor. Continue a utilizar a mesma janela do PowerShell para executar esses comandos. Ou, se estiver a utilizar uma nova janela do PowerShell, inicie sessão no módulo do Azure AD novamente usando `Connect-AzureAD`. 

1. Confirme que a aplicação correta e substitua *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* com o ObjectID que copiou na secção anterior.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

   Agora que confirmou que a aplicação, está pronto para atualizar a home page, da seguinte forma.

2. Crie um objeto de aplicativo em branco para manter as alterações que deseja fazer. Esta variável contém os valores que pretende atualizar. Nada é criado neste passo.

    ```
    $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
    ```

3. Defina o URL da home page para o valor que pretende. O valor tem de ser um caminho de subdomínio da aplicação publicada. Por exemplo, se alterar o URL da home page da `https://sharepoint-iddemo.msappproxy.net/` para `https://sharepoint-iddemo.msappproxy.net/hybrid/`, os utilizadores da aplicação vão diretamente para a home page personalizada.

    ```
    $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
    ```
4. Faça a atualização com o GUID (ObjectID) que copiou no "passo 1: Localizar o ObjectID da aplicação."

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Para confirmar que a alteração foi efetuada com êxito, reinicie a aplicação.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Quaisquer alterações que fizer para a aplicação poderão repor o URL da home page. Se repõe o seu URL da home page, repita os passos nesta secção para configurá-lo novamente.

## <a name="next-steps"></a>Passos Seguintes

- [Ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Ativar o Proxy de aplicações no portal do Azure](application-proxy-add-on-premises-application.md)

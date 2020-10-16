---
title: Página inicial personalizada para aplicativos publicados - Azure AD Application Proxy
description: Cobre o básico sobre conectores de aplicação AD AZure
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 155a91ded6c814e2d868e8edd4572459460d006f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88642066"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Desconfiem de uma página inicial personalizada para aplicações publicadas utilizando o Azure AD Application Proxy

Este artigo discute como configurar uma aplicação para direcionar um utilizador para uma página inicial personalizada. Quando publica uma aplicação com Application Proxy, define um URL interno, mas às vezes não é essa a página que um utilizador deve ver primeiro. Desaprote uma página inicial personalizada para que um utilizador obtenha a página certa quando aceder à aplicação. Um utilizador irá ver a página inicial personalizada que definiu, independentemente de aceder à aplicação a partir do Azure Ative Directory My Apps ou do launcher da aplicação Microsoft 365.

Quando um utilizador lança a aplicação, é direcionada por padrão ao URL de domínio raiz para a aplicação publicada. A página de aterragem é tipicamente definida como o URL da página inicial. Utilize o módulo Azure AD PowerShell para definir um URL de página inicial personalizado quando pretender que um utilizador de aplicações aterre numa página específica dentro da app.

Aqui está um cenário que explica por que a sua empresa definiria uma página inicial personalizada:

- Dentro da sua rede corporativa, um utilizador vai `https://ExpenseApp/login/login.aspx` para iniciar sôm e aceder à sua aplicação.
- Como tem outros ativos (como imagens) que o Application Proxy precisa de aceder ao nível superior da estrutura da pasta, publica a aplicação `https://ExpenseApp` como URL interno.
- O URL externo predefinido é `https://ExpenseApp-contoso.msappproxy.net` , que não leva um utilizador externo para a página de iniciar sposição.
- Em vez disso, pretende definir como URL de página inicial, para `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` que um utilizador externo veja primeiro a página de início de s início.

> [!NOTE]
> Quando dá acesso aos utilizadores a aplicações publicadas, as aplicações são apresentadas nas [Minhas Apps](../user-help/my-apps-portal-end-user-access.md) e no [launcher da aplicação Office 365.](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)

## <a name="before-you-start"></a>Antes de começar

Antes de definir o URL da página inicial, tenha em mente os seguintes requisitos:

- O caminho que especifica deve ser um caminho subdomínio do URL do domínio raiz.

  Por exemplo, se o URL de domínio raiz `https://apps.contoso.com/app1/` for, o URL da página inicial que configura deve começar por `https://apps.contoso.com/app1/` .

- Se fizer uma alteração na aplicação publicada, a alteração poderá redefinir o valor do URL da página inicial. Quando atualizar a aplicação no futuro, deverá verificar novamente e, se necessário, atualizar o URL da página inicial.

Pode definir o URL da página inicial através do portal Azure ou utilizando o PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Alterar a página inicial no portal Azure

Para alterar o URL da página inicial da sua aplicação através do portal AD Azure, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador.
1. Selecione **Azure Ative Directory**e, em seguida, **registos de Aplicações**. A lista de aplicações registadas aparece.
1. Escolha a sua aplicação na lista. Aparece uma página que mostra os detalhes da aplicação registada.
1. Under **Manage**, selecione **Branding**.
1. Atualize o **URL da página inicial**  com o seu novo caminho.

   ![Página de marcação para uma aplicação registrada mostrando o campo URL da página inicial](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Selecione **Guardar**.

## <a name="change-the-home-page-with-powershell"></a>Altere a página inicial com PowerShell

Para configurar a página inicial de uma aplicação utilizando o PowerShell, é necessário:

1. Instale o módulo Azure AD PowerShell.
1. Encontre o valor ObjectId da aplicação.
1. Atualize o URL da página inicial da aplicação utilizando comandos PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Instale o módulo Azure AD PowerShell

Antes de definir um URL de página inicial personalizado utilizando o PowerShell, instale o módulo Azure AD PowerShell.Pode descarregar o pacote da [PowerShell Gallery,](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)que utiliza o ponto final da API do Gráfico.

Para instalar a embalagem, siga estes passos:

1. Abra uma janela padrão powerShell e, em seguida, executar o seguinte comando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Se estiver a executar o comando como não administrador, use a `-scope currentuser` opção.

1. Durante a instalação, selecione **Y** para instalar dois pacotes a partir de Nuget.org. Ambos os pacotes são necessários.

### <a name="find-the-objectid-of-the-app"></a>Encontre o ObjectId da aplicação

Obtém o ObjectId da aplicação procurando a aplicação pelo seu nome de exibição ou página inicial.

1. Na mesma janela PowerShell, importe o módulo AD Azure.

   ```powershell
   Import-Module AzureAD
   ```

1. Inscreva-se no módulo AD AZure como administrador do inquilino.

   ```powershell
   Connect-AzureAD
   ```

1. Encontre a aplicação. Este exemplo utiliza o PowerShell para encontrar o ObjectId procurando a aplicação com um nome de exibição de `SharePoint` .

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Devia obter um resultado semelhante ao mostrado aqui. Copie o ObjectId GUID para utilizar na secção seguinte.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Em alternativa, basta retirar a lista de todas as aplicações, pesquisar a lista para a aplicação com um nome de exibição específico ou página inicial, e copiar o ObjectId da aplicação assim que a aplicação for encontrada.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Atualizar o URL da página inicial

Crie o URL da página inicial e atualize a sua aplicação com esse valor. Continue a utilizar a mesma janela PowerShell, ou se estiver a utilizar uma nova janela PowerShell, inscreva-se novamente no módulo AD Azure utilizando `Connect-AzureAD` . Em seguida, siga estes passos:

1. Crie uma variável para manter o valor ObjectId copiado na secção anterior. (Substitua o valor ObjectId utilizado neste exemplo sharePoint pelo valor ObjectId da sua aplicação.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Confirme que tem a aplicação correta executando o seguinte comando. A saída deve ser idêntica à saída que viu na secção anterior[(Localizar o ObjectId da aplicação).](#find-the-objectid-of-the-app)

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Crie um objeto de aplicação em branco para conter as alterações que pretende fazer.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Desa estava o URL da página inicial ao valor que deseja. O valor deve ser um caminho subdomínio da aplicação publicada. Por exemplo, se alterar o URL da página inicial de , os utilizadores de `https://sharepoint-iddemo.msappproxy.net/` `https://sharepoint-iddemo.msappproxy.net/hybrid/` aplicações vão diretamente para a página inicial personalizada.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Faça a atualização da página inicial.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Para confirmar que a mudança foi bem sucedida, executar o seguinte comando a partir do passo 2 novamente.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Para o nosso exemplo, a saída deve agora aparecer da seguinte forma:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Reinicie a aplicação para confirmar que a página inicial aparece como o primeiro ecrã, como esperado.

> [!NOTE]
> Quaisquer alterações que fizer à aplicação podem redefinir o URL da página inicial. Se o URL da página inicial reiniciar, repita os passos nesta secção para o repor.

## <a name="next-steps"></a>Passos seguintes

- [Ativar o acesso remoto ao SharePoint com o Proxy de Aplicações do Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Tutorial: Adicionar uma aplicação no local para acesso remoto através de Aplicação Proxy em Diretório Ativo Azure](application-proxy-add-on-premises-application.md)

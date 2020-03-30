---
title: Página inicial personalizada para aplicações publicadas - Procuração de aplicação ad ad azure
description: Cobre o básico sobre conectores de proxy de aplicação ad azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275603"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Delineie uma página inicial personalizada para aplicações publicadas utilizando o Proxy de Aplicação AD do Azure

Este artigo discute como configurar uma aplicação para direcionar um utilizador para uma página inicial personalizada. Ao publicar uma aplicação com Procuração de Aplicação, define um URL interno, mas às vezes não é a página que um utilizador deve ver primeiro. Delineie uma página inicial personalizada para que um utilizador obtenha a página certa quando acede à aplicação. Um utilizador verá a página inicial personalizada que definiu, independentemente de aceder à aplicação a partir do Painel de Acesso ao Diretório Ativo do Azure ou do lançador de aplicações office 365.

Quando um utilizador lança a aplicação, são direcionados por padrão para o URL de domínio raiz para a aplicação publicada. A página de aterragem é tipicamente definida como url de página inicial. Utilize o módulo PowerShell Da AD Azure para definir um URL de página inicial personalizado quando pretender que um utilizador de aplicações aterre numa página específica dentro da aplicação.

Aqui está um cenário que explica por que a sua empresa definiria uma página inicial personalizada:

- Dentro da sua rede corporativa, um utilizador vai fazer o sessão e aceder à `https://ExpenseApp/login/login.aspx` sua aplicação.
- Como tem outros ativos (como imagens) que o Application Proxy precisa de aceder ao `https://ExpenseApp` nível superior da estrutura da pasta, publica a app como URL interno.
- O URL externo `https://ExpenseApp-contoso.msappproxy.net`predefinido é , que não leva um utilizador externo para a página de entrada.
- Em vez `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` disso, pretende definir como URL da página inicial, para que um utilizador externo veja primeiro a página de início.

> [!NOTE]
> Quando dá aos utilizadores acesso a aplicações publicadas, as aplicações são apresentadas no Painel de [Acesso AD Azure](../user-help/my-apps-portal-end-user-access.md) e no lançador de [aplicações office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Antes de começar

Antes de definir o URL da página inicial, tenha em mente os seguintes requisitos:

- O caminho que especifica deve ser um caminho de subdomínio do URL de domínio raiz.

  Por exemplo, se o URL `https://apps.contoso.com/app1/`de domínio raiz for, o `https://apps.contoso.com/app1/`URL da página inicial que configura deve começar com .

- Se fizer uma alteração na aplicação publicada, a alteração poderá redefinir o valor do URL da página inicial. Quando atualizar a aplicação no futuro, deverá voltar a verificar e, se necessário, atualizar o URL da página inicial.

Pode definir o URL da página inicial através do portal Azure ou utilizando o PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Mude a página inicial no portal Azure

Para alterar o URL da página inicial da sua aplicação através do portal Azure AD, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador.
1. Selecione **Diretório Ativo Azure**, e, em seguida, registos de **aplicações.** A lista de aplicações registadas aparece.
1. Escolha a sua aplicação na lista. Aparece uma página que mostra os detalhes da aplicação registada.
1. Em **'Gerir',** selecione **Branding**.
1. Atualize o URL da **página inicial** com o seu novo caminho.

   ![Página de branding para uma aplicação registada que mostra o campo URL da Página Inicial](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Selecione **Guardar**.

## <a name="change-the-home-page-with-powershell"></a>Mude a página inicial com powerShell

Para configurar a página inicial de uma aplicação utilizando o PowerShell, é necessário:

1. Instale o módulo PowerShell Azure AD.
1. Encontre o valor ObjectId da aplicação.
1. Atualize o URL da página inicial da aplicação utilizando comandos PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Instale o módulo PowerShell Da AD Azure

Antes de definir um URL de página inicial personalizado utilizando o PowerShell, instale o módulo PowerShell Da AD Azure.Pode descarregar o pacote a partir da [PowerShell Gallery,](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)que utiliza o ponto final da API do Gráfico.

Para instalar o pacote, siga estes passos:

1. Abra uma janela padrão da PowerShell e, em seguida, executar o seguinte comando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Se estiver a comandar o comando como não `-scope currentuser` administrador, use a opção.

1. Durante a instalação, selecione **Y** para instalar duas embalagens a partir de Nuget.org. Ambos os pacotes são necessários.

### <a name="find-the-objectid-of-the-app"></a>Encontre o ObjectId da aplicação

Obtém o ObjectId da aplicação procurando a aplicação pelo seu nome de exibição ou página inicial.

1. Na mesma janela PowerShell, importe o módulo Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Inscreva-se no módulo Azure AD como administrador de inquilinos.

   ```powershell
   Connect-AzureAD
   ```

1. Encontre a aplicação. Este exemplo utiliza o PowerShell para encontrar o ObjectId `SharePoint`procurando a aplicação com o nome de exibição de .

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Devia sacá-lo com um resultado semelhante ao mostrado aqui. Copie o ObjectId GUID para utilizar na secção seguinte.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Em alternativa, basta restar a lista de todas as aplicações, pesquisar a lista da aplicação com um nome de exibição específico ou página inicial e copiar o ObjectId da aplicação assim que a aplicação for encontrada.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Atualizar o URL da página inicial

Crie o URL da página inicial e atualize a sua aplicação com esse valor. Continue a utilizar a mesma janela PowerShell, ou se estiver a utilizar uma nova janela `Connect-AzureAD`PowerShell, volte a entrar no módulo AD Azure utilizando . Em seguida, siga estes passos:

1. Crie uma variável para manter o valor ObjectId copiado na secção anterior. (Substitua o valor ObjectId utilizado neste exemplo do SharePoint com o valor ObjectId da sua aplicação.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Confirme que tem a aplicação correta executando o seguinte comando. A saída deve ser idêntica à saída que viu na secção anterior[(Encontre o ObjectId da aplicação).](#find-the-objectid-of-the-app)

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Crie um objeto de aplicação em branco para conter as alterações que pretende fazer.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Delineie o URL da página inicial para o valor que deseja. O valor deve ser um caminho de subdomínio da aplicação publicada. Por exemplo, se alterar o `https://sharepoint-iddemo.msappproxy.net/` URL `https://sharepoint-iddemo.msappproxy.net/hybrid/`da página inicial para , os utilizadores da aplicação vão diretamente para a página inicial personalizada.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Faça a atualização da página inicial.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Para confirmar que a mudança foi bem sucedida, corra o seguinte comando do passo 2 novamente.

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
> Quaisquer alterações que faça na aplicação podem redefinir o URL da página inicial. Se o URL da sua página inicial redefinir, repita os passos desta secção para o repor.

## <a name="next-steps"></a>Passos seguintes

- [Ativar o acesso remoto ao SharePoint com o Proxy de Aplicações do Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Tutorial: Adicione um pedido no local para acesso remoto através de Application Proxy no Diretório Ativo Azure](application-proxy-add-on-premises-application.md)
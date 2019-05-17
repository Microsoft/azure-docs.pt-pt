---
title: Definir uma página inicial personalizada para aplicações publicadas com o Proxy de aplicações do Azure AD | Documentos da Microsoft
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
ms.date: 04/17/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fa5c5638da390f4416afc9f4bd9c5d58c34cea8
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825583"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Definir uma página inicial personalizada para aplicações publicadas com o Proxy de aplicações do Azure AD

Este artigo descreve como configurar uma aplicação para direcione um utilizador para uma página inicial personalizada, que pode ser diferente dependendo se são internos ou externos. Quando publica uma aplicação com o Proxy de aplicações, definir um URL interno, mas, às vezes, isso não é a página de que um utilizador verá primeiro. Defina uma página inicial personalizada para que um usuário obtém a página correta quando acederem à aplicação. O usuário verá a página inicial personalizada que definir, independentemente se que aceder à aplicação a partir do painel de acesso de diretório Active Directory do Azure ou no iniciador de aplicações do Office 365.

Quando um utilizador inicia a aplicação, este estão direcionado por predefinição para o URL de domínio de raiz para a aplicação publicada. Normalmente, a página de destino está definida como o URL da home page. Utilize o módulo Azure AD PowerShell para definir um URL de página inicial personalizada quando pretender que o utilizador da aplicação aterrar numa página específica no aplicativo.

Um cenário de aqui que explica por que a sua empresa definiria uma página inicial personalizada e, por que deve ser diferente dependendo do tipo de utilizador:

- Como tem outros recursos (como imagens) que precisa de Proxy de aplicações de acesso no nível superior da estrutura de pasta, publicar a aplicação com `https://ExpenseApp` como o URL interno.
- No entanto, no interior da rede empresarial, um usuário vai a `https://ExpenseApp/login/login.aspx` para iniciar sessão e aceder à sua aplicação.
- O URL externo de predefinido é `https://ExpenseApp-contoso.msappproxy.net`, que não usa um utilizador externo para a página de início de sessão.
- Se pretender definir `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` como o URL externo de página inicial de página em vez disso, por isso, um utilizador externo vê a página de início de sessão pela primeira vez.

>[!NOTE]
>Quando concede aos utilizadores acesso a aplicações publicadas, as aplicações são apresentadas na [painel de acesso do Azure AD](../user-help/my-apps-portal-end-user-access.md) e o [iniciador de aplicações do Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Antes de começar

Antes de definir o URL da home page, tenha em atenção os seguintes requisitos:

- O caminho que especificar tem de ser um caminho de subdomínio do URL de domínio de raiz.

  Por exemplo, se o URL de domínio de raiz for `https://apps.contoso.com/app1/`, o URL da home page que configurar tem de começar com `https://apps.contoso.com/app1/`.

- Se fizer uma alteração para a aplicação publicada, a alteração poderá repor o valor de URL da home page. Quando atualizar a aplicação no futuro, deve verificar novamente e, se necessário, atualize o URL da home page.

Pode alterar a página de home externa ou interna através do portal do Azure ou através do PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Alterar a home page no portal do Azure

Para alterar as home pages do externas e internas da sua aplicação através do portal do Azure AD, siga estes passos:

1. Inicie sessão para o [portal do Azure Active Directory](https://aad.portal.azure.com/). É apresentado o dashboard do Centro de administração do Azure Active Directory.
2. Na barra lateral, selecione **do Azure Active Directory**. É apresentada a página de descrição geral do Azure AD.
3. Na barra lateral descrição geral, selecione **registos das aplicações**. É apresentada a lista de aplicações registadas.
4. Escolha a sua aplicação na lista. É apresentada uma página que mostra os detalhes da aplicação registada.
5. Selecione a ligação em **URIs de redirecionamento**, que apresenta o número de URIs de redirecionamento para a web e tipos de clientes públicos. É apresentada a página de autenticação para a aplicação registada.
6. Na última linha do **URIs de redirecionamento** da tabela, defina o **tipo** coluna para **cliente público (móveis e de ambiente de trabalho)** e, no **URI de REDIRECIONAMENTO**coluna, escreva o URL interno que pretende utilizar. É apresentada uma nova linha vazia abaixo da linha que acabaram de ser modificadas.
7. Na nova linha, defina o **tipo** coluna **Web**e, no **URI de REDIRECIONAMENTO** coluna, escreva o URL externo que pretende utilizar.
8. Se pretender eliminar quaisquer linhas URI de redirecionamento existentes, selecione o **eliminar** ícone (uma lata de lixo) ao lado de cada linha indesejada.
9. Selecione **Guardar**.

## <a name="change-the-home-page-with-powershell"></a>Alterar a home page com o PowerShell

Para configurar a home page de uma aplicação com o PowerShell, terá de:

1. Instale o módulo do PowerShell do Azure AD.
2. Determinar o valor de ObjectId da aplicação.
3. Atualize o URL da home page da aplicação utilizar comandos do PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Instalar o módulo do PowerShell do Azure AD

Antes de definir um URL de página inicial personalizada com o PowerShell, instale o módulo do PowerShell do Azure AD. Pode transferir o pacote a partir da [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), que utiliza o ponto de extremidade do Graph API.

Para instalar o pacote, siga estes passos:

1. Abra uma janela do PowerShell padrão e, em seguida, execute o seguinte comando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Se estiver a executar o comando como um não-administrador, utilize o `-scope currentuser` opção.

2. Durante a instalação, selecione **Y** para instalar dois pacotes de Nuget.org. Ambos os pacotes são necessários.

### <a name="find-the-objectid-of-the-app"></a>Localizar o ObjectId da aplicação

Obtenha o ObjectId da aplicação ao procurar a aplicação através do nome de exibição ou página inicial.

1. Na mesma janela do PowerShell, importe o módulo do Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

2. Inicie sessão no módulo do Azure AD como o administrador de inquilino.

   ```powershell
   Connect-AzureAD
   ```

3. Localize a aplicação. Este exemplo utiliza o PowerShell para localizar o ObjectId ao procurar a aplicação com um nome a apresentar do `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Deve obter um resultado semelhante à mostrada aqui. Copie o GUID de ObjectId para utilizar na secção seguinte.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Em alternativa, poderia apenas receber uma lista de todas as aplicações, pesquise a lista para a aplicação com um nome de apresentação específico ou a home page e copiar o ObjectId da aplicação depois da aplicação for encontrada.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Atualizar o URL da home page

Crie o URL da home page e atualizar a sua aplicação com esse valor. Continuar a utilizar a mesma janela do PowerShell, ou se estiver a utilizar uma nova janela do PowerShell, inicie sessão no módulo do Azure AD novamente usando `Connect-AzureAD`. Em seguida, siga estes passos:

1. Crie uma variável para conter o valor de ObjectId que copiou na secção anterior. (Substitua o valor de ObjectId utilizado para este exemplo do SharePoint com o valor de ObjectId da sua aplicação).

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

2. Confirme que tem a aplicação correta, executando o seguinte comando. O resultado deve ser idêntico à saída que viu na secção anterior ([localizar o ObjectId da aplicação](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

3. Crie um objeto de aplicativo em branco para manter as alterações que deseja fazer.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

4. Defina o URL da home page para o valor que pretende. O valor tem de ser um caminho de subdomínio da aplicação publicada. Por exemplo, se alterar o URL da home page da `https://sharepoint-iddemo.msappproxy.net/` para `https://sharepoint-iddemo.msappproxy.net/hybrid/`, os utilizadores da aplicação vão diretamente para a home page personalizada.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

5. Fazer a atualização da home page.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

6. Para confirmar que a alteração foi efetuada com êxito, execute o seguinte comando do passo 2 novamente.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   No nosso exemplo, o resultado deverá ser exibida da seguinte forma:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

7. Reinicie a aplicação para confirmar que a home page, aparece como o primeiro ecrã, conforme o esperado.

>[!NOTE]
>Quaisquer alterações que fizer para a aplicação poderão repor o URL da home page. Se repõe o seu URL da home page, repita os passos nesta secção para configurá-lo novamente.

## <a name="next-steps"></a>Passos Seguintes

- [Ativar o acesso remoto ao SharePoint com o Proxy de aplicações do Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Tutorial: Adicionar uma aplicação no local para acesso remoto através do Proxy de aplicações no Azure Active Directory](application-proxy-add-on-premises-application.md)
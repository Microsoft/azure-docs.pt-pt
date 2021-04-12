---
title: Implemente uma ferramenta de gestão para o Windows Virtual Desktop (clássico) utilizando o principal do serviço - Azure
description: Como implementar a ferramenta de gestão para o Windows Virtual Desktop (clássico) utilizando o PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 60eec4580e222123795db9554f56e74cb01c5257
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444450"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-powershell"></a>Implementar uma ferramenta de gestão virtual do Windows Desktop (clássica) com o PowerShell

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows.

Este artigo irá mostrar-lhe como implantar a ferramenta de gestão utilizando o PowerShell.

## <a name="important-considerations"></a>Considerações importantes

Cada subscrição do Azure Ative Directory (Azure AD) precisa da sua própria implementação separada da ferramenta de gestão. Esta ferramenta não suporta cenários Azure AD Business-to-Business (B2B).

Esta ferramenta de gestão é uma amostra. A Microsoft fornecerá importantes atualizações de segurança e qualidade. [O código fonte está disponível no GitHub.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy) Quer seja cliente ou parceiro, encorajamo-lo a personalizar a ferramenta para satisfazer as necessidades do seu negócio.

Os seguintes navegadores são compatíveis com a ferramenta de gestão:

- Google Chrome 68 ou mais tarde
- Microsoft Edge 40.15063 ou mais tarde
- Mozilla Firefox 52.0 ou mais tarde
- Safari 10 ou mais tarde (apenas para macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>O que precisa para implementar a ferramenta de gestão

Antes de implementar a ferramenta de gestão, precisará de um utilizador do Azure Ative Directory (Azure AD) para criar um registo de aplicações e implementar a UI de gestão. Este utilizador deve:

- Tenha permissão para criar recursos na sua subscrição Azure
- Tenha permissão para criar uma aplicação AD Azure. Siga estes passos para verificar se o seu utilizador tem as permissões necessárias seguindo as instruções nas [permissões Requeridas](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Depois de implementar e configurar a ferramenta de gestão, recomendamos que peça a um utilizador que lance a UI de gestão para se certificar de que tudo funciona. O utilizador que lançar o UI de gestão deve ter uma atribuição de função que lhes permite ver ou editar o inquilino virtual do Windows Desktop.

## <a name="set-up-powershell"></a>Configurar PowerShell

Começa por iniciar sessão nos módulos Az e Azure AD PowerShell. Eis como se inscreve:

1. Abra a PowerShell como administrador e navegue para o diretório onde guardou os scripts PowerShell.
2. Inscreva-se no Azure com uma conta que tenha permissões de Proprietário ou Contribuinte na subscrição Azure que pretende utilizar para criar a ferramenta de gestão executando o seguinte cmdlet:

    ```powershell
    Login-AzAccount
    ```

3. Executar o seguinte cmdlet para iniciar sação no Azure AD com a mesma conta que usou para o módulo Az PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. Depois disso, navegue para a pasta onde guardou os dois scripts PowerShell do RDS-Templates gitHub repo.

Mantenha a janela PowerShell que usou para iniciar um acordo aberto para executar cmdlets PowerShell adicionais durante a inscrição.

## <a name="create-an-azure-active-directory-app-registration"></a>Criar um registo de aplicativos Azure Ative Directory

Para implementar e configurar com sucesso a ferramenta de gestão, primeiro precisa de baixar os seguintes scripts PowerShell a partir do [repo GitHub-Modelos RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts)

```powershell
Set-Location -Path "c:\temp"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/createWvdMgmtUxAppRegistration.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\createWvdMgmtUxAppRegistration.ps1"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/updateWvdMgmtUxApiUrl.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\updateWvdMgmtUxApiUrl.ps1"
```

Executar os seguintes comandos para criar o registo da aplicação com permissões API necessárias:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$azureSubscription = Get-AzSubscription | Out-GridView -PassThru
$subscriptionId = $azureSubscription.Id
Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Agora que completou o registo da aplicação AD AZure, pode implementar a ferramenta de gestão.

## <a name="deploy-the-management-tool"></a>Implementar a ferramenta de gestão

Executar os seguintes comandos PowerShell para implantar a ferramenta de gestão e associá-la ao principal de serviço que acabou de criar:

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Depois de ter criado a aplicação web, tem de adicionar um URI redirecionado à aplicação AD Azure para iniciar súmis com sucesso nos utilizadores.

## <a name="set-the-redirect-uri"></a>Definir o URI de redirecionamento

Executar os seguintes comandos PowerShell para recuperar o URL da aplicação web e defini-lo como o redirecionamento de autenticação URI (também chamado de URL de resposta):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri
```

Agora que adicionou um URI de redirecionamento, precisa de atualizar o URL da API para que a ferramenta de gestão possa interagir com o serviço de backend da API.

## <a name="update-the-api-url-for-the-web-application"></a>Atualizar o URL da API para a aplicação web

Execute o seguinte script para atualizar a configuração de URL da API na extremidade frontal da aplicação web:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Agora que configura completamente a aplicação web da ferramenta de gestão, é hora de verificar a aplicação AD Azure e fornecer consentimento.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Verifique o pedido de AD Azure e forneça o seu consentimento

Para verificar a configuração da aplicação AD Azure e fornecer consentimento:

1. Abra o seu navegador de Internet e inscreva-se no [portal Azure](https://portal.azure.com/) com a sua conta administrativa.
2. A partir da barra de pesquisa no topo do portal Azure, procure **registos de Aplicações** e selecione o item em **Serviços.**
3. Selecione **todas as aplicações** e procure o nome de aplicação único que forneceu para o script PowerShell em [Criar um registo de aplicações Azure Ative Directory](#create-an-azure-active-directory-app-registration).
4. No painel do lado esquerdo do navegador, selecione **Autenticação** e certifique-se de que o URI de redirecionamento é o mesmo que o URL da aplicação web para a ferramenta de gestão, como mostra a imagem seguinte.

   [![A página de autenticação com o URI ](../media/management-ui-redirect-uri-inline.png) de redirecionamento introduzido](../media/management-ui-redirect-uri-expanded.png#lightbox)

5. No painel esquerdo, selecione **permissões da API** para confirmar que foram adicionadas permissões. Se for administrador global, selecione o **consentimento de `tenantname` administração grant para** o botão e siga as instruções de diálogo para fornecer o consentimento administrativo para a sua organização.

    [![A página ](../media/management-ui-permissions-inline.png) de permissões da API](../media/management-ui-permissions-expanded.png#lightbox)

Pode agora começar a usar a ferramenta de gestão.

## <a name="use-the-management-tool"></a>Utilize a ferramenta de gestão

Agora que montou a ferramenta de gestão a qualquer momento, pode lançá-la a qualquer momento, em qualquer lugar. Eis como lançar a ferramenta:

1. Abra o URL da aplicação web num navegador web. Se não se lembrar do URL, pode iniciar sação no Azure, encontrar o serviço de aplicação que implementou para a ferramenta de gestão e, em seguida, selecionar o URL.
2. Inscreva-se usando as suas credenciais de ambiente de trabalho virtual do Windows.

   > [!NOTE]
   > Se não concedeu o consentimento administrativo enquanto configura a ferramenta de gestão, cada utilizador que se inscreve terá de fornecer o seu próprio consentimento para o utilizador para utilizar a ferramenta.

3. Quando solicitado para escolher um grupo de inquilinos, selecione **o Grupo de Inquilinos Predefinidos** da lista de suspensos.
4. Quando selecionar **o Grupo inquilino predefinido,** deve aparecer um menu no lado esquerdo da janela. Neste menu, encontre o nome do seu grupo de inquilinos e selecione-o.

   > [!NOTE]
   > Se tiver um grupo de inquilinos personalizados, insira o nome manualmente em vez de escolher a partir da lista de drop-down.

## <a name="report-issues"></a>Comunicar problemas

Se encontrar problemas com a ferramenta de gestão ou outras ferramentas de ambiente de trabalho virtual do Windows, siga as instruções nos [modelos do Gestor de Recursos Azure para serviços de ambiente de trabalho remoto](https://github.com/Azure/RDS-Templates/blob/master/README.md) para os reportar no GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implantar e conectar-se à ferramenta de gestão, pode aprender a usar a Azure Service Health para monitorizar problemas de serviço e avisos de saúde. Para saber mais, consulte o nosso [serviço de alertas de serviço tutorial.](set-up-service-alerts-2019.md)

---
title: Implementar uma ferramenta de gestão para o Windows Virtual Desktop utilizando o principal de serviço - Azure
description: Como implementar a ferramenta de gestão para o Windows Virtual Desktop utilizando o PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127789"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Implementar uma ferramenta de gestão com a PowerShell

Este artigo irá mostrar-lhe como implementar a ferramenta de gestão utilizando o PowerShell.

## <a name="important-considerations"></a>Considerações importantes

A subscrição de cada inquilino azure Ative Directory (Azure AD) precisa da sua própria implantação separada da ferramenta de gestão. Esta ferramenta não suporta cenários Azure AD Business-to-Business (B2B). 

Esta ferramenta de gestão é uma amostra. A Microsoft fornecerá importantes atualizações de segurança e qualidade. [O código fonte está disponível no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Seja cliente ou parceiro, encorajamo-lo a personalizar a ferramenta para satisfazer as necessidades do seu negócio.

Os seguintes navegadores são compatíveis com a ferramenta de gestão:

- Google Chrome 68 ou mais tarde
- Microsoft Edge 40.15063 ou mais tarde
- Mozilla Firefox 52.0 ou mais tarde
- Safari 10 ou mais tarde (apenas macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>O que precisa para implementar a ferramenta de gestão

Antes de implementar a ferramenta de gestão, vai precisar de um utilizador do Azure Ative Directory (Azure AD) para criar um registo de aplicações e implementar a UI de gestão. Este utilizador deve:

- Tenha permissão para criar recursos na sua subscrição Azure
- Tenha permissão para criar uma aplicação Azure AD. Siga estes passos para verificar se o utilizador tem as permissões necessárias seguindo as instruções em [permissões requeridas](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Para implementar e configurar com sucesso a ferramenta de gestão, primeiro precisa de descarregar os seguintes scripts PowerShell do [repo DE Modelos RDS gitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) e guardá-los para a mesma pasta na sua máquina local.

  - criarWvdMgmtUxAppRegistration.ps1
  - atualizaçãoWvdMgmtUxApiUrl.ps1

Depois de implementar e configurar a ferramenta de gestão, recomendamos que peça a um utilizador que lance o UI de gestão para garantir que tudo funcione. O utilizador que lançar a UI de gestão deve ter uma atribuição de funções que lhes permita visualizar ou editar o inquilino do Windows Virtual Desktop.

## <a name="set-up-powershell"></a>Configurar powerShell

Inicie a sessão nos módulos Az e Azure AD PowerShell. Eis como fazer o sessão:

1. Abra a PowerShell como Administrador e navegue para o diretório onde guardou os scripts PowerShell.
2. Inscreva-se no Azure com uma conta que tenha permissões do Proprietário ou do Colaborador na subscrição do Azure que pretende utilizar para criar a ferramenta de gestão executando o seguinte cmdlet:

    ```powershell
    Login-AzAccount
    ```

3. Execute o seguinte cmdlet para iniciar sessão no Azure AD com a mesma conta que utilizou para o módulo Az PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. Depois disso, navegue para a pasta onde guardou os dois scripts PowerShell do repo DO RS-Templates GitHub.

Mantenha a janela PowerShell que usou para assinar abertamente para executar cmdlets powerShell adicionais durante a sua inscrição.

## <a name="create-an-azure-active-directory-app-registration"></a>Criar uma inscrição de aplicativo de Diretório Ativo Azure

Executar os seguintes comandos para criar o registo da aplicação com permissões API necessárias:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Agora que concluiu o registo da aplicação Azure AD, pode implementar a ferramenta de gestão.

## <a name="deploy-the-management-tool"></a>Implementar a ferramenta de gestão

Executar os seguintes comandos PowerShell para implementar a ferramenta de gestão e associá-la ao diretor de serviço que acabou de criar:
     
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

Depois de ter criado a aplicação web, deve adicionar um URI redirecionado à aplicação Azure AD para assinar com sucesso nos utilizadores.

## <a name="set-the-redirect-uri"></a>Definir o Redirecionamento URI

Executar os seguintes comandos PowerShell para recuperar o URL da aplicação web e defini-lo como o redirecionamento de autenticação URI (também chamado de URL de resposta):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Agora que adicionou um URI redirecionado, terá de atualizar o URL DaPI para que a ferramenta de gestão possa interagir com o serviço de backend API.

## <a name="update-the-api-url-for-the-web-application"></a>Atualizar o URL DaPi para a aplicação web

Executar o seguinte script para atualizar a configuração do URL DaPi na extremidade frontal da aplicação web:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Agora que configura completamente a aplicação web da ferramenta de gestão, é hora de verificar a aplicação Azure AD e dar consentimento.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Verifique o pedido da AD Azure e dê consentimento

Para verificar a configuração da aplicação Azure AD e dar consentimento:

1. Abra o seu navegador de Internet e inscreva-se no [portal Azure](https://portal.azure.com/) com a sua conta administrativa.
2. A partir da barra de pesquisa no topo do portal Azure, procure registos de **Aplicações** e selecione o item em **Serviços**.
3. **Selecione todas as aplicações** e procure o nome único da aplicação que forneceu para o script PowerShell no [Create a Azure Ative Directory .](#create-an-azure-active-directory-app-registration)
4. No painel do lado esquerdo do navegador, selecione **Autenticação** e certifique-se de que o URI redirecionamento é o mesmo que o URL da aplicação web para a ferramenta de gestão, como mostra a imagem seguinte.
   
   [![A página de autenticação com](media/management-ui-redirect-uri-inline.png) o URI redirecionado introduzido](media/management-ui-redirect-uri-expanded.png#lightbox)

5. No painel esquerdo, selecione **permissões DaPI** para confirmar que foram adicionadas permissões. Se você é um administrador global, selecione o consentimento do **administrador Grant para `tenantname` ** o botão e siga as instruções de diálogo para fornecer consentimento administrativo para a sua organização.
    
    [![A página de](media/management-ui-permissions-inline.png) permissões da API](media/management-ui-permissions-expanded.png#lightbox)

Agora pode começar a usar a ferramenta de gestão.

## <a name="use-the-management-tool"></a>Use a ferramenta de gestão

Agora que montou a ferramenta de gestão a qualquer momento, pode lançá-la a qualquer momento, em qualquer lugar. Eis como lançar a ferramenta:

1. Abra o URL da aplicação web num navegador web. Se não se lembrar do URL, pode iniciar sessão no Azure, encontrar o serviço de aplicações que implementou para a ferramenta de gestão e, em seguida, selecionar o URL.
2. Inscreva-se utilizando as suas credenciais de ambiente de trabalho virtual do Windows.
   
   > [!NOTE]
   > Se não concedeu o consentimento da administração enquanto configuraa a ferramenta de gestão, cada utilizador que se instássemos terá de fornecer o seu próprio consentimento do utilizador para utilizar a ferramenta.

3. Quando solicitado a escolher um grupo de inquilinos, selecione **Default Tenant Group** da lista de abandono.
4. Ao selecionar O **Grupo de Inquilinos Padrão,** deve aparecer um menu no lado esquerdo da sua janela. Neste menu, encontre o nome do seu grupo de inquilinos e selecione-o.
   
   > [!NOTE]
   > Se tiver um grupo de inquilinos personalizados, insira o nome manualmente em vez de escolher a partir da lista de lançamentos.

## <a name="report-issues"></a>Comunicar problemas

Se encontrar algum problema com a ferramenta de gestão ou outras ferramentas de ambiente de trabalho virtual do Windows, siga as instruções nos modelos do Gestor de [Recursos Azure para serviços](https://github.com/Azure/RDS-Templates/blob/master/README.md) de ambiente de trabalho remotos para os reportar no GitHub.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implementar e conectar-se com a ferramenta de gestão, pode aprender a usar a Azure Service Health para monitorizar problemas de serviço e aconselhamento de saúde. Para saber mais, consulte o tutorial de alertas de [serviço Configuração.](./set-up-service-alerts.md)

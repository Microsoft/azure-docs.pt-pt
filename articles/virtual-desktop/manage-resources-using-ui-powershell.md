---
title: Implantar uma ferramenta de gerenciamento para a área de trabalho virtual do Windows usando a entidade de serviço-Azure
description: Como implantar a ferramenta de gerenciamento para a área de trabalho virtual do Windows usando o PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
ms.openlocfilehash: d7304c1267c4a4f5548bb57ffb3e6016fac21d99
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122513"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Implantar uma ferramenta de gerenciamento com o PowerShell

Este artigo mostrará como implantar a ferramenta de gerenciamento usando o PowerShell.

## <a name="important-considerations"></a>Considerações importantes

Cada assinatura de locatário do Azure Active Directory (AD do Azure) precisa de sua própria implantação separada da ferramenta de gerenciamento. Esta ferramenta não dá suporte a cenários B2B (Business-to-Business) do Azure AD. 

Essa ferramenta de gerenciamento é um exemplo. A Microsoft fornecerá atualizações importantes de segurança e qualidade. [O código-fonte está disponível no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Seja você um cliente ou parceiro, incentivamos a personalização da ferramenta para atender às suas necessidades de negócios.

Os navegadores a seguir são compatíveis com a ferramenta de gerenciamento do:

- Google Chrome 68 ou posterior
- Microsoft Edge 40,15063 ou posterior
- Mozilla Firefox 52,0 ou posterior
- Safari 10 ou posterior (somente macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>O que você precisa para implantar a ferramenta de gerenciamento

Antes de implantar a ferramenta de gerenciamento, você precisará de um usuário Azure Active Directory (Azure AD) para criar um registro de aplicativo e implantar a interface do usuário de gerenciamento. Esse usuário deve:

- Ter permissão para criar recursos em sua assinatura do Azure
- Ter permissão para criar um aplicativo do Azure AD. Siga estas etapas para verificar se o usuário tem as permissões necessárias seguindo as instruções em [permissões necessárias](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

Para implantar e configurar com êxito a ferramenta de gerenciamento, primeiro você precisa baixar os seguintes scripts do PowerShell do repositório do [GitHub de modelos de RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) e salvá-los na mesma pasta em seu computador local.

  - createWvdMgmtUxAppRegistration. ps1
  - updateWvdMgmtUxApiUrl. ps1

Depois de implantar e configurar a ferramenta de gerenciamento, é recomendável solicitar que um usuário inicie a interface do usuário de gerenciamento para verificar se tudo funciona. O usuário que inicia a interface do usuário de gerenciamento deve ter uma atribuição de função que permita exibir ou editar o locatário da área de trabalho virtual do Windows.

## <a name="set-up-powershell"></a>Configurar o PowerShell

Comece entrando no módulo do PowerShell AZ e Azure AD. Veja como entrar:

1. Abra o PowerShell como administrador e navegue até o diretório em que você salvou os scripts do PowerShell.
2. Entre no Azure com uma conta que tenha permissões de proprietário ou colaborador na assinatura do Azure que você planeja usar para criar a ferramenta de gerenciamento executando o seguinte cmdlet:

    ```powershell
    Login-AzAccount
    ```

3. Execute o seguinte cmdlet para entrar no Azure AD com a mesma conta usada para o módulo AZ PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. Depois disso, navegue até a pasta em que você salvou os dois scripts do PowerShell do repositório GitHub de modelos de RDS.

Mantenha a janela do PowerShell que você usou para entrar em aberto para executar cmdlets do PowerShell adicionais enquanto estiver conectado.

## <a name="create-an-azure-active-directory-app-registration"></a>Criar um registro de aplicativo Azure Active Directory

Execute os seguintes comandos para criar o registro do aplicativo com as permissões de API necessárias:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Agora que você concluiu o registro do aplicativo do Azure AD, você pode implantar a ferramenta de gerenciamento.

## <a name="deploy-the-management-tool"></a>Implementar a ferramenta de gestão

Execute os seguintes comandos do PowerShell para implantar a ferramenta de gerenciamento e associá-la à entidade de serviço que você acabou de criar:
     
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

Depois de criar o aplicativo Web, você deve adicionar um URI de redirecionamento ao aplicativo do Azure AD para conectar com êxito os usuários.

## <a name="set-the-redirect-uri"></a>Definir o URI de redirecionamento

Execute os comandos do PowerShell a seguir para recuperar a URL do aplicativo Web e defini-la como o URI de redirecionamento de autenticação (também chamado de URL de resposta):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Agora que você adicionou um URI de redirecionamento, você precisa atualizar a URL da API para que a ferramenta de gerenciamento possa interagir com o serviço de back-end de API.

## <a name="update-the-api-url-for-the-web-application"></a>Atualizar a URL da API para o aplicativo Web

Execute o script a seguir para atualizar a configuração de URL da API no front-end do aplicativo Web:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Agora que você configurou totalmente o aplicativo Web da ferramenta de gerenciamento, é hora de verificar o aplicativo do Azure AD e fornecer consentimento.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Verificar o aplicativo Azure AD e fornecer consentimento

Para verificar a configuração de aplicativo do Azure AD e fornecer consentimento:

1. Abra seu navegador da Internet e entre no [portal do Azure](https://portal.azure.com/) com sua conta administrativa.
2. Na barra de pesquisa na parte superior da portal do Azure, procure **registros de aplicativo** e selecione o item em **Serviços**.
3. Selecione **todos os aplicativos** e pesquise o nome exclusivo do aplicativo fornecido para o script do PowerShell em [criar um Azure Active Directory registro de aplicativo](#create-an-azure-active-directory-app-registration).
4. No painel no lado esquerdo do navegador, selecione **autenticação** e verifique se o URI de redirecionamento é o mesmo da URL do aplicativo Web para a ferramenta de gerenciamento, conforme mostrado na imagem a seguir.
   
   [![a página de autenticação com o URI de redirecionamento inserido](media/management-ui-redirect-uri-inline.png)](media/management-ui-redirect-uri-expanded.png#lightbox)

5. No painel esquerdo, selecione **permissões de API** para confirmar que as permissões foram adicionadas. Se você for um administrador global, selecione o botão **conceder consentimento de administrador para `tenantname`** e siga os prompts de diálogo para fornecer consentimento de administrador para sua organização.
    
    [![a página permissões de API](media/management-ui-permissions-inline.png)](media/management-ui-permissions-expanded.png#lightbox)

Agora você pode começar a usar a ferramenta de gerenciamento.

## <a name="use-the-management-tool"></a>Usar a ferramenta de gerenciamento

Agora que você configurou a ferramenta de gerenciamento a qualquer momento, poderá iniciá-la a qualquer momento, em qualquer lugar. Veja como iniciar a ferramenta:

1. Abra a URL do aplicativo Web em um navegador da Web. Se você não se lembrar da URL, poderá entrar no Azure, localizar o serviço de aplicativo implantado para a ferramenta de gerenciamento e, em seguida, selecionar a URL.
2. Entre usando suas credenciais de área de trabalho virtual do Windows.
   
   > [!NOTE]
   > Se você não concedeu consentimento de administrador ao configurar a ferramenta de gerenciamento, cada usuário que se conectar precisará fornecer seu próprio consentimento de usuário para usar a ferramenta.

3. Quando for solicitado a escolher um grupo de locatários, selecione **grupo de locatários padrão** na lista suspensa.
4. Quando você seleciona **grupo de locatários padrão**, um menu deve aparecer no lado esquerdo da janela. Neste menu, localize o nome do seu grupo de locatários e selecione-o.
   
   > [!NOTE]
   > Se você tiver um grupo de locatários personalizado, insira o nome manualmente em vez de escolher na lista suspensa.

## <a name="report-issues"></a>Relatar problemas

Se você tiver problemas com a ferramenta de gerenciamento ou outras ferramentas de área de trabalho virtual do Windows, siga as instruções em [modelos de Azure Resource Manager para serviços de área de trabalho remota](https://github.com/Azure/RDS-Templates/blob/master/README.md) para relatá-las no github.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como implantar e se conectar à ferramenta de gerenciamento, você pode aprender a usar a ajuda do serviço do Azure para monitorar problemas de serviço e comunicados de integridade. Para saber mais, consulte nosso [tutorial de configuração de alertas de serviço](./set-up-service-alerts.md).

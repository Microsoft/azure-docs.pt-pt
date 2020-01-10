---
title: Enviar um email de um runbook de automação do Azure
description: Saiba como usar o SendGrid para enviar um email de dentro de um runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: 6acb68b7bbaa54db2e4143a42e43aede2caed35f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420695"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Tutorial: enviar um email de um runbook de automação do Azure

Você pode enviar um email de um runbook com o [SendGrid](https://sendgrid.com/solutions) usando o PowerShell. Este tutorial mostrará como criar um runbook reutilizável que envia um email usando uma chave de API armazenada no cofre de chaves [do Azure](/azure/key-vault/).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar um cofre de chaves do Azure
> * Armazenar sua chave de API do SendGrid no keyvault
> * Criar um runbook que recupere sua chave de API e envie um email

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário o seguinte:

* Assinatura do Azure: se você ainda não tiver um, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Crie uma conta do SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Conta de automação](automation-offering-get-started.md) com módulos **AZ** e a [conexão executar como](automation-create-runas-account.md)para armazenar e executar o runbook.

## <a name="create-an-azure-keyvault"></a>Criar um cofre de chaves do Azure

Você pode criar um cofre de chaves do Azure usando o seguinte script do PowerShell. Substitua os valores de variáveis por valores específicos do seu ambiente. Use o Azure Cloud Shell inserido por meio do botão <kbd>experimentar</kbd> , localizado no canto superior direito do bloco de código. Você também pode copiar e executar o código localmente se tiver o [módulo Azure PowerShell](/powershell/azure/install-az-ps) instalado no computador local.

> [!NOTE]
> Para recuperar sua chave de API, use as etapas encontradas em [localizar sua chave de API do SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Para outras maneiras de criar um cofre de chaves do Azure e armazenar um segredo, consulte [início rápido do keyvault](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importar módulos necessários para sua conta de automação

Para usar o Azure keyvault em um runbook, sua conta de automação precisará dos seguintes módulos:

* [AZ. Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [AZ. keyvault](https://www.powershellgallery.com/packages/Az.KeyVault).

Clique em <kbd>implantar na automação do Azure</kbd> na guia automação do Azure em opções de instalação. Essa ação abre a portal do Azure. Na página importar, selecione sua conta de automação e clique em <kbd>OK</kbd>.

Para obter métodos adicionais para adicionar os módulos necessários, consulte [Importar módulos](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Criar o runbook para enviar um email

Depois de ter criado um keyvault e armazenado sua chave de API do SendGrid, é hora de criar o runbook que recuperará a chave de API e enviará um email.

Esse runbook usa a [conta Executar como](automation-create-runas-account.md) AzureRunAsConnection para autenticar com o Azure para recuperar o segredo do Azure keyvault.

Use este exemplo para criar um runbook chamado **Send-GridMailMessage**. Você pode modificar o script do PowerShell e reutilizá-lo para diferentes cenários.

1. Vá para sua conta de automação do Azure.
2. Em **automação de processo**, selecione **Runbooks**.
3. Na parte superior da lista de runbooks, selecione **+ criar um runbook**.
4. Na página **Adicionar runbook** , digite **Send-GridMailMessage** para o nome do runbook. Para o tipo de runbook, selecione **PowerShell**. Em seguida, selecione **Criar**.
   ![criar](./media/automation-send-email/automation-send-email-runbook.png) de runbook
5. O runbook é criado e a página **Editar Runbook do PowerShell** é aberta.
   ![editar o runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Copie o exemplo do PowerShell a seguir na página **Editar** . Verifique se o `$VaultName` é o nome que você especificou ao criar o seu cofre de chaves.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Selecione **publicar** para salvar e publicar o runbook.

Para verificar se o runbook é executado com êxito, você pode seguir as etapas em [testar um runbook](manage-runbooks.md#test-a-runbook) ou [Iniciar um runbook](start-runbooks.md).
Se você não vir inicialmente seu email de teste, verifique suas pastas de **lixo eletrônico** e **spam** .

## <a name="clean-up"></a>Limpar

Quando já não precisar, elimine o runbook. Para tal, selecione o runbook na lista de runbooks e clique em **Eliminar**.

Exclua o cofre de chaves usando o cmdlet [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) .

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

* Para problemas ao criar ou iniciar o runbook, consulte [solucionar erros com runbooks](./troubleshoot/runbooks.md).
* Para atualizar os módulos em sua conta de automação, consulte [como atualizar os módulos de Azure PowerShell na automação do Azure](automation-update-azure-modules.md)].
* Para monitorar a execução do runbook, consulte [encaminhar o status do trabalho e fluxos de trabalho da automação para os logs do Azure monitor](automation-manage-send-joblogs-log-analytics.md).
* Para disparar um runbook usando um alerta, consulte [usar um alerta para disparar um runbook de automação do Azure](automation-create-alert-triggered-runbook.md).

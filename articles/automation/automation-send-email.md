---
title: Enviar um e-mail a partir de um runbook da automatização do Azure
description: Saiba como utilizar o SendGrid para enviar um e-mail a partir de um runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce05aadb53cc3ad24ed65ea139594010e1aef047
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234984"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Tutorial: Enviar um e-mail a partir de um runbook da automatização do Azure

Pode enviar um e-mail a partir de um runbook com [SendGrid](https://sendgrid.com/solutions) com o PowerShell. Este tutorial mostra-lhe como criar um runbook reutilizável que envia um e-mail com uma chave de API armazenada num [Azure KeyVault](/azure/key-vault/).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar um cofre de chaves do Azure
> * Store sua chave de API do SendGrid no Cofre de chaves
> * Criar um runbook que obtém a chave de API e envia um e-mail

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário o seguinte:

* Subscrição do Azure: Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Criar uma conta do SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Conta de automatização](automation-offering-get-started.md) com **Az** módulos, e [ligação Run as](automation-create-runas-account.md), para armazenar e executar o runbook.

## <a name="create-an-azure-keyvault"></a>Criar um cofre de chaves do Azure

Pode criar um cofre de chaves do Azure com o seguinte script do PowerShell. Substitua os valores das variáveis com valores específicos ao seu ambiente. Utilizar o Azure Cloud Shell embedded através da <kbd>experimentar</kbd> botão, localizado no canto superior direito do bloco de código. Também pode copiar e executar o código localmente, se tiver o [módulo do PowerShell do Azure](/powershell/azure/install-az-ps) instalado no seu computador local.

> [!NOTE]
> Para obter a chave de API, utilize as etapas descritas no [localizar a chave de API do SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

Para outras formas de criar um cofre de chaves do Azure e armazenar um segredo, consulte [inícios rápidos de KeyVault](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importar os módulos necessários para a sua conta de automatização

Para utilizar o Cofre de chaves do Azure dentro de um runbook, a sua conta de automatização terá os seguintes módulos:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault).

Clique em <kbd>implementar a automatização do Azure</kbd> no separador de automatização do Azure em Opções de instalação. Esta ação abre o portal do Azure. Na página de importação, selecione a sua conta de automatização e clique em <kbd>OK</kbd>.

Para obter métodos adicionais para adicionar os módulos necessários, consulte [importar módulos](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Criar o runbook para enviar um e-mail

Depois de ter criado um cofre de chaves e armazenada a chave de API do SendGrid, é hora de criar o runbook que irá obter a chave de API e enviar um e-mail.

Este runbook utiliza a AzureRunAsConnection [conta Run As](automation-create-runas-account.md) para autenticar com o Azure para recuperar o segredo do Cofre de chaves do Azure.

Utilize este exemplo para criar um runbook denominado **Send GridMailMessage**. Pode modificar o script do PowerShell e reutilizá-lo para diferentes cenários.

1. Aceda à sua conta de automatização do Azure.
2. Sob **automatização de processos**, selecione **Runbooks**.
3. Na parte superior da lista de runbooks, selecione **+ criar um runbook**.
4. Sobre o **adicionar Runbook** página, introduza **Send GridMailMessage** para o nome do runbook. Para o tipo de runbook, selecione **PowerShell**. Em seguida, selecione **Criar**.
   ![Criar Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. O runbook é criado e a página **Editar Runbook do PowerShell** é aberta.
   ![Editar o Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Copie o seguinte exemplo do PowerShell para o **editar** página. Certifique-se de que o `$VaultName` é o nome que especificou quando criou o seu Cofre de chaves.

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

7. Selecione **publicar** para guardar e publicar o runbook.

Para verificar que o runbook seja executado com êxito pode seguir os passos em [testar um runbook](manage-runbooks.md#test-a-runbook) ou [iniciar um runbook](start-runbooks.md).
Se não vir inicialmente o seu e-mail de teste, verifique sua **lixo** e **Spam** pastas.

## <a name="clean-up"></a>Limpar

Quando já não precisar, elimine o runbook. Para tal, selecione o runbook na lista de runbooks e clique em **Eliminar**.

Eliminar o Cofre de chaves com o [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) cmdlet.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Passos Seguintes

* Para problemas de criação ou ao iniciar o runbook, consulte [resolver problemas de erros com runbooks](./troubleshoot/runbooks.md).
* Para atualizar os módulos na conta de automatização, consulte [como atualizar módulos do Azure PowerShell na automatização do Azure](automation-update-azure-modules.md)].
* Para monitorizar a execução de runbooks, consulte [reencaminhar o estado da tarefa e fluxos de trabalho de automatização para registos do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).
* Para acionar um runbook com um alerta, consulte [utilize um alerta para acionar um runbook da automatização do Azure](automation-create-alert-triggered-runbook.md).

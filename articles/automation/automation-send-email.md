---
title: Envie um e-mail de um runbook da Azure Automation
description: Este artigo diz como enviar um e-mail de dentro de um livro de bordo.
services: automation
ms.subservice: process-automation
ms.date: 01/05/2021
ms.topic: conceptual
ms.openlocfilehash: 65fa226b368baa3b1d4f376600e610a518c48c02
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900326"
---
# <a name="send-an-email-from-a-runbook"></a>Enviar um e-mail a partir de um runbook

Pode enviar um e-mail de um livro com [a SendGrid](https://sendgrid.com/solutions) usando o PowerShell. 

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Uma conta SendGrid.](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account)
* [Conta de automação](./index.yml) com módulos **Az.**
* [Executar como conta](./manage-runas-account.md) para armazenar e executar o livro de execução.

## <a name="create-an-azure-key-vault"></a>Criar um Azure Key Vault

Pode criar um Cofre de Chaves Azure utilizando o seguinte script PowerShell. Substitua os valores variáveis por valores específicos do seu ambiente. Utilize o Azure Cloud Shell incorporado através do botão **Try It,** localizado no canto superior direito do bloco de código. Também pode copiar e executar o código localmente se tiver os [módulos Az instalados](/powershell/azure/install-az-ps) na sua máquina local.

> [!NOTE]
> Para recuperar a sua chave API, utilize os passos na [tecla SendGrid API](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key).

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

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Para outras formas de criar um Cofre de Chaves Azure e armazenar um segredo, consulte [os quickstarts do Key Vault](../key-vault/index.yml).

## <a name="import-required-modules-into-your-automation-account"></a>Importar módulos necessários na sua conta de Automação

Para utilizar o Cofre da Chave Azure dentro de um livro de recortes, tem de importar os seguintes módulos para a sua conta de Automação:

* [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Para obter instruções, consulte [os módulos Import Az](shared-resources/modules.md#import-az-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Crie o livro de corridas para enviar um e-mail

Depois de ter criado um Key Vault e armazenado a sua `SendGrid` chave API, é hora de criar o runbook que recupera a chave API e envia um e-mail. Vamos usar um runbook que usa `AzureRunAsConnection` como conta Run [As](./manage-runas-account.md) para autenticar com a Azure para recuperar o segredo do Azure Key Vault. Vamos chamar o runbook **Send-GridMailMessage.** Pode modificar o script PowerShell utilizado para fins de exemplo e reutilizá-lo para diferentes cenários.

1. Vá à sua conta Azure Automation.
2. Em **Automatização de Processos,** selecione **Runbooks**.
3. No topo da lista de runbooks, selecione **+ Crie um livro de bordo.**
4. Na página Add Runbook, **insira send-GridMailMessage** para o nome do livro de execuções. Para o tipo de livro de execução, selecione **PowerShell**. Em seguida, selecione **Criar**.
   ![Criar Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. O runbook é criado e a página Editar Runbook do PowerShell é aberta.
   ![Editar o Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Copie o exemplo PowerShell a seguir na página Editar. Certifique-se de que `VaultName` especifica o nome que escolheu para o seu Cofre de Chaves.

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
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValue
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

7. **Selecione Publicar** para guardar e publicar o livro de recortes.

Para verificar se o livro executa com sucesso, pode seguir os passos em [Test a runbook](manage-runbooks.md#test-a-runbook) ou [iniciar um livro de execução](start-runbooks.md).

Se não vir inicialmente o seu e-mail de teste, verifique as **pastas Junk** e **Spam.**

## <a name="clean-up-resources-after-the-email-operation"></a>Limpar recursos após a operação de e-mail

1. Quando o livro de execuções já não for necessário, selecione-o na lista de runbook e clique em **Eliminar**.

2. Elimine o cofre da chave utilizando o [cmdlet Remove-AzKeyVault.](/powershell/module/az.keyvault/remove-azkeyvault)

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

* Para enviar dados de trabalho de runbook para o seu espaço de trabalho Log Analytics, consulte [os dados de trabalho da Forward Azure Automation para os registos do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).
* Para monitorizar métricas e registos de nível básico, consulte [Utilize um alerta para acionar um livro de registo da Azure Automation](automation-create-alert-triggered-runbook.md).
* Para corrigir as questões que surgem durante as operações de runbook, consulte [as questões do livro de resolução de problemas](./troubleshoot/runbooks.md).

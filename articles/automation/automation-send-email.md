---
title: Envie um e-mail de um livro de execução da Automação Azure
description: Este artigo diz como enviar um e-mail de dentro de um livro de execução.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: conceptual
ms.openlocfilehash: a92f65bd88a5aec79a179a6e2d53de15c274add4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834565"
---
# <a name="send-an-email-from-a-runbook"></a>Enviar um e-mail a partir de um runbook

Pode enviar um e-mail a partir de um livro de execução com [sendGrid](https://sendgrid.com/solutions) usando powerShell. 

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Uma conta SendGrid.](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account)
* [Conta de automação](automation-offering-get-started.md) com módulos **Az.**
* [Executar Como conta](automation-create-runas-account.md) para armazenar e executar o livro de execução.

## <a name="create-an-azure-key-vault"></a>Criar um Azure Key Vault

Pode criar um Cofre de Chave Azure utilizando o seguinte script PowerShell. Substitua os valores variáveis por valores específicos do seu ambiente. Utilize a casca de nuvem azure incorporada através do botão **Try It,** localizado no canto superior direito do bloco de código. Também pode copiar e executar o código localmente se tiver os [módulos Az instalados](/powershell/azure/install-az-ps) na sua máquina local.

> [!NOTE]
> Para recuperar a sua chave API, utilize os passos em [Find your SendGrid API .](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key)

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

Para outras formas de criar um Cofre chave Azure e armazenar um segredo, consulte [quickstarts Key Vault](/azure/key-vault/).

## <a name="import-required-modules-into-your-automation-account"></a>Importar módulos necessários para a sua conta de Automação

Para utilizar o Cofre de Chaves Azure dentro de um livro de execução, deve importar os seguintes módulos para a sua conta Deautomação:

    * [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
    * [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Para obter instruções, consulte os [módulos Import Az](shared-resources/modules.md#import-az-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Crie o livro de corridas para enviar um e-mail

Depois de ter criado um Cofre chave e ter guardado a sua `SendGrid` chave API, é hora de criar o livro de corridas que recupera a chave API e envia um e-mail. Vamos usar um livro que usa `AzureRunAsConnection` como conta Run [As](automation-create-runas-account.md) para autenticar com o Azure para recuperar o segredo do Cofre de Chaves Azure. Vamos ligar para o livro de execução **Send-GridMailMessage**. Pode modificar o script PowerShell utilizado para fins por exemplo e reutilizá-lo para diferentes cenários.

1. Vá à sua conta de Automação Azure.
2. Em Fase **de Automação de Processos,** selecione **Runbooks**.
3. No topo da lista de livros de execução, selecione **+ Crie um livro de execução**.
4. Na página Add Runbook, introduza **send-GridMailMessage** para o nome do livro de execução. Para o tipo de livro de execução, selecione **PowerShell**. Em seguida, selecione **Criar**.
   ![Criar Livro de Corridas](./media/automation-send-email/automation-send-email-runbook.png)
5. O runbook é criado e a página Editar Runbook do PowerShell é aberta.
   ![Editar o Livro de Execução](./media/automation-send-email/automation-send-email-edit.png)
6. Copie o seguinte exemplo PowerShell na página Editar. Certifique-se de que o `VaultName` nome que escolheu para o seu Cofre chave.

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

7. Selecione **Publicar** para guardar e publicar o livro de execução.

Para verificar se o livro de execução executa com sucesso, pode seguir os passos em Teste de um livro de [execução](manage-runbooks.md#test-a-runbook) ou iniciar um livro de [execução](start-runbooks.md).

Se não vir inicialmente o seu e-mail de teste, verifique as suas pastas **Junk** e **Spam.**

## <a name="clean-up-resources-after-the-email-operation"></a>Limpar recursos após a operação de e-mail

1. Quando o livro de execução já não for necessário, selecione-o na lista de livros de execução e clique em **Eliminar**.

2. Elimine o Cofre da Chave utilizando o cmdlet [Remove-AzKeyVault.](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0)

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

* Para enviar dados de trabalho do livro de recortes para o seu espaço de trabalho Log Analytics, consulte os dados de trabalho da [Forward Azure Automation para os registos do Monitor Do Azure](automation-manage-send-joblogs-log-analytics.md).
* Para monitorizar métricas e registos de nível base, consulte Utilize um alerta para desencadear um livro de [execução da Automação Azure](automation-create-alert-triggered-runbook.md).
* Para corrigir as questões que surgem durante as operações do livro de corridas, consulte problemas de runbook da [Troubleshoot](./troubleshoot/runbooks.md).
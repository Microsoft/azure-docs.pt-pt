---
title: Envie um e-mail de um livro de execução da Automação Azure
description: Saiba como utilizar o SendGrid para enviar um e-mail de dentro de um livro de execução.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: 6acb68b7bbaa54db2e4143a42e43aede2caed35f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75420695"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Tutorial: Envie um e-mail de um livro de execução da Automação Azure

Pode enviar um e-mail a partir de um livro de execução com [sendGrid](https://sendgrid.com/solutions) usando powerShell. Este tutorial irá mostrar-lhe como criar um livro de execução reutilizável que envia um e-mail usando uma chave API armazenada no [Azure KeyVault](/azure/key-vault/).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar um Cofre de Chaves Azure
> * Guarde a sua chave SendGrid API no KeyVault
> * Crie um livro de corridas que recupere a sua chave API e envie um e-mail

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário o seguinte:

* Assinatura Azure: Se ainda não tiver um, pode ativar os seus benefícios de [subscrição mSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Criar uma conta SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Conta de automação](automation-offering-get-started.md) com módulos **Az,** e [Executar Como ligação,](automation-create-runas-account.md)para armazenar e executar o livro de execução.

## <a name="create-an-azure-keyvault"></a>Criar um Cofre de Chaves Azure

Pode criar um Azure KeyVault utilizando o seguinte script PowerShell. Substitua os valores variáveis por valores específicos do seu ambiente. Utilize a casca de nuvem azure incorporada através do botão <kbd>Try It,</kbd> localizado no canto superior direito do bloco de código. Também pode copiar e executar o código localmente se tiver o [Módulo PowerShell Azure](/powershell/azure/install-az-ps) instalado na sua máquina local.

> [!NOTE]
> Para recuperar a sua chave API, utilize os passos encontrados na [sua tecla SendGrid API](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

Para outras formas de criar um Azure KeyVault e armazenar um segredo, consulte [KeyVault Quickstarts](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importar módulos necessários para a sua Conta de Automação

Para utilizar o Azure KeyVault dentro de um livro de execução, a sua Conta de Automação necessitará dos seguintes módulos:

* [Az.Profile.](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault.](https://www.powershellgallery.com/packages/Az.KeyVault)

Clique em <kbd>Implementar para Automatização Azure</kbd> no separador Automação Azure em opções de instalação. Esta ação abre o portal Azure. Na página Import, selecione a sua Conta de Automação e clique <kbd>EM OK</kbd>.

Para obter métodos adicionais para adicionar os módulos necessários, consulte [módulos de importação](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Crie o livro de corridas para enviar um e-mail

Depois de ter criado um KeyVault e ter armazenado a sua chave SendGrid API, é hora de criar o livro de execução que irá recuperar a chave API e enviar um e-mail.

Este livro de execução utiliza o AzureRunAsConnection [Run Como conta](automation-create-runas-account.md) para autenticar com o Azure para recuperar o segredo do Azure KeyVault.

Use este exemplo para criar um livro chamado **Send-GridMailMessage**. Pode modificar o script PowerShell e reutilizá-lo para diferentes cenários.

1. Vá à sua conta de Automação Azure.
2. Em Fase **de Automação de Processos,** selecione **Runbooks**.
3. No topo da lista de livros de execução, selecione **+ Crie um livro de execução**.
4. Na página **Add Runbook,** introduza **send-GridMailMessage** para o nome do livro de execução. Para o tipo de livro de execução, selecione **PowerShell**. Em seguida, selecione **Criar**.
   ![Criar Livro de Corridas](./media/automation-send-email/automation-send-email-runbook.png)
5. O runbook é criado e a página **Editar Runbook do PowerShell** é aberta.
   ![Editar o Livro de Execução](./media/automation-send-email/automation-send-email-edit.png)
6. Copie o seguinte exemplo PowerShell na página **Editar.** Certifique-se `$VaultName` de que este é o nome que especificou quando criou o seu KeyVault.

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

## <a name="clean-up"></a>Limpeza

Quando já não precisar, elimine o runbook. Para tal, selecione o runbook na lista de runbooks e clique em **Eliminar**.

Elimine o cofre da chave utilizando o cmdlet [Remove-AzureRMKeyVault.](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps)

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

* Para problemas de criação ou início do seu livro de corridas, consulte erros de Resolução de [Problemas com livros de execução](./troubleshoot/runbooks.md).
* Para atualizar os módulos na sua Conta de Automação, consulte [como atualizar os módulos Azure PowerShell na Automação Azure].](automation-update-azure-modules.md)
* Para monitorizar a execução do livro de execução, consulte o estado de [trabalho para a frente e os fluxos de trabalho da Automação para os registos do Monitor Azure](automation-manage-send-joblogs-log-analytics.md).
* Para acionar um livro de recorrer utilizando um alerta, consulte Utilize um alerta para acionar um livro de [execução da Automação Azure](automation-create-alert-triggered-runbook.md).

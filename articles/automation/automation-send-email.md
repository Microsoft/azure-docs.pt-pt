---
title: Envie um e-mail de um livro de execução da Automação Azure
description: Saiba como utilizar o SendGrid para enviar um e-mail de dentro de um livro de execução.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: 4d825dee469497cbb56a91c913ff3ac51963058b
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855681"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Tutorial: Envie um e-mail de um livro de execução da Automação Azure

Pode enviar um e-mail a partir de um livro de execução com [sendGrid](https://sendgrid.com/solutions) usando powerShell. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie um cofre de chaves Azure.
> * Guarde `SendGrid` a sua chave API no cofre da chave.
> * Crie um livro de execução reutilizável que recupere a sua chave API e envie um e-mail usando uma chave API armazenada no [Cofre de Chaves Azure](/azure/key-vault/).

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário o seguinte:

* Assinatura Azure: Se ainda não tiver um, pode ativar os seus benefícios de [subscrição mSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Criar uma conta SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Conta de automação](automation-offering-get-started.md) com módulos **Az,** e [Executar Como ligação,](automation-create-runas-account.md)para armazenar e executar o livro de execução.

## <a name="create-an-azure-key-vault"></a>Criar um Azure Key Vault

Pode criar um Cofre de Chave Azure utilizando o seguinte script PowerShell. Substitua os valores variáveis por valores específicos do seu ambiente. Utilize a casca de nuvem azure incorporada através do botão **Try It,** localizado no canto superior direito do bloco de código. Também pode copiar e executar o código localmente se tiver o [Módulo PowerShell Azure](/powershell/azure/install-az-ps) instalado na sua máquina local.

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

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Para outras formas de criar um Cofre chave Azure e armazenar um segredo, consulte [Key Vault Quickstarts](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importar módulos necessários para a sua conta de Automação

Para utilizar o Cofre de Chaves Azure dentro de um livro de execução, a sua conta Automation necessita dos seguintes módulos:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Clique em **Implementar para Automatização Azure** no separador Automação Azure em opções de **instalação**. Esta ação abre o portal Azure. Na página Import, selecione a sua conta De automação e clique **EM OK**.

Para obter métodos adicionais para adicionar os módulos necessários, consulte [módulos de importação](/azure/automation/shared-resources/modules#importing-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Crie o livro de corridas para enviar um e-mail

Depois de ter criado um cofre `SendGrid` chave e ter guardado a sua chave API, é hora de criar o livro de corridas que recupera a chave API e envia um e-mail.

Este livro `AzureRunAsConnection` de corridas usa como [conta Run As](automation-create-runas-account.md) para autenticar com azure para recuperar o segredo do Cofre chave Azure.

Use este exemplo para criar um livro chamado **Send-GridMailMessage**. Pode modificar o script PowerShell e reutilizá-lo para diferentes cenários.

1. Vá à sua conta de Automação Azure.
2. Em Fase **de Automação de Processos,** selecione **Runbooks**.
3. No topo da lista de livros de execução, selecione **+ Crie um livro de execução**.
4. Na página **Add Runbook,** introduza **send-GridMailMessage** para o nome do livro de execução. Para o tipo de livro de execução, selecione **PowerShell**. Em seguida, selecione **Criar**.
   ![Criar Livro de Corridas](./media/automation-send-email/automation-send-email-runbook.png)
5. O runbook é criado e a página **Editar Runbook do PowerShell** é aberta.
   ![Editar o Livro de Execução](./media/automation-send-email/automation-send-email-edit.png)
6. Copie o seguinte exemplo PowerShell na página **Editar.** Certifique-se `$VaultName` de que é o nome que especificou quando criou o seu cofre chave.

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

Elimine o cofre da chave utilizando o cmdlet [Remove-AzKeyVault.](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0)

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

* Para problemas de criação ou início do seu livro de corridas, consulte erros de Resolução de [Problemas com livros de execução](./troubleshoot/runbooks.md).
* Para atualizar os módulos na sua conta Automation, consulte [como atualizar os módulos Azure PowerShell na Automação Azure].](automation-update-azure-modules.md)
* Para monitorizar a execução do livro de execução, consulte o estado de [trabalho para a frente e os fluxos de trabalho da Automação para os registos do Monitor Azure](automation-manage-send-joblogs-log-analytics.md).
* Para acionar um livro de recorrer utilizando um alerta, consulte Utilize um alerta para acionar um livro de [execução da Automação Azure](automation-create-alert-triggered-runbook.md).

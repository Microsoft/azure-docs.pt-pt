---
title: Como permitir a entrada do Cofre da Chave Azure
description: Como ativar o registo do Azure Key Vault, que guarda informações numa conta de armazenamento Azure que fornece.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 5e0007f3b0dad8a68e9d81cebbe9fe24b5a7db3c
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285651"
---
# <a name="how-to-enable-key-vault-logging"></a>Como permitir a exploração do Cofre de Chaves

Depois de criar um ou mais cofres chave, provavelmente vai querer monitorizar como e quando os seus cofres chave são acedidos, e por quem. Para obter todos os detalhes sobre a funcionalidade, consulte a [sessão de registo do Key Vault](logging.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* Um cofre de chaves que tiver utilizado.  
* O Azure CLI ou Azure PowerShell.
* Armazenamento suficiente no Azure para os seus registos do Cofre de Chaves.

Se optar por instalar e utilizar o CLI localmente, necessitará da versão Azure CLI 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Para iniciar seduca em Azure usando o CLI pode escrever:

```azurecli-interactive
az login
```

Se optar por instalar e utilizar o PowerShell localmente, necessitará da versão 1.0.0 ou posterior do módulo Azure PowerShell. Escreva `$PSVersionTable.PSVersion` para encontrar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

```powershell-interactive
Connect-AzAccount
```

## <a name="connect-to-your-key-vault-subscription"></a>Conecte-se à subscrição do Cofre de Chaves

O primeiro passo para a configuração do registo de chaves é ligar-se à subscrição que contém o seu cofre de chaves. Isto é especialmente importante se tiver várias subscrições associadas à sua conta.

Com o CLI Azure, pode visualizar todas as suas subscrições utilizando o comando da [lista de conta az](/cli/azure/account?view=azure-cli-latest#az_account_list) e, em seguida, ligar-se a uma usando [o conjunto de conta az](/cli/azure/account?view=azure-cli-latest#az_account_set):

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Com o Azure PowerShell, pode primeiro listar as suas subscrições utilizando o cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-4.7.0) e, em seguida, ligar-se a uma utilizando o [cmdlet Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-4.7.0) 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Crie uma conta de armazenamento para os seus registos

Embora possa utilizar uma conta de armazenamento existente para os seus registos, criaremos uma nova conta de armazenamento dedicada aos registos do Key Vault. 

Para facilitar a gestão adicional, também usaremos o mesmo grupo de recursos que o que contém o cofre chave. No [quickstart Azure CLI](quick-create-cli.md) e [no quickstart Azure PowerShell,](quick-create-powershell.md)este grupo de recursos chama-se **myResourceGroup,** e a localização é *a leste.* Substitua estes valores por os seus, conforme aplicável. 

Também precisamos fornecer um nome de conta de armazenamento. Os nomes das contas de armazenamento devem ser únicos, entre 3 e 24 caracteres de comprimento, e usar apenas números e letras minúsculas.  Por último, vamos criar uma conta de armazenamento do SKU "Standard_LRS".

Com o Azure CLI, utilize a [conta de armazenamento az criar](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create) comando.

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Com a Azure PowerShell, utilize o [cmdlet New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount?view=azps-4.7.0) Terá de fornecer a localização que corresponde ao grupo de recursos.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

Em qualquer dos casos, note o "id" da conta de armazenamento. A operação Azure CLI devolve o "id" na saída. Para obter o "id" com a Azure PowerShell, utilize [o Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-4.7.0) e atribua a saída a uma variável $sa. Em seguida, pode ver a conta de armazenamento com $sa.id. (O "$sa. Context" propriedade também será usada, mais tarde neste artigo.)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

O "id" da conta de armazenamento estará no formato "/subscrições/<o seu ID de subscrição->/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAcounts/<o seu nome de conta de armazenamento único>".

> [!NOTE]
> Se decidir utilizar uma conta de armazenamento existente, deve utilizar a mesma subscrição que o cofre-chave, e deve utilizar o modelo de implementação do Gestor de Recursos Azure, em vez do modelo clássico de implementação.

## <a name="obtain-your-key-vault-resource-id"></a>Obtenha o seu ID de recurso de cofre chave

No [quickstart do CLI](quick-create-cli.md) e [no arranque rápido do PowerShell,](quick-create-powershell.md)criou uma chave com um nome único.  Use este nome novamente nos degraus abaixo.  Se não se lembrar do nome do seu cofre chave, pode utilizar o comando da lista de chave Azure CLI [az](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_list) ou o cmdlet Azure PowerShell [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) para listá-los.

Use o nome do seu cofre chave para encontrar o seu ID de recursos.  Com a Azure CLI, use o comando [de show az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_show)

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Com a Azure PowerShell, utilize o [cmdlet Get-AzKeyVault.](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0)

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

O ID de recursos do seu cofre chave estará no formato "/subscrições/<o seu ID de subscrição->ID /resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<o seu nome de chave-> único". Reparem no próximo passo.

## <a name="enable-logging-using-azure-powershell"></a>Ativar o registo utilizando o Azure PowerShell

Para ativar o registo do Key Vault, utilizaremos as [definições de diagnóstico](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest) do monitor Azure CLI az criar um comando, ou o cmdlet [Set-AzDiagnosticSetting,](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) juntamente com o ID da conta de armazenamento e o ID do cofre chave.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

Com a Azure PowerShell, utilizaremos o [cmdlet Set-AzDiagnosticSetting,](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) com a bandeira **ativada definida** para **$true** e a categoria definida para `AuditEvent` (a única categoria para a marcação do cofre de chaves):

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

Opcionalmente, pode definir uma política de retenção para os seus registos, de modo a que os registos mais antigos sejam automaticamente eliminados após um período de tempo especificado. Por exemplo, pode definir a política de retenção que elimina automaticamente os registos com mais de 90 dias.

<!-- With the Azure CLI, use the [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_update) command. 

```azurecli-interactive
az monitor diagnostic-settings update 
```
-->

Com a Azure PowerShell, utilize o [cmdlet set-AzDiagnosticSetting.](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) 

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

O que está registado:

* Todos os pedidos autenticados da API, incluindo pedidos falhados como resultado de permissões de acesso, erros do sistema ou maus pedidos.
* Operações no cofre principal em si, incluindo criação, eliminação, definição de políticas de acesso ao cofre chave, e atualização de atributos chave do cofre, tais como tags.
* Operações com chaves e segredos no cofre, incluindo:
  * Criar, modificar ou apagar estas chaves ou segredos.
  * Assinar, verificar, encriptar, desencriptar, embrulhar e desembrulhar chaves, obter segredos e listar chaves e segredos (e as suas versões).
* Pedidos não autenticados que resultam numa resposta 401. Exemplos são pedidos que não têm um símbolo ao portador, que são mal formados ou expirados, ou que têm um token inválido.  
* Eventos de notificação para quase expiração, política de acesso expirado e de acesso ao cofre alterado (o novo evento de versão não está registado). Os eventos são registados independentemente de existir uma subscrição de eventos criada no cofre de chaves. Para mais informações ver, [esquema de eventos da Grade de Eventos para Key Vault](../../event-grid/event-schema-key-vault.md)

## <a name="access-your-logs"></a>Aceder aos seus registos

Os registos do Cofre chave são armazenados no recipiente "insights-logs-auditevent" na conta de armazenamento que forneceu. Para ver os registos, tem de descarregar bolhas.

Primeiro, lista todas as bolhas no contentor.  Com o Azure CLI, utilize o comando [da lista de bolhas de armazenamento az.](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_list)

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

Com a Azure PowerShell, utilize a lista [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) todas as bolhas deste recipiente, insira:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Como verá pela saída do comando Azure CLI ou do cmdlet Azure PowerShell, o nome das bolhas está no formato `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` . Os valores data e hora utilizam o UTC.

Como pode utilizar a mesma conta de armazenamento para recolher registos para vários recursos, o ID completo do recurso no nome blob é útil para aceder ou descarregar apenas as bolhas de que necessita. Mas antes de podermos fazer isso, teremos primeiro de saber como transferir todos os blobs.

Com o Azure CLI, use o comando [de descarregamento de blob de armazenamento az,](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_download) passe-lhe os nomes das bolhas, e o caminho para o arquivo onde deseja guardar os resultados.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

Com a Azure PowerShell, use o [cmdlet Gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) para obter uma lista das bolhas, em seguida, encaneie-a no cmdlet [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent?view=azps-4.7.0) para baixar os registos para o seu caminho escolhido.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

Quando corre este segundo cmdlet em PowerShell, o **/** delimiter nos nomes blob cria uma estrutura de pasta completa sob a pasta de destino. Você usará esta estrutura para descarregar e armazenar as bolhas como ficheiros.

Para transferir seletivamente blobs, utilize carateres universais. Por exemplo:

* Se tiver vários cofres de chaves e pretender transferir registos apenas para um cofre de chaves designado CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Se tiver vários grupos de recursos e pretender transferir os registos para apenas um grupo de recursos, utilize `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Se quiser fazer o download de todos os registos para o mês de janeiro de 2019, `-Blob '*/year=2019/m=01/*'` utilize:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Agora, está pronto para começar a procurar o conteúdo dos registos. Mas antes de avançarmos para isso, deves saber mais dois comandos:

Para obter detalhes sobre como ler os registos, consulte o [registo do Cofre de Chaves: Interprete os registos do Cofre de Chaves](logging.md#interpret-your-key-vault-logs)

## <a name="use-azure-monitor-logs"></a>Utilizar os registos do Azure Monitor

Pode utilizar a solução Key Vault nos registos do Azure Monitor para rever os registos do Key `AuditEvent` Vault. Nos registos do Azure Monitor, utiliza consultas de registo para analisar dados e obter a informação de que necessita.

Para obter mais informações, incluindo como configurar isto, consulte [o Azure Key Vault no Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Passos seguintes

- Para obter informações conceptuais, incluindo como interpretar registos do Key Vault, consulte a [sessão do Key Vault](logging.md)
- Para um tutorial que utilize o Azure Key Vault numa aplicação web .NET, consulte [Use Azure Key Vault a partir de uma aplicação web](tutorial-net-create-vault-azure-web-app.md).
- Para as referências de programação, consulte o [Guia para programadores do Cofre de Chaves do Azure](developers-guide.md).
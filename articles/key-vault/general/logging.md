---
title: Azure Key Vault registação / Microsoft Docs
description: Saiba como monitorizar o acesso aos seus cofres chave, permitindo o registo do Cofre da Chave Azure, que guarda informações numa conta de armazenamento Azure que fornece.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 263568ef53ec2353bf00ebcd5b48a212f0f1eb2d
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192791"
---
# <a name="azure-key-vault-logging"></a>Registo do Azure Key Vault

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Depois de criar um ou mais cofres chave, provavelmente vai querer monitorizar como e quando os seus cofres chave são acedidos, e por quem. Pode fazê-lo ativando o registo do Azure Key Vault, que guarda informações numa conta de armazenamento Azure que fornece. Um novo recipiente denominado **insights-logs-auditevent** é automaticamente criado para a sua conta de armazenamento especificada. Pode utilizar esta mesma conta de armazenamento para recolher registos para vários cofres chave.

Pode aceder à sua informação de registo 10 minutos (no máximo) após a operação do cofre da chave. Na maioria dos casos, o processo será ainda mais rápido.  Cabe-lhe gerir os seus registos na sua conta de armazenamento:

* Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
* Elimine os registos que já não pretende manter na sua conta de armazenamento.

Utilize este tutorial para ajudá-lo a começar com o registo do Cofre de Chaves do Azure. Irá criar uma conta de armazenamento, ativar o registo e interpretar as informações de registo recolhidas.  

> [!NOTE]
> Este tutorial não inclui instruções sobre a criação dos cofres de chaves, chaves ou segredos. Para esta informação, veja [o que é Azure Key Vault?](overview.md) Ou, para instruções de Azure CLI cross-platform, consulte [este tutorial equivalente).](manage-with-cli2.md)
>
> Este artigo fornece instruções da Azure PowerShell para atualizar a sessão de diagnóstico. Também pode atualizar o registo de diagnóstico utilizando o Monitor Azure na secção de **registos** de Diagnóstico do portal Azure. 
>

Para obter informações gerais sobre o Key Vault, veja [o que é o Cofre da Chave Azure?](overview.md) Para obter informações sobre onde o Cofre-Chave está disponível, consulte [a página de preços](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* Um cofre de chaves que tiver utilizado.  
* Azure PowerShell, versão mínima de 1.0.0. Para instalar o Azure PowerShell e associá-lo à sua subscrição do Azure, consulte o artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/). Se já instalou o Azure PowerShell e não conhece a versão, a partir da consola Azure PowerShell, entre em `$PSVersionTable.PSVersion` .  
* Armazenamento suficiente no Azure para os seus registos do Cofre de Chaves.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Ligue-se à subscrição do cofre de chaves

O primeiro passo para configurar a marcação de chaves é apontar o Azure PowerShell para o cofre de chaves que pretende registar.

Inicie uma sessão Azure PowerShell e inicie sessão na sua conta Azure utilizando o seguinte comando:  

```powershell
Connect-AzAccount
```

Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. A Azure PowerShell recebe todas as subscrições que estão associadas a esta conta. Por predefinição, o PowerShell utiliza o primeiro.

Talvez tenha de especificar a subscrição que usou para criar o cofre da chave. Introduza o seguinte comando para ver as subscrições da sua conta:

```powershell
Get-AzSubscription
```

Em seguida, para especificar a subscrição que está associada ao cofre chave que você vai registar, insira:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Apontar o PowerShell para a subscrição certa é um passo importante, especialmente se tiver várias subscrições associadas à sua conta. Para obter mais informações sobre como configurar o Azure PowerShell, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/).

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Crie uma conta de armazenamento para os seus registos

Embora possa utilizar uma conta de armazenamento existente para os seus registos, criaremos uma conta de armazenamento que será dedicada aos registos do Key Vault. Para conveniência para quando tivermos que especificar isso mais tarde, vamos armazenar os detalhes em uma variável chamada **sa**.

Para facilitar a gestão adicional, também usaremos o mesmo grupo de recursos que o que contém o cofre chave. A partir do tutorial de início , este grupo de recursos [chama-se](../secrets/quick-create-cli.md) **ContosoResourceGroup**, e continuaremos a usar a localização da Ásia Oriental. Substitua estes valores por os seus, conforme aplicável:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Se decidir utilizar uma conta de armazenamento existente, deve utilizar a mesma subscrição que o cofre da chave. E deve utilizar o modelo de implementação do Azure Resource Manager, em vez do modelo clássico de implementação.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>Identifique o cofre de chaves para os seus registos

No [tutorial de início,](../secrets/quick-create-cli.md)o nome do cofre chave era **ContosoKeyVault.** Continuaremos a usar esse nome e armazenaremos os detalhes numa variável chamada **KV:**

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging-using-azure-powershell"></a><a id="enable"></a>Ativar o registo utilizando o Azure PowerShell

Para ativar o registo do Key Vault, utilizaremos o **cmdlet Set-AzDiagnosticSetting,** juntamente com as variáveis que criamos para a nova conta de armazenamento e o cofre-chave. Também definiremos a bandeira **ativada** para **$true** e definiremos a categoria para `AuditEvent` (a única categoria para a extração de Cofre de Chaves):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

A saída é assim:

```output
StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccountContosoKeyVaultLogs
ServiceBusRuleId   :
StorageAccountName :
    Logs
    Enabled           : True
    Category          : AuditEvent
    RetentionPolicy
    Enabled : False
    Days    : 0
```

Esta saída confirma que o registo está agora ativado para o cofre da chave e guarda informações na sua conta de armazenamento.

Opcionalmente, pode definir uma política de retenção para os seus registos de modo a que os registos mais antigos sejam automaticamente eliminados. Por exemplo, defina a política de retenção, definindo a bandeira **-RetentionEnabled** **para $true**, e defina o parâmetro **-RetentionInDays** para **90** de modo a que os registos com mais de 90 dias sejam automaticamente eliminados.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

O que é registado:

* Todos os pedidos autenticados da API, incluindo pedidos falhados como resultado de permissões de acesso, erros do sistema ou maus pedidos.
* Operações no cofre principal em si, incluindo criação, eliminação, definição de políticas de acesso ao cofre chave, e atualização de atributos chave do cofre, tais como tags.
* Operações com chaves e segredos no cofre, incluindo:
  * Criar, modificar ou apagar estas chaves ou segredos.
  * Assinar, verificar, encriptar, desencriptar, embrulhar e desembrulhar chaves, obter segredos e listar chaves e segredos (e as suas versões).
* Pedidos não autenticados que resultam numa resposta 401. Exemplos são pedidos que não têm um símbolo ao portador, que são mal formados ou expirados, ou que têm um token inválido.  

## <a name="enable-logging-using-azure-cli"></a>Ativar o registo através do Azure CLI

```azurecli
az login

az account set --subscription {AZURE SUBSCRIPTION ID}

az provider register -n Microsoft.KeyVault

az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="access-your-logs"></a><a id="access"></a>Aceder aos seus registos

Os registos do Cofre chave são armazenados no recipiente **insights-logs-auditevent** na conta de armazenamento que forneceu. Para ver os registos, tem de descarregar bolhas.

Primeiro, crie uma variável para o nome do contentor. Você vai usar esta variável durante todo o resto da passagem.

```powershell
$container = 'insights-logs-auditevent'
```

Para listar todas as bolhas deste recipiente, insira:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

O resultado é semelhante a este:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Como pode ver desta saída, as bolhas seguem uma convenção de nomeação: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Os valores data e hora utilizam o UTC.

Como pode utilizar a mesma conta de armazenamento para recolher registos para vários recursos, o ID completo do recurso no nome blob é útil para aceder ou descarregar apenas as bolhas de que necessita. Mas antes de podermos fazer isso, teremos primeiro de saber como transferir todos os blobs.

Crie uma pasta para descarregar as bolhas. Por exemplo:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Em seguida, obtenha uma lista de todos os blobs:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Pipe this list through **Get-AzStorageBlobContent** para baixar as bolhas para a pasta de destino:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Quando corre este segundo comando, o **/** delimiter nos nomes blob cria uma estrutura de pasta completa sob a pasta de destino. Você usará esta estrutura para descarregar e armazenar as bolhas como ficheiros.

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

* Para consultar o estado das definições de diagnóstico para o recurso do seu cofre de chaves: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Para desativar o registo do seu cofre de chaves: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`


## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>Interpretar os registos do seu Cofre de Chaves

Os blobs individuais são armazenadas como texto, formatados como um blob JSON. Vamos ver um registo de exemplo. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

A tabela que se segue lista os nomes e descrições do campo:

| Nome do campo | Descrição |
| --- | --- |
| **Hora** |Data e hora na UTC. |
| **recursosId** |Identificação de recursos do Azure Resource Manager. Para os registos do Cofre de Chaves, este é sempre o ID de recurso key Vault. |
| **operaçãoName** |Nome da operação, conforme documentada na tabela seguinte. |
| **operaçãoVer** |Versão REST API solicitada pelo cliente. |
| **categoria** |Tipo de resultado. Para os registos key vault, `AuditEvent` é o valor único disponível. |
| **resultadoType** |Resultado do pedido da API REST. |
| **assinatura resultados** |Estado de HTTP. |
| **resultadoDescrição** |Descrição adicional sobre o resultado, quando disponível. |
| **duraçõesMs** |Tempo necessário para o processamento do pedido de API REST, em milissegundos. Não inclui a latência de rede, assim, o tempo que medir do lado do cliente poderá não corresponder a este período de tempo. |
| **callerIpAddress** |Endereço IP do cliente que fez o pedido. |
| **correlationId** |Um GUID opcional que o cliente pode passar para correlacionar os registos do lado do cliente com os registos do lado do serviço (Cofre de Chaves). |
| **identidade** |Identidade do símbolo que foi apresentado no pedido da API REST. Trata-se normalmente de um "utilizador", de um "principal de serviço", ou da combinação "user+appId", como no caso de um pedido que resulta de um cmdlet Azure PowerShell. |
| **propriedades** |Informação que varia com base na operação **(operaçãoName).** Na maioria dos casos, este campo contém informações do cliente (a cadeia de agente de utilizador passou pelo cliente), o pedido exato de API URI e o código de estado HTTP. Além disso, quando um objeto é devolvido como resultado de um pedido (por exemplo, **KeyCreate** ou **VaultGet),** também contém a chave URI (as `id` ), cofre URI, ou URI secreto. |

Os valores de campo **operationName** estão no formato *ObjectVerb.* Por exemplo:

* Todas as operações de cofre chave têm o `Vault<action>` formato, tais como `VaultGet` e `VaultCreate` .
* Todas as operações-chave têm o `Key<action>` formato, tais como `KeySign` e `KeyList` .
* Todas as operações secretas têm o `Secret<action>` formato, tais como `SecretGet` e `SecretListVersions` .

A tabela a seguir lista os valores do nome de **operação** e os comandos correspondentes da API REST:

| operationName | Comando API REST |
| --- | --- |
| **Autenticação** |Autenticar via Azure Ative Directory endpoint |
| **VaultGet** |[Obter informações sobre um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Criar ou atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Eliminar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Lista todos os cofres de chaves num grupo de recursos](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Criar uma chave](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Obter informações sobre uma chave](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importar uma chave para um cofre](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Apoiar uma chave](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Eliminar uma chave](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Restaurar uma chave](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Assinar com uma chave](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Verificar com uma chave](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Moldar uma chave](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Desenrolar uma chave](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Encriptar com uma chave](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Desencriptar com uma chave](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Atualizar uma chave](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Lista as chaves num cofre](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Lista as versões de uma chave](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Criar um segredo](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Obter um segredo](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Atualizar um segredo](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Eliminar um segredo](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Lista os segredos num cofre](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Lista as versões de um segredo](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Utilize registos do Monitor Azure

Pode utilizar a solução Key Vault nos registos do Azure Monitor para rever os registos do Key `AuditEvent` Vault. Nos registos do Azure Monitor, utiliza consultas de registo para analisar dados e obter a informação de que necessita. 

Para obter mais informações, incluindo como configurar isto, consulte [o Azure Key Vault no Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a><a id="next"></a>Passos seguintes

Para um tutorial que utilize o Azure Key Vault numa aplicação web .NET, consulte [Use Azure Key Vault a partir de uma aplicação web](tutorial-net-create-vault-azure-web-app.md).

Para as referências de programação, consulte o [Guia para programadores do Cofre de Chaves do Azure](developers-guide.md).

Para obter uma lista de Azure PowerShell 1.0 cmdlets para Azure Key Vault, consulte [as cmdlets do Cofre da Chave Azure](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

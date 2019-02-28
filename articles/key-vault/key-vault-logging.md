---
title: Registo do Cofre de chaves do Azure | Documentos da Microsoft
description: Utilize este tutorial para ajudá-lo a começar com o registo do Cofre de Chaves do Azure.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: c9d8dd366ecabe9eb508998d526ddfe7b1da300d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960528"
---
# <a name="azure-key-vault-logging"></a>Registo do Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Depois de criar um ou mais cofres de chaves, provavelmente desejará monitorizar como e quando os seus cofres de chaves são acedidos e por quem. Pode fazê-lo ao ativar o registo para o Azure Key Vault, que guarda as informações numa conta de armazenamento do Azure fornecidas por si. Um novo contentor designado **insights-logs-auditevent** é criado automaticamente para a sua conta de armazenamento especificada. Pode utilizar esta mesma conta de armazenamento para recolher registos para vários cofres de chaves.

Pode acessar suas informações de registo de 10 minutos (no máximo) após a operação de Cofre de chaves. Na maioria dos casos, o processo será ainda mais rápido.  Cabe-lhe gerir os seus registos na sua conta de armazenamento:

* Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
* Elimine os registos que já não pretende manter na sua conta de armazenamento.

Utilize este tutorial para ajudá-lo a começar com o registo do Cofre de Chaves do Azure. Irá criar uma conta de armazenamento, ativar o registo e interpretar as informações de registo recolhidas.  

> [!NOTE]
> Este tutorial não inclui instruções sobre a criação dos cofres de chaves, chaves ou segredos. Para obter estas informações, consulte [o que é o Azure Key Vault?](key-vault-overview.md). Ou, para obter instruções de CLI do Azure de várias plataformas, consulte [tutorial equivalente](key-vault-manage-with-cli2.md).
>
> Este artigo fornece instruções de Azure PowerShell para atualizar o registo de diagnósticos. Também pode atualizar o registo de diagnósticos com o Azure Monitor na **registos de diagnóstico** seção do portal do Azure. 
>

Para obter informações gerais sobre o Key Vault, consulte [o que é o Azure Key Vault?](key-vault-whatis.md). Para obter informações sobre onde o Key Vault está disponível, consulte a [página de preços](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* Um cofre de chaves que tiver utilizado.  
* O Azure PowerShell, versão mínima do 1.0.0. Para instalar o Azure PowerShell e associá-lo à sua subscrição do Azure, consulte o artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Se já tiver instalado o Azure PowerShell e não souber a versão, a partir da consola do Azure PowerShell, introduza `$PSVersionTable.PSVersion`.  
* Armazenamento suficiente no Azure para os seus registos do Cofre de Chaves.

## <a id="connect"></a>Ligar à sua subscrição do Cofre de chaves

A primeira etapa na configuração de log de chave é ponto Azure PowerShell para o Cofre de chaves que pretende iniciar sessão.

Inicie uma sessão do Azure PowerShell e inicie sessão na sua conta do Azure utilizando o seguinte comando:  

```PowerShell
Connect-AzAccount
```

Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. O Azure PowerShell obtém todas as subscrições que estão associadas esta conta. Por predefinição, o PowerShell utiliza primeiro.

Poderá ter de especificar a subscrição que utilizou para criar o seu Cofre de chaves. Introduza o seguinte comando para ver as subscrições da sua conta:

```PowerShell
Get-AzSubscription
```

Em seguida, para especificar a subscrição que está associada ao Cofre de chaves que vai estar a registar, introduza:

```PowerShell
Set-AzContext -SubscriptionId <subscription ID>
```

Apontar o PowerShell para a subscrição correta é um passo importante, especialmente se tiver várias assinaturas associadas à sua conta. Para obter mais informações sobre como configurar o Azure PowerShell, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

## <a id="storage"></a>Criar uma conta de armazenamento para os seus registos

Apesar de poder utilizar uma conta de armazenamento existente para os seus registos, vamos criar uma conta de armazenamento dedicada aos registos do Key Vault. Para sua comodidade quando temos de especificar isso mais tarde, iremos guardar os detalhes numa variável chamada **sa**.

Para facilitar ainda mais a gestão, também usaremos o mesmo grupo de recursos como aquela que contém o Cofre de chaves. Do [tutorial de introdução](key-vault-get-started.md), este grupo de recursos com o nome **ContosoResourceGroup**, e iremos continuar a utilizar a localização do leste asiático. Substitua estes valores pelos seus próprios, conforme aplicável:

```PowerShell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Se optar por utilizar uma conta de armazenamento existente, terá de utilizar a mesma subscrição que o seu Cofre de chaves. E ele tem de utilizar o modelo de implementação Azure Resource Manager, em vez do modelo de implementação clássica.
>
>

## <a id="identify"></a>Identifique o cofre de chaves para os seus registos

Na [tutorial de introdução](key-vault-get-started.md), o nome do Cofre de chaves foi **ContosoKeyVault**. Vamos continuar a utilizar esse nome e guardar os detalhes numa variável chamada **kv**:

```PowerShell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Ativar registo

Para ativar o registo de Cofre de chaves, iremos utilizar o **Set-AzDiagnosticSetting** cmdlet, juntamente com as variáveis que criamos para a nova conta de armazenamento e o Cofre de chaves. Também vamos definir o **-ativado** sinalizador para **$true** e definir a categoria para **AuditEvent** (a única categoria para o registo do Cofre de chaves):

```PowerShell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

O resultado tem o seguinte aspeto:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Esta saída confirma que o registo está agora ativado para o seu Cofre de chaves, e ele irá guardar as informações à sua conta de armazenamento.

Opcionalmente, pode definir uma política de retenção para os seus registos, de modo a que os registos mais antigos são automaticamente eliminados. Por exemplo, definir a política de retenção, definindo a **- RetentionEnabled** sinalizador para **$true**e defina o **- RetentionInDays** parâmetro **90**para que os registos com mais de 90 dias são eliminados automaticamente.

```PowerShell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

O que é registado:

* Autenticado todos os pedidos de REST API, incluindo pedidos falhados resultantes de permissões de acesso, erros de sistema ou pedidos incorretos.
* Operações na chave de cofre em si, incluindo a criação, eliminação, políticas de acesso do Cofre de chaves de definição e atualizar atributos do Cofre de chaves, como etiquetas.
* Operações sobre chaves e segredos no Cofre de chaves, incluindo:
  * Criar, modificar ou eliminação dessas chaves ou segredos.
  * Assinatura, verificar, encriptar, desencriptar, encapsulamento e chaves de abertura, obter segredos, listagem de chaves e segredos (e suas versões).
* Pedidos não autenticados que resultam numa resposta 401. Os exemplos são os pedidos que não tenham um token de portador incorretamente formulados ou expirados, ou que tenham um token inválido.  

## <a id="access"></a>Aceder aos seus registos

Registos do Cofre de chaves são armazenados no **insights-logs-auditevent** contentor na conta de armazenamento fornecida. Para ver os registos, terá de transferir blobs.

Primeiro, crie uma variável para o nome do contentor. Usará essa variável em todo o resto do passo a passo.

```PowerShell
$container = 'insights-logs-auditevent'
```

Para listar todos os blobs neste contentor, introduza:

```PowerShell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

O resultado é semelhante a este:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Como pode ver a partir desta saída, os blobs seguem uma convenção de nomenclatura: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Os valores data e hora utilizam o UTC.

Como pode usar a mesma conta de armazenamento para recolher registos para vários recursos, o ID do recurso completo no nome do blob é útil para aceder ou transferir os blobs de que precisa. Mas antes de podermos fazer isso, teremos primeiro de saber como transferir todos os blobs.

Crie uma pasta para transferir os blobs. Por exemplo:

```PowerShell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Em seguida, obtenha uma lista de todos os blobs:  

```PowerShell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Encaminhe esta lista através de **Get-AzStorageBlobContent** para transferir os blobs para a pasta de destino:

```PowerShell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Quando executar este segundo comando, o **/** delimitador dos nomes blob cria uma estrutura de pasta completa sob a pasta de destino. Usará essa estrutura para transferir e armazenar os blobs como ficheiros.

Para transferir seletivamente blobs, utilize carateres universais. Por exemplo:

* Se tiver vários cofres de chaves e pretender transferir registos apenas para um cofre de chaves designado CONTOSOKEYVAULT3:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Se tiver vários grupos de recursos e pretender transferir os registos para apenas um grupo de recursos, utilize `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Se quiser transferir todos os registos para o mês de Janeiro de 2019, utilize `-Blob '*/year=2019/m=01/*'`:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Agora, está pronto para começar a procurar o conteúdo dos registos. Mas antes de avançarmos para que, deve saber dois comandos mais:

* Para consultar o estado das definições de diagnóstico para o recurso do seu cofre de chaves: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Para desativar o registo do seu cofre de chaves: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Interpretar os registos do seu Cofre de Chaves

Os blobs individuais são armazenadas como texto, formatados como um blob JSON. Vamos examinar uma entrada de registo de exemplo. Execute este comando:

```PowerShell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Devolve uma entrada de registo semelhante a este:

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

A tabela seguinte lista os nomes de campo e descrições:

| Nome do campo | Descrição |
| --- | --- |
| **tempo** |Data e hora em UTC. |
| **resourceId** |ID de recurso de Gestor de recursos do Azure. Para registos do Cofre de chaves, isso é sempre o ID de recurso do Key Vault. |
| **operationName** |Nome da operação, conforme documentada na tabela seguinte. |
| **operationVersion** |Versão de REST API solicitada pelo cliente. |
| **category** |Tipo de resultado. Para registos do Cofre de chaves, **AuditEvent** é o único valor disponível. |
| **resultType** |Resultado do pedido de REST API. |
| **resultSignature** |Estado de HTTP. |
| **resultDescription** |Descrição adicional sobre o resultado, quando disponível. |
| **durationMs** |Tempo necessário para o processamento do pedido de API REST, em milissegundos. Não inclui a latência de rede, assim, o tempo que medir do lado do cliente poderá não corresponder a este período de tempo. |
| **callerIpAddress** |Endereço IP do cliente que efetuou o pedido. |
| **correlationId** |Um GUID opcional que o cliente pode passar para correlacionar os registos do lado do cliente com os registos do lado do serviço (Cofre de Chaves). |
| **identity** |Identidade do token que foi apresentado no pedido de REST API. Isto é normalmente um "utilizador", "principal de serviço" ou a combinação "utilizador + appId," como no caso de um pedido resultante de um cmdlet do PowerShell do Azure. |
| **Propriedades** |Informações que variam com base na operação (**operationName**). Na maioria dos casos, este campo contém informações de cliente (a cadeia de agente de utilizador passada pelo cliente), o URI de solicitação REST API exata e o código de estado HTTP. Além disso, quando um objeto é devolvido como resultado de um pedido (por exemplo, **KeyCreate** ou **VaultGet**), ele também contém a chave do cofre URI (como "id"), URI ou URI secreta. |

O **operationName** valores dos campos estão na *ObjectVerb* formato. Por exemplo:

* Todas as operações do Cofre de chaves têm o `Vault<action>` formatar, tais como `VaultGet` e `VaultCreate`.
* Todas as operações de chaves têm o `Key<action>` formatar, tais como `KeySign` e `KeyList`.
* Todas as operações de segredo têm o `Secret<action>` formatar, tais como `SecretGet` e `SecretListVersions`.

A tabela seguinte lista os **operationName** valores e os comandos correspondentes da REST API:

| operationName | Comando REST API |
| --- | --- |
| **Autenticação** |Autenticar através de ponto final do Azure Active Directory |
| **VaultGet** |[Obter informações sobre um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Criar ou atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Eliminar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Lista todos os cofres de chaves num grupo de recursos](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Criar uma chave](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Obter informações sobre uma chave](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importar uma chave para um cofre](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Fazer cópias de segurança de uma chave](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
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

## <a id="loganalytics"></a>Utilizar o Log Analytics

Pode utilizar a solução de Cofre de chaves no Azure Log Analytics para rever o Key Vault **AuditEvent** registos. Log Analytics, vai utilizar consultas de registo para analisar dados e obter as informações que necessárias. 

Para obter mais informações, incluindo informação sobre configuração, veja [Azure Key Vault solution in Log Analytics (Solução Cofre de Chaves do Azure no Log Analytics)](../azure-monitor/insights/azure-key-vault.md). Este artigo também contém instruções se tiver de migrar a partir da solução de Cofre de chaves antiga que foi oferecida durante a pré-visualização do Log Analytics, onde primeiro encaminhado os registos para uma conta de armazenamento do Azure e configurado o Log Analytics para ler a partir daí.

## <a id="next"></a>Passos seguintes

Para obter um tutorial que utiliza o Azure Key Vault num aplicativo web do .NET, consulte [utilização do Azure Key Vault a partir de uma aplicação web](tutorial-net-create-vault-azure-web-app.md).

Para as referências de programação, consulte o [Guia para programadores do Cofre de Chaves do Azure](key-vault-developers-guide.md).

Para obter uma lista dos cmdlets do Azure PowerShell 1.0 para o Azure Key Vault, consulte [cmdlets do Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Para um tutorial sobre a rotação de chaves e auditoria do registo com o Azure Key Vault, consulte [configurar o Cofre de chaves com auditoria e rotação de ponto-a-ponto](key-vault-key-rotation-log-monitoring.md).

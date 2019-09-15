---
title: Log de Azure Key Vault | Microsoft Docs
description: Utilize este tutorial para ajudá-lo a começar com o registo do Cofre de Chaves do Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 997651887c3c378e4791553d5ff05f585ad169ea
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000671"
---
# <a name="azure-key-vault-logging"></a>Registo do Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Depois de criar um ou mais cofres de chaves, você provavelmente desejará monitorar como e quando seus cofres de chaves são acessados e por quem. Você pode fazer isso habilitando o registro em log para Azure Key Vault, que salva informações em uma conta de armazenamento do Azure que você fornece. Um novo contêiner chamado **insights-logs-auditevent** é criado automaticamente para sua conta de armazenamento especificada. Você pode usar essa mesma conta de armazenamento para coletar logs para vários cofres de chaves.

Você pode acessar as informações de registro em log 10 minutos (no máximo) após a operação do cofre de chaves. Na maioria dos casos, o processo será ainda mais rápido.  Cabe-lhe gerir os seus registos na sua conta de armazenamento:

* Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
* Elimine os registos que já não pretende manter na sua conta de armazenamento.

Utilize este tutorial para ajudá-lo a começar com o registo do Cofre de Chaves do Azure. Você criará uma conta de armazenamento, habilitará o registro em log e interpretará as informações de log coletadas.  

> [!NOTE]
> Este tutorial não inclui instruções sobre a criação dos cofres de chaves, chaves ou segredos. Para obter essas informações, consulte [o que é Azure Key Vault?](key-vault-overview.md). Ou, para obter instruções de CLI do Azure de plataforma cruzada, consulte [este tutorial equivalente](key-vault-manage-with-cli2.md).
>
> Este artigo fornece Azure PowerShell instruções para atualizar o log de diagnósticos. Você também pode atualizar o log de diagnóstico usando Azure Monitor na seção **logs de diagnóstico** do portal do Azure. 
>

Para obter informações gerais sobre Key Vault, consulte [o que é Azure Key Vault?](key-vault-overview.md). Para obter informações sobre onde Key Vault está disponível, consulte a [página de preços](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* Um cofre de chaves que tiver utilizado.  
* Azure PowerShell, versão mínima do 1.0.0. Para instalar o Azure PowerShell e associá-lo à sua subscrição do Azure, consulte o artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Se você já tiver instalado o Azure PowerShell e não souber a versão, no console do Azure PowerShell, `$PSVersionTable.PSVersion`digite.  
* Armazenamento suficiente no Azure para os seus registos do Cofre de Chaves.

## <a id="connect"></a>Conectar-se à sua assinatura do cofre de chaves

A primeira etapa na configuração do registro em log de chave é apontar Azure PowerShell para o cofre de chaves que você deseja registrar em log.

Inicie uma sessão de Azure PowerShell e entre em sua conta do Azure usando o seguinte comando:  

```powershell
Connect-AzAccount
```

Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. Azure PowerShell obtém todas as assinaturas associadas a essa conta. Por padrão, o PowerShell usa o primeiro.

Talvez seja necessário especificar a assinatura que você usou para criar o cofre de chaves. Digite o seguinte comando para ver as assinaturas da sua conta:

```powershell
Get-AzSubscription
```

Em seguida, para especificar a assinatura associada ao cofre de chaves que será registrada, digite:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Apontar o PowerShell para a assinatura correta é uma etapa importante, especialmente se você tiver várias assinaturas associadas à sua conta. Para obter mais informações sobre como configurar o Azure PowerShell, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

## <a id="storage"></a>Criar uma conta de armazenamento para seus logs

Embora você possa usar uma conta de armazenamento existente para seus logs, criaremos uma conta de armazenamento que será dedicada a Key Vault logs. Para a conveniência de quando precisamos especificar isso mais tarde, armazenaremos os detalhes em uma variável chamada **SA**.

Para facilidade de gerenciamento adicional, também usaremos o mesmo grupo de recursos que contém o cofre de chaves. No [tutorial de introdução](key-vault-get-started.md), esse grupo de recursos é denominado **ContosoResourceGroup**e continuaremos a usar o local de Ásia Oriental. Substitua esses valores pelos seus próprios, conforme aplicável:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Se você decidir usar uma conta de armazenamento existente, ela deverá usar a mesma assinatura que o cofre de chaves. E ele deve usar o modelo de implantação Azure Resource Manager, em vez do modelo de implantação clássico.
>
>

## <a id="identify"></a>Identifique o cofre de chaves para os seus registos

No [tutorial de introdução](key-vault-get-started.md), o nome do cofre de chaves era **ContosoKeyVault**. Continuaremos a usar esse nome e armazenar os detalhes em uma variável chamada **kV**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Ativar registo

Para habilitar o log para Key Vault, usaremos o cmdlet **set-AzDiagnosticSetting** , junto com as variáveis que criamos para a nova conta de armazenamento e o cofre de chaves. Também vamos definir o sinalizador **-Enabled** para **$true** e definir a categoria como **AuditEvent** (a única categoria para Key Vault log):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

A saída tem a seguinte aparência:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Essa saída confirma que o log agora está habilitado para o cofre de chaves e salvará informações em sua conta de armazenamento.

Opcionalmente, você pode definir uma política de retenção para seus logs, de modo que os logs mais antigos sejam excluídos automaticamente. Por exemplo, defina a política de retenção definindo o sinalizador **-RetentionEnabled** como **$true**e defina o parâmetro **-RetentionInDays** como **90** para que os logs com mais de 90 dias sejam excluídos automaticamente.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

O que é registado:

* Todas as solicitações da API REST autenticadas, incluindo solicitações com falha, como resultado de permissões de acesso, erros do sistema ou solicitações inadequadas.
* Operações no próprio cofre de chaves, incluindo criação, exclusão, configuração de políticas de acesso do cofre de chaves e atualização de atributos do Key Vault, como marcas.
* Operações em chaves e segredos no cofre de chaves, incluindo:
  * Criando, modificando ou excluindo essas chaves ou segredos.
  * Assinatura, verificação, criptografia, descriptografia, encapsulamento e desencapsulamento de chaves, obtenção de segredos e listagem de chaves e segredos (e suas versões).
* Pedidos não autenticados que resultam numa resposta 401. Exemplos são solicitações que não têm um token de portador, malformados ou expirados, ou que têm um token inválido.  

## <a id="access"></a>Aceder aos seus registos

Os logs de Key Vault são armazenados no contêiner **insights-logs-auditevent** na conta de armazenamento que você forneceu. Para exibir os logs, você precisa baixar BLOBs.

Primeiro, crie uma variável para o nome do contentor. Você usará essa variável em todo o restante do guia.

```powershell
$container = 'insights-logs-auditevent'
```

Para listar todos os BLOBs neste contêiner, digite:

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

Como pode ver a partir desta saída, os blobs seguem uma convenção de nomenclatura: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Os valores data e hora utilizam o UTC.

Como você pode usar a mesma conta de armazenamento para coletar logs de vários recursos, a ID de recurso completa no nome do blob é útil para acessar ou baixar apenas os blobs de que você precisa. Mas antes de podermos fazer isso, teremos primeiro de saber como transferir todos os blobs.

Crie uma pasta para baixar os BLOBs. Por exemplo:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Em seguida, obtenha uma lista de todos os blobs:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Redirecione essa lista por meio de **Get-AzStorageBlobContent** para baixar os BLOBs para a pasta de destino:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Quando executar este segundo comando, o **/** delimitador dos nomes blob cria uma estrutura de pasta completa sob a pasta de destino. Você usará essa estrutura para baixar e armazenar os BLOBs como arquivos.

Para transferir seletivamente blobs, utilize carateres universais. Por exemplo:

* Se tiver vários cofres de chaves e pretender transferir registos apenas para um cofre de chaves designado CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Se tiver vários grupos de recursos e pretender transferir os registos para apenas um grupo de recursos, utilize `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Se você quiser baixar todos os logs do mês de janeiro de 2019, use `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Agora, está pronto para começar a procurar o conteúdo dos registos. Mas antes de passarmos para isso, você deve saber mais dois comandos:

* Para consultar o estado das definições de diagnóstico para o recurso do seu cofre de chaves: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Para desativar o registo do seu cofre de chaves: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Interpretar os registos do seu Cofre de Chaves

Os blobs individuais são armazenadas como texto, formatados como um blob JSON. Vejamos um exemplo de entrada de log. Execute este comando:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Ele retorna uma entrada de log semelhante a esta:

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

A tabela a seguir lista os nomes de campo e as descrições:

| Nome do campo | Descrição |
| --- | --- |
| **tempo** |Data e hora em UTC. |
| **resourceId** |ID de recurso de Gestor de recursos do Azure. Para logs de Key Vault, essa é sempre a ID de recurso de Key Vault. |
| **operationName** |Nome da operação, conforme documentada na tabela seguinte. |
| **operationVersion** |Versão da API REST solicitada pelo cliente. |
| **category** |Tipo de resultado. Para logs de Key Vault, **AuditEvent** é o único valor disponível. |
| **resultType** |Resultado da solicitação da API REST. |
| **resultSignature** |Estado de HTTP. |
| **resultDescription** |Descrição adicional sobre o resultado, quando disponível. |
| **durationMs** |Tempo necessário para o processamento do pedido de API REST, em milissegundos. Não inclui a latência de rede, assim, o tempo que medir do lado do cliente poderá não corresponder a este período de tempo. |
| **callerIpAddress** |Endereço IP do cliente que fez a solicitação. |
| **correlationId** |Um GUID opcional que o cliente pode passar para correlacionar os registos do lado do cliente com os registos do lado do serviço (Cofre de Chaves). |
| **identity** |Identidade do token que foi apresentado na solicitação da API REST. Isso geralmente é um "usuário", uma "entidade de serviço" ou a combinação "usuário + appId", como no caso de uma solicitação resultante de um cmdlet Azure PowerShell. |
| **Propriedades** |Informações que variam de acordo com a operação (**operationName**). Na maioria dos casos, esse campo contém informações do cliente (a cadeia de caracteres do agente do usuário passada pelo cliente), o URI exato da solicitação da API REST e o código de status HTTP. Além disso, quando um objeto é retornado como resultado de uma solicitação (por exemplo, **keycreate** ou **VaultGet**), ele também contém o URI da chave (como "ID"), o URI do cofre ou o URI do segredo. |

Os valores do campo **operationName** estão no formato *objectverbal* . Por exemplo:

* Todas as operações do cofre de `Vault<action>` chaves têm o formato `VaultGet` , `VaultCreate`como e.
* Todas as operações de chave `Key<action>` têm o formato, `KeySign` como `KeyList`e.
* Todas as operações secretas `Secret<action>` têm o formato, `SecretGet` como `SecretListVersions`e.

A tabela a seguir lista os valores de **operationName** e os comandos da API REST correspondentes:

| operationName | Comando da API REST |
| --- | --- |
| **Autenticação** |Autenticar via ponto de extremidade Azure Active Directory |
| **VaultGet** |[Obter informações sobre um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Criar ou atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Eliminar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Lista todos os cofres de chaves num grupo de recursos](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Criar uma chave](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Obter informações sobre uma chave](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **Importação de** |[Importar uma chave para um cofre](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Fazer backup de uma chave](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
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

## <a id="loganalytics"></a>Usar logs de Azure Monitor

Você pode usar a solução Key Vault em logs de Azure Monitor para examinar Key Vault logs do **AuditEvent** . Nos logs de Azure Monitor, você usa consultas de log para analisar dados e obter as informações necessárias. 

Para obter mais informações, incluindo como configurar isso, consulte [Azure Key Vault solução nos logs de Azure monitor](../azure-monitor/insights/azure-key-vault.md). Este artigo também contém instruções se você precisar migrar da solução de Key Vault antiga que foi oferecida durante a visualização de logs de Azure Monitor, em que você primeiro direcionou os logs para uma conta de armazenamento do Azure e configurou Azure Monitor logs para ler a partir daí.

## <a id="next"></a>Passos seguintes

Para obter um tutorial que usa Azure Key Vault em um aplicativo Web do .NET, consulte [usar o Azure Key Vault de um aplicativo Web](tutorial-net-create-vault-azure-web-app.md).

Para as referências de programação, consulte o [Guia para programadores do Cofre de Chaves do Azure](key-vault-developers-guide.md).

Para obter uma lista de cmdlets Azure PowerShell 1,0 para Azure Key Vault, consulte [cmdlets Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Para obter um tutorial sobre a rotação de chaves e a auditoria de log com Azure Key Vault, consulte [configurar Key Vault com a rotação de chaves e a auditoria de ponta a ponta](key-vault-key-rotation-log-monitoring.md).

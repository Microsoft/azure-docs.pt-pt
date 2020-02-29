---
title: Exploração madeireira do Cofre de Chaves Azure  Microsoft Docs
description: Utilize este tutorial para ajudá-lo a começar com o registo do Cofre de Chaves do Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 8915970cd4c70228fad3b49921f4c81d6d90aa72
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195350"
---
# <a name="azure-key-vault-logging"></a>Registo do Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Depois de criar um ou mais cofres chave, provavelmente vai querer monitorizar como e quando os seus cofres chave são acedidos, e por quem. Pode fazê-lo permitindo a exploração de login para o Azure Key Vault, que guarda informações numa conta de armazenamento Azure que fornece. Um novo recipiente chamado **insights-logs-auditevent** é automaticamente criado para a sua conta de armazenamento especificada. Pode utilizar esta mesma conta de armazenamento para recolher registos para vários cofres chave.

Pode aceder à sua informação de registo 10 minutos (no máximo) após a operação do cofre chave. Na maioria dos casos, o processo será ainda mais rápido.  Cabe-lhe gerir os seus registos na sua conta de armazenamento:

* Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
* Elimine os registos que já não pretende manter na sua conta de armazenamento.

Utilize este tutorial para ajudá-lo a começar com o registo do Cofre de Chaves do Azure. Criará uma conta de armazenamento, ativará o registo e interpretará as informações de registo recolhidas.  

> [!NOTE]
> Este tutorial não inclui instruções sobre a criação dos cofres de chaves, chaves ou segredos. Para esta informação, veja [o que é o Cofre chave Azure?](key-vault-overview.md) Ou, para instruções azure CLI de plataforma cruzada, consulte [este tutorial equivalente](key-vault-manage-with-cli2.md).
>
> Este artigo fornece instruções azure PowerShell para atualizar o registo de diagnóstico. Também pode atualizar o registo de diagnóstico utilizando o Monitor Azure na secção de **registos de diagnóstico** do portal Azure. 
>

Para obter informações sobre o Cofre chave, veja [o que é o Cofre chave Azure?](key-vault-overview.md) Para obter informações sobre onde o Key Vault está disponível, consulte a [página de preços](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* Um cofre de chaves que tiver utilizado.  
* Azure PowerShell, versão mínima de 1.0.0. Para instalar o Azure PowerShell e associá-lo à sua subscrição do Azure, consulte o artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Se já instalou o Azure PowerShell e não conhece a versão, a partir da consola Azure PowerShell, introduza `$PSVersionTable.PSVersion`.  
* Armazenamento suficiente no Azure para os seus registos do Cofre de Chaves.

## <a id="connect"></a>Ligue-se à subscrição do seu cofre chave

O primeiro passo para a configuração da exploração da chave é apontar o Azure PowerShell para o cofre chave que pretende registar.

Inicie uma sessão Azure PowerShell e inicie o seu serviço Azure utilizando o seguinte comando:  

```powershell
Connect-AzAccount
```

Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. A Azure PowerShell recebe todas as subscrições que estão associadas a esta conta. Por padrão, o PowerShell utiliza o primeiro.

Pode ter de especificar a subscrição que usou para criar o seu cofre chave. Insira o seguinte comando para ver as subscrições da sua conta:

```powershell
Get-AzSubscription
```

Em seguida, para especificar a subscrição que está associada com o cofre chave que você vai registar, insira:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Apontar o PowerShell para a subscrição certa é um passo importante, especialmente se tiver várias subscrições associadas à sua conta. Para obter mais informações sobre como configurar o Azure PowerShell, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

## <a id="storage"></a>Crie uma conta de armazenamento para os seus registos

Embora possa utilizar uma conta de armazenamento existente para os seus registos, criaremos uma conta de armazenamento que será dedicada aos registos do Key Vault. Para conveniência para quando tivermos que especificar isto mais tarde, vamos armazenar os detalhes em uma variável chamada **sa**.

Para uma maior facilidade de gestão, também usaremos o mesmo grupo de recursos que aquele que contém o cofre chave. A partir do tutorial de início, este grupo de recursos [chama-se](key-vault-get-started.md) **ContosoResourceGroup,** e continuaremos a usar a localização da Ásia Oriental. Substitua estes valores por si próprio, conforme aplicável:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Se decidir utilizar uma conta de armazenamento existente, deve utilizar a mesma subscrição que o seu cofre chave. E deve usar o modelo de implantação do Gestor de Recursos Azure, em vez do modelo clássico de implantação.
>
>

## <a id="identify"></a>Identifique o cofre de chaves para os seus registos

No [tutorial de início,](key-vault-get-started.md)o nome chave do cofre era **ContosoKeyVault.** Continuaremos a usar esse nome e armazenaros os detalhes numa variável chamada **kv:**

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Ativar registo

Para ativar o registo do Key Vault, utilizaremos o cmdlet **Set-AzDiagnosticSetting,** juntamente com as variáveis que criámos para a nova conta de armazenamento e o cofre chave. Também definiremos a bandeira **ativada** para **$true** e definiremos a categoria para **AuditEvent** (a única categoria para a exploração de key vault):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

A saída é assim:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Esta saída confirma que o registo está agora ativado para o seu cofre chave, e irá guardar informações para a sua conta de armazenamento.

Opcionalmente, pode definir uma política de retenção para os seus registos de modo a que os registos mais antigos sejam automaticamente eliminados. Por exemplo, defina a política de retenção, definindo a bandeira **-RetençãoActivada** para **$true**, e definir o parâmetro **-RetençãoInDays** para **90** para que os registos com mais de 90 dias sejam automaticamente eliminados.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

O que é registado:

* Todos os pedidos autenticados da API REST, incluindo pedidos falhados como resultado de permissões de acesso, erros do sistema ou maus pedidos.
* Operações no cofre chave em si, incluindo criação, eliminação, definição de políticas de acesso ao cofre chave, e atualização de atributos chave do cofre, tais como tags.
* Operações sobre chaves e segredos no cofre da chave, incluindo:
  * Criar, modificar ou apagar estas chaves ou segredos.
  * Assinar, verificar, encriptar, desencriptar, embrulhar e desembrulhar chaves, obter segredos e listar chaves e segredos (e as suas versões).
* Pedidos não autenticados que resultam numa resposta 401. Exemplos são pedidos que não têm um símbolo portador, que são mal formados ou expirados, ou que têm um token inválido.  

## <a id="access"></a>Aceder aos seus registos

Os registos key Vault são armazenados no recipiente de **insights-logs-auditevent** na conta de armazenamento que forneceu. Para ver os registos, tem de descarregar bolhas.

Primeiro, crie uma variável para o nome do contentor. Você vai usar esta variável durante todo o resto da passagem.

```powershell
$container = 'insights-logs-auditevent'
```

Para listar todas as bolhas deste recipiente, introduza:

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

Como pode ver a partir desta saída, as bolhas seguem uma convenção de nomeação: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Os valores data e hora utilizam o UTC.

Uma vez que pode usar a mesma conta de armazenamento para recolher registos para vários recursos, o ID completo de recursos no nome blob é útil para aceder ou descarregar apenas as bolhas de que necessita. Mas antes de podermos fazer isso, teremos primeiro de saber como transferir todos os blobs.

Crie uma pasta para descarregar as bolhas. Por exemplo:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Em seguida, obtenha uma lista de todos os blobs:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Pipe esta lista através do **Get-AzStorageBlobContent** para descarregar as bolhas para a pasta de destino:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Quando executa este segundo comando, o **/** delimitador nos nomes blob cria uma estrutura de pasta completa sob a pasta de destino. Você usará esta estrutura para descarregar e armazenar as bolhas como ficheiros.

Para transferir seletivamente blobs, utilize carateres universais. Por exemplo:

* Se tiver vários cofres de chaves e pretender transferir registos apenas para um cofre de chaves designado CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Se tiver vários grupos de recursos e pretender transferir os registos para apenas um grupo de recursos, utilize `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Se quiser baixar todos os registos para o mês de janeiro de 2019, utilize `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Agora, está pronto para começar a procurar o conteúdo dos registos. Mas antes de passarmos a isso, deve saber mais dois comandos:

* Para consultar o estado das definições de diagnóstico para o recurso do seu cofre de chaves: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Para desativar o registo do seu cofre de chaves: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Interpretar os registos do seu Cofre de Chaves

Os blobs individuais são armazenadas como texto, formatados como um blob JSON. Vamos ver um exemplo de entrada de registo. Execute este comando:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Devolve uma entrada de registo semelhante a esta:

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

A tabela seguinte enumera os nomes e descrições do campo:

| Nome do campo | Descrição |
| --- | --- |
| **tempo** |Data e hora na UTC. |
| **recursosId** |ID de recurso de Gestor de recursos do Azure. Para os registos do Cofre chave, este é sempre o ID de recurso key vault. |
| **operaçãoNome** |Nome da operação, conforme documentada na tabela seguinte. |
| **operaçãoVersão** |Versão REST API solicitada pelo cliente. |
| **categoria** |Tipo de resultado. Para os registos key vault, **auditEvent** é o valor único e disponível. |
| **resultadoTipo** |Resultado do pedido de API REST. |
| **resultadoAssinatura** |Estado de HTTP. |
| **resultadoDescrição** |Descrição adicional sobre o resultado, quando disponível. |
| **duraçãoMs** |Tempo necessário para o processamento do pedido de API REST, em milissegundos. Não inclui a latência de rede, assim, o tempo que medir do lado do cliente poderá não corresponder a este período de tempo. |
| **callerIpAddress** |Endereço IP do cliente que fez o pedido. |
| **coralaid** |Um GUID opcional que o cliente pode passar para correlacionar os registos do lado do cliente com os registos do lado do serviço (Cofre de Chaves). |
| **identidade** |Identidade do símbolo que foi apresentado no pedido rest API. Trata-se geralmente de um "utilizador", um "diretor de serviço", ou a combinação "user+appId", como no caso de um pedido que resulta de um cmdlet Azure PowerShell. |
| **propriedades** |Informação que varia com base na operação **(operaçãoNome**). Na maioria dos casos, este campo contém informações do cliente (a cadeia do agente utilizador passada pelo cliente), o pedido exato da API REST API, e o código de estado HTTP. Além disso, quando um objeto é devolvido como resultado de um pedido (por exemplo, **KeyCreate** ou **VaultGet),** também contém a chave URI (como "id"), cofre URI, ou URI secreto. |

Os valores de campo **do nome da operação** estão no formato *ObjectVerb.* Por exemplo:

* Todas as operações de cofre têm o formato `Vault<action>`, como `VaultGet` e `VaultCreate`.
* Todas as operações-chave têm o formato `Key<action>`, como `KeySign` e `KeyList`.
* Todas as operações secretas têm o formato `Secret<action>`, como `SecretGet` e `SecretListVersions`.

A tabela seguinte lista os valores de nome de **funcionamento** e os comandos correspondentes da API REST:

| operationName | Comando REST API |
| --- | --- |
| **Autenticação** |Autenticação via Ponto final do Diretório Ativo Azure |
| **VaultGet** |[Obter informações sobre um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Criar ou atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **Abóbada Delete** |[Eliminar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **Lista de Cofres** |[Lista todos os cofres de chaves num grupo de recursos](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Criar uma chave](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Obter informações sobre uma chave](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **ChaveImport** |[Importar uma chave para um cofre](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Apoiar uma chave](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **TeclaEliminar** |[Eliminar uma chave](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **Restabelecimento da chave** |[Restaurar uma chave](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **Chave** |[Assinar com uma chave](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyCheck** |[Verificar com uma chave](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Moldar uma chave](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Desenrolar uma chave](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Encriptar com uma chave](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Desencriptar com uma chave](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Atualizar uma chave](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **Lista de Chaves** |[Lista as chaves num cofre](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **Versão sinuosa** |[Lista as versões de uma chave](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Criar um segredo](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Obter um segredo](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **Atualização Secreta** |[Atualizar um segredo](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **Eliminação secreta** |[Eliminar um segredo](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **Lista Secreta** |[Lista os segredos num cofre](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Lista as versões de um segredo](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Utilize os registos do Monitor Azure

Pode utilizar a solução Key Vault nos registos do Monitor Azure para rever os registos do Key Vault **AuditEvent.** Nos registos do Monitor Azure, utiliza consultas de registo para analisar dados e obter a informação de que necessita. 

Para mais informações, incluindo como configurar isto, consulte a [solução Azure Key Vault nos registos do Monitor Azure](../azure-monitor/insights/azure-key-vault.md). Este artigo também contém instruções se precisar de migrar da antiga solução Key Vault que foi oferecida durante a pré-visualização dos registos do Monitor Azure, onde primeiro encaminhou os seus registos para uma conta de armazenamento Azure e configuraram os registos do Monitor Azure para ler a partir daí.

## <a id="next"></a>Passos seguintes

Para um tutorial que utiliza o Cofre chave Azure numa aplicação web .NET, consulte [use Azure Key Vault a partir de uma aplicação web](tutorial-net-create-vault-azure-web-app.md).

Para as referências de programação, consulte o [Guia para programadores do Cofre de Chaves do Azure](key-vault-developers-guide.md).

Para obter uma lista de 1.0 cmdlets Azure PowerShell para Azure Key Vault, consulte O cofre de [chaves Azure cmdlets](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Para um tutorial sobre rotação de chaves e auditoria de log com Azure Key Vault, consulte [Configurar o Cofre chave com rotação e auditoria](key-vault-key-rotation-log-monitoring.md)de chave de ponta a ponta .

---
title: Solução de Cofre de chaves do Azure no Log Analytics | Documentos da Microsoft
description: Pode utilizar a solução do Azure Key Vault no Log Analytics para rever os registos do Azure Key Vault.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: richrund
ms.openlocfilehash: c3148ebe11ba0e23cbded5965234ece9fb6082aa
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317701"
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Solução de análise do Cofre de chaves do Azure no Log Analytics

![Símbolo do Key Vault](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode utilizar a solução do Azure Key Vault no Log Analytics para rever os registos AuditEvent do Azure Key Vault.

Para utilizar a solução, terá de ativar o registo de diagnósticos do Azure Key Vault e direcionar os diagnósticos para uma área de trabalho do Log Analytics. Não é necessário escrever os registos para o armazenamento de Blobs do Azure.

> [!NOTE]
> Em Janeiro de 2017, alterar a forma como suportada de enviar registos do Key Vault para o Log Analytics. Se a solução de Cofre de chaves que está a utilizar mostra *(preterido)* no título, consulte [migrar da solução de Cofre de chaves do antiga](#migrating-from-the-old-key-vault-solution) para obter os passos que tem de seguir.
>
>

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes instruções para instalar e configurar a solução do Azure Key Vault:

1. Ativar a solução de Cofre de chaves do Azure partir [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) ou utilizando o processo descrito na [adicionar soluções Log Analytics da Galeria de soluções](../../azure-monitor/insights/solutions.md).
2. Ativar registo de diagnósticos para os recursos do Cofre de chaves monitorizar, através da [portal](#enable-key-vault-diagnostics-in-the-portal) ou [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Ativar diagnósticos de Cofre de chaves no portal

1. No portal do Azure, navegue para o recurso do Key Vault para monitorizar
2. Selecione *registos de diagnóstico* para abrir a página seguinte

   ![imagem de mosaico do Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Clique em *ativar os diagnósticos* para abrir a página seguinte

   ![imagem de mosaico do Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Para ativar os diagnósticos, clique em *nos* sob *Estado*
5. Clique a caixa de verificação *enviar para o Log Analytics*
6. Selecione uma área de trabalho do Log Analytics existente ou crie uma área de trabalho
7. Para habilitar *AuditEvent* registos, clique na caixa de verificação, em Log
8. Clique em *guardar* para ativar o registo de diagnóstico para o Log Analytics

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Ativar diagnósticos de Key Vault com o PowerShell
O seguinte script do PowerShell fornece um exemplo de como utilizar `Set-AzDiagnosticSetting` para ativar o registo de diagnóstico para o Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Rever os detalhes de recolha de dados do Azure Key Vault
Solução de Cofre de chaves do Azure recolhe registos de diagnóstico diretamente a partir do Key Vault.
Não é necessário escrever os registos para o armazenamento de Blobs do Azure e sem agente é obrigatório para a recolha de dados.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o Azure Key Vault.

| Plataforma | Agente direto | Agente do Systems Center Operations Manager | Azure | Gestor de operações necessárias? | Dados de agente do Operations Manager enviados por grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | na chegada |

## <a name="use-azure-key-vault"></a>Utilizar o Azure Key Vault
Depois de [instalar a solução](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), ver os dados do Key Vault ao clicar o **Azure Key Vault** mosaico do **descrição geral** página do Log Analytics.

![imagem de mosaico do Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-tile.png)

Depois de clicar no **descrição geral** mosaico, pode ver resumos dos seus registos e, em seguida, desagregar detalhes para as seguintes categorias:

* Volume de todas as operações do Cofre de chaves ao longo do tempo
* Falha de volumes de operações ao longo do tempo
* Média da latência operacional por operação
* Qualidade de serviço para operações com o número de operações que levam mais de 1000 ms e uma lista de operações que levam mais de 1000 ms

![imagem do dashboard do Cofre de chaves do Azure](media/azure-key-vault/log-analytics-keyvault01.png)

![imagem do dashboard do Cofre de chaves do Azure](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Para ver os detalhes para qualquer operação
1. Sobre o **descrição geral** página, clique no **Azure Key Vault** mosaico.
2. Sobre o **do Azure Key Vault** dashboard, reveja as informações de resumo em um dos painéis de e, em seguida, clique num para ver informações detalhadas sobre ele na página de pesquisa de registo de.

    Em qualquer uma das páginas de pesquisa de registo, pode ver os resultados por tempo, os resultados detalhados e seu histórico de pesquisa de registos. Também pode filtrar por facetas para refinar os resultados.

## <a name="log-analytics-records"></a>Registos do Log Analytics
A solução de Cofre de chaves do Azure analisa os registos que tenham um tipo de **KeyVaults** que são recolhidos a partir de [os registos AuditEvent](../../key-vault/key-vault-logging.md) no diagnóstico do Azure.  Propriedades para estes registos estão na tabela a seguir:  

| Propriedade | Descrição |
|:--- |:--- |
| Type |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |Endereço IP do cliente que efetuou o pedido |
| Categoria | *AuditEvent* |
| CorrelationId |Um GUID opcional que o cliente pode passar para correlacionar os registos do lado do cliente com os registos do lado do serviço (Cofre de Chaves). |
| DurationMs |Tempo necessário para o processamento do pedido de API REST, em milissegundos. Desta vez não inclui a latência de rede, para que o tempo que medir do lado do cliente pode não corresponder neste momento. |
| httpStatusCode_d |Código de estado HTTP devolvido pelo pedido (por exemplo, *200*) |
| id_s |ID exclusivo do pedido |
| identity_claim_appid_g | GUID para o id da aplicação |
| OperationName |Nome da operação, conforme documentado no [registo do Cofre de chaves do Azure](../../key-vault/key-vault-logging.md) |
| OperationVersion |Versão de REST API solicitada pelo cliente (por exemplo *2015-06-01*) |
| requestUri_s |URI do pedido |
| Recurso |Nome do Cofre de chaves |
| ResourceGroup |Grupo de recursos do Cofre de chaves |
| ResourceId |ID do Recurso do Azure Resource Manager Para registos do Cofre de chaves, este é o ID de recurso do Key Vault. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| resultSignature |Estado de HTTP (por exemplo, *OK*) |
| ResultType |Resultado do pedido de REST API (por exemplo, *êxito*) |
| SubscriptionId |ID de subscrição do Azure da subscrição que contém o Key Vault |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrar a partir da antiga solução de Cofre de chaves
Em Janeiro de 2017, alterar a forma como suportada de enviar registos do Key Vault para o Log Analytics. Estas alterações fornecem as seguintes vantagens:
+ Os registos são escritos diretamente ao Log Analytics sem a necessidade de usar uma conta de armazenamento
+ Menos latência a partir da hora de quando os registos são gerados para eles estejam disponíveis no Log Analytics
+ Menos passos de configuração
+ Um formato comum para todos os tipos de diagnóstico do Azure

Para utilizar a solução atualizada:

1. [Configurar diagnóstico sejam enviadas diretamente para o Log Analytics do Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Ativar a solução de Cofre de chaves do Azure utilizando o processo descrito [adicionar soluções Log Analytics da Galeria de soluções](../../azure-monitor/insights/solutions.md)
3. Atualizar quaisquer consultas guardadas, dashboards ou alertas para usar o novo tipo de dados
  + Tipo é a mudança do: KeyVaults para AzureDiagnostics. Pode usar o ResourceType para filtrar para registos do Cofre de chave.
  - Em vez de: `KeyVaults`, utilize `AzureDiagnostics | where ResourceType'=="VAULTS"`
  + Campos: (Os nomes de campos diferenciam maiúsculas de minúsculas)
  - Para qualquer campo que tem um sufixo de \_s, \_1!d, ou \_g o nome, altere o primeiro caráter em minúsculas
  - Para qualquer campo que tem um sufixo de \_o nome, os dados é dividido em campos individuais com base nos nomes de campos aninhados. Por exemplo, o UPN do chamador é armazenado num campo `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   - Campo CallerIpAddress alterado para CallerIPAddress
   - Campo RemoteIPCountry já não está presente
4. Remover os *análise do Key Vault (preterido)* solução. Se estiver a utilizar o PowerShell, utilize `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Os dados recolhidos antes da alteração não é visível na solução de novo. Pode continuar a consultar estes dados com o tipo de antigo e nomes de campos.

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passos Seguintes
* Uso [pesquisas de registos no Log Analytics](../../azure-monitor/log-query/log-query-overview.md) para ver os dados detalhados do Azure Key Vault.

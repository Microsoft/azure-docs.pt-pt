---
title: Azure Key Vault solução no Azure Monitor | Microsoft Docs
description: Você pode usar a solução Azure Key Vault no Azure Monitor para examinar os logs de Azure Key Vault.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: bwren
ms.openlocfilehash: 1e0e9a0d76e644ec48ecd423a105dd89629d290c
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997694"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Solução de análise de Azure Key Vault no Azure Monitor

![Símbolo de Key Vault](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Você pode usar a solução Azure Key Vault no Azure Monitor para examinar Azure Key Vault logs do AuditEvent.

Para usar a solução, você precisa habilitar o log de diagnósticos de Azure Key Vault e direcionar o diagnóstico para um espaço de trabalho do Log Analytics. Não é necessário gravar os logs no armazenamento de BLOBs do Azure.

> [!NOTE]
> Em janeiro de 2017, o modo com suporte de envio de logs de Key Vault para Log Analytics alterado. Se a solução de Key Vault que você está usando for mostrada *(preterida)* no título, consulte [migrando da solução de Key Vault antiga](#migrating-from-the-old-key-vault-solution) para obter as etapas que você precisa seguir.
>
>

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Use as instruções a seguir para instalar e configurar a solução de Azure Key Vault:

1. Use o processo descrito em [Adicionar soluções de Azure monitor da Galeria de soluções](../../azure-monitor/insights/solutions.md) para adicionar a solução de Azure Key Vault ao espaço de trabalho do log Analytics.
2. Habilitar o log de diagnóstico para os recursos de Key Vault a serem monitorados, usando o [portal](#enable-key-vault-diagnostics-in-the-portal) ou o [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Habilitar o diagnóstico de Key Vault no portal

1. Na portal do Azure, navegue até o recurso de Key Vault para monitorar
2. Selecione *configurações de diagnóstico* para abrir a página a seguir

   ![imagem do bloco de Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Clique em *Ativar diagnóstico* para abrir a página a seguir

   ![imagem do bloco de Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Dê um nome para a configuração de diagnóstico.
5. Clique na caixa de seleção *Enviar para log Analytics*
6. Selecione um espaço de trabalho Log Analytics existente ou crie um espaço de trabalho
7. Para habilitar os logs do *AuditEvent* , clique na caixa de seleção em log
8. Clique em *salvar* para habilitar o log de diagnósticos para log Analytics espaço de trabalho.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Habilitar o diagnóstico de Key Vault usando o PowerShell
O script do PowerShell a seguir fornece um exemplo de como `Set-AzDiagnosticSetting` usar o para habilitar o log de diagnóstico para Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Examinar Azure Key Vault detalhes da coleta de dados
Azure Key Vault solução coleta logs de diagnóstico diretamente do Key Vault.
Não é necessário gravar os logs no armazenamento de BLOBs do Azure e nenhum agente é necessário para a coleta de dados.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para Azure Key Vault.

| Plataforma | Agente direto | Agente de Operations Manager do Systems Center | Azure | Operations Manager necessário? | Operations Manager dados do agente enviados por meio do grupo de gerenciamento | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | na chegada |

## <a name="use-azure-key-vault"></a>Utilizar o Azure Key Vault
Depois de [instalar a solução](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), exiba os dados de Key Vault clicando no bloco **análise do Key Vault** na página **visão geral** Azure monitor. Abra essa página no menu **Azure monitor** clicando em **mais** na seção insights. 

![imagem do bloco de Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-tile.png)

Depois de clicar no bloco **análise do Key Vault** , você poderá exibir resumos de seus logs e, em seguida, analisar os detalhes para as seguintes categorias:

* Volume de todas as operações do Key Vault ao longo do tempo
* Volumes de operação com falha ao longo do tempo
* Latência operacional média por operação
* Qualidade de serviço para operações com o número de operações que levam mais de 1000 MS e uma lista de operações que levam mais de 1000 MS

![imagem do painel de Azure Key Vault](media/azure-key-vault/log-analytics-keyvault01.png)

![imagem do painel de Azure Key Vault](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Para exibir detalhes de qualquer operação
1. Na página **visão geral** , clique no bloco **análise do Key Vault** .
2. No painel **Azure Key Vault** , examine as informações resumidas em uma das folhas e clique em uma para exibir informações detalhadas sobre ela na página pesquisa de logs.

    Em qualquer uma das páginas de pesquisa de log, você pode exibir os resultados por tempo, resultados detalhados e seu histórico de pesquisa de log. Você também pode filtrar por facetas para restringir os resultados.

## <a name="azure-monitor-log-records"></a>Azure Monitor registros de log
A solução de Azure Key Vault analisa os registros que têm um tipo de **cofre** de chaves que são coletados dos [logs do AuditEvent](../../key-vault/key-vault-logging.md) no diagnóstico do Azure.  As propriedades desses registros estão na tabela a seguir:  

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |Endereço IP do cliente que fez a solicitação |
| `Category` | *AuditEvent* |
| `CorrelationId` |Um GUID opcional que o cliente pode passar para correlacionar os registos do lado do cliente com os registos do lado do serviço (Cofre de Chaves). |
| `DurationMs` |Tempo necessário para o processamento do pedido de API REST, em milissegundos. Esse tempo não inclui a latência de rede, portanto, o tempo que você mede no lado do cliente pode não corresponder a esse tempo. |
| `httpStatusCode_d` |Código de status HTTP retornado pela solicitação (por exemplo, *200*) |
| `id_s` |ID exclusiva da solicitação |
| `identity_claim_appid_g` | GUID para a ID do aplicativo |
| `OperationName` |Nome da operação, conforme documentado em [log de Azure Key Vault](../../key-vault/key-vault-logging.md) |
| `OperationVersion` |Versão da API REST solicitada pelo cliente (por exemplo, *2015-06-01*) |
| `requestUri_s` |URI da solicitação |
| `Resource` |Nome do cofre de chaves |
| `ResourceGroup` |Grupo de recursos do cofre de chaves |
| `ResourceId` |ID do Recurso do Azure Resource Manager Para logs de Key Vault, essa é a ID de recurso de Key Vault. |
| `ResourceProvider` |*MICROSOFT.KEYVAULT* |
| `ResourceType` | *VAULTS* |
| `ResultSignature` |Status HTTP (por exemplo, *OK*) |
| `ResultType` |Resultado da solicitação da API REST (por exemplo, *êxito*) |
| `SubscriptionId` |ID de assinatura do Azure da assinatura que contém o Key Vault |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrando da solução de Key Vault antiga
Em janeiro de 2017, o modo com suporte de envio de logs de Key Vault para Log Analytics alterado. Essas alterações oferecem as seguintes vantagens:
+ Os logs são gravados diretamente em um espaço de trabalho Log Analytics sem a necessidade de usar uma conta de armazenamento
+ Menos latência a partir do momento em que os logs são gerados para serem disponibilizados no Log Analytics
+ Menos etapas de configuração
+ Um formato comum para todos os tipos de diagnóstico do Azure

Para usar a solução atualizada:

1. [Configurar o diagnóstico para ser enviado diretamente a um Log Analytics espaço de trabalho de Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Habilite a solução de Azure Key Vault usando o processo descrito em [Adicionar soluções de Azure monitor do Galeria de soluções](../../azure-monitor/insights/solutions.md)
3. Atualizar quaisquer consultas, painéis ou alertas salvos para usar o novo tipo de dados
   + O tipo é alterado de: Keyvaults para AzureDiagnostics. Você pode usar o ResourceType para filtrar os logs de Key Vault.
   + Em vez de `KeyVaults`:, use`AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Campo (Os nomes de campo diferenciam maiúsculas de minúsculas)
   + Para qualquer campo que tenha um sufixo de \_s, \_d ou \_g no nome, altere o primeiro caractere para minúscula
   + Para qualquer campo que tenha um sufixo de \_o em nome, os dados são divididos em campos individuais com base nos nomes de campo aninhados. Por exemplo, o UPN do chamador é armazenado em um campo`identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Campo CallerIpAddress alterado para CallerIPAddress
   + O campo RemoteIPCountry não está mais presente
4. Remova a solução *análise do Key Vault (preterida)* . Se você estiver usando o PowerShell, use`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Os dados coletados antes da alteração não são visíveis na nova solução. Você pode continuar a consultar esses dados usando o tipo e os nomes de campo antigos.

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passos Seguintes
* Use [consultas de log em Azure monitor](../../azure-monitor/log-query/log-query-overview.md) para exibir dados detalhados de Azure Key Vault.

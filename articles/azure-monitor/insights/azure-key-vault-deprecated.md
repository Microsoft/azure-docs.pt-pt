---
title: Solução Azure Key Vault no Azure Monitor Microsoft Docs
description: Pode utilizar a solução Azure Key Vault no Azure Monitor para rever os registos do Cofre de Chaves Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: f6d8929c8fd59836ff297f226851890892c10acc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445137"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Solução Azure Key Vault Analytics no Azure Monitor

> [!NOTE]
> Esta solução é prevadizada. [Recomendamos agora a utilização do Monitor Azure para o Cofre de Chaves](./key-vault-insights-overview.md).

![Símbolo do cofre da chave](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode utilizar a solução Azure Key Vault no Azure Monitor para rever os registos Azure Key Vault AuditEvent.

Para utilizar a solução, é necessário ativar o registo de diagnósticos Azure Key Vault e direcionar os diagnósticos para um espaço de trabalho log Analytics. Não é necessário escrever os registos para o armazenamento da Azure Blob.

> [!NOTE]
> Em janeiro de 2017, a forma suportada de enviar registos do Key Vault para Log Analytics mudou. Se a solução Key Vault que está a utilizar estiver a utilizar mostra *(depreciada)* no título, consulte a [migração da antiga solução Key Vault](#migrating-from-the-old-key-vault-solution) para os passos que precisa de seguir.
>
>

## <a name="install-and-configure-the-solution"></a>Instale e configuure a solução
Utilize as seguintes instruções para instalar e configurar a solução Azure Key Vault:

1. Utilize o processo descrito nas [soluções Add Azure Monitor da Galeria solutions](./solutions.md) para adicionar a solução Azure Key Vault ao seu espaço de trabalho Log Analytics.
2. Permitir a monitorização dos recursos do Cofre de Chaves, utilizando o [portal](#enable-key-vault-diagnostics-in-the-portal) ou o [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Ativar diagnósticos de Cofre de Chaves no portal

1. No portal Azure, navegue para o recurso Key Vault para monitorizar
2. Selecione *definições de Diagnóstico* para abrir a página seguinte

   ![Screenshot da página de definições de Diagnóstico para o recurso Key Vault ContosoKVSCUS.A opção de ligar os diagnósticos é realçada.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Clique *em Ligar os diagnósticos* para abrir a página seguinte

   ![Screenshot da página para configurar as definições de Diagnóstico. As opções de Envio para Log Analytics, AuditEvent e AllMetrics são selecionadas.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Dê um nome à definição de diagnóstico.
5. Clique na caixa de verificação para *Enviar para Registar Analytics*
6. Selecione um espaço de trabalho log analytics existente ou crie um espaço de trabalho
7. Para ativar os registos *AuditEvent,* clique na caixa de verificação em Log
8. Clique em *Guardar* para ativar o registo de diagnósticos para log analytics espaço de trabalho.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Ativar diagnósticos de Cofre de Chaves usando PowerShell
O seguinte script PowerShell fornece um exemplo de como usar `Set-AzDiagnosticSetting` para permitir a sessão de registo de recursos para o Cofre de Chaves:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Rever detalhes da recolha de dados do Azure Key Vault
A solução Azure Key Vault recolhe registos de diagnóstico diretamente do Cofre de Chaves.
Não é necessário escrever os registos para o armazenamento da Azure Blob e nenhum agente é necessário para a recolha de dados.

A tabela seguinte mostra métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o Azure Key Vault.

| Plataforma | Agente direto | Agente gestor de operações do Centro de Sistemas | Azure | Diretor de Operações necessário? | Dados de agente do Gestor de Operações enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | na chegada |

## <a name="use-azure-key-vault"></a>Utilizar o Azure Key Vault
Depois de [instalar a solução,](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)consulte os dados do Key Vault, clicando no azulejo **Key Vault Analytics** a partir da página **'Vista Geral do** Monitor Azure'. Abra esta página a partir do menu **Azure Monitor** clicando **mais** na secção **Insights.** 

![Screenshot do azulejo Key Vault Analytics na página geral do Monitor Azure mostrando um gráfico do volume de operações do cofre chave ao longo do tempo.](media/azure-key-vault/log-analytics-keyvault-tile.png)

Depois de clicar no azulejo **Key Vault Analytics,** pode ver resumos dos seus registos e, em seguida, perfurar em detalhes para as seguintes categorias:

* Volume de todas as operações de cofre chave ao longo do tempo
* Volumes de operação falhados ao longo do tempo
* Latência operacional média por operação
* Qualidade do serviço para operações com o número de operações que levam mais de 1000 ms e uma lista de operações que levam mais de 1000 ms

![Screenshot do painel Azure Key Vault mostrando azulejos com dados gráficos para todas as operações, operações falhadas e latência operacional média.](media/azure-key-vault/log-analytics-keyvault01.png)

![Screenshot do painel Azure Key Vault mostrando azulejos com dados para a Latência Operacional Média, Qualidade de Serviço e Pesquisas Recomendadas.](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Para ver detalhes para qualquer operação
1. Na página **'Visão Geral',** clique no azulejo **Key Vault Analytics.**
2. No painel **Azure Key Vault,** reveja as informações de resumo numa das lâminas e, em seguida, clique numa para ver informações detalhadas sobre o mesmo na página de pesquisa de registo.

    Em qualquer uma das páginas de pesquisa de registo, pode ver resultados pelo tempo, resultados detalhados e o histórico de pesquisa de registo. Também pode filtrar por facetas para reduzir os resultados.

## <a name="azure-monitor-log-records"></a>Registos de registos do Azure Monitor
A solução Azure Key Vault analisa registos que têm um tipo de **KeyVaults** que são recolhidos a partir de [registos AuditEvent](../../key-vault/general/logging.md) em Azure Diagnostics.  As propriedades para estes registos encontram-se na tabela seguinte:  

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |Endereço IP do cliente que fez o pedido |
| `Category` | *Evento de Auditoria* |
| `CorrelationId` |Um GUID opcional que o cliente pode passar para correlacionar os registos do lado do cliente com os registos do lado do serviço (Cofre de Chaves). |
| `DurationMs` |Tempo necessário para o processamento do pedido de API REST, em milissegundos. Desta vez não inclui a latência da rede, por isso o tempo que medir do lado do cliente pode não corresponder desta vez. |
| `httpStatusCode_d` |Código de estado HTTP devolvido pelo pedido (por exemplo, *200*) |
| `id_s` |ID exclusivo do pedido |
| `identity_claim_appid_g` | GUID para o ID da aplicação |
| `OperationName` |Nome da operação, conforme documentado no [Azure Key Vault Logging](../../key-vault/general/logging.md) |
| `OperationVersion` |Versão REST API solicitada pelo cliente (por exemplo *2015-06-01)* |
| `requestUri_s` |Uri do pedido |
| `Resource` |Nome do cofre chave |
| `ResourceGroup` |Grupo de recursos do cofre chave |
| `ResourceId` |ID do Recurso do Azure Resource Manager Para os registos do Cofre de Chaves, este é o ID de recurso do Cofre de Chaves. |
| `ResourceProvider` |*A MICROSOFT. KEYVAULT* |
| `ResourceType` | *COFRES* |
| `ResultSignature` |Estado HTTP (por exemplo, *OK)* |
| `ResultType` |Resultado do pedido de API REST (por exemplo, *Sucesso)* |
| `SubscriptionId` |ID de assinatura Azure da assinatura que contém o Cofre-Chave |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrando da antiga solução Key Vault
Em janeiro de 2017, a forma suportada de enviar registos do Key Vault para Log Analytics mudou. Estas alterações fornecem as seguintes vantagens:
+ Os registos são escritos diretamente num espaço de trabalho do Log Analytics sem a necessidade de utilizar uma conta de armazenamento
+ Menos latência a partir do momento em que os registos são gerados para que estejam disponíveis no Log Analytics
+ Menos passos de configuração
+ Um formato comum para todos os tipos de diagnósticos Azure

Para utilizar a solução atualizada:

1. [Configurar diagnósticos para serem enviados diretamente para um espaço de trabalho Log Analytics a partir do Cofre de Chaves](#enable-key-vault-diagnostics-in-the-portal)  
2. Ativar a solução Azure Key Vault utilizando o processo descrito nas [soluções Add Azure Monitor da Galeria de Soluções](./solutions.md)
3. Atualize quaisquer consultas guardadas, dashboards ou alertas para utilizar o novo tipo de dados
   + O tipo é alterado de: KeyVaults para AzureDiagnostics. Pode utilizar o ResourceType para filtrar os registos do cofre de chaves.
   + Em vez de: `KeyVaults` , usar `AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Campos: (Os nomes de campo são sensíveis a casos)
   + Para qualquer campo que tenha um sufixo de \_ s, \_ d ou g \_ no nome, mude o primeiro personagem para minúsculas
   + Para qualquer campo que tenha um sufixo de \_ o em nome, os dados são divididos em campos individuais com base nos nomes de campo aninhados. Por exemplo, a UPN do chamador é armazenada num campo `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Field CallerIpAddress alterado para CallerIPAddress
   + Field RemoteIPCountry já não está presente
4. Remova a solução *Key Vault Analytics (Deprecada).* Se estiver a utilizar o PowerShell, use `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Os dados recolhidos antes da alteração não são visíveis na nova solução. Pode continuar a consultar estes dados utilizando os nomes antigos do Tipo e do campo.

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passos seguintes
* Utilize [consultas de log no Azure Monitor](../log-query/log-query-overview.md) para ver dados detalhados do Cofre da Chave Azure.


---
title: Solução Azure Key Vault no Monitor Azure [ Microsoft Docs
description: Pode utilizar a solução Azure Key Vault no Azure Monitor para rever os registos do Cofre de Chaves Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 7da2fa2ddfbd9c71563dd8bd2e17b14c6dee62b3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455467"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Solução azure key vault Analytics no Monitor Azure

![Símbolo do cofre da chave](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode utilizar a solução Azure Key Vault no Azure Monitor para rever os registos de Auditoria do Cofre de Chaves do Azure.

Para utilizar a solução, é necessário ativar o registo de diagnósticos do Cofre de Chaves Azure e direcionar os diagnósticos para um espaço de trabalho de Log Analytics. Não é necessário escrever os registos para o armazenamento da Blob Azure.

> [!NOTE]
> Em janeiro de 2017, a forma suportada de enviar registos de Key Vault para Log Analytics mudou. Se a solução Key Vault estiver a usar espetáculos *(depreciados)* no título, consulte a [migração da antiga solução Key Vault](#migrating-from-the-old-key-vault-solution) para os passos que precisa seguir.
>
>

## <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes instruções para instalar e configurar a solução Azure Key Vault:

1. Utilize o processo descrito nas [soluções Add Azure Monitor da Galeria Solutions](../../azure-monitor/insights/solutions.md) para adicionar a solução Azure Key Vault ao seu espaço de trabalho Log Analytics.
2. Ativar o registo de diagnósticos para os recursos do Key Vault para monitorizar, utilizando o [portal](#enable-key-vault-diagnostics-in-the-portal) ou [powerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Ativar diagnósticos chave vault no portal

1. No portal Azure, navegue até ao recurso Key Vault para monitorizar
2. Selecione *definições de Diagnóstico* para abrir a seguinte página

   ![imagem de azulejo azure key vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Clique *em ligar diagnósticos* para abrir a seguinte página

   ![imagem de azulejo azure key vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Dê um nome à definição de diagnóstico.
5. Clique na caixa de verificação para *enviar para registar análises*
6. Selecione um espaço de trabalho existente no Log Analytics ou crie um espaço de trabalho
7. Para *AuditEvent* ativar registos auditais, clique na caixa de verificação em Registo
8. Clique em *Guardar* para ativar o registo de diagnósticos para log Analytics espaço de trabalho.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Ativar diagnósticos key vault usando PowerShell
O seguinte script PowerShell fornece um `Set-AzDiagnosticSetting` exemplo de como usar para ativar o registo de recursos para o Cofre chave:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Rever detalhes da recolha de dados do Cofre de Chaves Azure
A solução Azure Key Vault recolhe registos de diagnóstico diretamente do Cofre chave.
Não é necessário escrever os registos para o armazenamento da Blob Azure e nenhum agente é necessário para a recolha de dados.

A tabela que se segue mostra métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o Cofre chave azure.

| Plataforma | Agente direto | Agente gestor de operações do Centro de Sistemas | Azure | Gestor de Operações necessário? | Dados do agente de operações enviados através de grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | à chegada |

## <a name="use-azure-key-vault"></a>Utilizar o Azure Key Vault
Depois de [instalar a solução,](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)veja os dados do Key Vault clicando no azulejo **Key Vault Analytics** a partir da página de visão **geral** do Monitor Azure. Abra esta página a partir do menu **Do Monitor Azure** clicando **mais** na secção **Insights.** 

![imagem de azulejo azure key vault](media/azure-key-vault/log-analytics-keyvault-tile.png)

Depois de clicar no azulejo **Key Vault Analytics,** pode ver resumos dos seus registos e, em seguida, perfurar detalhes para as seguintes categorias:

* Volume de todas as operações chave do cofre ao longo do tempo
* Volumes de operação falhados ao longo do tempo
* Latência operacional média por operação
* Qualidade de serviço para operações com o número de operações que levam mais de 1000 ms e uma lista de operações que levam mais de 1000 ms

![imagem do painel do Cofre de Chaves Azure](media/azure-key-vault/log-analytics-keyvault01.png)

![imagem do painel do Cofre de Chaves Azure](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Para ver detalhes para qualquer operação
1. Na página **'Overview',** clique no azulejo **Key Vault Analytics.**
2. No painel do Cofre de **Chaves Azure,** reveja as informações sumárias numa das lâminas e clique numa para ver informações detalhadas sobre o mesmo na página de pesquisa de registo.

    Em qualquer uma das páginas de pesquisa de registo, pode visualizar resultados por tempo, resultados detalhados e o seu histórico de pesquisa de registo. Também pode filtrar por facetas para reduzir os resultados.

## <a name="azure-monitor-log-records"></a>Registos de registos do Monitor Azure
A solução Azure Key Vault analisa registos que têm um tipo de **KeyVaults** que são recolhidos a partir de [registos de AuditEvent](../../key-vault/general/logging.md) em Diagnósticos Azure.  As propriedades para estes registos estão na tabela seguinte:  

| Propriedade | Descrição |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |Endereço IP do cliente que fez o pedido |
| `Category` | *Evento de Auditoria* |
| `CorrelationId` |Um GUID opcional que o cliente pode passar para correlacionar os registos do lado do cliente com os registos do lado do serviço (Cofre de Chaves). |
| `DurationMs` |Tempo necessário para o processamento do pedido de API REST, em milissegundos. Desta vez não inclui latência de rede, pelo que o tempo que mede do lado do cliente pode não corresponder desta vez. |
| `httpStatusCode_d` |Código de estado HTTP devolvido pelo pedido (por exemplo, *200*) |
| `id_s` |Identificação única do pedido |
| `identity_claim_appid_g` | GUID para o ID de aplicação |
| `OperationName` |Nome da operação, como documentado na [Exploração do Cofre](../../key-vault/general/logging.md) de Chaves Azure |
| `OperationVersion` |Versão REST API solicitada pelo cliente (por exemplo *2015-06-01*) |
| `requestUri_s` |Uri do pedido |
| `Resource` |Nome do cofre da chave |
| `ResourceGroup` |Grupo de recursos do cofre chave |
| `ResourceId` |ID do Recurso do Azure Resource Manager Para os registos do Cofre chave, este é o ID de recurso key vault. |
| `ResourceProvider` |*A MICROSOFT. COFRE DE CHAVES* |
| `ResourceType` | *COFRES* |
| `ResultSignature` |Estado HTTP (por exemplo, *OK)* |
| `ResultType` |Resultado do pedido de API REST (por exemplo, *Sucesso)* |
| `SubscriptionId` |Id de subscrição Azure da subscrição que contém o Cofre chave |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrando da antiga solução Key Vault
Em janeiro de 2017, a forma suportada de enviar registos de Key Vault para Log Analytics mudou. Estas alterações proporcionam as seguintes vantagens:
+ Os registos são escritos diretamente para um espaço de trabalho log Analytics sem a necessidade de usar uma conta de armazenamento
+ Menos latência a partir do momento em que os registos são gerados para que estejam disponíveis no Log Analytics
+ Menos passos de configuração
+ Um formato comum para todos os tipos de diagnósticos Azure

Para utilizar a solução atualizada:

1. [Configure diagnósticos a serem enviados diretamente para um espaço de trabalho de Log Analytics a partir do Cofre chave](#enable-key-vault-diagnostics-in-the-portal)  
2. Ative a solução Azure Key Vault utilizando o processo descrito nas [soluções Add Azure Monitor da Galeria Solutions](../../azure-monitor/insights/solutions.md)
3. Atualizar quaisquer consultas, dashboards ou alertas guardados para usar o novo tipo de dados
   + O tipo é alterado de: KeyVaults para AzureDiagnostics. Pode utilizar o ResourceType para filtrar os registos do cofre de chaves.
   + Em vez `KeyVaults`de: , usar`AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Campos: (Os nomes de campo são sensíveis aos casos)
   + Para qualquer campo que tenha \_um \_sufixo \_de sufixo de sufixo de s, d, ou g no nome, mude o primeiro personagem para minúscula
   + Para qualquer campo que tenha \_um sufixo de o em nome, os dados são divididos em campos individuais com base nos nomes de campo aninhados. Por exemplo, a UPN do chamador é armazenada num campo`identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Chamada de campoIpAddress alterado para CallerIPAddress
   + Field RemoteIPCountry já não está presente
4. Remova a solução *Key Vault Analytics (Depreciada).* Se estiver a usar powerShell, use`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Os dados recolhidos antes da alteração não são visíveis na nova solução. Pode continuar a consultar estes dados utilizando os antigos nomes de Tipo e campo.

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passos seguintes
* Utilize consultas de [registo no Monitor Azure](../../azure-monitor/log-query/log-query-overview.md) para visualizar dados detalhados do Cofre de Chaves Azure.

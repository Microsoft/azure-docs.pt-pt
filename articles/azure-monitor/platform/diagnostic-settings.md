---
title: Criar definições de diagnóstico para enviar registos e métricas de plataforma para diferentes destinos
description: Envie métricas e registos da plataforma Azure Monitor para registos do Monitor Azure, armazenamento Azure ou Hubs de Eventos Azure utilizando uma definição de diagnóstico.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: a037eddb13645036fcbe501ecba33923733b6d03
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84944377"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>Criar definições de diagnóstico para enviar registos e métricas de plataforma para diferentes destinos
[Os registos da plataforma](platform-logs-overview.md) em Azure, incluindo os registos de atividades Azure e registos de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e para a plataforma Azure em que dependem. [As métricas da plataforma](data-platform-metrics.md) são recolhidas por padrão e normalmente armazenadas na base de dados de métricas do Azure Monitor. Este artigo fornece detalhes sobre a criação e configuração de configurações de diagnóstico para enviar métricas de plataforma e registos de plataforma para diferentes destinos.

> [!IMPORTANT]
> Antes de criar uma definição de diagnóstico para o registo de Atividade, deve primeiro desativar qualquer configuração antiga. Consulte [os métodos de recolha legacy](activity-log.md#legacy-collection-methods) para mais detalhes.

Cada recurso Azure requer a sua própria definição de diagnóstico, que define os seguintes critérios:

- Categorias de registos e dados métricos enviados para os destinos definidos na definição. As categorias disponíveis variam para diferentes tipos de recursos.
- Um ou mais destinos para enviar os registos. Os destinos atuais incluem log analytics espaço de trabalho, Centros de Eventos e Armazenamento Azure.

Uma única definição de diagnóstico não pode definir mais do que um dos destinos. Se pretender enviar dados para mais de um determinado tipo de destino (por exemplo, dois espaços de trabalho diferentes do Log Analytics), então crie várias configurações. Cada recurso pode ter até 5 configurações de diagnóstico.

> [!NOTE]
> [As métricas da plataforma](metrics-supported.md) são enviadas automaticamente para as [Métricas do Monitor Azure](data-platform-metrics.md). As definições de diagnóstico podem ser usadas para enviar métricas para certos serviços Azure monitor para análise com outros dados de monitorização usando [consultas de registo](../log-query/log-query-overview.md) com determinadas limitações. 
>  
>  
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões. *Por exemplo:* A métrica 'IOReadBytes' de um Blockchain pode ser explorada e mapeada a um nível por nó. No entanto, quando exportado através de configurações de diagnóstico, a métrica exportada representa como todos os bytes lidos para todos os nós. Além disso, devido a limitações internas, nem todas as métricas são exportáveis para Registos monitores Azure / Log Analytics. Para mais informações, consulte a [lista de métricas exportáveis.](metrics-supported-export-diagnostic-settings.md) 
>  
>  
> Para contornar estas limitações para métricas específicas, sugerimos que extraí-las manualmente utilizando as [Métricas REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list) e importá-las em Registos monitores Azure utilizando a [API do coletor de dados do Monitor Azure.](data-collector-api.md)  


## <a name="destinations"></a>Destinos

Os registos e métricas da plataforma podem ser enviados para os destinos na tabela seguinte. Siga cada link na tabela seguinte para obter detalhes sobre o envio de dados para esse destino.

| Destino | Description |
|:---|:---|
| [Área de trabalho do Log Analytics](#log-analytics-workspace) | O envio de registos e métricas para um espaço de trabalho do Log Analytics permite analisá-los com outros dados de monitorização recolhidos pelo Azure Monitor utilizando consultas de registos poderosas e também para alavancar outras funcionalidades do Azure Monitor, tais como alertas e visualizações. |
| [Centros de eventos](#event-hub) | O envio de registos e métricas para Os Centros de Eventos permite-lhe transmitir dados para sistemas externos, tais como SIEMs de terceiros e outras soluções de análise de registo. |
| [Conta de armazenamento Azure](#azure-storage) | Arquivar registos e métricas para uma conta de armazenamento Azure é útil para auditoria, análise estática ou backup. Em comparação com os Registos do Monitor Azure e um espaço de trabalho log Analytics, o armazenamento do Azure é mais barato e os registos podem ser mantidos lá indefinidamente. |


## <a name="prerequisites"></a>Pré-requisitos
Quaisquer destinos para a definição de diagnóstico devem ser criados com as permissões necessárias. Consulte as secções abaixo para obter os requisitos necessários para cada destino.

### <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
[Crie um novo espaço de trabalho](../learn/quick-create-workspace.md) se ainda não tiver um. O espaço de trabalho não tem de estar na mesma subscrição que os registos de envio de recursos, desde que o utilizador que configura a definição tenha acesso RBAC adequado a ambas as subscrições.

### <a name="event-hub"></a>Hub de eventos
[Crie um centro de eventos](../../event-hubs/event-hubs-create.md) se ainda não tiver um. O espaço de nomes do Event Hubs não tem de estar na mesma subscrição que a subscrição que está a emitir registos, desde que o utilizador que configura a definição tenha acesso adequado ao RBAC a ambas as subscrições e ambas as subscrições estejam no mesmo inquilino da AAD.

A política de acesso partilhado para o espaço de nome define as permissões que o mecanismo de streaming tem. O streaming para Os Centros de Eventos requer permissões de Gestão, Envio e Escuta. Pode criar ou modificar políticas de acesso partilhado no portal Azure no separador Configurar para o seu espaço de nomes Desempaços de Evento. Para atualizar a definição de diagnóstico para incluir o streaming, tem de ter a permissão ListKey nessa regra de autorização do Event Hubs. 


### <a name="azure-storage"></a>Storage do Azure
[Crie uma conta de armazenamento Azure](../../storage/common/storage-account-create.md) se ainda não tiver uma. A conta de armazenamento não tem de estar na mesma subscrição que os registos de envio de recursos, desde que o utilizador que configura a definição tenha acesso RBAC adequado a ambas as subscrições.

Não deve utilizar uma conta de armazenamento existente que tenha outros dados de não monitorização armazenados nele para que possa controlar melhor o acesso aos dados. No entanto, se estiver a arquivar os registos de Atividade e os registos de recursos em conjunto, poderá optar por utilizar a mesma conta de armazenamento para manter todos os dados de monitorização num local central.

Para enviar os dados para armazenamento imutável, descreva a política imutável para a conta de armazenamento, tal como descrito no [Conjunto e gere as políticas de imutabilidade para o armazenamento blob](../../storage/blobs/storage-blob-immutability-policies-manage.md). Deve seguir todas as etapas deste artigo, incluindo permitir a colocação de bolhas de apêndice protegido.

> [!NOTE]
> As contas do Azure Data Lake Storage Gen2 não são atualmente suportadas como um destino para as definições de diagnóstico, mesmo que estas possam ser listadas como uma opção válida no portal do Azure.



## <a name="create-in-azure-portal"></a>Criar no portal do Azure

Pode configurar as definições de diagnóstico no portal Azure, quer a partir do menu Azure Monitor, quer do menu para o recurso.

1. Onde configurar as definições de diagnóstico no portal Azure depende do recurso.

   - Para obter um único recurso, clique nas **definições de Diagnóstico** no **Monitor** no menu do recurso.

        ![Definições de diagnóstico](media/diagnostic-settings/menu-resource.png)

   - Para um ou mais recursos, clique em **Definições** de Diagnóstico em **Definições** no menu Azure Monitor e, em seguida, clique no recurso.

      ![Definições de diagnóstico](media/diagnostic-settings/menu-monitor.png)

   - Para o registo de atividade, clique em **Iniciarções de Atividade** no menu **Azure Monitor** e, em seguida, **definições de Diagnóstico**. Certifique-se de que desativa qualquer configuração antiga para o registo de Atividade. Consulte [as definições existentes](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) para obter mais detalhes.

        ![Definições de diagnóstico](media/diagnostic-settings/menu-activity-log.png)

2. Se não existirem definições no recurso selecionado, é-lhe pedido que crie uma definição. Clique **na definição de diagnóstico de adicionar**.

   ![Adicionar definição de diagnóstico - sem definições existentes](media/diagnostic-settings/add-setting.png)

   Se houver definições existentes no recurso, vê uma lista de definições já configuradas. Clique em **Adicionar definição de diagnóstico** para adicionar uma nova definição ou editar a **definição** existente. Cada configuração não pode ter mais do que um dos tipos de destino.

   ![Adicionar definição de diagnóstico - definições existentes](media/diagnostic-settings/edit-setting.png)

3. Dê um nome se já não tiver um.

    ![Adicionar definição de diagnóstico](media/diagnostic-settings/setting-new-blank.png)

4. **Detalhes da categoria (o que fazer)** - Verifique a caixa para cada categoria de dados que pretende enviar para destinos especificados mais tarde. A lista de categorias varia para cada serviço Azure.

     - **A AllMetrics** encaminha as métricas da plataforma de um recurso para a loja Azure Logs, mas em forma de registo. Estas métricas são geralmente enviadas apenas para a base de dados de séries de séries de métricas Azure Monitor. Enviando-os para a loja Azure Monitor Logs (que é pes pescável através do Log Analytics) para integrá-los em consultas que procuram em outros registos. Esta opção pode não estar disponível para todos os tipos de recursos. Quando é suportado, [as métricas suportadas pelo Azure Monitor](metrics-supported.md) listam as métricas recolhidas para que tipos de recursos.

       > [!NOTE]
       > Consulte a limitação das métricas de encaminhamento para registos do Monitor Azure anteriormente neste artigo.  


     - **Os registos** listam as diferentes categorias disponíveis dependendo do tipo de recurso. Consulte as categorias que pretende encaminhar para um destino.

5. **Detalhes do destino** - Verifique a caixa para cada destino. Quando verifica cada caixa, as opções parecem permitir-lhe adicionar informações adicionais.

      ![Enviar para Log Analytics ou Centros de Eventos](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** - Introduza a subscrição e o espaço de trabalho.  Se não tiver um espaço de trabalho, tem de [criar um antes de prosseguir.](../learn/quick-create-workspace.md)

    1. **Centros de eventos** - Especifique os seguintes critérios:
       - A subscrição da qual o centro de eventos faz parte
       - O espaço de nomes do centro de eventos - Se ainda não tiver um, terá de [criar um](../../event-hubs/event-hubs-create.md)
       - Um nome de hub de Evento (opcional) para enviar todos os dados para. Se não especificar um nome, é criado um centro de eventos para cada categoria de registo. Se estiver a enviar várias categorias, pode querer especificar um nome para limitar o número de centros de eventos criados. Consulte as quotas e limites do [Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) para mais detalhes.
       - Uma política do Event Hub (opcional) Uma política define as permissões que o mecanismo de streaming tem. Para obter mais informações, consulte [as funcionalidades de Centros de Eventos.](../../event-hubs/event-hubs-features.md#publisher-policy)

    1. **Armazenamento** - Escolha a política de subscrição, armazenamento e retenção.

        ![Enviar para armazenamento](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Considere definir a política de retenção para 0 e eliminar manualmente os seus dados do armazenamento usando um trabalho programado para evitar possíveis confusões no futuro.
        >
        > Em primeiro lugar, se estiver a usar o armazenamento para arquivar, geralmente deseja os seus dados por mais de 365 dias. Em segundo lugar, se escolher uma política de retenção superior a 0, a data de validade é anexada aos registos no momento do armazenamento. Não pode alterar a data dos registos uma vez guardados.
        >
        > Por exemplo, se definir a política de retenção para *workflowSruntime* para 180 dias e, em seguida, 24 horas depois fixá-lo para 365 dias, os registos armazenados durante as primeiras 24 horas serão automaticamente eliminados após 180 dias, enquanto todos os registos subsequentes desse tipo serão automaticamente eliminados após 365 dias. Mudar a política de retenção mais tarde não faz com que as primeiras 24 horas de registos permaneçam por perto durante 365 dias.

6. Clique em **Guardar**.

Após alguns momentos, a nova definição aparece na sua lista de definições para este recurso, e os registos são transmitidos para os destinos especificados à medida que novos dados do evento são gerados. Pode levar até 15 minutos entre quando um evento é emitido e quando [aparece num espaço de trabalho log analytics](data-ingestion-time.md).

## <a name="create-using-powershell"></a>Criar com o PowerShell

Utilize o [cmdlet Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) para criar uma definição de diagnóstico com [a Azure PowerShell](powershell-quickstart-samples.md). Consulte a documentação deste cmdlet para descrições dos seus parâmetros.

> [!IMPORTANT]
> Não é possível utilizar este método para o registo de Atividade Azure. Em vez disso, utilize [a definição de diagnóstico no Monitor Azure utilizando um modelo de Gestor de Recursos](diagnostic-settings-template.md) para criar um modelo de Gestor de Recursos e implementá-lo com o PowerShell.

Segue-se um exemplo de PowerShell cmdlet para criar uma definição de diagnóstico utilizando os três destinos.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Criar usando O Azure CLI

Utilize as [definições de diagnóstico do monitor az criar](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) comando para criar uma definição de diagnóstico com [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Consulte a documentação deste comando para descrições dos seus parâmetros.

> [!IMPORTANT]
> Não é possível utilizar este método para o registo de Atividade Azure. Em vez disso, utilize [criar a definição de diagnóstico no Monitor Azure utilizando um modelo de Gestor de Recursos](diagnostic-settings-template.md) para criar um modelo de Gestor de Recursos e implantá-lo com OCli.

Segue-se um comando CLI de exemplo para criar uma definição de diagnóstico utilizando os três destinos.

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-resource-manager-template"></a>Criar usando o modelo de gestor de recursos
Consulte [as amostras do modelo do Gestor de Recursos para configurações de diagnóstico no Azure Monitor](../samples/resource-manager-diagnostic-settings.md) para criar ou atualizar definições de diagnóstico com um modelo de Gestor de Recursos.

## <a name="create-using-rest-api"></a>Criar com a API REST
Consulte [Definições de diagnóstico](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) para criar ou atualizar definições de diagnóstico utilizando a [API do Monitor Azure](https://docs.microsoft.com/rest/api/monitor/).

## <a name="create-using-azure-policy"></a>Criar usando a política de Azure
Uma vez que é necessário criar uma definição de diagnóstico para cada recurso Azure, a Política Azure pode ser usada para criar automaticamente uma definição de diagnóstico à medida que cada recurso é criado. Consulte [o Monitor Azure em escala utilizando a Política Azure](deploy-scale.md) para obter detalhes.


## <a name="next-steps"></a>Passos seguintes

- [Ler mais sobre Logs da plataforma Azure](platform-logs-overview.md)

---
title: Utilize configurações de diagnóstico para recolher métricas e registos da plataforma e em Azure
description: Envie métricas e registos da plataforma Azure Monitor para Registos de Monitores Azure, armazenamento Azure ou Hubs de Eventos Azure utilizando uma definição de diagnóstico.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: cbef0244f30a7cf14f8fea4c6a445cf0de662dc4
ms.sourcegitcommit: 291b2972c7f28667dc58f66bbe9d9f7d11434ec1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737900"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Criar definição de diagnóstico para recolher registos e métricas de recursos em Azure

[Os registos](platform-logs-overview.md) da plataforma em Azure, incluindo os registos de registos de atividades do Azure e dos recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure de que dependem. [As métricas da plataforma](data-platform-metrics.md) são recolhidas por padrão e normalmente armazenadas na base de dados de métricas do Monitor Azure.

Este artigo fornece detalhes sobre a criação e configuração de configurações de diagnóstico para enviar métricas de plataforma e registos de plataformas para diferentes destinos.

> [!IMPORTANT]
> Antes de criar uma definição de diagnóstico para recolher o registo de Atividade, deve primeiro desativar qualquer configuração do legado. Consulte o [registo da Atividade Azure com definições de legado](diagnostic-settings-legacy.md) para mais detalhes.

Cada recurso Azure requer a sua própria definição de diagnóstico, que define os seguintes critérios:

- Categorias de registos e dados métricos enviados para os destinos definidos na definição. As categorias disponíveis variarão para diferentes tipos de recursos.
- Um ou mais destinos para enviar os registos. Os destinos atuais incluem log analytics workspace, Event Hubs e Armazenamento Azure.

Uma única definição de diagnóstico pode definir não mais do que um dos destinos. Se quiser enviar dados para mais do que um tipo de destino particular (por exemplo, dois espaços de trabalho diferentes do Log Analytics), então crie várias configurações. Cada recurso pode ter até 5 configurações de diagnóstico.

> [!NOTE]
> [As métricas da plataforma](metrics-supported.md) são recolhidas automaticamente para [as Métricas do Monitor Azure](data-platform-metrics.md). As definições de diagnóstico podem ser utilizadas para recolher métricas para determinados serviços Azure em Registos do Monitor Azure para análise com outros dados de monitorização utilizando consultas de [registo](../log-query/log-query-overview.md) com determinadas limitações. 
>  
>  
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões. *Por exemplo:* A métrica 'IOReadBytes' num Blockchain pode ser explorada e cartografada a um nível de nó. No entanto, quando exportado através de definições de diagnóstico, a métrica exportada representa como todos os bytes lidos para todos os nós. Além disso, devido a limitações internas, nem todas as métricas são exportáveis para registos de monitores Do Azure / Log Analytics. Para mais informações, consulte a [lista de métricas exportáveis.](metrics-supported-export-diagnostic-settings.md) 
>  
>  
> Para contornar estas limitações para métricas específicas, sugerimos que as extrai manualmente utilizando a API DE [CAIXA métricae](https://docs.microsoft.com/rest/api/monitor/metrics/list) importe-as em Registos de Monitor Estanque utilizando a API do colecionador de [dados Do Monitor Azure](data-collector-api.md).  

## <a name="destinations"></a>Destinos

Os registos e métricas da plataforma podem ser enviados para os destinos na tabela seguinte. Siga cada link na tabela seguinte para obter detalhes sobre o envio de dados para esse destino.

| Destino | Descrição |
|:---|:---|
| [Área de trabalho do Log Analytics](resource-logs-collect-workspace.md) | A recolha de registos e métricas num espaço de trabalho do Log Analytics permite-lhe analisá-los com outros dados de monitorização recolhidos pelo Azure Monitor utilizando poderosas consultas de registo e também para alavancar outras funcionalidades do Monitor de Acesso, como alertas e visualizações. |
| [Centros de eventos](resource-logs-stream-event-hubs.md) | O envio de registos e métricas para Os Centros de Eventos permite-lhe transmitir dados para sistemas externos, tais como SIEMs de terceiros e outras soluções de análise de registo. |
| [Conta de armazenamento azure](resource-logs-collect-storage.md) | Arquivar registos e métricas para uma conta de armazenamento Azure é útil para auditoria, análise estática ou backup. Em comparação com os Registos do Monitor Azure e um espaço de trabalho do Log Analytics, o armazenamento do Azure é mais barato e os registos podem ser mantidos lá indefinidamente. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Criar configurações de diagnóstico no portal Azure

Pode configurar as definições de diagnóstico no portal Azure, quer a partir do menu Do Monitor Azure, quer a partir do menu do recurso.

1. Quando configurar as definições de diagnóstico no portal Azure depende do recurso.

   - Para um único recurso, clique em **definições de diagnóstico** no **Monitor** no menu do recurso.

        ![Definições de diagnóstico](media/diagnostic-settings/menu-resource.png)

   - Para um ou mais recursos, clique em **definições** de diagnóstico em **Definições** no menu Do Monitor Do Azure e, em seguida, clique no recurso.

      ![Definições de diagnóstico](media/diagnostic-settings/menu-monitor.png)

   - Para o registo de atividade, clique em **iniciar sessão de atividade** no menu Do Monitor **Azure** e, em seguida, **definições de diagnóstico**. Certifique-se de que desativa qualquer configuração do legado para o registo de Atividade. Consulte [as definições existentes](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) para obter mais detalhes.

        ![Definições de diagnóstico](media/diagnostic-settings/menu-activity-log.png)

2. Se não existirem definições no recurso selecionado, é-lhe solicitado que crie uma definição. Clique em **adicionar definição de diagnóstico**.

   ![Adicionar definição de diagnóstico - sem definições existentes](media/diagnostic-settings/add-setting.png)

   Se existirem definições existentes no recurso, consulte uma lista de definições já configuradas. Clique em **Adicionar definição de diagnóstico** para adicionar uma nova definição ou editar **definição** para editar uma existente. Cada definição não pode ter mais do que um dos tipos de destino.

   ![Adicionar definição de diagnóstico - definições existentes](media/diagnostic-settings/edit-setting.png)

3. Dê um nome ao seu cenário se já não tiver um.

    ![Adicionar definição de diagnóstico](media/diagnostic-settings/setting-new-blank.png)

4. Detalhes da **categoria (o que encaminhar)** - Verifique a caixa para cada categoria de dados que pretende enviar para destinos especificados posteriormente. A lista de categorias varia para cada serviço Azure.

     - **A AllMetrics** encaminha as métricas da plataforma de um recurso para a loja De Registos Azure, mas em forma de registo. Estas métricas são geralmente enviadas apenas para a base de dados da série de tempo das métricas Do Monitor Azure. Enviando-os para a loja de registos do Monitor Azure (que é pesquisável através do Log Analytics) para integrá-los em consultas que procuram em outros registos. Esta opção pode não estar disponível para todos os tipos de recursos. Quando é suportado, [as métricas suportadas](metrics-supported.md) pelo Azure Monitor listam quais as métricas recolhidas para que tipos de recursos.

       > [!NOTE]
       > Consulte a limitação das métricas de encaminhamento para os Registos do Monitor Azure no início deste artigo.  


     - **Os registos** listam as diferentes categorias disponíveis dependendo do tipo de recurso. Verifique quaisquer categorias que deseja supor para um destino.

5. **Detalhes** do destino - Verifique a caixa para cada destino. Ao verificar cada caixa, as opções parecem permitir-lhe adicionar informações adicionais.

      ![Enviar para Log Analytics ou Centros de Eventos](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** - Introduza a subscrição e o espaço de trabalho.  Se não tem espaço de trabalho, tem de criar um antes de [prosseguir.](../learn/quick-create-workspace.md)

    1. Centros de **eventos** - Especifique os seguintes critérios:
       - A subscrição que o centro de eventos faz parte de
       - O espaço de nome do hub do Evento - Se ainda não tiver um, terá de [criar um](../../event-hubs/event-hubs-create.md)
       - Um nome de hub do Evento (opcional) para enviar todos os dados para. Se não especificar um nome, é criado um centro de eventos para cada categoria de registo. Se estiver a enviar várias categorias, poderá querer especificar um nome para limitar o número de centros de eventos criados. Consulte as quotas e limites de limites do [Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) para detalhes.
       - Uma política do Event Hub (opcional) Uma política define as permissões que o mecanismo de streaming tem. Para mais informações, consulte as [funcionalidades do Event-hubs.](../../event-hubs/event-hubs-features.md#publisher-policy)

    1. **Armazenamento** - Escolha a política de subscrição, armazenamento e retenção.

        ![Enviar para armazenamento](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Considere definir a política de retenção para 0 e apagar manualmente os seus dados do armazenamento usando um trabalho programado para evitar uma possível confusão no futuro.
        >
        > Primeiro, se estiver a usar o armazenamento para arquivar, geralmente quer os seus dados por mais de 365 dias. Em segundo lugar, se escolher uma política de retenção superior a 0, a data de validade está anexada aos registos no momento do armazenamento. Não pode alterar a data para os registos uma vez armazenados.
        >
        > Por exemplo, se definir a política de retenção para *WorkflowRuntime* para 180 dias e, em seguida, 24 horas depois fixá-la para 365 dias, os registos armazenados durante as primeiras 24 horas serão automaticamente apagados após 180 dias, enquanto todos os registos subsequentes desse tipo serão automaticamente apagados após 365 dias. Mudar a política de retenção mais tarde não faz com que as primeiras 24 horas de registos fiquem por aqui por 365 dias.

6. Clique em **Guardar**.

Após alguns momentos, a nova configuração aparece na sua lista de configurações para este recurso, e os registos são transmitidos para os destinos especificados à medida que novos dados do evento são gerados. Pode levar até 15 minutos entre quando um evento é emitido e quando aparece num espaço de [trabalho de Log Analytics](data-ingestion-time.md).

## <a name="create-diagnostic-settings-using-powershell"></a>Criar definições de diagnóstico usando powerShell

Utilize o cmdlet [Set-AzDiagnosticSettingSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) para criar uma definição de diagnóstico com [o Azure PowerShell](powershell-quickstart-samples.md). Consulte a documentação para este cmdlet para obter descrições dos seus parâmetros.

> [!IMPORTANT]
> Não é possível utilizar este método para o registo da Atividade Azure. Em vez disso, utilize a definição de [diagnóstico Create no Monitor Azure utilizando um modelo](diagnostic-settings-template.md) de Gestor de Recursos para criar um modelo de Gestor de Recursos e implementá-lo com powerShell.

Segue-se um exemplo de cmdlet PowerShell para criar uma definição de diagnóstico utilizando os três destinos.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-diagnostic-settings-using-azure-cli"></a>Criar configurações de diagnóstico usando o Azure CLI

Utilize as [definições de diagnóstico](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) do monitor az criar comando para criar uma definição de diagnóstico com o [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Consulte a documentação para este comando para obter descrições dos seus parâmetros.

> [!IMPORTANT]
> Não é possível utilizar este método para o registo da Atividade Azure. Em vez disso, utilize a definição de [diagnóstico Create no Monitor Azure utilizando um modelo](diagnostic-settings-template.md) de Gestor de Recursos para criar um modelo de Gestor de Recursos e implementá-lo com CLI.

Segue-se um exemplo de comando CLI para criar uma definição de diagnóstico utilizando os três destinos.

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

### <a name="configure-diagnostic-settings-using-rest-api"></a>Configure as definições de diagnóstico utilizando a API REST

Consulte [as Definições de Diagnóstico](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) para criar ou atualizar as definições de diagnóstico utilizando a [API REST DO Monitor Azure](https://docs.microsoft.com/rest/api/monitor/).

### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Configure as definições de diagnóstico usando o modelo de Gestor de Recursos

Consulte [a definição de diagnóstico no Monitor Azure utilizando um modelo](diagnostic-settings-template.md) de Gestor de Recursos para criar ou atualizar as definições de diagnóstico com um modelo de Gestor de Recursos.

## <a name="next-steps"></a>Passos seguintes

- [Ler mais sobre a plataforma Azure Logs](platform-logs-overview.md)

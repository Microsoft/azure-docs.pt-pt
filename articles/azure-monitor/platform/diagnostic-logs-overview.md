---
title: Descrição geral dos registos de diagnóstico do Azure
description: Saiba quais são os registos de diagnóstico do Azure e como pode usá-los para compreender os eventos que ocorrem dentro de um recurso do Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 704c12bc2ea16fcad5672dde4181f63495fbe967
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870844"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Recolher e consumir dados de registo dos seus recursos do Azure

## <a name="what-are-azure-monitor-diagnostic-logs"></a>Quais são os registos de diagnóstico do Azure Monitor

**Registos de diagnóstico do Azure Monitor** registos emitidos por um serviço do Azure que fornecem dados avançados e frequentes sobre o funcionamento desse serviço. O Azure Monitor disponibiliza dois tipos de registos de diagnóstico:
* **Registos de inquilino** -estes registos provenientes de serviços de inquilinos existentes fora de uma subscrição do Azure, como registos do Azure Active Directory.
* **Registos de recursos** -estes registos provenientes de serviços do Azure que implementar recursos dentro de uma subscrição do Azure, tais como grupos de segurança de rede ou contas de armazenamento.

    ![Registos do diagnóstico de recursos vs outros tipos de registos](./media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

O conteúdo estes registos varia consoante o tipo de serviço e de recursos do Azure. Por exemplo, contadores de regras do grupo de segurança de rede e de auditorias do Cofre de chaves são dois tipos de registos de diagnóstico.

Estes registos são diferentes do [registo de atividades](activity-logs-overview.md). O registo de atividades fornece informações sobre as operações executadas nos recursos na sua subscrição com o Resource Manager, por exemplo, criar uma máquina virtual ou eliminar uma aplicação lógica. O registo de atividades é um registo ao nível da subscrição. Os registos de diagnóstico ao nível de recursos fornecem informações aprofundadas de operações executadas dentro desse recurso em si, por exemplo, obter um segredo a partir de um cofre de chave.

Estes registos também diferem dos registos de diagnóstico de nível de sistema operacional convidado. Os registos de diagnóstico do SO convidado são esses recolhidos por um agente em execução numa máquina virtual ou outros suportado o tipo de recurso. Os registos de diagnóstico ao nível de recursos exigem sem agente e captura dados de recursos específicos da plataforma do Azure em si, enquanto os registos de diagnóstico de nível de sistema operacional convidado capturar os dados do sistema operativo e aplicações em execução numa máquina virtual.

Nem todos os serviços suportam os registos de diagnóstico descritos aqui. [Este artigo contém uma lista de secção quais os serviços que suportam registos de diagnóstico](./../../azure-monitor/platform/diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>O que pode fazer com os registos de diagnóstico
Aqui estão algumas das coisas que pode fazer com os registos de diagnóstico:

![Colocação lógica dos registos de diagnóstico](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Guarde-as para um [ **conta de armazenamento** ](../../azure-monitor/platform/archive-diagnostic-logs.md) para inspeção de auditoria ou manual. Pode especificar o tempo (em dias) de retenção usando **definições de diagnóstico de recursos**.
* [Stream-lhes **os Hubs de eventos** ](diagnostic-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou de uma solução de análise personalizada, como o Power BI.
* Analisá-los com [do Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md), onde os dados são gravados imediatamente para o Azure Monitor sem a necessidade de primeiro escrever os dados para o armazenamento.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Pode utilizar uma conta de armazenamento ou o espaço de nomes de Hubs de eventos que não esteja na mesma subscrição que emite os registos. O utilizador que configura a definição tem de ter o acesso RBAC adequado para ambas as subscrições.

> [!NOTE]
>  Atualmente não é possível arquivar os registos de fluxo de rede para uma conta de armazenamento que está atrás de uma rede virtual protegida.

> [!WARNING]
> O formato dos dados de registo na conta de armazenamento vai ser alterado para Linhas de JSON a 1 de novembro de 2018. [Leia este artigo para obter uma descrição do impacto e saber como atualizar a sua ferramenta para trabalhar com o novo formato.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

## <a name="diagnostic-settings"></a>Definições de diagnóstico

Registos de diagnóstico de recursos são configurados com definições de diagnóstico de recursos. Os registos de diagnóstico do inquilino estão configurados ao utilizar uma definição de diagnóstico do inquilino. **Definições de diagnóstico** para um controle de serviço:

* Onde os registos de diagnóstico e métricas são enviadas (conta de armazenamento, os Hubs de eventos, e/ou do Azure Monitor).
* As categorias de registo são enviadas e se também são enviados dados métricos.
* O tempo em que cada categoria de registo deve ser mantida numa conta de armazenamento
    - A retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647.
    - Se as políticas de retenção são definidas, mas armazenamento de registos numa conta de armazenamento está desativado (por exemplo, se apenas as opções de Hubs de eventos ou o Log Analytics estão selecionadas), as políticas de retenção não têm efeito.
    - Políticas de retenção são aplicado por dia, portanto, no final do dia (UTC), registos a partir do dia em que está, agora, além de retenção de política são eliminadas. Por exemplo, se tivesse uma política de retenção de um dia, no início do dia hoje os registos de ontem de before dia serão eliminados. O processo de eliminação começa a meia-noite UTC, mas tenha em atenção que pode demorar até 24 horas para os registos para ser eliminado da sua conta de armazenamento.

Estas definições são facilmente configuradas a partir das definições de diagnóstico no portal, com o Azure PowerShell e CLI de comandos ou utilizando o [API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: A métrica "Mensagens recebidas" num Hub de eventos pode ser explorada e representada um por nível de fila. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Como ativar a recolha de registos de diagnóstico

Recolha de registos de diagnóstico pode ser ativada [como parte da criação de um recurso num modelo do Resource Manager](./../../azure-monitor/platform/diagnostic-logs-stream-template.md) ou depois de um recurso é criado da página desse recurso no portal. Também pode ativar a recolha em qualquer altura utilizando o Azure PowerShell ou CLI de comandos ou utilizando a API de REST do Azure Monitor.

> [!TIP]
> Estas instruções não poderão aplicar-se diretamente a todos os recursos. Veja as ligações de esquema na parte inferior desta página para entender as etapas especiais que podem ser aplicadas a determinados tipos de recursos.

### <a name="enable-collection-of-diagnostic-logs-in-the-portal"></a>Ativar a recolha de registos de diagnóstico no portal

Pode ativar a recolha de registos de diagnóstico de recursos no portal do Azure depois de ter sido criado um recurso ao aceder a um recurso específico ou ao navegar para o Azure Monitor. Para ativar esta opção através do Azure Monitor:

1. Na [portal do Azure](https://portal.azure.com), navegue para o Azure Monitor e clique em **definições de diagnóstico**

    ![Secção de monitorização do Azure Monitor](media/diagnostic-logs-overview/diagnostic-settings-blade.png)

2. Opcionalmente, filtrar a lista por grupo de recursos ou tipo de recurso, em seguida, clique no recurso para o qual pretende configurar uma definição de diagnóstico.

3. Se não existem definições existem no recurso que selecionou, lhe for pedido para criar uma definição. Clique em "Ativar diagnósticos."

   ![Adicionar definição de diagnóstico - sem definições existentes](media/diagnostic-logs-overview/diagnostic-settings-none.png)

   Se existirem definições existentes no recurso, verá uma lista das definições já configurada neste recurso. Clique em "Adicionar definição de diagnóstico".

   ![Adicionar definição de diagnóstico - existente definições](media/diagnostic-logs-overview/diagnostic-settings-multiple.png)

3. Dar a sua definição de um nome, marque as caixas para cada destino ao qual pretende enviar dados e configurar qual o recurso é utilizado para cada destino. Opcionalmente, defina um número de dias para reter estes registos ao utilizar o **retenção (dias)** controlos de deslize (apenas aplicáveis para o destino de conta de armazenamento). A retenção de zero dias armazena os logs de indefinidamente.

   ![Adicionar definição de diagnóstico - existente definições](media/diagnostic-logs-overview/diagnostic-settings-configure.png)

4. Clique em **Guardar**.

Após alguns instantes, a nova definição é apresentada na sua lista de definições para este recurso e os registos de diagnóstico são enviados para os destinos especificados quando novos dados de evento são gerados.

Definições de diagnóstico de inquilino só podem ser configuradas no painel do portal para o serviço de inquilino - estas definições não aparecem no painel de definições de diagnóstico do Azure Monitor. Por exemplo, os registos de auditoria do Azure Active Directory são configurados ao clicar no **definições de exportação de dados** no painel de registos de auditoria.

![Definições de diagnóstico do AAD](./media/diagnostic-logs-overview/diagnostic-settings-aad.png)

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Ativar a recolha de registos de diagnóstico de recursos através do PowerShell

Para ativar a recolha de registos de diagnóstico de recursos através do Azure PowerShell, utilize os seguintes comandos:

Para ativar o armazenamento de registos de diagnóstico numa conta de armazenamento, utilize este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

O ID de conta de armazenamento é o ID de recurso para a conta de armazenamento para o qual pretende enviar os registos.

Para ativar a transmissão em fluxo de registos de diagnóstico para um hub de eventos, use este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

O ID de regra de barramento de serviço é uma cadeia de caracteres com este formato: `{Service Bus resource ID}/authorizationrules/{key name}`.

Para permitir o envio de registos de diagnóstico para uma área de trabalho do Log Analytics, use este comando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Pode obter o ID de recurso da sua área de trabalho do Log Analytics com o seguinte comando:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Pode combinar estes parâmetros para ativar várias opções de saída.

Atualmente, não é possível configurar as definições de diagnóstico de inquilino com o Azure PowerShell.

### <a name="enable-collection-of-resource-diagnostic-logs-via-the-azure-cli"></a>Ativar a recolha de registos de diagnóstico de recursos através da CLI do Azure

Para ativar a recolha de registos de diagnóstico de recursos através da CLI do Azure, utilize o [az monitor diagnostic-settings criar](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) comando.

Para ativar o armazenamento de registos de diagnóstico numa conta de armazenamento:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

O `--resource-group` argumento só é necessária se `--storage-account` não é um ID de objeto.

Para ativar a transmissão em fluxo de registos de diagnóstico para um hub de eventos:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

O ID da regra é uma cadeia de caracteres com este formato: `{Service Bus resource ID}/authorizationrules/{key name}`.

Para permitir o envio de registos de diagnóstico para uma área de trabalho do Log Analytics:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

O `--resource-group` argumento só é necessária se `--workspace` não é um ID de objeto

Com qualquer comando, pode adicionar categorias adicionais no registo de diagnóstico através da adição de dicionários para a matriz JSON passada como o `--logs` parâmetro. Pode combinar a `--storage-account`, `--event-hub`, e `--workspace` parâmetros para ativar várias opções de saída.

Atualmente, não é possível configurar as definições de diagnóstico de inquilino com a CLI.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Ativar a recolha de registos de diagnóstico de recursos através da REST API

Para alterar as definições de diagnóstico utilizando a API de REST do Azure Monitor, consulte [este documento](https://docs.microsoft.com/rest/api/monitor/).

Atualmente, não é possível configurar as definições de diagnóstico de inquilino com a API de REST do Azure Monitor.

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Gerir definições de diagnóstico de recursos no portal

Certifique-se de que todos os seus recursos são configurados com definições de diagnóstico. Navegue para **Monitor** no portal e open **das definições de diagnóstico**.

![Painel de registos de diagnóstico no portal](./media/diagnostic-logs-overview/diagnostic-settings-nav.png)

Poderá ter de clicar em "Todos os serviços" para localizar a secção Monitor.

Aqui pode ver e filtrar todos os recursos que suportam definições de diagnóstico para ver se tem o diagnóstico ativado. Também pode desagregar para baixo para ver se várias definições estão definidas num recurso e verificar que a conta de armazenamento, o espaço de nomes de Hubs de eventos e/ou as área de trabalho do Log Analytics que estão a fluir para dados.

![Resultados de registos de diagnóstico no portal](./media/diagnostic-logs-overview/diagnostic-settings-blade.png)

Adicionar uma definição de diagnóstico abre a vista de definições de diagnóstico, onde pode ativar, desativar ou modificar as definições de diagnóstico para o recurso selecionado.

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Serviços suportados, categorias e esquemas para os registos de diagnóstico

[Veja este artigo](../../azure-monitor/platform/diagnostic-logs-schema.md) para uma lista completa dos serviços suportados e as categorias de registo e os esquemas usados por esses serviços.

## <a name="next-steps"></a>Passos Seguintes

* [Stream registos de diagnóstico de recursos ao **dos Hubs de eventos**](diagnostic-logs-stream-event-hubs.md)
* [Alterar definições de diagnóstico de recursos com a API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)
* [Analisar registos do armazenamento do Azure com o Azure Monitor](collect-azure-metrics-logs.md)

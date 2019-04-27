---
title: Exemplos de início rápido do Azure CLI de Monitor
description: Comandos da CLI de exemplo para recursos do Azure Monitor. Monitor do Azure é um serviço do Microsoft Azure que lhe permite enviar notificações de alerta, chamar URLs da web com base nos valores de dados de telemetria configurado e serviços de Cloud de dimensionamento automático, as máquinas virtuais e aplicações Web.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: fa3293346fee6f6666db01dab5587dd760df84b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740888"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Exemplos de início rápido do Azure CLI de Monitor
Este artigo mostra-lhe exemplo comandos de interface de linha de comandos (CLI) para o ajudar a aceder aos recursos do Azure Monitor. O Azure Monitor permite-lhe para serviços de Cloud de dimensionamento automático, as máquinas virtuais e aplicações Web e para enviar notificações de alerta ou chamar URLs da web com base nos valores de dados de telemetria configurado.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não instalou a CLI do Azure, siga as instruções para [instalar a CLI do Azure](/cli/azure/install-azure-cli). Também pode utilizar [Azure Cloud Shell](/azure/cloud-shell) para executar a CLI como uma experiência interativa no seu browser. Veja uma referência completa de todos os comandos disponíveis na [referência da CLI do Azure Monitor](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
A primeira etapa é para início de sessão sua conta do Azure.

```azurecli
az login
```

Depois de executar este comando, terá de iniciar sessão através das instruções no ecrã. Funcionam todos os comandos no contexto de sua assinatura padrão.

Para listar os detalhes da sua subscrição atual, utilize o seguinte comando.

```azurecli
az account show
```

Para alterar o contexto de trabalho para uma subscrição diferente, utilize o seguinte comando.

```azurecli
az account set -s <Subscription ID or name>
```

Para ver uma lista de todos os comandos do Azure Monitor suportados, execute o seguinte.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Ver o registo de atividade para uma subscrição

Para ver uma lista de eventos de registo de atividade, execute o seguinte.

```azurecli
az monitor activity-log list
```

Experimente o seguinte para ver todas as opções disponíveis.

```azurecli
az monitor activity-log list -h
```

Eis um exemplo para registos de lista por um resourceGroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Exemplo para registos de lista pelo autor da chamada

```azurecli
az monitor activity-log list --caller myname@company.com
```

Exemplo para registos de lista pelo autor da chamada num tipo de recurso, dentro de um intervalo de datas

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Trabalhar com alertas 
> [!NOTE]
> Neste momento, apenas alertas (clássicos) é suportada na CLI. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Obter regras de alerta (clássicas) num grupo de recursos

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Criar uma regra de (clássica) alerta métrica

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Eliminar uma regra de alerta (clássica)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Perfis de registo

Utilize as informações nesta secção para trabalhar com perfis de registo.

### <a name="get-a-log-profile"></a>Obter um perfil de registo

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Adicionar um perfil de registo com retenção

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adicionar um perfil de registo com retenção e de EventHub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Remover um perfil de registo

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnóstico

Utilize as informações nesta secção para trabalhar com definições de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obter uma definição de diagnóstico

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Criar uma definição de registo de diagnóstico 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
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

### <a name="delete-a-diagnostic-setting"></a>Eliminar uma definição de diagnóstico

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Dimensionamento Automático

Utilize as informações nesta secção para trabalhar com definições de dimensionamento automático. Terá de modificar estes exemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obter definições de dimensionamento automático para um grupo de recursos

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obter definições de dimensionamento automático por nome num grupo de recursos

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Configurações do conjunto de dimensionamento automático

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

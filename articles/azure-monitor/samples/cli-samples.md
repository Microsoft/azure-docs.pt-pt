---
title: Amostras de CLI monitor Azure
description: Amostra de comandos CLI para funcionalidades do Monitor Azure. O Azure Monitor é um serviço Microsoft Azure, que permite enviar notificações de alerta, chamar URLs web com base em valores de dados de telemetria configurados e serviços de cloud de escala automática, máquinas virtuais e Web Apps.
ms.subservice: ''
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/16/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 196a1d580bd5888a6604c3f98b108e39c08a3412
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945017"
---
# <a name="azure-monitor-cli-samples"></a>Amostras de CLI monitor Azure
Este artigo mostra que você experimenta comandos de interface de linha de comando (CLI) para ajudá-lo a aceder às funcionalidades do Azure Monitor. O Azure Monitor permite-lhe serviços de nuvem de escala automática, máquinas virtuais e aplicações web e enviar notificações de alerta ou chamar URLs web com base em valores de dados de telemetria configurados.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não instalou o Azure CLI, siga as instruções para [instalar o Azure CLI](/cli/azure/install-azure-cli). Também pode usar [a Azure Cloud Shell](/azure/cloud-shell) para executar o CLI como uma experiência interativa no seu navegador. Consulte uma referência completa de todos os comandos disponíveis na referência CLI do [Monitor Azure](/cli/azure/monitor). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
O primeiro passo é iniciar sessão na sua conta Azure.

```azurecli
az login
```

Depois de executar este comando, tem de iniciar sômposições através das instruções no ecrã. Todos os comandos funcionam no contexto da sua subscrição padrão.

Enuncie os detalhes da sua subscrição atual.

```azurecli
az account show
```

Mude o contexto de trabalho para uma subscrição diferente.

```azurecli
az account set -s <Subscription ID or name>
```

Veja uma lista de todos os comandos do Azure Monitor suportados.

```azurecli
az monitor -h
```

## <a name="view-activity-log"></a>Ver registo de atividade

Veja uma lista de eventos de registo de atividades.

```azurecli
az monitor activity-log list
```

Veja todas as opções disponíveis.

```azurecli
az monitor activity-log list -h
```

Listar registos por um Grupo de Recursos.

```azurecli
az monitor activity-log list --resource-group <group name>
```

Listar registos por chamada.

```azurecli
az monitor activity-log list --caller myname@company.com
```

Listar registos por chamada num tipo de recurso, dentro de um intervalo de data.

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Trabalhar com alertas 
> [!NOTE]
> Apenas alertas (clássicos) são suportados em CLI neste momento. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Receba as regras (clássicas) de um grupo de recursos

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Criar uma regra de alerta métrico (clássico)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Eliminar uma regra de alerta (clássico)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Perfis de registo

Utilize as informações nesta secção para trabalhar com perfis de registo.

### <a name="get-a-log-profile"></a>Obtenha um perfil de registo

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Adicione um perfil de registo com retenção

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adicione um perfil de registo com retenção e EventHub

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

Utilize as informações nesta secção para trabalhar com as definições de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obtenha uma definição de diagnóstico

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Criar uma definição de diagnóstico 

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

### <a name="delete-a-diagnostic-setting"></a>Excluir uma definição de diagnóstico

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Dimensionamento Automático

Utilize as informações nesta secção para trabalhar com configurações de autoescala. É preciso modificar estes exemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obtenha definições de autoescalação para um grupo de recursos

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obtenha definições de autoescalação pelo nome num grupo de recursos

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Definir definições de autoescala

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

---
title: Amostras de arranque rápido do Azure Monitor CLI
description: Comandos CLI de amostra para as características do Monitor Azure. O Azure Monitor é um serviço Microsoft Azure que permite enviar notificações de alerta, ligar para URLs web com base em valores de dados de telemetria configurados e serviços de cloud de escala automática, máquinas virtuais e aplicações web.
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 69687cee8b8a907b82f2c848242ac64d54dedb87
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248934"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Amostras de arranque rápido do Azure Monitor CLI
Este artigo mostra que experimenta comandos de interface de linha de comando (CLI) para ajudá-lo a aceder às funcionalidades do Monitor Azure. O Azure Monitor permite-lhe serviços de cloud, máquinas virtuais e aplicações web e enviar notificações de alerta ou call web URLs com base em valores de dados de telemetria configurados.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não instalou o Azure CLI, siga as instruções para [instalar o Azure CLI](/cli/azure/install-azure-cli). Também pode usar o [Azure Cloud Shell](/azure/cloud-shell) para executar o CLI como uma experiência interativa no seu navegador. Consulte uma referência completa de todos os comandos disponíveis na referência CLI do [Monitor Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
O primeiro passo é fazer login na sua conta Azure.

```azurecli
az login
```

Depois de executar este comando, tem de iniciar sessão através das instruções no ecrã. Todos os comandos funcionam no contexto da sua subscrição predefinida.

Para listar os detalhes da sua subscrição atual, utilize o seguinte comando.

```azurecli
az account show
```

Para alterar o contexto de trabalho para uma subscrição diferente, utilize o seguinte comando.

```azurecli
az account set -s <Subscription ID or name>
```

Para ver uma lista de todos os comandos suportados do Monitor Azure, execute o seguinte.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Ver registo de atividade para uma subscrição

Para visualizar uma lista de eventos de registo de atividade, execute o seguinte.

```azurecli
az monitor activity-log list
```

Experimente o seguinte para ver todas as opções disponíveis.

```azurecli
az monitor activity-log list -h
```

Aqui está um exemplo para listar registos por um grupo de recursos

```azurecli
az monitor activity-log list --resource-group <group name>
```

Exemplo para listar registos por chamada

```azurecli
az monitor activity-log list --caller myname@company.com
```

Exemplo para listar registos por chamada num tipo de recurso, dentro de um intervalo de data

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Trabalhar com alertas 
> [!NOTE]
> Apenas os alertas (clássicos) são suportados no CLI neste momento. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Receba regras de alerta (clássica) num grupo de recursos

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

### <a name="delete-a-diagnostic-setting"></a>Eliminar uma definição de diagnóstico

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Dimensionamento Automático

Utilize as informações nesta secção para trabalhar com definições de escala automática. Tens de modificar estes exemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obtenha configurações de escala automática para um grupo de recursos

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obtenha configurações de escala automática por nome num grupo de recursos

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Definir definições de escala automática

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```


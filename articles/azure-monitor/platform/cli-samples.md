---
title: Exemplos de início rápido da CLI do Azure Monitor
description: Comandos de exemplo da CLI para recursos de Azure Monitor. Azure Monitor é um serviço de Microsoft Azure que permite enviar notificações de alerta, chamar URLs da Web com base em valores de dados de telemetria configurados e serviços de nuvem de dimensionamento automático, máquinas virtuais e aplicativos Web.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 05/16/2018
ms.openlocfilehash: 48ce748a95f58abb060cd6f54ac29c877356f5de
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555580"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Exemplos de início rápido da CLI do Azure Monitor
Este artigo mostra exemplos de comandos da CLI (interface de linha de comando) para ajudá-lo a acessar recursos do Azure Monitor. Azure Monitor permite dimensionar automaticamente serviços de nuvem, máquinas virtuais e aplicativos Web e enviar notificações de alerta ou chamar URLs da Web com base em valores de dados de telemetria configurados.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não instalou o CLI do Azure, siga as instruções para [instalar o CLI do Azure](/cli/azure/install-azure-cli). Você também pode usar [Azure cloud Shell](/azure/cloud-shell) para executar a CLI como uma experiência interativa em seu navegador. Consulte uma referência completa de todos os comandos disponíveis na [referência da CLI do Azure monitor](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
A primeira etapa é fazer logon em sua conta do Azure.

```azurecli
az login
```

Depois de executar esse comando, você precisa entrar usando as instruções na tela. Todos os comandos funcionam no contexto da sua assinatura padrão.

Para listar os detalhes de sua assinatura atual, use o comando a seguir.

```azurecli
az account show
```

Para alterar o contexto de trabalho para uma assinatura diferente, use o comando a seguir.

```azurecli
az account set -s <Subscription ID or name>
```

Para exibir uma lista de todos os comandos de Azure Monitor com suporte, execute o seguinte.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Exibir o log de atividades de uma assinatura

Para exibir uma lista de eventos do log de atividades, execute o seguinte.

```azurecli
az monitor activity-log list
```

Tente o seguinte para exibir todas as opções disponíveis.

```azurecli
az monitor activity-log list -h
```

Aqui está um exemplo para listar os logs por um resourcegroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Exemplo para listar logs por chamador

```azurecli
az monitor activity-log list --caller myname@company.com
```

Exemplo para listar logs por chamador em um tipo de recurso, dentro de um intervalo de datas

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Trabalhar com alertas 
> [!NOTE]
> Somente os alertas (clássicos) têm suporte na CLI no momento. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Obter regras de alerta (clássico) em um grupo de recursos

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Criar uma regra de alerta de métrica (clássico)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Excluir uma regra de alerta (clássico)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Perfis de log

Use as informações desta seção para trabalhar com perfis de log.

### <a name="get-a-log-profile"></a>Obter um perfil de log

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Adicionar um perfil de log com retenção

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adicionar um perfil de log com retenção e EventHub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Remover um perfil de log

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnósticos

Use as informações desta seção para trabalhar com as configurações de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obter uma configuração de diagnóstico

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Criar uma configuração de log de diagnóstico 

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

### <a name="delete-a-diagnostic-setting"></a>Excluir uma configuração de diagnóstico

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Dimensionamento automático

Use as informações nesta seção para trabalhar com as configurações de dimensionamento automático. Você precisa modificar esses exemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obter configurações de AutoEscala para um grupo de recursos

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obter configurações de autoescala por nome em um grupo de recursos

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Definir configurações de dimensionamento automático

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```


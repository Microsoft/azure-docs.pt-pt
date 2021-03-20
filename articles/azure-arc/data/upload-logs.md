---
title: Carregar registos para o Azure Monitor
description: Os registos de upload do Azure Arc permitiram serviços de dados ao Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92378146"
---
# <a name="upload-logs-to-azure-monitor"></a>Carregar registos para o Azure Monitor

Periodicamente, pode exportar registos e depois enviá-los para Azure. A exportação e o upload de registos também criam e atualizam o controlador de dados, o exemplo gerido pelo SQL e os recursos do grupo de servidores de hiperescala PostgreSQL em Azure.

> [!NOTE] 
> Durante o período de pré-visualização, não há qualquer custo para a utilização de serviços de dados ativados pelo Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Antes de começar

Antes de poder fazer o upload de registos, tem de: 

1. [Criar um espaço de trabalho de análise de log analytics](#create-a-log-analytics-workspace)
1. [Atribuir ID e chave partilhada para variáveis ambientais](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho de análise de log analytics

Para criar um espaço de trabalho de análise de registo, execute estes comandos para criar um Log Analytics Workspace e definir a informação de acesso em variáveis ambientais.

> [!NOTE]
> Ignore este passo se já tiver um espaço de trabalho.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

Exemplo de saída:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Atribuir ID e chave partilhada para variáveis ambientais

Guarde a análise do espaço de trabalho de log `customerId` como uma variável ambiental a ser usada mais tarde:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

Este comando devolve as teclas de acesso necessárias para ligar ao seu espaço de trabalho de análise de registo:

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Exemplo de saída:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Guarde a chave primária numa variável ambiental a utilizar mais tarde:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>Definir variáveis de ambiente final e confirmar

Definir o URL de autoridade SPN numa variável ambiental:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>Verificar variáveis ambientais

Verifique se todas as variáveis ambientais necessárias são definidas se quiser:


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

Com as variáveis ambientais definidas, pode fazer o upload de registos para o espaço de trabalho do registo. 

## <a name="upload-logs-to-azure-monitor"></a>Carregar registos para o Azure Monitor

 Para carregar registos para o seu Arco Azure ativou instâncias geridas pelo SQL e os grupos de servidores de hiperescala pós-SQL ativados executam os seguintes comandos CLI-

1. Faça login no controlador de dados Azure Arc com [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .

   ```console
   azdata login
   ```

   Siga as instruções para definir o espaço de nome, o nome de utilizador do administrador e a palavra-passe. 

1. Exporte todos os registos para o ficheiro especificado:

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. Faça upload de registos para um espaço de trabalho de analítica de registo de monitores Azure:

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Veja os seus registos no portal Azure

Após os registos serem carregados, deverá ser capaz de consultar os mesmos com o explorador de consultas de registo da seguinte forma:

1. Abra o portal Azure e, em seguida, procure o seu espaço de trabalho pelo nome na barra de pesquisa no topo e, em seguida, selecione-o.
2. Selecione Registos no painel esquerdo.
3. Selecione Get Start (ou selecione os links na página 'Iniciar-se' para saber mais sobre o Log Analytics se for novo na página).
4. Siga o tutorial para saber mais sobre o Log Analytics se esta for a sua primeira vez usando o Log Analytics.
5. Expanda os Registos Personalizados na parte inferior da lista de tabelas e verá uma tabela com o nome "sql_instance_logs_CL".
6. Selecione o ícone 'olho' ao lado do nome da mesa.
7. Selecione o botão 'Ver em editor de consulta'.
8. Terá agora uma consulta no editor de consultas que mostrará os 10 eventos mais recentes no registo.
9. A partir daqui, pode experimentar a consulta de registos, com o editor de consultas, definir alertas, etc.

## <a name="automating-uploads-optional"></a>Automatização de uploads (opcional)

Se quiser carregar métricas e registos numa base programada, pode criar um script e executá-lo num temporizador a cada poucos minutos. Abaixo está um exemplo de automatização dos uploads usando um script linux shell.

No seu editor de texto/código favorito, adicione o seguinte script ao ficheiro e guarde como um ficheiro executável de scripts como .sh (Linux/Mac) ou .cmd, .bat, .ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Tornar o ficheiro de script executável

```console
chmod +x myuploadscript.sh
```

Execute o guião a cada 20 minutos:

```console
watch -n 1200 ./myuploadscript.sh
```

Você também pode usar um programador de trabalho como cron ou Windows Task Scheduler ou um orquestrador como Ansible, Puppet ou Chef.

## <a name="next-steps"></a>Passos seguintes

[Carregar métricas e registos para O Azure Monitor](upload-metrics.md)

[Faça upload de dados de utilização, métricas e registos para o Azure Monitor](upload-usage-data.md)

[Faça upload de dados de faturação para a Azure e veja-os no portal Azure](view-billing-data-in-azure.md)

[Ver recurso do controlador de dados Azure Arc no portal Azure](view-data-controller-in-azure-portal.md)

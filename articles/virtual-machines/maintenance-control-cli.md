---
title: Controlo de manutenção para máquinas virtuais Azure usando CLI
description: Saiba como controlar quando a manutenção é aplicada aos seus VMs Azure utilizando o controlo de manutenção e o CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: d94cd649df9da6b36ac484d4fc1e6acef7a21bb7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026170"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Atualizações de controlo com o Controlo de Manutenção e o CLI Azure

O controlo de manutenção permite-lhe decidir quando aplicar atualizações da plataforma à infraestrutura de hospedagem dos seus VMs isolados e anfitriões dedicados ao Azure. Este tópico abrange as opções Azure CLI para controlo de manutenção. Para obter mais sobre os benefícios da utilização do controlo de manutenção, das suas limitações e outras opções de gestão, consulte [atualizações da plataforma de gestão com o Controlo de Manutenção.](maintenance-control.md)

## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Utilize `az maintenance configuration create` para criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myConfig* telescópio para o anfitrião. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

Copie o ID de configuração da saída para utilizar mais tarde.

A `--maintenance-scope host` utilização garante que a configuração de manutenção é utilizada para controlar atualizações na infraestrutura do anfitrião.

Se tentar criar uma configuração com o mesmo nome, mas num local diferente, terá um erro. Os nomes de configuração devem ser exclusivos do seu grupo de recursos.

Pode consultar as configurações de manutenção disponíveis utilizando `az maintenance configuration list` .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Criar uma configuração de manutenção com janela programada
Também pode declarar uma janela agendada quando o Azure aplicará as atualizações dos seus recursos. Este exemplo cria uma configuração de manutenção chamada myConfig com uma janela programada de 5 horas na quarta segunda-feira de cada mês. Uma vez que crie uma janela programada, já não terá de aplicar as atualizações manualmente.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> A **duração da** manutenção deve ser *de 2 horas* ou mais. A **recorrência** da manutenção deve ser definida pelo menos uma vez em 35 dias.

A recorrência da manutenção pode ser expressa diariamente, semanal ou mensalmente. Alguns exemplos incluem:
- **diariamente**- manutenção-janela-recur-cada: "Dia" **ou** "3Days"
- **semanalmente**- manutenção-janela-recur-every: "3Weeks" **ou** "Week Saturday,Sunday"
- **mensal**- manutenção-janela-repetição-cada: "Mês dia23,dia24" **ou** "Mês último domingo" **ou** "Quarta Segunda-feira mês"


## <a name="assign-the-configuration"></a>Atribuir a configuração

Utilize `az maintenance assignment create` para atribuir a configuração ao seu VM isolado ou anfitrião dedicado Azure.

### <a name="isolated-vm"></a>VM isolado

Aplique a configuração num VM utilizando o ID da configuração. Especifique `--resource-type virtualMachines` e forneça o nome do VM para , e o grupo de recursos para o `--resource-name` VM em , e a `--resource-group` localização do VM para `--location` . 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Anfitrião dedicado

Para aplicar uma configuração a um anfitrião dedicado, é necessário `--resource-type hosts` incluir, `--resource-parent-name` com o nome do grupo anfitrião, e `--resource-parent-type hostGroups` . 

O parâmetro `--resource-id` é a identificação do hospedeiro. Você pode usar [a az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) para obter a identificação do seu anfitrião dedicado.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Configuração de verificação

Pode verificar se a configuração foi aplicada corretamente ou verificar qual a configuração atualmente aplicada utilizando `az maintenance assignment list` .

### <a name="isolated-vm"></a>VM isolado

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Anfitrião dedicado 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Verifique se há atualizações pendentes

Utilize `az maintenance update list` para ver se existem atualizações pendentes. Atualização -subscrição para ser o ID para a subscrição que contém o VM.

Se não houver atualizações, o comando devolverá uma mensagem de erro, que conterá o texto: `Resource not found...StatusCode: 404` .

Se houver atualizações, apenas uma será devolvida, mesmo que existam várias atualizações pendentes. Os dados desta atualização serão devolvidos num objeto:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>VM isolado

Verifique se há atualizações pendentes de um VM isolado. Neste exemplo, a saída é formatada como uma tabela de legibilidade.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Anfitrião dedicado

Para verificar as atualizações pendentes de um anfitrião dedicado. Neste exemplo, a saída é formatada como uma tabela de legibilidade. Substitua os valores pelos recursos pelos seus.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Aplicar atualizações

Utilize `az maintenance apply update` para aplicar atualizações pendentes. No sucesso, este comando devolverá JSON contendo os detalhes da atualização.

### <a name="isolated-vm"></a>VM isolado

Crie um pedido para aplicar atualizações a um VM isolado.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Anfitrião dedicado

Aplique atualizações a um anfitrião dedicado.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Verifique o estado da aplicação de atualizações 

Pode verificar o andamento das atualizações utilizando `az maintenance applyupdate get` . 

Pode utilizar `default` como nome de atualização para ver os resultados da última atualização, ou substituir pelo nome da `myUpdateName` atualização que foi devolvida quando executou `az maintenance applyupdate create` .

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime será o momento em que a atualização foi concluída, iniciada por si ou pela plataforma no caso de não ser utilizada a janela de auto-manutenção. Se nunca tiver havido uma atualização aplicada através do controlo de manutenção, mostrará o valor predefinido.

### <a name="isolated-vm"></a>VM isolado

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Anfitrião dedicado

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Excluir uma configuração de manutenção

Utilize `az maintenance configuration delete` para eliminar uma configuração de manutenção. A eliminação da configuração remove o controlo de manutenção dos recursos associados.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Próximos passos
Para saber mais, consulte [Manutenção e atualizações.](maintenance-and-updates.md)

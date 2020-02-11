---
title: Controlo da manutenção
description: Aprenda a controlar quando a manutenção é aplicada aos seus VMs Azure utilizando o Controlo de Manutenção.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: cf4c8c57121a06a607770470c5aef69c5edc0c5f
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115632"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Pré-visualização: Atualizações de controlo com controlo de manutenção e o CLI Azure

Gerencie as atualizações da plataforma, que não requerem um reboot, utilizando o controlo de manutenção. O Azure atualiza frequentemente a sua infraestrutura para melhorar a fiabilidade, desempenho, segurança ou lançar novas funcionalidades. A maioria das atualizações são transparentes para os utilizadores. Algumas cargas de trabalho sensíveis, como jogos, streaming de mídia e transações financeiras, não podem tolerar mesmo alguns segundos de um congelamento de VM ou desconexão para manutenção. O controlo de manutenção dá-lhe a opção de esperar nas atualizações da plataforma e aplicá-las dentro de uma janela de 35 dias. 

O controlo de manutenção permite-lhe decidir quando aplicar atualizações aos seus VMs isolados e anfitriões dedicados azure.

Com controlo de manutenção, pode:
- Atualizações do lote num pacote de atualização.
- Aguarde até 35 dias para aplicar atualizações. 
- Amate automaticamente as atualizações da plataforma para a sua janela de manutenção utilizando funções Azure.
- As configurações de manutenção funcionam em assinaturas e grupos de recursos. 

> [!IMPORTANT]
> O Controlo de Manutenção encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="limitations"></a>Limitações

- Os VMs devem estar num [hospedeiro dedicado,](./linux/dedicated-hosts.md)ou ser criados com um [tamanho VM isolado](./linux/isolation.md).
- Após 35 dias, será aplicada uma atualização automaticamente.
- O utilizador deve ter acesso ao Colaborador do **Recurso.**


## <a name="install-the-maintenance-extension"></a>Instale a extensão de manutenção

Se optar por instalar o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) localmente, precisa da versão 2.0.76 ou posterior.

Instale a extensão CLI de pré-visualização `maintenance` localmente ou na Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Utilize `az maintenance configuration create` para criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myConfig* ao anfitrião. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

Copie o ID de configuração da saída para utilizar mais tarde.

A utilização `--maintenanceScope host` garante que o config de manutenção é utilizado para controlar as atualizações do hospedeiro.

Se tentar criar uma configuração com o mesmo nome, mas num local diferente, terá um erro. Os nomes de configuração devem ser exclusivos da sua subscrição.

Pode consultar as configurações de manutenção disponíveis utilizando `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Atribuir a configuração

Utilize `az maintenance assignment create` para atribuir a configuração ao seu VM isolado ou ao anfitrião dedicado Azure.

### <a name="isolated-vm"></a>VM isolado

Aplique a configuração num VM utilizando o ID da configuração. Especifique `--resource-type virtualMachines` e forneça o nome do VM para `--resource-name`, e o grupo de recursos para o VM em `--resource-group`, e a localização do VM para `--location`. 

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

Para aplicar uma configuração a um anfitrião dedicado, você precisa incluir `--resource-type hosts`, `--resource-parent-name` com o nome do grupo anfitrião, e `--resource-parent-type hostGroups`. 

O parâmetro `--resource-id` é a identificação do hospedeiro. Você pode usar [az vm host get-view](/cli/azure/vm/host#az-vm-host-get-instance-view) para obter a identificação do seu anfitrião dedicado.

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

## <a name="check-configuration"></a>Verificar a configuração

Pode verificar se a configuração foi aplicada corretamente ou verificar qual a configuração atualmente aplicada utilizando `az maintenance assignment list`.

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


## <a name="check-for-pending-updates"></a>Verifique as atualizações pendentes

Utilize `az maintenance update list` para ver se existem atualizações pendentes. Atualização -- subscrição para ser o ID para a subscrição que contém o VM.

Se não houver atualizações, o comando devolverá uma mensagem de erro, que conterá o texto: `Resource not found...StatusCode: 404`.

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

Verifique se há atualizações pendentes para um VM isolado. Neste exemplo, a saída é formatada como uma tabela de legibilidade.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Anfitrião dedicado

Para verificar as atualizações pendentes para um anfitrião dedicado. Neste exemplo, a saída é formatada como uma tabela de legibilidade. Substitua os valores pelos recursos pelos seus.

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

Utilize `az maintenance apply update` para aplicar atualizações pendentes. No sucesso, este comando devolverá a JSON contendo os detalhes da atualização.

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

Aplique atualizações para um anfitrião dedicado.

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

Pode verificar o progresso das atualizações utilizando `az maintenance applyupdate get`. 

Pode utilizar `default` como nome de atualização para ver os resultados da última atualização, ou substituir `myUpdateName` pelo nome da atualização que foi devolvida quando fez `az maintenance applyupdate create`.

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


## <a name="delete-a-maintenance-configuration"></a>Eliminar uma configuração de manutenção

Utilize `az maintenance configuration delete` para eliminar uma configuração de manutenção. A eliminação da configuração remove o controlo de manutenção dos recursos associados.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais, consulte [Manutenção e atualizações.](maintenance-and-updates.md)

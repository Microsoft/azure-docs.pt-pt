---
title: Controle de manutenção para máquinas virtuais do Azure
description: Saiba como controlar quando o da manutenção é aplicado às suas VMs do Azure usando o controle de manutenção.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 6172b5da60037051517a43b1b3b8b91b50ab2aac
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895893"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Visualização: controlar as atualizações com o controle de manutenção e o CLI do Azure

Gerenciar atualizações de plataforma, que não exigem uma reinicialização, usando o controle de manutenção. O Azure frequentemente atualiza sua infraestrutura para melhorar a confiabilidade, desempenho, segurança ou lançamento de novos recursos. A maioria das atualizações é transparente para os usuários. Algumas cargas de trabalho confidenciais, como jogos, streaming de mídia e transações financeiras, não podem tolerar até poucos segundos de uma VM congelando ou desconectando para manutenção. O controle de manutenção oferece a opção de aguardar atualizações de plataforma e aplicá-las em uma janela sem interrupção de 35 dias. 

O controle de manutenção permite que você decida quando aplicar atualizações às VMs isoladas e aos hosts dedicados do Azure.

Com o controle de manutenção, você pode:
- Atualizações em lote em um pacote de atualização.
- Aguarde até 35 dias para aplicar atualizações. 
- Automatize as atualizações de plataforma para sua janela de manutenção usando Azure Functions.
- As configurações de manutenção funcionam em assinaturas e grupos de recursos. 

> [!IMPORTANT]
> O controle de manutenção está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Limitações

- As VMs devem estar em um [host dedicado](./linux/dedicated-hosts.md)ou ser criadas usando um [tamanho de VM isolado](./linux/isolation.md).
- Após 35 dias, uma atualização será aplicada automaticamente.
- O usuário deve ter acesso ao **proprietário do recurso** .


## <a name="install-the-maintenance-extension"></a>Instalar a extensão de manutenção

Se você optar por instalar o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) localmente, precisará da versão 2.0.76 ou posterior.

Instale a extensão CLI do `maintenance` Preview localmente ou em Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Use `az maintenance configuration create` para criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myconfig* com escopo para o host. 

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

Copie a ID de configuração da saída para usar mais tarde.

O uso de `--maintenanceScope host` garante que a configuração de manutenção seja usada para controlar atualizações no host.

Se você tentar criar uma configuração com o mesmo nome, mas em um local diferente, receberá um erro. Os nomes de configuração devem ser exclusivos para sua assinatura.

Você pode consultar as configurações de manutenção disponíveis usando `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Atribuir a configuração

Use `az maintenance assignment create` para atribuir a configuração à VM isolada ou ao host dedicado do Azure.

### <a name="isolated-vm"></a>VM isolada

Aplique a configuração a uma VM usando a ID da configuração. Especifique `--resource-type virtualMachines` e forneça o nome da VM para `--resource-name`e o grupo de recursos para a VM em `--resource-group`e o local da VM para `--location`. 

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

Para aplicar uma configuração a um host dedicado, você precisa incluir `--resource-type hosts`, `--resource-parent-name` com o nome do grupo de hosts e `--resource-parent-type hostGroups`. 

O parâmetro `--resource-id` é a ID do host. Você pode usar [AZ VM host Get-Instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) para obter a ID do host dedicado.

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

## <a name="check-configuration"></a>Verificar configuração

Você pode verificar se a configuração foi aplicada corretamente ou verificar qual configuração está aplicada no momento usando `az maintenance assignment list`.

### <a name="isolated-vm"></a>VM isolada

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


## <a name="check-for-pending-updates"></a>Verificar se há atualizações pendentes

Use `az maintenance update list` para ver se há atualizações pendentes. Update--Subscription para ser a ID da assinatura que contém a VM.

### <a name="isolated-vm"></a>VM isolada

Verifique se há atualizações pendentes para uma VM isolada. Neste exemplo, a saída é formatada como uma tabela para facilitar a leitura.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Anfitrião dedicado

Para verificar se há atualizações pendentes para um host dedicado. Neste exemplo, a saída é formatada como uma tabela para facilitar a leitura. Substitua os valores dos recursos pelos seus próprios.

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

Use `az maintenance apply update` para aplicar atualizações pendentes.

### <a name="isolated-vm"></a>VM isolada

Crie uma solicitação para aplicar atualizações a uma VM isolada.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myMaintenanceRG\
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Anfitrião dedicado

Aplicar atualizações a um host dedicado.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Verificar o status da aplicação de atualizações 

Você pode verificar o progresso das atualizações usando `az maintenance applyupdate get`. 

### <a name="isolated-vm"></a>VM isolada

Substitua `myUpdateName` pelo nome da atualização que foi retornada quando você executou `az maintenance applyupdate create`.

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name myUpdateName 
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
   --apply-update-name default \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Excluir uma configuração de manutenção

Use `az maintenance configuration delete` para excluir uma configuração de manutenção. A exclusão da configuração remove o controle de manutenção dos recursos associados.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais, consulte [manutenção e atualizações](maintenance-and-updates.md).

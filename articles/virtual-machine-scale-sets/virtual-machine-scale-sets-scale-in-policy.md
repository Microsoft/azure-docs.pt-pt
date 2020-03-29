---
title: Utilize políticas personalizadas de escala com conjuntos de escala virtual Azure
description: Saiba como usar políticas personalizadas de escala com conjuntos de escala virtual Azure que usam configuração de escala automática para gerir a contagem de casos
services: virtual-machine-scale-sets
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: ffcdaf76bdd08ee5505ddbeff6a6698e231b6171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919843"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Utilize políticas personalizadas de escala com conjuntos de escala virtual Azure

Uma implementação de conjunto de escala de máquina virtual pode ser dimensionada ou dimensionada com base numa série de métricas, incluindo plataformas e métricas personalizadas definidas pelo utilizador. Enquanto um scale-out cria novas máquinas virtuais baseadas no modelo de conjunto de escala, um scale-in afeta máquinas virtuais em funcionamento que podem ter configurações e/ou funções diferentes à medida que a carga de trabalho definida pela escala evolui. 

A funcionalidade de política scale-in fornece aos utilizadores uma forma de configurar a ordem em que as máquinas virtuais são dimensionadas, através de três configurações de escala: 

1. Predefinição
2. Mais recenteVM
3. Vm mais antigo

### <a name="default-scale-in-policy"></a>Política de escala padrão

Por predefinição, o conjunto de escala de máquina virtual aplica esta política para determinar em que instâncias serão dimensionadas. Com a política *Predefinido,* os VMs são selecionados para escala na seguinte ordem:

1. Equilibrar as máquinas virtuais em zonas de disponibilidade (se o conjunto de escala for implantado na configuração zonal)
2. Equilibrar máquinas virtuais em domínios de falha (melhor esforço)
3. Eliminar a máquina virtual com a mais alta instância de identificação

Os utilizadores não precisam de especificar uma política de escala se apenas querem que a ordem por defeito seja seguida.

Note que o equilíbrio entre zonas de disponibilidade ou domínios de avaria não move instâncias através de zonas de disponibilidade ou domínios de falha. O equilíbrio é alcançado através da eliminação de máquinas virtuais a partir das zonas de disponibilidade desequilibradas ou domínios de avaria até que a distribuição de máquinas virtuais se torne equilibrada.

### <a name="newestvm-scale-in-policy"></a>Nova política de escala VM

Esta política eliminará a mais recente máquina virtual criada no conjunto de escala, depois de equilibrar os VMs em zonas de disponibilidade (para implantações zonais). Ativar esta política requer uma mudança de configuração no modelo de conjunto de escala de máquina virtual.

### <a name="oldestvm-scale-in-policy"></a>Política de escala vm mais antiga

Esta política eliminará a máquina virtual criada mais antiga no conjunto de escala, depois de equilibrar Os VMs em zonas de disponibilidade (para implantações zonais). Ativar esta política requer uma mudança de configuração no modelo de conjunto de escala de máquina virtual.

## <a name="enabling-scale-in-policy"></a>Permitir a política de escala

Uma política de escala é definida no modelo de conjunto de escala de máquina virtual. Tal como referido nas secções acima referidas, é necessária uma definição de política de escala na utilização das políticas "NewestVM" e "Mais antigaVM". O conjunto de escala de máquina virtual utilizará automaticamente a política de escala 'Padrão' se não existir uma definição de política de escala no modelo de conjunto de escala. 

Uma política de escala pode ser definida no modelo de conjunto de escala de máquina virtual das seguintes formas:

### <a name="azure-portal"></a>Portal do Azure
 
Os passos seguintes definem a política de escala na criação de um novo conjunto de escala. 
 
1. Vá a conjuntos de **escala de máquinas virtuais.**
1. Selecione **+ Adicione** para criar um novo conjunto de escala.
1. Vá ao separador **De Escalonamento.** 
1. Localize a secção **política scale-in.**
1. Selecione uma política de escala a partir da queda.
1. Quando terminar de criar o novo conjunto de escala, selecione **Review + crie** o botão.

### <a name="using-api"></a>Utilizar a API

Execute um PUT na balança de máquinas virtuais utilizando a API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Crie um grupo de recursos e, em seguida, crie um novo conjunto de escala com a política de escala definida como *VM mais antigo*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

O exemplo seguinte adiciona uma política de escala, ao mesmo tempo que cria um novo conjunto de escala. Primeiro crie um grupo de recursos, em seguida, crie um novo conjunto de escala com a política de escala como *O VM mais antigo*. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Usando o modelo

No seu modelo, em "propriedades", adicione o seguinte:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Os blocos acima especificam que o conjunto de escala de máquina virtual eliminará o VM mais antigo num conjunto de escala equilibrada em zona, quando é acionado um scale-in (através da escala automática ou da eliminação manual).

Quando um conjunto de escala de máquina virtual não é equilibrado em zona, o conjunto de escala eliminará primeiro os VMs através da ou zona desequilibrada. Dentro das zonas desequilibradas, o conjunto de escala utilizará a política de escala especificada acima para determinar em que VM escalar. Neste caso, dentro de uma zona desequilibrada, o conjunto de escala selecionará o VM mais antigo dessa zona a eliminar.

Para o conjunto de máquinas virtuais não zonais, a política seleciona o VM mais antigo em toda a escala definida para a eliminação.

O mesmo processo aplica-se quando se utiliza o "NewestVM" na política de escala acima referida.

## <a name="modifying-scale-in-policies"></a>Modificação de políticas de escala

A modificação da política de escala segue o mesmo processo que a aplicação da política de escala. Por exemplo, se, no exemplo acima referido, quiser mudar a política de 'Mais AntigaVM' para 'NewestVM', pode fazê-lo através de:

### <a name="azure-portal"></a>Portal do Azure

Pode modificar a política de escala de uma escala existente definida através do portal Azure. 
 
1. Num conjunto de escala de máquina virtual existente, **selecione Escalar** a partir do menu à esquerda.
1. Selecione o separador **Política Scale-In.**
1. Selecione uma política de escala a partir da queda.
1. Quando tiver terminado, selecione **Guardar**. 

### <a name="using-api"></a>Utilizar a API

Execute um PUT na balança de máquinas virtuais utilizando a API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Atualizar a política de escala de um conjunto de escala existente:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Segue-se um exemplo para a atualização da política de escala de um conjunto de escala existente: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Usando o modelo

No seu modelo, em "propriedades", modifique o modelo como abaixo e recoloque: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

O mesmo processo será aplicável se decidir mudar 'NewestVM' para 'Default' ou 'Mais AntigoVM'

## <a name="instance-protection-and-scale-in-policy"></a>Proteção de exemplos e política de escala

Os conjuntos de escala de máquinas virtuais fornecem dois tipos de proteção por [exemplo:](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)

1. Proteger contra a escala
2. Proteger contra ações de conjunto de escala

Uma máquina virtual protegida não é eliminada através de uma ação de escala, independentemente da política de escala aplicada. Por exemplo, se VM_0 (VM mais antigo no conjunto de escala) estiver protegido da escala dentro, e o conjunto de escala tiver uma política de escala "VM mais antiga" ativada, VM_0 não serão considerados para serem dimensionados, mesmo que seja o VM mais antigo no conjunto de escala. 

Uma máquina virtual protegida pode ser eliminada manualmente pelo utilizador a qualquer momento, independentemente da política de escala ativada no conjunto de escala. 

## <a name="usage-examples"></a>Exemplos de utilização 

Os exemplos abaixo demonstram como um conjunto de escala de máquina virtual irá selecionar VMs para serem eliminados quando um evento de escala é desencadeado. As máquinas virtuais com as iDs de maior instância são assumidas como os mais recentes VMs no conjunto de escala e os VMs com as iDs de menor instância são assumidos como os VMs mais antigos no conjunto de escala. 

### <a name="oldestvm-scale-in-policy"></a>Política de escala vm mais antiga

| Evento                 | IDs de instância na Zona1  | IDs de instância na Zona2  | IDs de instância na Zona 3  | Seleção scale-in                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial (Inicial)               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Escala              | 3, 4, 5, 10            | ***2,*** 6, 9, 11      | 1, 7, 8                | Escolha entre a Zona 1 e 2, embora a Zona 3 tenha o VM mais antigo. Elimine o VM2 da Zona 2, uma vez que é o VM mais antigo daquela zona.   |
| Escala              | ***3,*** 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Escolha a Zona 1, embora a Zona 3 tenha o VM mais antigo. Elimine o VM3 da Zona 1, uma vez que é o VM mais antigo daquela zona.                  |
| Escala              | 4, 5, 10               | 6, 9, 11               | ***1,*** 7, 8          | As zonas estão equilibradas. Elimine o VM1 na Zona 3, uma vez que é o VM mais antigo do conjunto de escala.                                               |
| Escala              | ***4,*** 5, 10         | 6, 9, 11               | 7, 8                   | Escolha entre a Zona 1 e a Zona 2. Elimine o VM4 na Zona 1, uma vez que é o VM mais antigo em todas as duas Zonas.                              |
| Escala              | 5, 10                  | ***6,*** 9, 11         | 7, 8                   | Escolha a Zona 2, embora a Zona 1 tenha o VM mais antigo. Elimine o VM6 na Zona 1, uma vez que é o VM mais antigo daquela zona.                    |
| Escala              | ***5,*** 10            | 9, 11                  | 7, 8                   | As zonas estão equilibradas. Elimine o VM5 na Zona 1, uma vez que é o VM mais antigo do conjunto de escala.                                                |

Para conjuntos de escala de máquinas virtuais não zonais, a política seleciona o VM mais antigo em toda a escala definida para a eliminação. Qualquer VM "protegido" será ignorado para eliminação.

### <a name="newestvm-scale-in-policy"></a>Nova política de escala VM

| Evento                 | IDs de instância na Zona1  | IDs de instância na Zona2  | IDs de instância na Zona 3  | Seleção scale-in                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial (Inicial)               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Escala              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Escolha entre a Zona 1 e 2. Elimine o VM11 da Zona 2, uma vez que é o vM mais recente nas duas zonas.                                |
| Escala              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Escolha a Zona 1 porque tem mais VMs do que as outras duas zonas. Elimine o VM10 da Zona 1, uma vez que é o vM mais recente dessa Zona.          |
| Escala              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | As zonas estão equilibradas. Elimine o VM9 na Zona 2, uma vez que é o vM mais recente no conjunto de escala.                                                |
| Escala              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Escolha entre a Zona 1 e a Zona 3. Elimine o VM8 na Zona 3, uma vez que é o vM mais recente dessa Zona.                                      |
| Escala              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Escolha a Zona 1, embora a Zona 3 tenha o vM mais recente. Elimine o VM5 na Zona 1, uma vez que é o vM mais recente dessa Zona.                    |
| Escala              | 3, 4                   | 2, 6                   | 1, ***7***             | As zonas estão equilibradas. Elimine o VM7 na Zona 3, uma vez que é o vM mais recente no conjunto de escala.                                                |

Para conjuntos de escala de máquinas virtuais não zonais, a política seleciona o vM mais recente em toda a escala definida para a eliminação. Qualquer VM "protegido" será ignorado para eliminação. 

## <a name="troubleshoot"></a>Resolução de problemas

1. Falha na escalaInPolicy Se tiver um erro 'BadRequest' com uma mensagem de erro indicando "Não consegui encontrar o membro 'scalePolicy' no objeto de 'propriedades' do tipo", verifique a versão API utilizada para o conjunto de escala de máquina virtual. A versão API 2019-03-01 ou superior é necessária para esta funcionalidade.

2. Seleção errada de VMs para escala-in Consulte os exemplos acima referidos. Se o seu conjunto de escala de máquina virtual for uma implantação zonal, a política de escala é aplicada primeiro às Zonas desequilibradas e, em seguida, através da escala definida uma vez que é zona equilibrada. Se a ordem de escala não for consistente com os exemplos acima, levante uma consulta com a equipa de conjunto de máquinas virtuais para resolução de problemas.

## <a name="next-steps"></a>Passos seguintes

Aprenda a implementar a [sua aplicação](virtual-machine-scale-sets-deploy-app.md) em conjuntos de escala de máquinas virtuais.
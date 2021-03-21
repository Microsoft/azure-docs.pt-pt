---
title: Use políticas de escala personalizadas com conjuntos de escala de máquina virtual Azure
description: Saiba como usar políticas de escala personalizadas com conjuntos de escala de máquina virtual Azure que usam configuração de escala automática para gerir a contagem de instâncias
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 9ca6310705d54d563aae746ab2dbfe6cb412e6a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747808"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Use políticas de escala personalizadas com conjuntos de escala de máquina virtual Azure

Uma configuração de escala de máquina virtual pode ser dimensionada ou dimensionada com base numa série de métricas, incluindo métricas personalizadas definidas pela plataforma e pelo utilizador. Enquanto uma escala-out cria novas máquinas virtuais com base no modelo de conjunto de escala, uma escala-in afeta máquinas virtuais que podem ter configurações e/ou funções diferentes à medida que a carga de trabalho definida em escala evolui. 

A funcionalidade de política de dimension in fornece aos utilizadores uma forma de configurar a ordem em que as máquinas virtuais são dimensionada, através de três configurações de escala: 

1. Predefinição
2. NewestVM
3. Mais antigoVM

### <a name="default-scale-in-policy"></a>Política de escala padrão

Por predefinição, o conjunto de escalas de máquina virtual aplica esta política para determinar em que instância(s) será dimensionada. Com a política *Predefinido,* os VMs são selecionados para escala na seguinte ordem:

1. Equilibrar as máquinas virtuais em zonas de disponibilidade (se o conjunto de escala for implantado na configuração zonal)
2. Equilibrar máquinas virtuais em domínios de avaria (melhor esforço)
3. Eliminar máquina virtual com o iD de instância mais alta

Os utilizadores não precisam de especificar uma política de escala se apenas quiserem que a encomenda por defeito seja seguida.

Note que o equilíbrio entre zonas de disponibilidade ou domínios de avaria não move instâncias através de zonas de disponibilidade ou domínios de avaria. O equilíbrio é conseguido através da eliminação de máquinas virtuais das zonas de disponibilidade desequilibrada ou dos domínios de avaria até que a distribuição de máquinas virtuais se torne equilibrada.

### <a name="newestvm-scale-in-policy"></a>Política de escala mais recente da NEWVM

Esta política eliminará a mais recente máquina virtual criada no conjunto de escala, depois de equilibrar os VMs em zonas de disponibilidade (para implementações zonais). Ativar esta política requer uma alteração de configuração no modelo de conjunto de escala de máquina virtual.

### <a name="oldestvm-scale-in-policy"></a>Política de escala mais antiga daVM

Esta política eliminará a máquina virtual mais antiga criada no conjunto de escalas, depois de equilibrar os VMs em zonas de disponibilidade (para implementações zonais). Ativar esta política requer uma alteração de configuração no modelo de conjunto de escala de máquina virtual.

## <a name="enabling-scale-in-policy"></a>Habilitando a política de dimensionamento

Uma política de escala é definida no modelo de conjunto de escala de máquina virtual. Como referido nas secções acima, é necessária uma definição de política de escala ao utilizar as políticas "NewestVM" e "OldVM". O conjunto de escalas de máquinas virtuais utilizará automaticamente a política de escala 'Predefinido' se não houver definição de política de escala encontrada no modelo de definição de escala. 

Uma política de escala pode ser definida no modelo de conjunto de escala de máquina virtual das seguintes formas:

### <a name="azure-portal"></a>Portal do Azure
 
Os passos a seguir definem a política de dimension de escala ao criar um conjunto de escala nova. 
 
1. Aceda aos **conjuntos de escala de máquina virtual**.
1. **Selecione + Adicione** para criar um conjunto de escala nova.
1. Vá ao **separador Escalar.** 
1. Localize a secção **de política de scale-in.**
1. Selecione uma política de escala a partir da queda.
1. Quando terminar de criar o novo conjunto de escala, selecione **Rever + criar** o botão.

### <a name="using-api"></a>Utilizar a API

Execute um PUT na escala de máquina virtual utilizando a API 2019-03-01:

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

Crie um grupo de recursos e, em seguida, crie um conjunto de nova escala com uma definição de política de escala como *OVM Mais Antigo*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

O exemplo a seguir adiciona uma política de escala ao mesmo tempo que cria um novo conjunto de escala. Primeiro criar um grupo de recursos, em seguida, criar uma nova escala definida com a política de escala como *OVM Mais Antigo*. 

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

Os blocos acima especificam que o conjunto de escala de máquina virtual eliminará o VM mais antigo num conjunto de escala equilibrado em zona, quando uma escala é ativada (através de autoescala ou eliminação manual).

Quando um conjunto de balança de máquina virtual não estiver equilibrado na zona, o conjunto de escala apagará primeiro os VMs através da zona(s) desequilibrada. Dentro das zonas desequilibradas, o conjunto de escala utilizará a política de escala-in especificada acima para determinar em que VM se dimensionará. Neste caso, dentro de uma zona desequilibrada, o conjunto de escala selecionará o VM mais antigo dessa zona a eliminar.

Para o conjunto de escala de máquina virtual não zonal, a política seleciona o VM mais antigo em toda a escala definida para eliminação.

O mesmo processo aplica-se quando se utiliza o "NewestVM" na política de escala acima.

## <a name="modifying-scale-in-policies"></a>Modificação das políticas de dimensionamento

A modificação da política de dimensionamento segue o mesmo processo que a aplicação da política de dimensionamento. Por exemplo, se, no exemplo acima, pretender alterar a política de 'OldVM' para 'NewestVM', pode fazê-lo através de:

### <a name="azure-portal"></a>Portal do Azure

Pode modificar a política de escala de uma escala existente definida através do portal Azure. 
 
1. Num conjunto de balança de máquina virtual existente, selecione **Escalar** a partir do menu à esquerda.
1. Selecione o separador **'Política de Escala-In'.**
1. Selecione uma política de escala a partir da queda.
1. Quando tiver terminado, selecione **Guardar**. 

### <a name="using-api"></a>Utilizar a API

Execute um PUT na escala de máquina virtual utilizando a API 2019-03-01:

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

Segue-se um exemplo para atualizar a política de escala de um conjunto de escalas existente: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Usando o modelo

No seu modelo, em "propriedades", modifique o modelo como abaixo e reimplante: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

O mesmo processo será aplicado se decidir alterar 'NewestVM' para 'Predefinido' ou 'Mais AntigoVM'

## <a name="instance-protection-and-scale-in-policy"></a>Política de proteção de instâncias e de escala

Conjuntos de escala de máquina virtual fornecem dois tipos de proteção de [instância:](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)

1. Proteger da escala
2. Proteger contra ações de escala

Uma máquina virtual protegida não é eliminada através de uma ação de escala, independentemente da política de escala aplicada. Por exemplo, se VM_0 (VM mais antigo no conjunto de escala) estiver protegida da escala, e o conjunto de escala tiver a política de escala 'Mais antigaVM' ativada, VM_0 não serão considerados para ser dimensionados, mesmo sendo o VM mais antigo do conjunto de escala. 

Uma máquina virtual protegida pode ser eliminada manualmente pelo utilizador a qualquer momento, independentemente da política de escala ativada no conjunto de escalas. 

## <a name="usage-examples"></a>Exemplos de utilização 

Os exemplos abaixo demonstram como um conjunto de escala de máquina virtual seleciona VMs para serem eliminados quando um evento de escala é ativado. As máquinas virtuais com iDs de instância mais elevada são assumidas como os mais recentes VMs no conjunto de escala e os VMs com os IDs de menor instância são assumidos como os VMs mais antigos no conjunto de escala. 

### <a name="oldestvm-scale-in-policy"></a>Política de escala mais antiga daVM

| Evento                 | IDs de instância na Zona1  | IDs de instância na Zona2  | IDs de instância na Zona3  | Seleção Scale-in                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial (Inicial)               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Escala-in              | 3, 4, 5, 10            | ***2,*** 6, 9, 11      | 1, 7, 8                | Escolha entre a Zona 1 e 2, mesmo que a Zona 3 tenha o VM mais antigo. Elimine o VM2 da Zona 2, uma vez que é o VM mais antigo daquela zona.   |
| Escala-in              | ***3,*** 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Escolha a Zona 1, mesmo que a Zona 3 tenha o VM mais antigo. Elimine o VM3 da Zona 1, uma vez que é o VM mais antigo daquela zona.                  |
| Escala-in              | 4, 5, 10               | 6, 9, 11               | ***1,*** 7, 8          | As zonas estão equilibradas. Elimine o VM1 na Zona 3, uma vez que é o VM mais antigo do conjunto de escala.                                               |
| Escala-in              | ***4,*** 5, 10         | 6, 9, 11               | 7, 8                   | Escolha entre a Zona 1 e a Zona 2. Apague o VM4 na Zona 1, uma vez que é o VM mais antigo através das duas Zonas.                              |
| Escala-in              | 5, 10                  | ***6,*** 9, 11         | 7, 8                   | Escolha a Zona 2 mesmo que a Zona 1 tenha o VM mais antigo. Apague o VM6 na Zona 1, uma vez que é o VM mais antigo daquela zona.                    |
| Escala-in              | ***5***, 10            | 9, 11                  | 7, 8                   | As zonas estão equilibradas. Elimine o VM5 na Zona 1, uma vez que é o VM mais antigo do conjunto de escala.                                                |

Para conjuntos de escala de máquina virtual não zonais, a política seleciona o VM mais antigo em toda a escala definida para eliminação. Qualquer VM "protegido" será ignorado para eliminação.

### <a name="newestvm-scale-in-policy"></a>Política de escala mais recente da NEWVM

| Evento                 | IDs de instância na Zona1  | IDs de instância na Zona2  | IDs de instância na Zona3  | Seleção Scale-in                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Initial (Inicial)               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Escala-in              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Escolha entre a Zona 1 e 2. Elimine o VM11 da Zona 2, uma vez que é o mais recente VM nas duas zonas.                                |
| Escala-in              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Escolha a Zona 1 pois tem mais VMs do que as outras duas zonas. Elimine o VM10 da Zona 1, uma vez que é o mais recente VM naquela Zona.          |
| Escala-in              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | As zonas estão equilibradas. Elimine o VM9 na Zona 2, uma vez que é o mais recente VM no conjunto de escala.                                                |
| Escala-in              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Escolha entre a Zona 1 e a Zona 3. Apague o VM8 na Zona 3, uma vez que é o mais recente VM naquela Zona.                                      |
| Escala-in              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Escolha a Zona 1 mesmo que a Zona 3 tenha o VM mais recente. Apague o VM5 na Zona 1, uma vez que é o mais recente VM naquela Zona.                    |
| Escala-in              | 3, 4                   | 2, 6                   | 1, ***7***             | As zonas estão equilibradas. Elimine o VM7 na Zona 3, uma vez que é o mais recente VM no conjunto de escala.                                                |

Para conjuntos de escala de máquina virtual não zonais, a política seleciona o mais recente VM em toda a escala definida para eliminação. Qualquer VM "protegido" será ignorado para eliminação. 

## <a name="troubleshoot"></a>Resolução de problemas

1. Falha na ativação da escalaInPolicy Se obtém um erro de 'BadRequest' com uma mensagem de erro indicando "Não foi possível encontrar o membro 'scaleInPolicy' no objeto de 'propriedades' tipo", então verifique a versão API utilizada para o conjunto de escala de máquina virtual. Para esta funcionalidade é necessária a versão API 2019-03-01 ou superior.

2. Seleção errada de VMs para escala-in Consulte os exemplos acima. Se o seu conjunto de escala de máquina virtual for uma implementação zonal, a política de escala é aplicada primeiro nas Zonas desequilibradas e, em seguida, através da escala definida uma vez que é equilibrada zona. Se a ordem de dimensionamento não for consistente com os exemplos acima, levante uma consulta com a equipa de conjunto de escalas de máquina virtual para a resolução de problemas.

## <a name="next-steps"></a>Passos seguintes

Saiba como implementar a [sua aplicação](virtual-machine-scale-sets-deploy-app.md) em conjuntos de escala de máquina virtual.

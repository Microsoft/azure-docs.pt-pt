---
title: Implementar VMs e conjuntos de escalas para anfitriões dedicados usando o CLI
description: Implementar VMs e conjuntos de escalas para anfitriões dedicados utilizando o CLI Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/12/2020
ms.author: cynthn
ms.openlocfilehash: d2cf78d328017f96552bd51794ac997c394d18f1
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008423"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>Implementar para anfitriões dedicados usando o CLI Azure
 

Este artigo guia-o através da forma de criar um [anfitrião dedicado](../dedicated-hosts.md) a Azure para hospedar as suas máquinas virtuais (VMs). 

Certifique-se de que instalou a versão 2.0.70 ou posterior do Azure CLI e inscreveu-se numa conta Azure utilizando `az login` . 


## <a name="limitations"></a>Limitações

- Os tamanhos e tipos de hardware disponíveis para anfitriões dedicados variam por região. Consulte a [página de preços do](https://aka.ms/ADHPricing) anfitrião para saber mais.

## <a name="create-resource-group"></a>Criar grupo de recursos 
Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie o grupo de recursos com a criação do grupo AZ. O exemplo a seguir cria um grupo de recursos chamado *myDHResourceGroup* na localização *leste dos EUA.*

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>Lista Disponíveis SKUs anfitrião em uma região

Nem todos os SKUs hospedeiros estão disponíveis em todas as regiões, e zonas de disponibilidade. 

Liste a disponibilidade do anfitrião e quaisquer restrições de oferta antes de começar a alistar anfitriões dedicados. 

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>Criar um grupo de anfitriões 

Um **grupo anfitrião** é um recurso que representa uma coleção de anfitriões dedicados. Você cria um grupo de anfitriões em uma região e uma zona de disponibilidade, e adiciona anfitriões a ele. Ao planear uma elevada disponibilidade, existem opções adicionais. Pode utilizar uma ou ambas as seguintes opções com os seus anfitriões dedicados: 
- Abrangendo várias zonas de disponibilidade. Neste caso, você é obrigado a ter um grupo de anfitriões em cada uma das zonas que você deseja usar.
- Abrangem vários domínios de avaria que são mapeados para prateleiras físicas. 
 
Em qualquer dos casos, é necessário fornecer a contagem de domínio de avaria para o seu grupo anfitrião. Se não pretender abranger os domínios de avaria no seu grupo, utilize uma contagem de domínio de avaria de 1. 

Também pode decidir utilizar as zonas de disponibilidade e os domínios de avaria. 


Neste exemplo, usaremos [o grupo anfitrião AZ VM](/cli/azure/vm/host/group#az-vm-host-group-create) criar para criar um grupo anfitrião usando tanto zonas de disponibilidade como domínios de falhas. 

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

Adicione o `--automatic-placement true` parâmetro para ter os seus VMs e séries de instâncias colocadas automaticamente nos anfitriões, dentro de um grupo anfitrião. Para obter mais informações, consulte [Manual vs. colocação automática ](../dedicated-hosts.md#manual-vs-automatic-placement).


### <a name="other-examples"></a>Outros exemplos

Também pode utilizar [o grupo anfitrião AZ VM](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo anfitrião na zona de disponibilidade 1 (e sem domínios de avaria).

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
As seguintes utilizações [do grupo anfitrião AZ VM criam](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo anfitrião usando apenas domínios de avaria (a ser utilizado em regiões onde as zonas de disponibilidade não são suportadas). 

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Criar um anfitrião 

Agora vamos criar um anfitrião dedicado no grupo anfitrião. Além de um nome para o anfitrião, você é obrigado a fornecer o SKU para o anfitrião. Host SKU captura a série VM suportada, bem como a geração de hardware para o seu anfitrião dedicado.  

Para obter mais informações sobre os SKUs e preços do anfitrião, consulte [os preços do Anfitrião Dedicado Azure](https://aka.ms/ADHPricing).

Use [o hospedeiro az vm criar](/cli/azure/vm/host#az-vm-host-create) para criar um hospedeiro. Se definir uma contagem de domínio de avaria para o seu grupo anfitrião, será solicitado que especifique o domínio de avaria para o seu anfitrião.  

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual 
Crie uma máquina virtual dentro de um hospedeiro dedicado utilizando [a criação az vm](/cli/azure/vm#az-vm-create). Se especificou uma zona de disponibilidade ao criar o seu grupo anfitrião, é-lhe exigido que utilize a mesma zona ao criar a máquina virtual.

```azurecli-interactive
az vm create \
   -n myVM \
   --image debian \
   --host-group myHostGroup \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

Para colocar o VM num hospedeiro específico, utilize `--host` em vez de especificar o grupo anfitrião com `--host-group` .
 
> [!WARNING]
> Se criar uma máquina virtual num hospedeiro que não tenha recursos suficientes, a máquina virtual será criada num estado FALHADO. 

## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento 

Quando implementar um conjunto de escala, especifique o grupo anfitrião.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

Se quiser escolher manualmente qual o anfitrião para implantar a escala definida, adicione `--host` e o nome do anfitrião.


## <a name="check-the-status-of-the-host"></a>Verifique o estado do anfitrião

Pode verificar o estado de saúde do anfitrião e quantas máquinas virtuais ainda pode implantar no hospedeiro utilizando [a visão de exemplo do anfitrião az vm](/cli/azure/vm/host#az-vm-host-get-instance-view).

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 A saída será semelhante a esta:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Exportação como modelo 
Você pode exportar um modelo se você agora quiser criar um ambiente de desenvolvimento adicional com os mesmos parâmetros, ou um ambiente de produção que o corresponda. O Gestor de Recursos utiliza modelos JSON que definem todos os parâmetros para o seu ambiente. Você constrói ambientes inteiros fazendo referência a este modelo JSON. Pode construir modelos JSON manualmente ou exportar um ambiente existente para criar o modelo JSON para si. Utilize [a exportação do grupo AZ](/cli/azure/group#az-group-export) para exportar o seu grupo de recursos.

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Este comando cria o `myDHResourceGroup.json` ficheiro no seu diretório de trabalho atual. Quando cria um ambiente a partir deste modelo, é solicitado para todos os nomes de recursos. Pode preencher estes nomes no seu ficheiro de modelo adicionando o `--include-parameter-default-value` parâmetro ao `az group export` comando. Edite o seu modelo JSON para especificar os nomes dos recursos ou crie uma parameters.jsno ficheiro que especifique os nomes dos recursos.
 
Para criar um ambiente a partir do seu modelo, utilize a implementação do [grupo az create](/cli/azure/group/deployment#az-group-deployment-create).

```azurecli-interactive
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Limpeza 

Está a ser cobrado pelos seus anfitriões dedicados, mesmo quando não são implantadas máquinas virtuais. Deve eliminar quaisquer anfitriões que não esteja a utilizar para economizar custos.  

Só é possível eliminar um hospedeiro quando já não existem máquinas virtuais a usá-lo. Eliminar os VMs utilizando [az vm delete](/cli/azure/vm#az-vm-delete).

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

Depois de eliminar os VMs, pode eliminar o anfitrião utilizando [o anfitrião az vm delete](/cli/azure/vm/host#az-vm-host-delete).

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Uma vez eliminado todos os seus anfitriões, poderá eliminar o grupo anfitrião utilizando [o grupo anfitrião AZ VM](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Também pode eliminar todo o grupo de recursos num único comando. Isto eliminará todos os recursos criados no grupo, incluindo todos os VMs, anfitriões e grupos anfitriões.
 
```azurecli-interactive
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Passos seguintes

- Para mais informações, consulte a visão geral dos [anfitriões dedicados.](../dedicated-hosts.md)

- Também pode criar anfitriões dedicados utilizando o [portal Azure.](../dedicated-hosts-portal.md)

- Existe o modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa ambas as zonas e domínios de avaria para máxima resiliência numa região.
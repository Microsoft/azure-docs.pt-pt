---
title: Desloque os VMs linux para anfitriões dedicados usando o CLI
description: Implemente VMs para anfitriões dedicados usando o Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: f712d1be76a9bb3dc4856b9f1fa7c7b805296dea
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970759"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Implementar VMs para anfitriões dedicados usando o Azure CLI
 

Este artigo guia-o através de como criar um [anfitrião dedicado](dedicated-hosts.md) azure para hospedar as suas máquinas virtuais (VMs). 

Certifique-se de que instalou a versão 2.0.70 do Azure CLI ou posteriormente, e inscreveu-se numa conta Azure utilizando `az login`. 


## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são atualmente suportados em anfitriões dedicados.
- A versão inicial suporta a seguinte série VM: DSv3, ESv3, FSv2, LSv2 e MSv2. 
 

## <a name="create-resource-group"></a>Criar grupo de recursos 
Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie o grupo de recursos com o grupo Az criar. O exemplo seguinte cria um grupo de recursos chamado *myDHResourceGroup* na localização *dos EUA Orientais.*

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Criar um grupo de anfitriões 

Um **grupo de anfitriões** é um recurso que representa uma coleção de anfitriões dedicados. Cria-se um grupo de acolhimento numa região e numa zona de disponibilidade e adiciona-lhe anfitriões. Ao planear uma alta disponibilidade, existem opções adicionais. Pode utilizar uma ou ambas as seguintes opções com os seus anfitriões dedicados: 
- Atravesse várias zonas de disponibilidade. Neste caso, é-lhe exigido que tenha um grupo de acolhimento em cada uma das zonas que deseja utilizar.
- Atravesse vários domínios de falha que são mapeados em prateleiras físicas. 
 
Em qualquer dos casos, é necessário fornecer a contagem de domínio de avaria para o seu grupo anfitrião. Se não quiser abranger domínios de avaria no seu grupo, utilize uma contagem de domínio de falha de 1. 

Também pode decidir utilizar ambas as zonas de disponibilidade e domínios de avaria. 

Neste exemplo, usaremos o [grupo anfitrião Az vm](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de anfitriões usando tanto as zonas de disponibilidade como os domínios de falha. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Outros exemplos

Também pode usar o [grupo anfitrião az vm criar](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de anfitriões na zona de disponibilidade 1 (e sem domínios de falha).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
As seguintes utilizações do [grupo anfitrião Az vm criar](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de anfitriões usando apenas domínios de falha (para serem usados em regiões onde as zonas de disponibilidade não são suportadas). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Criar um hospedeiro 

Agora vamos criar um anfitrião dedicado no grupo anfitrião. Além de um nome para o anfitrião, é necessário fornecer o SKU para o anfitrião. Host SKU captura a série VM suportada, bem como a geração de hardware para o seu anfitrião dedicado.  Os seguintes valores sKU são apoiados: DSv3_Type1 e ESv3_Type1.

Para obter mais informações sobre o anfitrião SKUs e preços, consulte [o preço do Anfitrião Dedicado Azure.](https://aka.ms/ADHPricing)

Use [o hospedeiro az vm criar](/cli/azure/vm/host#az-vm-host-create) para criar um hospedeiro. Se definir uma contagem de domínio de avaria para o seu grupo anfitrião, será-lhe pedido que especifique o domínio de avaria para o seu anfitrião.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual 
Crie uma máquina virtual dentro de um hospedeiro dedicado usando [az vm criar](/cli/azure/vm#az-vm-create). Se especificou uma zona de disponibilidade ao criar o seu grupo de anfitriões, é-lhe exigido que utilize a mesma zona ao criar a máquina virtual.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> Se criar uma máquina virtual num hospedeiro que não tenha recursos suficientes, a máquina virtual será criada num estado FALHADO. 


## <a name="check-the-status-of-the-host"></a>Verifique o estado do anfitrião

Pode verificar o estado de saúde do hospedeiro e quantas máquinas virtuais ainda pode implementar para o hospedeiro utilizando a visualização de [get-instance do anfitrião az vm](/cli/azure/vm/host#az-vm-host-get-instance-view).

```bash
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
 
## <a name="export-as-a-template"></a>Exportar como modelo 
Você pode exportar um modelo se você agora quiser criar um ambiente de desenvolvimento adicional com os mesmos parâmetros, ou um ambiente de produção que o corresponda. O Gestor de Recursos utiliza modelos JSON que definem todos os parâmetros para o seu ambiente. Você constrói ambientes inteiros fazendo referência a este modelo JSON. Você pode construir modelos JSON manualmente ou exportar um ambiente existente para criar o modelo JSON para você. Use [a exportação do grupo AZ](/cli/azure/group#az-group-export) para exportar o seu grupo de recursos.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Este comando cria o ficheiro `myDHResourceGroup.json` no seu atual diretório de trabalho. Quando você cria um ambiente a partir deste modelo, você é solicitado para todos os nomes de recursos. Pode povoar estes nomes no seu ficheiro de modelo adicionando o parâmetro `--include-parameter-default-value` ao comando `az group export`. Edite o seu modelo JSON para especificar os nomes dos recursos ou crie um ficheiro parameters.json que especifica os nomes dos recursos.
 
Para criar um ambiente a partir do seu modelo, use a criação de implementação do [grupo Az](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Limpeza 

Está a ser cobrado pelos seus anfitriões dedicados mesmo quando não são implementadas máquinas virtuais. Deve eliminar quaisquer anfitriões que não esteja a utilizar para poupar custos.  

Só é possível eliminar um hospedeiro quando já não existem máquinas virtuais que o utilizem. Elimine os VMs utilizando [az vm delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Depois de eliminar os VMs, pode eliminar o hospedeiro utilizando o [hospedeiro Az vm apagar](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Uma vez eliminado todos os seus anfitriões, pode eliminar o grupo anfitrião utilizando o [grupo anfitrião Az vm .](/cli/azure/vm/host/group#az-vm-host-group-delete)  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Também pode eliminar todo o grupo de recursos num único comando. Isto eliminará todos os recursos criados no grupo, incluindo todos os VMs, anfitriões e grupos de acolhimento.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Passos seguintes

- Para mais informações, consulte a visão geral dos [anfitriões dedicados.](dedicated-hosts.md)

- Também pode criar anfitriões dedicados utilizando o [portal Azure.](dedicated-hosts-portal.md)

- Há um modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa tanto zonas como domínios de falha para a máxima resiliência numa região.
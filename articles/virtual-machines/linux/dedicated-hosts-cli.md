---
title: Implantar hosts dedicados do Azure usando a CLI
description: Implante VMs em hosts dedicados usando o CLI do Azure.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: b301012425e0a2590fa5ac22985abe9c96fbd419
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834927"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Implantar VMs em hosts dedicados usando o CLI do Azure
 

Este artigo orienta você sobre como criar um [host dedicado](dedicated-hosts.md) do Azure para hospedar suas máquinas virtuais (VMS). 

Certifique-se de ter instalado CLI do Azure versão 2.0.70 ou posterior e conectado a uma conta do Azure usando `az login`. 


## <a name="limitations"></a>Limitações

- Atualmente, não há suporte para conjuntos de dimensionamento de máquinas virtuais em hosts dedicados.
- A versão inicial dá suporte à seguinte série de VMs: DSv3 e ESv3. 
 

## <a name="create-resource-group"></a>Criar grupo de recursos 
Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie o grupo de recursos com AZ Group Create. O exemplo a seguir cria um grupo de recursos chamado *myDHResourceGroup* no local *leste dos EUA* .

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Criar um grupo de anfitriões 

Um **grupo de hosts** é um recurso que representa uma coleção de hosts dedicados. Você cria um grupo de hosts em uma região e uma zona de disponibilidade e adiciona hosts a ele. Ao planejar a alta disponibilidade, há opções adicionais. Você pode usar uma ou ambas as opções a seguir com seus hosts dedicados: 
- Alcance entre várias zonas de disponibilidade. Nesse caso, é necessário ter um grupo de hosts em cada uma das zonas que você deseja usar.
- Alcance entre vários domínios de falha que são mapeados para racks físicos. 
 
Em ambos os casos, você precisa fornecer a contagem de domínios de falha para seu grupo de hosts. Se você não quiser abranger domínios de falha em seu grupo, use uma contagem de domínio de falha de 1. 

Você também pode optar por usar zonas de disponibilidade e domínios de falha. 

Neste exemplo, usaremos o grupo de [hosts de VM AZ](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de hosts usando zonas de disponibilidade e domínios de falha. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Outros exemplos

Você também pode usar [AZ VM host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de hosts na zona de disponibilidade 1 (e nenhum domínio de falha).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
O seguinte usa [AZ VM host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) para criar um grupo de hosts usando somente domínios de falha (para ser usado em regiões em que não há suporte para zonas de disponibilidade). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Criar um host 

Agora, vamos criar um host dedicado no grupo de hosts. Além de um nome para o host, você deve fornecer a SKU para o host. O SKU do host captura a série de VMs com suporte, bem como a geração de hardware para seu host dedicado.  Há suporte para os seguintes valores de SKU: DSv3_Type1 e ESv3_Type1.

Para obter mais informações sobre os preços e as SKUs do host, consulte [preços do host dedicado do Azure](https://aka.ms/ADHPricing).

Use [AZ VM host Create](/cli/azure/vm/host#az-vm-host-create) para criar um host. Se você definir uma contagem de domínios de falha para seu grupo de hosts, será solicitado que você especifique o domínio de falha para o host.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Crie uma máquina virtual 
Crie uma máquina virtual em um host dedicado usando [AZ VM Create](/cli/azure/vm#az-vm-create). Se você especificou uma zona de disponibilidade ao criar o grupo de hosts, será necessário usar a mesma zona ao criar a máquina virtual.

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
> Se você criar uma máquina virtual em um host que não tem recursos suficientes, a máquina virtual será criada em um estado de falha. 


## <a name="check-the-status-of-the-host"></a>Verificar o status do host

Você pode verificar o status de integridade do host e quantas máquinas virtuais você ainda pode implantar no host usando [AZ VM host Get-Instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view).

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 A saída terá uma aparência semelhante a esta:
 
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
 
## <a name="export-as-a-template"></a>Exportar como um modelo 
Você pode exportar um modelo se agora quiser criar um ambiente de desenvolvimento adicional com os mesmos parâmetros ou um ambiente de produção que corresponda a ele. O Gerenciador de recursos usa modelos JSON que definem todos os parâmetros para o seu ambiente. Você cria ambientes inteiros referenciando esse modelo JSON. Você pode criar modelos JSON manualmente ou exportar um ambiente existente para criar o modelo JSON para você. Use [AZ Group Export](/cli/azure/group#az-group-export) para exportar seu grupo de recursos.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Esse comando cria o arquivo de `myDHResourceGroup.json` no diretório de trabalho atual. Ao criar um ambiente a partir desse modelo, você será solicitado a fornecer todos os nomes de recursos. Você pode preencher esses nomes no arquivo de modelo adicionando o parâmetro `--include-parameter-default-value` ao comando `az group export`. Edite o modelo JSON para especificar os nomes dos recursos ou crie um arquivo Parameters. JSON que especifique os nomes dos recursos.
 
Para criar um ambiente a partir de seu modelo, use [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Limpeza 

Você está sendo cobrado por seus hosts dedicados, mesmo quando não há máquinas virtuais implantadas. Você deve excluir os hosts que não está usando no momento para economizar custos.  

Você só pode excluir um host quando não houver mais máquinas virtuais usando-o. Exclua as VMs usando [AZ VM Delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Depois de excluir as VMs, você pode excluir o host usando [AZ VM host Delete](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Depois de excluir todos os seus hosts, você poderá excluir o grupo de hosts usando [AZ VM host Group Delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Você também pode excluir o grupo de recursos inteiro em um único comando. Isso excluirá todos os recursos criados no grupo, incluindo todas as VMs, hosts e grupos de hosts.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações, consulte a visão geral dos [hosts dedicados](dedicated-hosts.md) .

- Você também pode criar hosts dedicados usando o [portal do Azure](dedicated-hosts-portal.md).

- Há um modelo de exemplo, encontrado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter máxima resiliência em uma região.
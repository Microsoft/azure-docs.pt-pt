---
title: Tutorial - Criar e gerir Os VMs do Linux com o Azure CLI
description: Neste tutorial, vai aprender a utilizar a CLI do Azure para criar e gerir VMs do Linux no Azure
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 77213fe83a56a33f4d10658f85465349949be792
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555606"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli"></a>Tutorial: Criar e Gerir VMs do Linux com a CLI do Azure

As máquinas virtuais do Azure proporcionam um ambiente informático totalmente configurável e flexível. Este tutorial abrange itens de implementação básicos de máquinas virtuais do Azure, como selecionar um tamanho de VM, selecionar uma imagem de VM e implementar uma VM. Saiba como:

> [!div class="checklist"]
> * Criar e ligar a uma VM
> * Selecionar e utilizar imagens de VM
> * Ver e utilizar tamanhos específicos de VM
> * Redimensionar uma VM
> * Visualizar e compreender o estado de uma VM

Este tutorial utiliza o CLI dentro da [Azure Cloud Shell](../../cloud-shell/overview.md), que é constantemente atualizado para a versão mais recente. Para abrir a Cloud Shell, selecione **Experimente-a** a partir da parte superior de qualquer bloco de código.

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). 

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual. Neste exemplo, é criado um grupo de recursos chamado *myResourceGroupVM* na região *eastus*. 

```azurecli-interactive
az group create --name myResourceGroupVM --location eastus
```

O grupo de recursos é especificado ao criar ou modificar uma VM, o que pode ser visto ao longo deste tutorial.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm). 

Quando cria uma máquina virtual, várias opções estão disponíveis, como a imagem do sistema operativo, as credenciais administrativas e o dimensionamento do disco. O exemplo seguinte cria uma VM chamada *myVM* que executa o Ubuntu Server. Uma conta de utilizador chamada *azureuser* é criada na VM, as chaves SSH são geradas caso não existam na localização predefinida da chave (*~/.ssh*):

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A criação da VM pode demorar alguns minutos. Quando a VM tiver sido criada, a CLI do Azure produz informações sobre a VM. Tome nota do `publicIpAddress`, este endereço pode ser utilizado para aceder à máquina virtual... 

```output
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Ligar à VM

Pode agora ligar à VM com o SSH no Azure Cloud Shell ou a partir do seu computador local. Substitua o endereço IP de exemplo pelo `publicIpAddress` que anotou no passo anterior.

```bash
ssh azureuser@52.174.34.95
```

Assim que tiver sessão iniciada na VM, pode instalar e configurar as aplicações. Quando tiver terminado, feche a sessão SSH como normalmente:

```bash
exit
```

## <a name="understand-vm-images"></a>Compreender as imagens de VM

O Azure Marketplace inclui várias imagens que podem ser utilizadas para criar as VMs. Nos passos anteriores, uma máquina virtual foi criada com uma imagem de Ubuntu. Neste passo, a CLI do Azure é utilizada para procurar no marketplace uma imagem do CentOS, que é utilizada para implementar uma segunda máquina virtual. 

Para ver uma lista das imagens mais frequentemente utilizadas, utilize o comando [az vm image list](/cli/azure/vm/image).

```azurecli-interactive 
az vm image list --output table
```

A saída do comando devolve as imagens de VM mais populares no Azure.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

Uma lista completa pode ser vista ao adicionar o argumento `--all`. Também é possível filtrar a lista de imagens por `--publisher` ou `–-offer`. Neste exemplo, a lista está filtrada para todas as imagens com uma oferta que corresponde ao *CentOS*. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Saída parcial:

```output
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Para implementar uma VM com uma imagem específica, anote o valor na coluna *Urn*, que consiste no publicador, oferta, SKU e, opcionalmente, um número de versão para [identificar](cli-ps-findimage.md#terminology) a imagem. Ao especificar a imagem, o número de versão da imagem pode ser substituído por "mais recente", que seleciona a versão mais recente da distribuição. Neste exemplo, o argumento `--image` é utilizado para especificar a versão mais recente de uma imagem do CentOS 6.5.  

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Compreender os tamanhos de VM

Um tamanho de máquina virtual determina a quantidade de recursos de computação, como a CPU, memória e GPU que ficam disponíveis para a máquina virtual. As máquinas virtuais devem ser dimensionadas de forma adequada para a carga de trabalho esperada. Se a carga de trabalho aumentar, uma máquina virtual existente pode ser redimensionada.

### <a name="vm-sizes"></a>Tamanhos de VMs

A tabela seguinte categoriza tamanhos em casos de utilização.  

| Tipo                     | Tamanhos comuns           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Fins gerais](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| CPU para memória equilibrada. Ideal para desenvolvimento/teste e aplicações e soluções de dados pequenas a médias.  |
| [Com otimização de computação](../sizes-compute.md)   | Fsv2          | CPU para memória elevada. É adequado para aplicações de tráfego médio, dispositivos de rede e processos em lote.        |
| [Com otimização de memória](../sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Memória-para-núcleo elevada. É ideal para bases de dados relacionais, caches médias a grandes e análise dentro da memória.                 |
| [Com otimização de armazenamento](../sizes-storage.md)      | Lsv2, Ls              | Débito e E/S de disco elevados. Ideal para bases de dados de Macrodados, SQL e NoSQL.                                                         |
| [GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | VMs especializadas destinadas a composição gráfica e edição de vídeo exigentes.       |
| [Elevado desempenho](../sizes-hpc.md) | H        | As nossas mais avançadas VMs com CPU, com interfaces de rede de alto débito (RDMA) opcionais. |


### <a name="find-available-vm-sizes"></a>Localizar todos os tamanhos de VM disponíveis

Para ver uma lista de tamanhos de VM disponíveis numa região específica, utilize o comando [az vm list-sizes](/cli/azure/vm). 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Saída parcial:

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Criar uma VM com tamanho específico

No exemplo anterior de criação de VM, não foi fornecido um tamanho, resultando num tamanho predefinido. Pode ser selecionado um tamanho de VM no momento da criação, com [az vm create](/cli/azure/vm) e o argumento `--size`. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Redimensionar uma VM

Depois de implementar uma VM, esta pode ser redimensionada para aumentar ou diminuir a alocação de recursos. Pode ver o tamanho atual de uma VM com [az vm show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

Antes de redimensionar uma VM, verifique se o tamanho pretendido está disponível no cluster do Azure atual. O comando [az vm list-vm-resize-options](/cli/azure/vm) comando devolve a lista de tamanhos. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```

Se o tamanho pretendido estiver disponível, a VM pode ser redimensionada de um estado ligado, no entanto, é reiniciada durante a operação. Utilize o comando [az vm resize]( /cli/azure/vm) para efetuar o redimensionamento.

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Se o tamanho pretendido não estiver no cluster atual, a VM tem de ser desalocada antes de a operação de redimensionamento poder ocorrer. Utilize o comando [az vm deallocate]( /cli/azure/vm) para parar e desalocar a VM. Tenha em atenção que, quando a VM for novamente ligada, quaisquer dados no disco temporário poderão ser removidos. O endereço IP público também é alterado, a menos que esteja a ser utilizado um endereço IP estático. 

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

Depois de desalocada, pode ocorrer o redimensionamento. 

```azurecli-interactive
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Depois do redimensionamento, a VM pode ser iniciada.

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Estados de energia da VM

Uma VM do Azure pode ter um de vários estados de energia. Este estado representa o estado atual da VM do ponto de vista do hipervisor. 

### <a name="power-states"></a>Estados de energia

| Estado de Energia | Description
|----|----|
| A iniciar | Indica que a máquina virtual está a iniciar. |
| Em Execução | Indica que a máquina virtual está em execução. |
| A parar | Indica que a máquina virtual está a ser parada. | 
| Parada | Indica que a máquina virtual está parada. As máquinas virtuais no estado de paragem continuam a incorrer em custos de computação.  |
| A desalocar | Indica que a máquina virtual está a ser desalocada. |
| Desalocada | Indica que a máquina virtual foi removida do hipervisor, mas continua disponível no painel de controlo. As máquinas virtuais no estado Desalocada não incorrem em custos de computação. |
| - | Indica que o estado de energia da máquina virtual é desconhecido. |

### <a name="find-the-power-state"></a>Encontre o estado de poder

Para obter o estado de uma VM específica, utilize o comando [az vm get-instance-view](/cli/azure/vm). Certifique-se de que especifica um nome válido para a máquina virtual e o grupo de recursos. 

```azurecli-interactive
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Resultado:

```output
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

Para recuperar o estado de potência de todos os VMs na sua subscrição, utilize as [Máquinas Virtuais - Liste Todas as API](/rest/api/compute/virtualmachines/listall) com o status de **parâmetros Definidos** para *verdadeiros*.

## <a name="management-tasks"></a>Tarefas de gestão

Durante o ciclo de vida de uma máquina virtual, poderá querer executar tarefas de gestão, como iniciar, parar ou eliminar uma máquina virtual. Além disso, pode querer criar scripts para automatizar tarefas repetitivas ou complexas. Ao utilizar a CLI do Azure, muitas tarefas comuns de gestão podem ser executadas na linha de comandos ou em scripts. 

### <a name="get-ip-address"></a>Obter endereço IP

Este comando devolve os endereços IP privados e públicos de uma máquina virtual.  

```azurecli-interactive
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Parar a máquina virtual

```azurecli-interactive
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Iniciar a máquina virtual

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Eliminar grupo de recursos

Eliminar um grupo de recursos também elimina todos os recursos nele contidos, como a VM, a rede virtual e o disco. O parâmetro `--no-wait` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo.

```azurecli-interactive
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre a criação e gestão básica de VMs, como:

> [!div class="checklist"]
> * Criar e ligar a uma VM
> * Selecionar e utilizar imagens de VM
> * Ver e utilizar tamanhos específicos de VM
> * Redimensionar uma VM
> * Visualizar e compreender o estado de uma VM

Avance para o próximo tutorial para saber mais sobre os discos de VM.  

> [!div class="nextstepaction"]
> [Criar e Gerir discos VM](./tutorial-manage-disks.md)
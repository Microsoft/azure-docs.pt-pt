---
title: 'Tutorial: Criar & gerir um conjunto de escala de máquina virtual – Azure CLI'
description: Saiba como utilizar a CLI do Azure para criar um conjunto de dimensionamento de máquinas virtuais, juntamente com algumas tarefas de gestão comuns, como iniciar e parar uma instância ou alterar a capacidade do conjunto de dimensionamento.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 0f94823b958ae5f95789dd4ef9a62057bdf764a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517471"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Tutorial: Criar e gerir um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure
Um conjunto de dimensionamento de máquinas virtuais permite implementar e gerir um conjunto de máquinas virtuais idênticas e de dimensionamento automático. Ao longo do ciclo de vida dos conjuntos de dimensionamento de máquinas virtuais, poderá ter de executar uma ou mais tarefas de gestão. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar e ligar a um conjunto de dimensionamento de máquinas virtuais
> * Selecionar e utilizar imagens de VM
> * Ver e utilizar tamanhos de instância de VM específicos
> * Dimensionar manualmente um conjunto de dimensionamento
> * Executar tarefas de gestão comuns de conjuntos de dimensionamento

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.29 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de um conjunto de dimensionamento de máquinas virtuais. Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Neste exemplo, um grupo de recursos chamado *myResourceGroup* é criado na região *leste.* 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

O nome do grupo de recursos é especificado quando cria ou modifica um conjunto de dimensionamento neste tutorial.


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento
Crie um conjunto de dimensionamento de máquinas virtuais com o comando [az vmss create](/cli/azure/vmss). O exemplo seguinte cria um conjunto nomeado de dimensionamento *myScaleSet* e gera chaves SSH, caso não existam:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação e configuração de todas as instâncias de VM e recursos do conjunto de dimensionamento demora alguns minutos. Para distribuir o tráfego pelas instâncias de VM individuais, é também criado um balanceador de carga.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Ver as instâncias da VM num conjunto de dimensionamento
Para ver uma lista de instâncias de VM num conjunto de dimensionamento, utilize [az vmss list-instances](/cli/azure/vmss) da seguinte forma:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

O seguinte resultado de exemplo mostra duas instâncias da VM no conjunto de dimensionamento:

```output
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


A primeira coluna no resultado mostra um *InstanceId*. Para ver informações adicionais sobre uma instância de VM específica, adicione o parâmetro `--instance-id` a [az vmss get-instance-view](/cli/azure/vmss). O seguinte exemplo mostra informações sobre a instância da VM *1*:

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>Listar informações de ligação
Um endereço IP público é atribuído ao balanceador de carga que encaminha tráfego para as instâncias de VM individuais. Por predefinição, as regras de Tradução de Endereços de Rede (NAT) são adicionadas ao balanceador de carga do Azure que encaminha o tráfego de ligação remota para cada VM numa determinada porta. Para ligar às instâncias de VM remotas num conjunto de dimensionamento, crie uma ligação remota a um número de porta e endereço IP público atribuído.

Para listar as portas e o endereço para ligar a instâncias de VM num conjunto de dimensionamento, utilize [az vmss list-instance-connection-info](/cli/azure/vmss):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

O seguinte resultado de exemplo mostra o nome de instância, o endereço IP público do balanceador de carga e o número de porta para o qual as regras NAT encaminham o tráfego:

```output
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH para a sua primeira instância de VM. Especifique o seu número de porta e endereço IP público com o parâmetro `-p`, conforme apresentado no comando anterior:

```console
ssh azureuser@13.92.224.66 -p 50001
```

Após iniciar sessão na instância da VM, pode fazer algumas alterações de configuração manuais, conforme necessário. Por enquanto, encerre a sessão SSH como habitualmente:

```console
exit
```


## <a name="understand-vm-instance-images"></a>Compreender as imagens de instâncias de VM
Quando criou um conjunto de dimensionamento no início do tutorial, foi especificada uma `--image` de *UbuntuLTS* para as instâncias de VM. O Azure Marketplace inclui muitas imagens que podem ser usadas para criar instâncias VM. Para ver uma lista das imagens mais frequentemente utilizadas, utilize o comando [az vm image list](/cli/azure/vm/image).

```azurecli-interactive
az vm image list --output table
```

O seguinte resultado de exemplo mostra as imagens de VM mais comuns no Azure. O *UrnAlias* pode ser utilizado para especificar uma destas imagens comuns quando criar um conjunto de dimensionamento.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

Para ver uma lista completa, adicione o argumento `--all`. Também é possível filtrar a lista de imagens por `--publisher` ou `–-offer`. No seguinte exemplo, a lista está filtrada para todas as imagens com uma oferta que corresponde ao *CentOS*:

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

O resultado condensado seguinte mostra algumas das imagens disponíveis do CentOS 7.3:

```output
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

Para implementar um conjunto de dimensionamento que utilize uma imagem específica, utilize o valor na coluna *Urn*. Ao especificar a imagem, o número de versão da imagem pode ser substituído por *latest*, que seleciona a versão mais recente da distribuição. No seguinte exemplo, o argumento `--image` é utilizado para especificar a versão mais recente de uma imagem do CentOS 7.3.

> [!IMPORTANT]
> Recomendamos a utilização da versão de imagem *mais recente.* Especifique as 'últimas' para utilizar a versão mais recente de uma imagem disponível no momento de implementação. Note que mesmo que utilize 'mais recente', a imagem VM não atualizará automaticamente após o tempo de implantação, mesmo que uma nova versão esteja disponível.

Uma vez que a criação e configuração de todas as instâncias de VMs e recursos do conjunto de dimensionamento demora alguns minutos, não precisa de implementar o seguinte conjunto de dimensionamento:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>Compreender os tamanhos de instâncias de VM
Um tamanho de instância de VM, ou *SKU*, determina a quantidade de recursos de computação, como a CPU, GPU e memória que ficam disponíveis para a instância de VM. As instâncias de VMs num conjunto de dimensionamento precisam de ter o tamanho adequado para a carga de trabalho esperada.

### <a name="vm-instance-sizes"></a>Tamanhos de instância de VM
A tabela seguinte categoriza tamanhos de VM comuns em casos de utilização.

| Tipo                     | Tamanhos comuns           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Fins gerais](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| CPU para memória equilibrada. Ideal para desenvolvimento/teste e aplicações e soluções de dados pequenas a médias.  |
| [Com otimização de computação](../virtual-machines/sizes-compute.md)   | Fs, F             | CPU para memória elevada. É adequado para aplicações de tráfego médio, dispositivos de rede e processos em lote.        |
| [Com otimização de memória](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Memória-para-núcleo elevada. É ideal para bases de dados relacionais, caches médias a grandes e análise dentro da memória.                 |
| [Com otimização de armazenamento](../virtual-machines/sizes-storage.md)      | Ls                | Débito e E/S de disco elevados. Ideal para bases de dados de Macrodados, SQL e NoSQL.                                                         |
| [GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | VMs especializadas destinadas a composição gráfica e edição de vídeo exigentes.       |
| [Elevado desempenho](../virtual-machines/sizes-hpc.md) | H, A8-11          | As nossas mais avançadas VMs com CPU, com interfaces de rede de alto débito (RDMA) opcionais. 

### <a name="find-available-vm-instance-sizes"></a>Localizar tamanhos de instâncias de VM disponíveis
Para ver uma lista de tamanhos de instâncias de VM disponíveis numa região específica, utilize o comando [az vm list-sizes](/cli/azure/vm).

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

O resultado será semelhante ao seguinte exemplo condensado, que mostra os recursos atribuídos a cada tamanho de VM:

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>Criar um conjunto de dimensionamento com um tamanho de instância de VM específico
Quando criou um conjunto de dimensionamento no início do tutorial, foi fornecido um SKU de VM predefinido de *Standard_D1_v2* para as instâncias de VMs. Pode especificar um tamanho de instância de VM diferente com base no resultado de [az vm list-sizes](/cli/azure/vm). O seguinte exemplo cria um conjunto de dimensionamento com o parâmetro `--vm-sku` para especificar um tamanho de instância de VM de *Standard_F1*. Uma vez que a criação e configuração de todas as instâncias de VMs e recursos do conjunto de dimensionamento demora alguns minutos, não precisa de implementar o seguinte conjunto de dimensionamento:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Quando criou um conjunto de dimensionamento no início do tutorial, duas instâncias de VM foram implementadas por predefinição. Pode especificar o parâmetro `--instance-count` com [az vmss create](/cli/azure/vmss) para mudar o número de instâncias criadas com um conjunto de dimensionamento. Para aumentar ou diminuir o número de instâncias de VM no seu conjunto de dimensionamento existente, pode gerir a capacidade manualmente. O conjunto de dimensionamento cria ou remove o número necessário de instâncias de VM e, em seguida, configura o balanceador de carga de forma a distribuir tráfego.

Para aumentar ou diminuir manualmente o número de instâncias de VM no conjunto de dimensionamento, utilize [az vmss scale](/cli/azure/vmss). O exemplo seguinte define o número de instâncias de VMs no seu conjunto de dimensionamento como *3*:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

São necessários alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Para ver o número de instâncias que tem agora no conjunto de dimensionamento, utilize [az vmss show](/cli/azure/vmss) e consulte *sku.capacity*:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>Tarefas comuns de gestão
Agora, pode criar um conjunto de dimensionamento, listar as informações de ligação e ligar-se a instâncias de VMs. Aprendeu como utilizar uma imagem de SO diferente para as suas instâncias de VM, selecionar um tamanho de VM diferente ou dimensionar manualmente o número de instâncias. Como parte da sua gestão quotidiana, poderá precisar de parar, iniciar ou reiniciar as instâncias de VMs no seu conjunto de dimensionamento.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Parar e desalocar instâncias de VMs num conjunto de dimensionamento
Para parar uma ou mais instâncias de VMs num conjunto de dimensionamento, utilize [az vmss stop](/cli/azure/vmss). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais instâncias de VMs para parar. Se não especificar um ID de instância, todas as instâncias de VMs no conjunto de dimensionamento são paradas. O seguinte exemplo para a instância *1*:

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

As instâncias de VMs paradas permanecem alocadas e continuam a incorrer em custos de computação. Por outro lado, se preferir que as instâncias de VMs sejam desalocadas e incorrer apenas em custos de armazenamento, utilize [az vmss deallocate](/cli/azure/vmss). O seguinte exemplo para e desaloca a instância *1*:

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>Iniciar instâncias de VMs num conjunto de dimensionamento
Para iniciar uma ou mais instâncias de VMs num conjunto de dimensionamento, utilize [az vmss start](/cli/azure/vmss). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais instâncias de VMs para iniciar. Se não especificar um ID de instância, todas as instâncias de VMs no conjunto de dimensionamento são iniciadas. O seguinte exemplo inicia a instância *1*:

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Reiniciar instâncias de VMs num conjunto de dimensionamento
Para reiniciar uma ou mais instâncias de VMs num conjunto de dimensionamento, utilize [az vmss restart](/cli/azure/vmss). O parâmetro `--instance-ids` permite-lhe especificar uma ou mais instâncias de VMs para reiniciar. Se não especificar um ID de instância, todas as instâncias de VMs no conjunto de dimensionamento são reiniciadas. O seguinte exemplo reinicia a instância *1*:

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>Limpar os recursos
Quando eliminar um grupo de recursos, todos os recursos nele contidos, como as instâncias de VMs, a rede virtual e os discos, também são eliminados. O parâmetro `--no-wait` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a executar algumas tarefas básicas de criação e gestão de conjuntos de dimensionamento com a CLI do Azure:

> [!div class="checklist"]
> * Criar e ligar a um conjunto de dimensionamento de máquinas virtuais
> * Selecionar e utilizar imagens de VM
> * Ver e utilizar tamanhos específicos de VM
> * Dimensionar manualmente um conjunto de dimensionamento
> * Executar tarefas de gestão comuns de conjuntos de dimensionamento

Avance para o próximo tutorial para saber mais sobre os discos de conjuntos de dimensionamento.

> [!div class="nextstepaction"]
> [Utilizar discos de dados com conjuntos de dimensionamento](tutorial-use-disks-cli.md)

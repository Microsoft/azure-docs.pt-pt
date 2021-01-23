---
title: Tutorial - Criar e gerir redes virtuais Azure para Os VMs Linux
description: Neste tutorial, vai aprender a utilizar a CLI do Azure para criar e gerir redes virtuais do Azure para máquinas virtuais do Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c806e5d1d9c9242b6ba57496327c672d7fed0654
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704494"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-linux-virtual-machines-with-the-azure-cli"></a>Tutorial: Criar e gerir redes virtuais do Azure para máquinas virtuais do Linux com a CLI do Azure

As máquinas virtuais do Azure utilizam a rede do Azure para a comunicação de rede interna e externa. Este tutorial mostra a implementação de duas máquinas virtuais e a configuração da rede do Azure para essas VMs Os exemplos neste tutorial partem do princípio de que as VMs alojam uma aplicação Web com um back-end de base de dados. No entanto, não é implementada nenhuma aplicação no tutorial. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma rede virtual e uma sub-rede
> * Crie um endereço IP público
> * Criar uma VM de front-end
> * Proteger o tráfego de rede
> * Criar uma VM de back-end

Este tutorial utiliza o CLI dentro da [Azure Cloud Shell](../../cloud-shell/overview.md), que é constantemente atualizado para a versão mais recente. Para abrir a Cloud Shell, selecione **Experimente-a** a partir da parte superior de qualquer bloco de código.

Se optar por instalar e utilizar a CLI localmente, este tutorial requer que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="vm-networking-overview"></a>Descrição geral do funcionamento em rede de VMs

As redes virtuais Azure permitem ligações de rede seguras entre máquinas virtuais, internet e outros serviços Azure, como a Base de Dados Azure SQL. As redes virtuais são divididas em segmentos lógicos, chamados sub-redes. As sub-redes são utilizadas para controlar o fluxo de rede e como limite de segurança. Ao implementar uma VM, incluem, geralmente, uma interface de rede virtual, que é ligada a uma sub-rede.

À medida que concluir o tutorial, são criados os seguintes recursos de rede virtual:

![Rede virtual com duas sub-redes](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - a rede virtual que as VMs utilizam para comunicar entre si e com a Internet.
- *myFrontendSubnet* - a sub-rede em *myVNet* que os recursos de front-end utilizam.
- *myPublicIPAddress* - o endereço IP público utilizado para aceder a *myFrontendVM* a partir da Internet.
- *myFrontentNic* - a interface de rede utilizada por *myFrontendVM* para comunicar com *myBackendVM*.
- *myFrontendVM* - a VM utilizada para comunicar entre a Internet e *myBackendVM*.
- *myBackendNSG* - o grupo de segurança de rede que controla a comunicação entre *myFrontendVM* e *myBackendVM*.
- *myBackendSubnet* - a sub-rede associada a *myBackendNSG* e utilizada pelos recursos de back-end.
- *myBackendNic* - a interface de rede utilizada por *myBackendVM* para comunicar com *myFrontendVM*.
- *myBackendVM* - a VM que utiliza as portas 22 e 3306 para comunicar com *myFrontendVM*.

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e uma sub-rede

Neste tutorial, é criada uma rede virtual individual com duas sub-redes. Uma sub-rede de front-end para alojar uma aplicação Web e uma sub-rede de back-end para alojar um servidor de bases de dados.

Antes de poder criar uma rede virtual, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myRGNetwork* na localização leste.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Criar a rede virtual

Utilize o comando [az network vnet create](/cli/azure/network/vnet) para criar uma rede virtual. Neste exemplo, a rede tem o nome *mvVNet* e recebe o prefixo de endereço *10.0.0.0/16*. Também é criada uma sub-rede com o nome *myFrontendSubnet* e o prefixo *10.0.1.0/24*. Mais tarde no tutorial, vai ser ligada uma VM de front-end a esta sub-rede. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Criar sub-rede

É adicionada uma sub-rede nova à rede virtual com o comando [az network vnet subnet create](/cli/azure/network/vnet/subnet). Neste exemplo, a sub-rede tem o nome *myBackendSubnet* e recebe o prefixo de endereço *10.0.2.0/24*. Esta sub-rede é utilizada com todos os serviços de back-end.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

Nesta fase, já está criada uma rede e segmentada em duas sub-redes, uma para os serviços de front-end e outra para os de back-end. Na secção seguinte, são criadas máquinas virtuais e ligadas a estas sub-redes.

## <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Os endereços IP públicos permitem que os recursos do Azure estejam acessíveis na Internet. O método de atribuição do endereço IP público pode ser configurado como dinâmico ou estático. Por predefinição, o endereço IP público é atribuído dinamicamente. Os endereços IP dinâmicos são libertados quando uma VM é desalocada. Este comportamento faz com que o endereço IP mude durante qualquer operação que inclua desalocar uma VM.

O método de alocação pode ser definido como estático, o que garante que o endereço IP permanece atribuído a uma VM, mesmo durante o estado desalocado. Quando é utilizado um endereço IP atribuído estaticamente, não é possível especificar o próprio endereço IP. Em vez disso, é alocado partir de um conjunto de endereços disponíveis.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

Quando cria uma VM com o comando [az vm create](/cli/azure/vm), o método alocação predefinido do endereço IP público é dinâmico. Se criar uma máquina virtual com o comando [az vm create](/cli/azure/vm), inclua o argumento `--public-ip-address-allocation static` para atribuir um endereço IP público estático. Esta operação não é demonstrada neste tutorial. No entanto, na secção seguinte, um endereço IP alocado dinamicamente vai ser alterado para um endereço alocado de forma estática. 

### <a name="change-allocation-method"></a>Alterar o método de alocação

O método de alocação do endereço IP pode ser alterado com o comando [az network public-ip update](/cli/azure/network/public-ip). Neste exemplo, o método de alocação do endereço IP da VM de front-end é alterado para estático.

Em primeiro lugar, anule a alocação da VM.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

Utilize o comando [az network public-ip update](/cli/azure/network/public-ip) para atualizar o método de alocação. Neste caso, `--allocation-method` vai ser definido como *estático*.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

Inicie a VM.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>Sem endereço IP público

Muitas vezes, as VMs não têm de estar acessíveis através da internet. Para criar uma VM sem um endereço IP público, utilize o argumento `--public-ip-address ""` com um conjunto vazio de aspas duplas. Esta configuração é demonstrada mais à frente neste tutorial.

## <a name="create-a-front-end-vm"></a>Criar uma VM de front-end

Utilize o comando [az vm create](/cli/azure/vm) para criar a VM chamada *myFrontendVM* com *myPublicIPAddress*.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>Proteger o tráfego de rede

Os grupos de segurança de rede (NSG) contêm uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos ligados a Redes Virtuais do Azure (VNet). Os NSGs podem ser associados a sub-redes ou a interfaces de rede individuais. Quando um NSG é associado uma interface de rede, aplica apenas a VM associada. Quando um NSG é associado a uma sub-rede, as regras são aplicadas a todos os recursos ligados à mesma. 

### <a name="network-security-group-rules"></a>Regras do grupo de segurança de rede

As regras do NSG definem as portas de rede através das quais o tráfego é permitido ou recusado. As regras podem incluir intervalos de endereços IP de origem e de destino, para que o tráfego seja controlado entre sistemas ou sub-redes específicos. As regras do NSG também incluem uma prioridade (entre 1 e 4096). As regras são avaliadas por ordem de prioridade. Uma regra com uma prioridade de 100 é avaliada antes de uma regra com prioridade de 200.

Todos os NSGs contêm um conjunto de regras predefinidas. As regras predefinidas não podem ser eliminadas, mas como lhes é atribuída a prioridade mais baixa, podem ser substituídas pelas regras que criar.

As regras predefinidas dos NSGs são:

- **Rede virtual** - o tráfego que tem origem e termina numa rede virtual é permitido nas direções de entrada e de saída.
- **Internet** - o tráfego de saída é permitido, mas o tráfego de entrada é bloqueado.
- **Balanceador de carga** - permitir que o balanceador de carga do Azure sonde o estado de funcionamento das VMs e das instâncias de função. Se não estiver a utilizar um conjunto com balanceamento de carga, pode substituir esta regra.

### <a name="create-network-security-groups"></a>Criar grupos de segurança de rede

Pode ser criado um grupo de segurança de rede ao mesmo tempo que uma VM com o comando [az vm create](/cli/azure/vm). Ao fazê-lo, o NSG é associado à interface de rede das VMs e é criada automaticamente uma regra de NSG para permitir tráfego na porta *22* a partir de qualquer origem. Anteriormente no tutorial, o NSG de front-end foi criado automaticamente com a VM de front-end. Também foi criada automaticamente uma regra de NSG para a porta 22. 

Em alguns casos, poderá ser útil pré-criar um NSG, por exemplo, quando não devem ser criadas regras de SSH predefinidas ou quando o NSG deve ser ligado a uma sub-rede. 

Utilize o comando [az network nsg create](/cli/azure/network/nsg) para criar um grupo de segurança de rede.

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

Em vez de associar o NSG a uma interface de rede, este é associado a uma sub-rede. Nesta configuração, qualquer VM que esteja ligada à sub-rede herda as regras do NSG.

Atualize a sub-rede existente com o nome *myBackendSubnet* com o NSG novo.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>Proteger o tráfego de entrada

Quando a VM de front-end foi criada, foi criada uma regra de NSG para permitir o tráfego de entrada na porta 22. Esta regra permite ligações SSH à VM. Neste exemplo, o tráfego também deve ser permitido na porta *80*. Esta configuração permite aceder a uma aplicação Web na VM.

Utilize o comando [az network nsg rule create](/cli/azure/network/nsg/rule) para criar uma regra para a porta *80*.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

A VM de front-end só é acessível na porta *22* e na porta *80*. O restante tráfego é bloqueado no grupo de segurança de rede. Poderá ser útil visualizar as configurações das regras do NSG. Devolva a configuração da regra do NSG com o comando [az network rule list](/cli/azure/network/nsg/rule). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>Proteger o tráfego de VM para VM

Também é possível aplicar regras de grupos de segurança de rede entre VMs. Neste exemplo, a VM de front-end tem de comunicar com a VM de back-end nas portas *22* e *3306*. Esta configuração permite ligações SSH a partir da VM de front-end e também permite que uma aplicação nesta última comunique com uma base de dados MySQL de back-end. Todo o outro tráfego deve ser bloqueado entre as máquinas virtuais de front-end e de back-end.

Utilize o comando [az network nsg rule create](/cli/azure/network/nsg/rule) para criar uma regra para a porta 22. Repare que o argumento `--source-address-prefix` especifica o valor *10.0.1.0/24*. Esta configuração assegura que apenas o tráfego da sub-rede de front-end é permitido através do NSG.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Agora, adicione uma regra para o tráfego de MySQL na porta 3306.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

Por fim, porque os NSGs têm uma regra predefinida, permitindo todo o tráfego entre VMs na mesma VNet, pode ser criada uma regra para os NSGs de back-end, de modo a bloquear todo o tráfego. Repare que a `--priority` é atribuído o valor de *300*, que é menor do que as regras do NSG e do MySQL. Esta configuração garante que o tráfego de SSH e do MySQL ainda é permitido através do NSG.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>Criar a VM de back-end

Agora, crie uma máquina virtual que vai ser ligada a *myBackendSubnet*. Repare que o argumento `--nsg` tem um valor de aspas duplas vazias. Não é necessário criar um NSG com a VM. A VM está ligada à sub-rede de back-end, que está protegida com o NSG de back-end criado previamente. Este NSG aplica-se à VM. Além disso, repare que o argumento `--public-ip-address` tem um valor de aspas duplas vazias. Esta configuração cria uma VM sem um endereço IP público. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A VM de back-end só é acessível na porta *22* e na porta *3306* a partir da sub-rede de front-end. O restante tráfego é bloqueado no grupo de segurança de rede. Poderá ser útil visualizar as configurações das regras do NSG. Devolva a configuração da regra do NSG com o comando [az network rule list](/cli/azure/network/nsg/rule). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>Próximos passos

Neste tutorial, criou e protegeu redes do Azure relacionadas com máquinas virtuais. Aprendeu a:

> [!div class="checklist"]
> * Criar uma rede virtual e uma sub-rede
> * Crie um endereço IP público
> * Criar uma VM de front-end
> * Proteger o tráfego de rede
> * Criar a VM de back-end

Para aprender a proteger os seus discos VM, consulte [backup e recuperação de desastres para discos](backup-and-disaster-recovery-for-azure-iaas-disks.md).

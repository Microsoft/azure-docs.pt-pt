---
title: Tutorial - Use uma imagem VM personalizada em um conjunto de escala com Azure CLI
description: Saiba como utilizar a CLI do Azure para criar uma imagem de VM personalizada que pode utilizar para implementar um conjunto de dimensionamento de máquinas virtuais
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6d9f625bf425a33b690fd303a4f13d032bd59fa0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80062724"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Tutorial: Criar e utilizar uma imagem personalizada para conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure
Quando cria um conjunto de dimensionamento, tem de especificar uma imagem a ser utilizada quando as instâncias de VM são implementadas. Para reduzir o número de tarefas após as instâncias de VM serem implementadas, pode utilizar uma imagem de VM personalizada. Esta imagem de VM personalizada inclui instalações ou configurações de aplicações obrigatórias. Quaisquer instâncias de VM criadas no conjunto de dimensionamento utilizam a imagem de VM personalizada e estão prontas para apresentar o seu tráfego de aplicações. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar e personalizar uma VM
> * Desaprovisionar e generalizar a VM
> * Criar uma imagem de VM personalizada
> * Implementar um conjunto de dimensionamento que utiliza a imagem de VM personalizada

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.29 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Criar e configurar uma VM de origem

>[!NOTE]
> Este tutorial explica o processo de criar e utilizar uma imagem de VM generalizada. Não é suportado para criar uma escala definido a partir de uma imagem VM especializada.

Em primeiro lugar, crie um grupo de recursos com [az group create](/cli/azure/group) e, em seguida, crie uma VM com [az vm create](/cli/azure/vm). Esta VM é depois utilizada como a origem de uma imagem de VM personalizada. O exemplo seguinte cria uma VM com o nome *myVM* no grupo de recursos com o nome *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

O endereço IP público da sua VM é apresentado na saída do comando [az vm create](/cli/azure/vm). Execute o SSH ao endereço IP público da VM, da seguinte forma:

```console
ssh azureuser@<publicIpAddress>
```

Para personalizar a sua VM, vamos instalar um servidor Web básico. Quando a instância de VM no conjunto de dimensionamento for implementada, terá todos os pacotes necessários para executar uma aplicação Web. Utilize `apt-get` para instalar o *NGINX*, da seguinte forma:

```bash
sudo apt-get install -y nginx
```

O último passo para preparar a sua VM para utilização como imagem personalizada é desaprovisionar a sua VM. Este passo remove informações específicas de máquina da VM e torna possível implementar várias VMs a partir de uma única imagem. Quando a VM é desaprovisionada, o nome do anfitrião é reposto a *localhost.localdomain*. Também são eliminadas as chaves de anfitrião do SSH, as configurações do servidor de nomes, a palavra-passe de raiz e as concessões DHCP em cache.

Para desaprovisionar a VM, utilize o agente da VM do Azure (*waagent*). O agente da VM do Azure é instalado em cada VM e é utilizado para comunicar com a plataforma do Azure. O parâmetro `-force` informa o agente para aceitar pedidos para repor as informações específicas da máquina.

```bash
sudo waagent -deprovision+user -force
```

Feche a ligação SSH à VM:

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Criar uma imagem de VM personalizada a partir da VM de origem
A VM de origem está agora personalizada com o servidor Web Nginx instalado. Vamos criar a imagem de VM personalizada para utilizar com um conjunto de dimensionamento.

Para criar uma imagem, a VM tem de ser desalocada. Desaloque a VM com [az vm deallocate](/cli//azure/vm). Em seguida, defina o estado da VM como generalizado com [az vm generalize](/cli//azure/vm), para que a plataforma do Azure saiba que a VM está pronta para utilizar uma imagem personalizada. Pode criar apenas uma imagem a partir de uma VM generalizada:


```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

Poderão ser necessários alguns minutos para desalocar e generalizar a VM.

Agora, crie uma imagem da VM com [az image create](/cli//azure/image). O exemplo seguinte cria uma imagem designada *myImage* a partir da sua VM:

> [NOTA] Se a localização do Grupo de Recursos e `--location` da Máquina Virtual for diferente, pode adicionar o parâmetro aos comandos abaixo para especificar a localização da fonte VM utilizada para criar a imagem. 

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Criar um conjunto de dimensionamento a partir da imagem de VM personalizada
Crie um conjunto com de dimensionamento com [az vmss create](/cli/azure/vmss#az-vmss-create). Em vez de uma imagem de plataforma, como *UbuntuLTS* ou *CentOS*, especifique o nome da sua imagem de VM personalizada. O exemplo seguinte cria um conjunto de dimensionamento com o nome *myScaleSet*, que utiliza a imagem personalizada com o nome *myImage* do passo anterior:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.


## <a name="test-your-scale-set"></a>Testar o seu conjunto de dimensionamento
Para permitir que o tráfego alcance o conjunto de dimensionamento e verificar se o servidor Web funciona corretamente, crie um balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule). O exemplo seguinte cria uma regra com o nome *myLoadBalancerRuleWeb*, que permite tráfego na porta *TCP**80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Para ver o seu conjunto de dimensionamento em ação, obtenha o endereço IP público do seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip). O exemplo seguinte obtém o endereço IP para *myScaleSetLBPublicIP*, criado como parte do conjunto de dimensionamento:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Escreva o endereço IP público no seu browser. A página Web NGINX predefinida é apresentada, conforme mostrado no seguinte exemplo:

![Nginx executado da imagem de VM personalizada](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Limpar recursos
Para remover o seu conjunto de dimensionamento e recursos adicionais, elimine o grupo de recursos e todos os respetivos recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar e a utilizar uma imagem de VM personalizada nos seus conjuntos de dimensionamento com a CLI do Azure:

> [!div class="checklist"]
> * Criar e personalizar uma VM
> * Desaprovisionar e generalizar a VM
> * Criar uma imagem de VM personalizada
> * Implementar um conjunto de dimensionamento que utiliza a imagem de VM personalizada

Prossiga para o próximo tutorial para saber como implementar aplicações no seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Implementar aplicações nos seus conjuntos de dimensionamento](tutorial-install-apps-cli.md)

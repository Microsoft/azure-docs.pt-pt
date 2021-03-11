---
title: Capture uma imagem gerida de um Linux VM usando Azure CLI
description: Capture uma imagem gerida de um Azure VM para usar para implantações em massa utilizando o CLI Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 10/08/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.collection: linux
ms.openlocfilehash: 6b345f159ca30d93e43aae8fe34e7d469c57795b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556660"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Como criar uma imagem gerida de uma máquina virtual ou VHD

Para criar várias cópias de uma máquina virtual (VM) para utilização em Azure para desenvolvimento e teste, capture uma imagem gerida do VM ou do OS VHD. Para criar, armazenar e partilhar imagens em escala, consulte [galerias de imagem partilhadas.](../shared-images-cli.md)

Uma imagem gerida suporta até 20 implementações simultâneas. A tentativa de criar mais de 20 VMs simultaneamente, a partir da mesma imagem gerida, pode resultar em intervalos de provisão devido às limitações de desempenho de armazenamento de um único VHD. Para criar mais de 20 VMs simultaneamente, utilize uma imagem [das Galerias de Imagem Partilhada](../shared-image-galleries.md) configurada com 1 réplica para cada 20 implementações VM simultâneas.

Para criar uma imagem gerida, terá de remover informações pessoais da conta. Nos passos seguintes, desprovisionia um VM existente, transcolote-o e cria-se uma imagem. Pode utilizar esta imagem para criar VMs em qualquer grupo de recursos dentro da sua subscrição.

Para criar uma cópia do seu VM Linux existente para cópia de segurança ou depuração, ou para carregar um VHD Linux especializado a partir de um VM no local, consulte [upload e crie um Linux VM a partir de imagem de disco personalizado](upload-vhd.md).  

Pode utilizar o serviço **Azure VM Image Builder (Visualização Pública)** para construir a sua imagem personalizada, não precisa de aprender quaisquer ferramentas, ou configurar oleodutos, simplesmente fornecendo uma configuração de imagem, e o Image Builder criará a Imagem. Para obter mais informações, consulte ["Começar com o Azure VM Image Builder".](../image-builder-overview.md)

Vai precisar dos seguintes itens antes de criar uma imagem:

* Um Azure VM criado no modelo de implementação do Gestor de Recursos que utiliza discos geridos. Se ainda não criou um Linux VM, pode utilizar o [portal](quick-create-portal.md), o [Azure CLI](quick-create-cli.md)ou [os modelos Resource Manager](create-ssh-secured-vm-from-template.md). Configure o VM conforme necessário. Por exemplo, [adicione discos de dados,](add-disk.md)aplique atualizações e instale aplicações. 

* O mais recente [Azure CLI](/cli/azure/install-az-cli2) instalou e foi iniciado numa conta Azure com [login az](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Prefere um tutorial?

Para uma versão simplificada deste artigo, e para testar, avaliar ou aprender sobre VMs em Azure, consulte [Criar uma imagem personalizada de um VM Azure utilizando o CLI](tutorial-custom-images.md).  Caso contrário, continue a ler aqui para obter o quadro completo.


## <a name="step-1-deprovision-the-vm"></a>Passo 1: Desprovisionar o VM
Em primeiro lugar, irá desprovisionar o VM utilizando o agente Azure VM para eliminar ficheiros e dados específicos da máquina. Utilize o `waagent` comando com o parâmetro na sua fonte `-deprovision+user` Linux VM. Para obter mais informações, veja o [Guia de utilizador do Agente Linux do Azure](../extensions/agent-linux.md).

1. Ligue-se ao seu Linux VM com um cliente SSH.
2. Na janela SSH, insira o seguinte comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Só executar este comando num VM que irá capturar como uma imagem. Este comando não garante que a imagem seja limpa de toda a informação sensível ou seja adequada para redistribuição. O `+user` parâmetro também remove a última conta de utilizador a provisionada. Para manter as credenciais de conta de utilizador no VM, utilize apenas `-deprovision` .
 
3. Entre **para** continuar. Pode adicionar o `-force` parâmetro para evitar este passo de confirmação.
4. Após o comando estar concluído, entre na **saída** para fechar o cliente SSH.  O VM ainda estará em execução neste momento.

## <a name="step-2-create-vm-image"></a>Passo 2: Criar imagem VM
Utilize o CLI Azure para marcar o VM como generalizado e capturar a imagem. Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Os nomes dos parâmetros incluem *myResourceGroup,* *myVnet* e *myVM*.

1. Deallocate o VM que você desprovisionou com [az vm deallocate](/cli/azure/vm). O exemplo seguinte é o que a VM nomeou *myVM* no grupo de recursos denominado *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    Aguarde que o VM transorganizar completamente antes de seguir em frente. Isto pode levar alguns minutos para ser concluído.  O VM é encerrado durante a negociação.

2. Marque o VM como generalizado com [az vm generalizar](/cli/azure/vm). O exemplo a seguir marca o VM nomeado *myVM* no grupo de recursos chamado *myResourceGroup* como generalizado.
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    Um VM que foi generalizado já não pode ser reiniciado.

3. Crie uma imagem do recurso VM com [a imagem az criar](/cli/azure/image#az-image-create). O exemplo a seguir cria uma imagem chamada *myImage* no grupo de recursos chamado *myResourceGroup* usando o recurso VM chamado *myVM*.
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
        --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A imagem é criada no mesmo grupo de recursos que o seu VM de origem. Pode criar VMs em qualquer grupo de recursos dentro da sua subscrição a partir desta imagem. Do ponto de vista da gestão, poderá pretender criar um grupo de recursos específico para os seus recursos e imagens VM.
   >
   > Se quiser armazenar a sua imagem em armazenamento resiliente à zona, precisa de a criar numa região que suporte [zonas de disponibilidade](../../availability-zones/az-overview.md) e inclua o `--zone-resilient true` parâmetro.
   
Este comando devolve JSON que descreve a imagem VM. Guarde esta saída para referência posterior.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passo 3: Criar um VM a partir da imagem capturada
Crie um VM utilizando a imagem que criou com [az vm criar](/cli/azure/vm). O exemplo a seguir cria um VM chamado *myVMDeployed* a partir da imagem chamada *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Criar o VM em outro grupo de recursos 

Pode criar VMs a partir de uma imagem em qualquer grupo de recursos dentro da sua subscrição. Para criar um VM num grupo de recursos diferente da imagem, especifique o ID completo do recurso para a sua imagem. Use [a lista de imagens az](/cli/azure/image#az-image-list) para ver uma lista de imagens. O resultado será semelhante ao seguinte exemplo.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

O exemplo a seguir utiliza [a criação de az vm](/cli/azure/vm#az-vm-create) para criar um VM num grupo de recursos diferente da imagem de origem, especificando o ID do recurso de imagem.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Passo 4: Verificar a implantação

SSH na máquina virtual que criou para verificar a implementação e começar a usar o novo VM. Para ligar via SSH, encontre o endereço IP ou FQDN do seu VM com [a az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Passos seguintes
Para criar, armazenar e partilhar imagens em escala, consulte [galerias de imagem partilhadas.](../shared-images-cli.md)
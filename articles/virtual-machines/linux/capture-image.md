---
title: Capture uma imagem de um VM Linux usando O ClI Azure
description: Capture uma imagem de um VM Azure para usar para implantações em massa utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 77f6244651551763f5460432655d66267775a256
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967915"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Como criar uma imagem de uma máquina virtual ou VHD

Para criar várias cópias de uma máquina virtual (VM) para utilização em Azure, capture uma imagem do VM ou do VHD osS. Para criar uma imagem para implementação, terá de remover informações pessoais da conta. Nos seguintes passos, você desprovisiona um VM existente, desaloca-lo e criar uma imagem. Pode utilizar esta imagem para criar VMs em qualquer grupo de recursos dentro da sua subscrição.

Para criar uma cópia do seu VM Linux existente para cópia de segurança ou depuração, ou para carregar um VHD Linux especializado a partir de um VM no local, consulte [o Upload e crie um VM Linux](upload-vhd.md)a partir de uma imagem personalizada do disco .  

Pode utilizar o serviço **Azure VM Image Builder (Public Preview)** para construir a sua imagem personalizada, não precisa de aprender quaisquer ferramentas, ou configurar oleodutos, simplesmente fornecendo uma configuração de imagem, e o Image Builder criará a Imagem. Para mais informações, consulte [Getting Started with Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

Você precisará dos seguintes itens antes de criar uma imagem:

* Um VM Azure criado no modelo de implementação do Gestor de Recursos que utiliza discos geridos. Se ainda não criou um VM Linux, pode utilizar o [portal](quick-create-portal.md), o [Azure CLI](quick-create-cli.md)ou os [modelos De Gestor de Recursos](create-ssh-secured-vm-from-template.md). Configure o VM conforme necessário. Por exemplo, adicione discos de [dados,](add-disk.md)aplique atualizações e instale aplicações. 

* O mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e ser registado numa conta Azure com [login az](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Prefere um tutorial em vez disso?

Para uma versão simplificada deste artigo, e para testar, avaliar ou aprender sobre VMs em Azure, consulte [Criar uma imagem personalizada de um VM Azure utilizando o CLI](tutorial-custom-images.md).  Caso contrário, continue a ler aqui para obter o quadro completo.


## <a name="step-1-deprovision-the-vm"></a>Passo 1: Desprovisionamento do VM
Primeiro irá desfornecer o VM utilizando o agente Azure VM para eliminar ficheiros e dados específicos da máquina. Utilize o comando `waagent` com o parâmetro `-deprovision+user` na sua fonte Linux VM. Para obter mais informações, veja o [Guia de utilizador do Agente Linux do Azure](../extensions/agent-linux.md).

1. Ligue-se ao seu VM Linux com um cliente SSH.
2. Na janela SSH, insira o seguinte comando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Só executa este comando num VM que vai capturar como imagem. Este comando não garante que a imagem seja desmarcada de todas as informações sensíveis ou seja adequada para redistribuição. O parâmetro `+user` também remove a última conta de utilizador provisionada. Para manter as credenciais de conta de utilizador no VM, utilize apenas `-deprovision`.
 
3. Insira **y** para continuar. Pode adicionar o parâmetro `-force` para evitar este passo de confirmação.
4. Depois de o comando estar concluído, introduza a **saída** para fechar o cliente SSH.  O VM ainda estará a funcionar neste momento.

## <a name="step-2-create-vm-image"></a>Passo 2: Criar imagem VM
Utilize o AZURE CLI para marcar o VM como generalizado e capturar a imagem. Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Exemplo nomes de parâmetros incluem *myResourceGroup,* *myVnet,* e *myVM*.

1. Deslocar o VM que desaloou com [az vm desalocado](/cli/azure/vm). O exemplo seguinte desafeta o VM nomeado *myVM* no grupo de recursos chamado *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Aguarde que o VM desloque completamente antes de seguir em frente. Isto pode levar alguns minutos para ser concluído.  O VM é desligado durante a deatribuição.

2. Marque o VM como generalizado com [az vm generalizar](/cli/azure/vm). O exemplo que se segue marca o nome de *VM myVM* no grupo de recursos chamado *myResourceGroup* como generalizado.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    Um VM que foi generalizado já não pode ser reiniciado.

3. Criar uma imagem do recurso VM com a criação de [imagem az](/cli/azure/image#az-image-create). O exemplo seguinte cria uma imagem chamada *myImage* no grupo de recursos chamado *myResourceGroup* usando o recurso VM chamado *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A imagem é criada no mesmo grupo de recursos que a sua fonte VM. Pode criar VMs em qualquer grupo de recursos dentro da sua subscrição a partir desta imagem. Do ponto de vista da gestão, poderá querer criar um grupo de recursos específico sintetizada para os seus recursos e imagens VM.
   >
   > Se você gostaria de armazenar sua imagem em armazenamento resiliente de zona, você precisa criá-la em uma região que suporta zonas de [disponibilidade](../../availability-zones/az-overview.md) e incluir o parâmetro `--zone-resilient true`.
   
Este comando devolve a JSON que descreve a imagem VM. Guarde esta saída para referência posterior.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passo 3: Criar um VM a partir da imagem capturada
Crie um VM utilizando a imagem que criou com a criação de [az vm](/cli/azure/vm). O exemplo seguinte cria um VM chamado *myVMDeployed* a partir da imagem chamada *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Criação do VM em outro grupo de recursos 

Pode criar VMs a partir de uma imagem em qualquer grupo de recursos dentro da sua subscrição. Para criar um VM num grupo de recursos diferente da imagem, especifique o ID completo do recurso para a sua imagem. Use a [lista de imagens az](/cli/azure/image#az-image-list) para ver uma lista de imagens. O resultado será semelhante ao seguinte exemplo.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

O exemplo seguinte usa [a z vm criar](/cli/azure/vm#az-vm-create) para criar um VM num grupo de recursos que não seja a imagem de origem, especificando o ID do recurso de imagem.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Passo 4: Verificar a implantação

SSH na máquina virtual que criou para verificar a implementação e começar a utilizar o novo VM. Para ligar via SSH, encontre o endereço IP ou FQDN do seu VM com [o show az vm](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Passos seguintes
Pode criar vários VMs a partir da sua imagem VM fonte. Para fazer alterações na sua imagem: 

- Crie um VM a partir da sua imagem.
- Faça quaisquer atualizações ou alterações de configuração.
- Siga novamente os passos para desprovisionar, desalocar, generalizar e criar uma imagem.
- Utilize esta nova imagem para futuras implementações. Pode apagar a imagem original.

Para obter mais informações sobre a gestão dos seus VMs com o CLI, consulte [O ClI Azure](/cli/azure).

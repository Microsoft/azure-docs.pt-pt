---
title: Criar e carregar uma imagem de OpenBSD VM para o Azure | Documentos da Microsoft
description: Saiba como criar e carregar um disco rígido virtual (VHD) que contém o sistema de operativo OpenBSD para criar uma máquina virtual do Azure através da CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: d8640881e83084dac7f4725115f48dcf7d29e787
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007601"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Criar e carregar uma imagem de disco OpenBSD para o Azure
Este artigo mostra-lhe como criar e carregar um disco rígido virtual (VHD) que contém o sistema de operativo OpenBSD. Depois de carregá-lo, pode utilizá-lo como sua própria imagem para criar uma máquina virtual (VM) no Azure através da CLI do Azure.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem os seguintes itens:

* **Uma subscrição do Azure** -se não tiver uma conta, pode criar um em apenas alguns minutos. Se tiver uma assinatura do MSDN, consulte [crédito mensal do Azure para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, saiba como [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).  
* **CLI do Azure** -Certifique-se de que tem a versão mais recente [CLI do Azure](/cli/azure/install-azure-cli) instalado e iniciado sessão sua conta do Azure com [início de sessão az](/cli/azure/reference-index).
* **Sistema de operativo OpenBSD instalado num arquivo. vhd** – um sistema operativo de OpenBSD suportado ([versão 6.1 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.1/amd64/)) tem de estar instalado para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd. Por exemplo, pode utilizar uma solução de virtualização, como o Hyper-V para criar o ficheiro. vhd e instalar o sistema operativo. Para obter instruções sobre como instalar e utilizar o Hyper-V, consulte [instalar o Hyper-V e criar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Preparar a imagem de OpenBSD para o Azure
Na VM onde instalou o sistema de operativo OpenBSD 6.1, que adicionou o Hyper-V suporta, conclua os seguintes procedimentos:

1. Se DHCP não estiver ativado durante a instalação, ative o serviço da seguinte forma:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Configure uma consola de série da seguinte forma:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Configure a instalação do pacote da seguinte forma:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Por predefinição, o `root` utilizador é desativado em máquinas virtuais no Azure. Os utilizadores podem executar comandos com privilégios elevados ao utilizar o `doas` comando OpenBSD VM. Doas está ativada por predefinição. Para obter mais informações, consulte [doas.conf](https://man.openbsd.org/doas.conf.5). 

5. Instalar e configurar os pré-requisitos para o agente do Azure da seguinte forma:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. A versão mais recente do agente do Azure sempre pode ser encontrada no [GitHub](https://github.com/Azure/WALinuxAgent/releases). Instale o agente da seguinte forma:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Depois de instalar o agente do Azure, é uma boa idéia para verificar que está a ser executado da seguinte forma:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Desaprovisionar o sistema para limpá-lo e torná-lo adequado para reprovisionamento. O seguinte comando também elimina a última conta de utilizador aprovisionado e os dados associados:

    ```sh
    waagent -deprovision+user -force
    ```

Agora pode encerrar a VM.


## <a name="prepare-the-vhd"></a>Preparar o VHD
O formato VHDX não é suportado no Azure, apenas **fixo VHD**. Pode converter o disco para o formato VHD fixo, utilizando o Gestor de Hyper-V ou o Powershell [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) cmdlet. Um exemplo é como seguinte.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Criar recursos de armazenamento e carregar
Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Para carregar o VHD, crie uma conta de armazenamento com [criar conta de armazenamento az](/cli/azure/storage/account). Nomes de conta de armazenamento tem de ser exclusivo, então, fornecer seu próprio nome. O exemplo seguinte cria uma conta de armazenamento com o nome *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Para controlar o acesso à conta de armazenamento, obtenha a chave de armazenamento com [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys) da seguinte forma:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Separar logicamente os VHDs, carregar, criar um contentor na conta de armazenamento com [criar contentor de armazenamento az](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Por fim, carregue o VHD com o [carregamento de BLOBs de armazenamento az](/cli/azure/storage/blob) da seguinte forma:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Criar VM a partir do VHD
Pode criar uma VM com um [script de exemplo](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) ou diretamente com [az vm criar](/cli/azure/vm). Para especificar o VHD de OpenBSD que carregou, utilize o `--image` parâmetro da seguinte forma:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Obtenha o endereço IP para a sua VM OpenBSD com [az vm lista-ip-addresses](/cli/azure/vm) da seguinte forma:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Agora pode SSH à VM OpenBSD como normal:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Passos Seguintes
Se quiser saber mais sobre o suporte de Hyper-V no OpenBSD6.1, leia [OpenBSD 6.1](https://www.openbsd.org/61.html) e [hyperv.4](https://man.openbsd.org/hyperv.4).

Se quiser criar uma VM a partir de disco gerido, leia [disco az](/cli/azure/disk). 

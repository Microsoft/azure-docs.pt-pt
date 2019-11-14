---
title: Criar e carregar uma imagem de VM OpenBSD no Azure
description: Saiba como criar e carregar um VHD (disco rígido virtual) que contém o sistema operacional OpenBSD para criar uma máquina virtual do Azure por meio do CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: ee15836906eef0b9205691f9a6003cea0b9fae80
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036457"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Criar e carregar uma imagem de disco OpenBSD no Azure
Este artigo mostra como criar e carregar um VHD (disco rígido virtual) que contém o sistema operacional OpenBSD. Depois de carregá-lo, você pode usá-lo como sua própria imagem para criar uma VM (máquina virtual) no Azure por meio do CLI do Azure.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha os seguintes itens:

* **Uma assinatura do Azure** -se você não tiver uma conta, poderá criar uma em apenas alguns minutos. Se você tiver uma assinatura do MSDN, confira [crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, saiba como [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).  
* **CLI do Azure** -Verifique se você tem as [CLI do Azure](/cli/azure/install-azure-cli) mais recentes instaladas e conectadas à sua conta do Azure com [AZ login](/cli/azure/reference-index).
* **Sistema operacional OpenBSD instalado em um arquivo. vhd** -um sistema operacional OpenBSD com suporte ([versão 6,2 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.2/amd64/)) deve ser instalado em um disco rígido virtual. Existem várias ferramentas para criar arquivos. vhd. Por exemplo, você pode usar uma solução de virtualização, como o Hyper-V, para criar o arquivo. vhd e instalar o sistema operacional. Para obter instruções sobre como instalar e usar o Hyper-V, consulte [instalar o Hyper-v e criar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Preparar imagem do OpenBSD para o Azure
Na VM em que você instalou o sistema operacional OpenBSD 6,1, que adicionou o suporte do Hyper-V, conclua os seguintes procedimentos:

1. Se o DHCP não estiver habilitado durante a instalação, habilite o serviço da seguinte maneira:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Configure um console serial da seguinte maneira:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Configure a instalação do pacote da seguinte maneira:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Por padrão, o usuário `root` está desabilitado em máquinas virtuais no Azure. Os usuários podem executar comandos com privilégios elevados usando o comando `doas` na VM OpenBSD. O doas é habilitado por padrão. Para obter mais informações, consulte [doas. conf](https://man.openbsd.org/doas.conf.5). 

5. Instale e configure os pré-requisitos para o agente do Azure da seguinte maneira:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. A versão mais recente do agente do Azure sempre pode ser encontrada no [GitHub](https://github.com/Azure/WALinuxAgent/releases). Instale o agente da seguinte maneira:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Depois de instalar o agente do Azure, é uma boa ideia verificar se ele está em execução da seguinte maneira:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Desprovisione o sistema para limpá-lo e torná-lo adequado para o reprovisionamento. O comando a seguir também exclui a última conta de usuário provisionada e os dados associados:

    ```sh
    waagent -deprovision+user -force
    ```

Agora você pode desligar sua VM.


## <a name="prepare-the-vhd"></a>Preparar o VHD
Não há suporte para o formato VHDX no Azure, somente **VHD fixo**. Você pode converter o disco para o formato VHD fixo usando o Gerenciador do Hyper-V ou o cmdlet [Convert-VHD](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) do PowerShell. Um exemplo é o seguinte.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Criar recursos de armazenamento e carregar
Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Para carregar seu VHD, crie uma conta de armazenamento com [AZ Storage Account Create](/cli/azure/storage/account). Os nomes de conta de armazenamento devem ser exclusivos, por isso forneça seu próprio nome. O exemplo a seguir cria uma conta de armazenamento chamada *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Para controlar o acesso à conta de armazenamento, obtenha a chave de armazenamento com a [lista de chaves de conta de armazenamento AZ](/cli/azure/storage/account/keys) da seguinte maneira:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Para separar logicamente os VHDs que você carrega, crie um contêiner dentro da conta de armazenamento com [AZ Storage container Create](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Por fim, carregue o VHD com o [upload de blob de armazenamento AZ](/cli/azure/storage/blob) da seguinte maneira:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Criar VM a partir de seu VHD
Você pode criar uma VM com um [script de exemplo](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) ou diretamente com [AZ VM Create](/cli/azure/vm). Para especificar o VHD OpenBSD que você carregou, use o parâmetro `--image` da seguinte maneira:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Obtenha o endereço IP para sua VM OpenBSD com [AZ VM List-IP-addresses](/cli/azure/vm) da seguinte maneira:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Agora você pode SSH para sua VM OpenBSD como de costume:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Passos seguintes
Se você quiser saber mais sobre o suporte do Hyper-V no OpenBSD 6.1, leia [OpenBSD 6,1](https://www.openbsd.org/61.html) e [HyperV. 4](https://man.openbsd.org/hyperv.4).

Se você quiser criar uma VM do disco gerenciado, leia [AZ Disk](/cli/azure/disk). 

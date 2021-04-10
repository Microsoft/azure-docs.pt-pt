---
title: Criar e carregar uma imagem OpenBSD
description: Saiba como criar e carregar um disco rígido virtual (VHD) que contém o sistema operativo OpenBSD para criar uma máquina virtual Azure através do Azure CLI
author: gbowerman
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 3437fcd2d1d521aad237ecf6d3f7937b4835d376
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554603"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Criar e carregar uma imagem de disco OpenBSD para Azure
Este artigo mostra-lhe como criar e carregar um disco rígido virtual (VHD) que contém o sistema operativo OpenBSD. Depois de o fazer o upload, pode usá-lo como sua própria imagem para criar uma máquina virtual (VM) em Azure através do Azure CLI.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem os seguintes itens:

* **Uma subscrição do Azure** - Se não tiver uma conta, pode criar uma em apenas alguns minutos. Se tiver uma subscrição da MSDN, consulte [o crédito mensal do Azure para assinantes do Estúdio Visual.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Caso contrário, aprenda a [criar uma conta de teste gratuita.](https://azure.microsoft.com/pricing/free-trial/)  
* **Azure CLI** - Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-azure-cli) instalado e iniciado na sua conta Azure com [login az](/cli/azure/reference-index).
* **O sistema operativo OpenBSD instalado num ficheiro .vhd** - Um sistema operativo OpenBSD suportado [(versão 6.6 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) deve ser instalado num disco rígido virtual. Existem várias ferramentas para criar ficheiros .vhd. Por exemplo, pode utilizar uma solução de virtualização como o Hyper-V para criar o ficheiro .vhd e instalar o sistema operativo. Para obter instruções sobre como instalar e utilizar o Hyper-V, consulte [instalar o Hyper-V e criar uma máquina virtual](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).


## <a name="prepare-openbsd-image-for-azure"></a>Prepare a imagem OpenBSD para O Azure
No VM onde instalou o sistema operativo OpenBSD 6.1, que acrescentou suporte Hyper-V, complete os seguintes procedimentos:

1. Se o DHCP não estiver ativado durante a instalação, ative o serviço da seguinte forma:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Configurar uma consola em série da seguinte forma:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Configure a instalação do pacote da seguinte forma:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Por predefinição, o `root` utilizador é desativado em máquinas virtuais em Azure. Os utilizadores podem executar comandos com privilégios elevados utilizando o `doas` comando em VM OpenBSD. Doas é ativado por padrão. Para mais informações, consulte [doas.conf](https://man.openbsd.org/doas.conf.5). 

5. Instalação e configuração de pré-requisitos para o Agente Azul da seguinte forma:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. A mais recente versão do agente Azure pode sempre ser encontrada no [GitHub.](https://github.com/Azure/WALinuxAgent/releases) Instale o agente da seguinte forma:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Depois de instalar o Agente Azure, é uma boa ideia verificar se está a funcionar da seguinte forma:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Desprovisionar o sistema para limpá-lo e torná-lo adequado para reprovisionar. O seguinte comando também elimina a última conta de utilizador a provisionada e os dados associados:

    ```sh
    waagent -deprovision+user -force
    ```

Agora pode desligar o seu VM.


## <a name="prepare-the-vhd"></a>Preparar o VHD
O formato VHDX não é suportado em Azure, apenas **VHD fixo**. Pode converter o disco para o formato VHD fixo utilizando o Hyper-V Manager ou o cmdlet [de conversão Powershell.](/powershell/module/hyper-v/convert-vhd) Um exemplo é como seguir.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Criar recursos de armazenamento e carregar
Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Para fazer o upload do seu VHD, crie uma conta de armazenamento com [a conta de armazenamento AZ criar](/cli/azure/storage/account). Os nomes das contas de armazenamento devem ser únicos, por isso forneça o seu próprio nome. O exemplo a seguir cria uma conta de armazenamento denominada *mystorageaccount:*

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Para controlar o acesso à conta de armazenamento, obtenha a chave de armazenamento com [a lista de chaves de conta de armazenamento AZ](/cli/azure/storage/account/keys) da seguinte forma:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Para separar logicamente os VHDs que carrega, crie um recipiente dentro da conta de armazenamento com [o recipiente de armazenamento AZ criar:](/cli/azure/storage/container)

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Finalmente, faça o upload do seu VHD com [o upload da bolha de armazenamento az](/cli/azure/storage/blob) da seguinte forma:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Crie VM a partir do seu VHD
Pode criar um VM com um [script de amostra](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-vhd) ou diretamente com a [criação de az vm](/cli/azure/vm). Para especificar o VHD OpenBSD que carregou, utilize o `--image` parâmetro da seguinte forma:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Obtenha o endereço IP para o seu VM OpenBSD com [endereços ip-ip listas az vm](/cli/azure/vm) da seguinte forma:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Agora pode SSH para o seu VM OpenBSD normalmente:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Passos seguintes
Se quiser saber mais sobre o suporte ao Hiper-V no OpenBSD6.1, leia [OpenBSD 6.1](https://www.openbsd.org/61.html) e [hyperv.4](https://man.openbsd.org/hyperv.4).

Se pretender criar um VM a partir de disco gerido, leia [o disco az](/cli/azure/disk).
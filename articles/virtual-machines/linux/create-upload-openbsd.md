---
title: Criar e carregar uma imagem OpenBSD
description: Aprenda a criar e carregar um disco rígido virtual (VHD) que contém o sistema operativo OpenBSD para criar uma máquina virtual Azure através do Azure CLI
author: thomas1206
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: d4ecc539d71933c4aecc9124b903c57cb72838de
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969489"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Crie e carregue uma imagem de disco OpenBSD para o Azure
Este artigo mostra-lhe como criar e carregar um disco rígido virtual (VHD) que contém o sistema operativo OpenBSD. Depois de o carregar, pode usá-lo como imagem própria para criar uma máquina virtual (VM) em Azure através do Azure CLI.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que tem os seguintes itens:

* **Uma subscrição Azure** - Se não tiver uma conta, pode criar uma em apenas alguns minutos. Se tiver uma subscrição MSDN, consulte o [crédito Mensal Azure para assinantes do Estúdio Visual.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Caso contrário, aprenda a [criar uma conta de teste gratuita.](https://azure.microsoft.com/pricing/free-trial/)  
* **Azure CLI** - Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-azure-cli) instalado e iniciado na sua conta Azure com [login az](/cli/azure/reference-index).
* **Sistema operativo OpenBSD instalado num ficheiro .vhd** - Um sistema operativo OpenBSD suportado[(6.6 versão AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) deve ser instalado num disco rígido virtual. Existem múltiplas ferramentas para criar ficheiros .vhd. Por exemplo, pode utilizar uma solução de virtualização como o Hyper-V para criar o ficheiro .vhd e instalar o sistema operativo. Para obter instruções sobre como instalar e utilizar o Hyper-V, consulte [instalar o Hyper-V e criar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Prepare a imagem OpenBSD para o Azure
No VM onde instalou o sistema operativo OpenBSD 6.1, que adicionou suporte Hyper-V, completa os seguintes procedimentos:

1. Se o DHCP não estiver ativado durante a instalação, ative o serviço da seguinte forma:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Configurar uma consola em série da seguinte forma:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Configurar a instalação do pacote da seguinte forma:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Por predefinição, o utilizador `root` é desativado em máquinas virtuais em Azure. Os utilizadores podem executar comandos com privilégios elevados utilizando o comando `doas` no OpenBSD VM. Doas é ativado por defeito. Para mais informações, consulte [doas.conf](https://man.openbsd.org/doas.conf.5). 

5. Instale e configure os pré-requisitos para o Agente Azure da seguinte forma:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
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

7. Desprovisionie o sistema para limpá-lo e torná-lo adequado para reprovisionamento. O seguinte comando também elimina a última conta de utilizador provisionada e os dados associados:

    ```sh
    waagent -deprovision+user -force
    ```

Agora pode desligar o seu VM.


## <a name="prepare-the-vhd"></a>Prepare o VHD
O formato VHDX não é suportado em Azure, apenas **VHD fixo**. Pode converter o disco em formato VHD fixo utilizando o Hyper-V Manager ou o cmdlet [de conversão](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) Powershell. Um exemplo é como seguir.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Criar recursos de armazenamento e upload
Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Para fazer o upload do seu VHD, crie uma conta de armazenamento com a criação de uma conta de [armazenamento Az](/cli/azure/storage/account). Os nomes das contas de armazenamento devem ser únicos, por isso forneça o seu próprio nome. O exemplo seguinte cria uma conta de armazenamento chamada *mystorage account:*

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Para controlar o acesso à conta de armazenamento, obtenha a chave de armazenamento com a lista de chaves da [conta de armazenamento az](/cli/azure/storage/account/keys) da seguinte forma:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Para separar logicamente os VHDs que carrega, crie um recipiente dentro da conta de armazenamento com o recipiente de [armazenamento az criar:](/cli/azure/storage/container)

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Finalmente, faça o upload do seu VHD com o upload de blob de [armazenamento az](/cli/azure/storage/blob) da seguinte forma:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Crie VM a partir do seu VHD
Pode criar um VM com um [script de amostra](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) ou diretamente com a criação de [az vm](/cli/azure/vm). Para especificar o VHD OpenBSD que carregou, utilize o parâmetro de `--image` da seguinte forma:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Obtenha o endereço IP para o seu VM OpenBSD com [endereços ip-lista az vm](/cli/azure/vm) da seguinte forma:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Agora pode sSH para o seu VM OpenBSD normalmente:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Passos seguintes
Se quiser saber mais sobre suporte hiper-V no OpenBSD6.1, leia [OpenBSD 6.1](https://www.openbsd.org/61.html) e [hyperv.4](https://man.openbsd.org/hyperv.4).

Se quiser criar um VM a partir de disco gerido, leia [o disco az](/cli/azure/disk). 

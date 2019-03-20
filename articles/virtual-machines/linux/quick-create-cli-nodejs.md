---
title: Criar uma VM do Linux com a CLI clássica do Azure | Documentos da Microsoft
description: Criar uma VM do Linux no Azure com a CLI clássica do Azure
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: facb1115-2b4e-4ef3-9905-330e42beb686
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2016
ms.author: v-livech
ms.openlocfilehash: 569e90c7908ce435689a80f7917b20275703f537
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990410"
---
# <a name="create-a-linux-vm-using-the-azure-classic-cli"></a>Criar uma VM do Linux com a CLI clássica do Azure

Este artigo mostra como implementar rapidamente uma máquina virtual (VM) com Linux no Azure através do comando `azure vm quick-create` na interface de linha de comandos (CLI) do Azure. O comando `quick-create` implementa uma VM numa infraestrutura básica segura que pode utilizar para criar protótipos ou testar rapidamente um conceito.

> [!NOTE]
> Para criar uma VM com a CLI do Azure, veja [criar uma VM com a CLI do Azure](../windows/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Também pode implementar rapidamente uma VM com Linux através do [portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O artigo requer uma [ficheiros de chaves públicos e privados SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="quick-commands"></a>Comandos rápidos

O exemplo seguinte mostra como implementar uma VM CoreOS e anexar a chave Secure Shell (SSH) (os seus argumentos podem ser diferentes):

```azurecli
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas

As instruções seguintes têm uma VM UbuntuLTS a ser implementada, passo a passo, com explicações do que cada passo está a fazer.

## <a name="vm-quick-create-aliases"></a>Criação rápida de aliases para VM

Uma forma rápida de escolher uma distribuição consiste em utilizar os aliases da CLI do Azure mapeados para as distribuições de SO mais comuns. A tabela seguinte lista os aliases (a partir da versão 0.10 da CLI do Azure). Todas as implementações que utilizem `quick-create` são predefinidas para VMs apoiadas pelo armazenamento de unidade de estado sólido (SSD), que oferece aprovisionamento mais rápido e acesso ao disco de elevado desempenho. (Estes aliases representam uma parte muito pequena das distribuições disponíveis no Azure. Encontre mais imagens no Azure Marketplace ao [procurar uma imagem no PowerShell](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [na Web](https://azure.microsoft.com/marketplace/virtual-machines/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) ou ao [carregar a sua própria imagem personalizada](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

| Alias | Publicador | Oferta | SKU | Versão |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7.2 |mais recente |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |
| Debian |credativ |Debian |8 |mais recente |
| openSUSE |SUSE |openSUSE |13.2 |mais recente |
| RHEL |Red Hat |RHEL |7.2 |mais recente |
| UbuntuLTS |Canónico |Ubuntu Server |14.04.4-LTS |mais recente |

As secções seguintes utilizam o alias `UbuntuLTS` para a opção **ImageURN** (`-Q`) para implementar um Ubuntu 14.04.4 LTS Server.

O exemplo `quick-create` anterior apenas chamou o sinalizador `-M` para identificar a chave pública SSH para carregar ao mesmo tempo que desativou palavras-passe SSH, pelo que lhe são pedidos os argumentos seguintes:

* o nome do grupo de recursos (normalmente, qualquer cadeia serve para o seu primeiro grupo de recursos do Azure)
* o nome da VM
* localização (`westus` ou `westeurope` são boas predefinições)
* o linux (para que o Azure saiba que SO pretende)
* o nome de utilizador

O exemplo seguinte especifica todos os valores, para que não sejam necessários mais pedidos. Assim, desde que tenha um `~/.ssh/id_rsa.pub` como um ficheiro de chave pública com o formato ssh-rsa, funciona tal como está:

```azurecli
azure vm quick-create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --admin-username myAdminUser \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --image-urn UbuntuLTS
```

O resultado deverá ser semelhante ao do bloco de saída seguinte:

```azurecli
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Iniciar sessão na nova VM
Inicie sessão na sua VM com o endereço IP público apresentado no resultado. Também pode utilizar o nome de domínio completamente qualificado (FQDN) apresentado:

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

O processo de início de sessão deve ter um aspeto semelhante ao do bloco de saída:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Passos Seguintes
O comando `azure vm quick-create` é a forma mais rápida de implementar uma VM para que possa iniciar sessão numa shell bash e começar a trabalhar. No entanto, utilizar o comando `vm quick-create` não lhe dá um controlo extenso nem lhe permite criar um ambiente mais complexo.  Para implementar uma VM com Linux personalizada para a sua infraestrutura, pode seguir qualquer um dos artigos seguintes:

* [Criar um ambiente personalizado para uma VM com Linux diretamente através dos comandos da CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM com Linux Protegida por SSH no Azure através de modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Também pode [utilizar o controlador `docker-machine` do Azure com vários comandos para criar rapidamente uma VM do Linux como anfitrião docker](docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

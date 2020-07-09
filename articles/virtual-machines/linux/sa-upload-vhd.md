---
title: Faça upload de um disco Linux personalizado com Azure CLI
description: Criar e carregar um disco rígido virtual (VHD) para Azure utilizando o modelo de implementação do Gestor de Recursos e o CLI Azure
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.custom: storage accounts
ms.openlocfilehash: 7ec9b670f8b2eb1731511deb1d01cfc7db55054f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758568"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Faça upload e crie um Linux VM a partir de disco personalizado com o Azure CLI

Este artigo mostra-lhe como carregar um disco rígido virtual (VHD) para uma conta de armazenamento Azure com o CLI Azure e criar VMs Linux a partir deste disco personalizado. Esta funcionalidade permite-lhe instalar e configurar um Linux distro para os seus requisitos e, em seguida, utilizar esse VHD para criar rapidamente máquinas virtuais Azure (VMs).

Este tópico utiliza contas de armazenamento para os VHDs finais, mas também pode fazer estes passos usando [discos geridos.](upload-vhd.md) 

## <a name="quick-commands"></a>Comandos rápidos
Se precisar de realizar rapidamente a tarefa, a seguinte secção detalha os comandos base para enviar um VHD para Azure. Informações e contextos mais detalhados para cada passo podem ser encontrados o resto do documento, [a partir daqui.](#requirements)

Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Nomes de parâmetros de exemplo incluídos `myResourceGroup` `mystorageaccount` , e `mydisks` .

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Crie uma conta de armazenamento para manter os seus discos virtuais com [a conta de armazenamento AZ criar](/cli/azure/storage/account). O exemplo a seguir cria uma conta de armazenamento `mystorageaccount` denominada:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Enuma as chaves de acesso da sua conta de armazenamento com [a lista de chaves de conta de armazenamento AZ](/cli/azure/storage/account/keys). Tome nota `key1` de:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Crie um recipiente dentro da sua conta de armazenamento utilizando a chave de armazenamento que obteve com [o recipiente de armazenamento AZ criar](/cli/azure/storage/container). O exemplo a seguir cria um recipiente denominado `mydisks` utilizando o valor da chave de armazenamento a partir `key1` de:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Por fim, faça o upload do seu VHD para o recipiente que criou com o upload da bolha de [armazenamento Az](/cli/azure/storage/blob). Especifique o caminho local para o seu VHD em `/path/to/disk/mydisk.vhd` :

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Especifique o URI para o seu disco `--image` () com [a criação de az vm](/cli/azure/vm). O exemplo a seguir cria um VM nomeado `myVM` utilizando o disco virtual previamente carregado:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

A conta de armazenamento de destino tem de ser a mesma de onde fez o upload do seu disco virtual. Também precisa de especificar, ou responder a solicitações para, todos os parâmetros adicionais exigidos pelo **az vm criar** comando como rede virtual, endereço IP público, nome de utilizador e teclas SSH. Pode ler mais sobre os [parâmetros clássicos do Gestor de Recursos CLI disponíveis.](../azure-cli-arm-commands.md#virtual-machines)

## <a name="requirements"></a>Requirements
Para completar os seguintes passos, é necessário:

* **Sistema operativo Linux instalado num ficheiro .vhd** - Instale uma distribuição Linux com base no [Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou consulte [informações para distribuições não endossadas)](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)num disco virtual no formato VHD. Existem várias ferramentas para criar um VM e VHD:
  * Instale e configuure [o QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [o KVM,](https://www.linux-kvm.org/page/RunningKVM)tendo o cuidado de utilizar o VHD como formato de imagem. Se necessário, pode [converter uma imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) utilizando `qemu-img convert` .
  * Também pode utilizar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> O novo formato VHDX não é suportado no Azure. Quando criar um VM, especifique o VHD como o formato. Se necessário, pode converter discos VHDX em VHD utilizando [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ou o [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet PowerShell. Além disso, o Azure não suporta o upload de VHDs dinâmicos, por isso é necessário converter esses discos em VHDs estáticos antes de carregar. Pode utilizar ferramentas como [Azure VHD Utilities para GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para converter discos dinâmicos durante o processo de upload para Azure.
> 
> 

* Os VMs criados a partir do seu disco personalizado devem residir na mesma conta de armazenamento que o próprio disco
  * Crie uma conta de armazenamento e um recipiente para segurar tanto o seu disco personalizado como os VMs criados
  * Depois de ter criado todos os seus VMs, pode eliminar com segurança o seu disco

Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Nomes de parâmetros de exemplo incluídos `myResourceGroup` `mystorageaccount` , e `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Prepare o disco para ser carregado
A Azure suporta várias distribuições Linux (ver [Distribuição Endossada).](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Os seguintes artigos guiam-no através da preparação das várias distribuições Linux que são suportadas no Azure:

* **[Distribuições baseadas em CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES e openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Outras - Distribuições Não Endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Consulte também as **[Notas de Instalação Linux](create-upload-generic.md#general-linux-installation-notes)** para obter dicas mais gerais sobre a preparação das imagens Linux para Azure.

> [!NOTE]
> A [plataforma Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) aplica-se aos VMs que executam o Linux apenas quando uma das distribuições endossadas é utilizada com os detalhes de configuração especificados em "Versões Suportadas" em [Linux em Distribuições Apoiadas pelo Azure.](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Os grupos de recursos, logicamente, reúnem todos os recursos Azure para suportar as suas máquinas virtuais, como a rede virtual e o armazenamento. Para obter mais grupos de recursos de informação, consulte [a visão geral dos grupos de recursos.](../../azure-resource-manager/management/overview.md) Antes de carregar o seu disco personalizado e criar VMs, primeiro precisa de criar um grupo de recursos com [criação de grupo az](/cli/azure/group).

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento para o seu disco personalizado e VMs com [conta de armazenamento az criar](/cli/azure/storage/account). Quaisquer VMs com discos não geridos que crie a partir do seu disco personalizado precisam de estar na mesma conta de armazenamento que aquele disco. 

O exemplo a seguir cria uma conta de armazenamento nomeada `mystorageaccount` no grupo de recursos anteriormente criado:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Chaves da conta de armazenamento de lista
O Azure gera duas chaves de acesso de 512 bits para cada conta de armazenamento. Estas chaves de acesso são utilizadas quando autenticam a conta de armazenamento, tais como a realização de operações de escrita. Para obter mais informações sobre as chaves de acesso à conta de armazenamento, consulte [as teclas de acesso à conta de armazenamento](../../storage/common/storage-account-keys-manage.md). Vê as chaves de acesso com [a lista de chaves de conta de armazenamento Az](/cli/azure/storage/account/keys).

Veja as teclas de acesso para a conta de armazenamento que criou:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

O resultado é semelhante a:

```output
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

Tome nota da `key1` forma como irá usá-lo para interagir com a sua conta de armazenamento nos próximos passos.

## <a name="create-a-storage-container"></a>Criar um recipiente de armazenamento
Da mesma forma que cria diferentes diretórios para organizar logicamente o seu sistema de ficheiros local, cria contentores dentro de uma conta de armazenamento para organizar os seus discos. Uma conta de armazenamento pode conter qualquer número de contentores. Criar um recipiente com [recipiente de armazenamento az criar](/cli/azure/storage/container).

O exemplo a seguir cria um recipiente `mydisks` denominado:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Upload VHD
Agora faça o upload do seu disco personalizado com [o upload da bolha de armazenamento Az](/cli/azure/storage/blob). Você faz o upload e armazena o seu disco personalizado como uma bolha de página.

Especifique a sua chave de acesso, o recipiente que criou no passo anterior e, em seguida, o caminho para o disco personalizado no seu computador local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Crie a VM
Para criar um VM com discos não geridos, especifique o URI para o seu disco `--image` () com [az vm criar](/cli/azure/vm). O exemplo a seguir cria um VM nomeado `myVM` utilizando o disco virtual previamente carregado:

Especifica o `--image` parâmetro com [az vm criar](/cli/azure/vm) para apontar para o seu disco personalizado. Certifique-se de que `--storage-account` corresponde à conta de armazenamento onde o seu disco personalizado está armazenado. Não é preciso utilizar o mesmo recipiente que o disco personalizado para armazenar os seus VMs. Certifique-se de criar quaisquer recipientes adicionais da mesma forma que os passos anteriores antes de carregar o seu disco personalizado.

O exemplo a seguir cria um VM nomeado `myVM` a partir do seu disco personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Ainda precisa de especificar, ou responder a solicitações, todos os parâmetros adicionais exigidos pelo **az vm criar** comando, como o nome de utilizador e as teclas SSH.


## <a name="resource-manager-template"></a>Modelo do Resource Manager
Os modelos do Gestor de Recursos Azure são ficheiros JavaScript Object Notation (JSON) que definem o ambiente que pretende construir. Os modelos são divididos em diferentes fornecedores de recursos, tais como computação ou rede. Pode usar os modelos existentes ou escrever os seus. Leia mais sobre [a utilização de Gestor de Recursos e modelos.](../../azure-resource-manager/management/overview.md)

Dentro do `Microsoft.Compute/virtualMachines` fornecedor do seu modelo, tem um nó `storageProfile` que contém os detalhes de configuração para o seu VM. Os dois principais parâmetros a editar são os `image` URIs que `vhd` apontam para o seu disco personalizado e para o disco virtual do seu novo VM. O seguinte mostra um exemplo do JSON para a utilização de um disco personalizado:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Pode utilizar [este modelo existente para criar um VM a partir de uma imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou ler sobre a [criação dos seus próprios modelos de Gestor de Recursos Azure.](../../azure-resource-manager/templates/template-syntax.md) 

Uma vez configurado um modelo, utilize [a implementação do grupo AZ](/cli/azure/group/deployment) para criar os seus VMs. Especifique o URI do seu modelo JSON com o `--template-uri` parâmetro:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Se tiver um ficheiro JSON armazenado localmente no seu computador, pode utilizar o `--template-file` parâmetro em vez disso:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Próximos passos
Depois de ter preparado e carregado o seu disco virtual personalizado, pode ler mais sobre [a utilização de Gestor de Recursos e modelos.](../../azure-resource-manager/management/overview.md) Também pode querer [adicionar um disco de dados](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) aos seus novos VMs. Se tiver aplicações em execução nos seus VMs a que necessita de aceder, certifique-se de [abrir portas e pontos finais](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


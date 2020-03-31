---
title: Faça upload de um disco Linux personalizado com O ClI Azure
description: Crie e carregue um disco rígido virtual (VHD) para o Azure utilizando o modelo de implementação do Gestor de Recursos e o Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: bc90a409dd2695ce16f8c7d5909f8e2d7867673c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060246"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Faça upload e crie um VM Linux a partir de disco personalizado com o Azure CLI

Este artigo mostra-lhe como enviar um disco rígido virtual (VHD) para uma conta de armazenamento Azure com o Azure CLI e criar VMs Linux a partir deste disco personalizado. Esta funcionalidade permite-lhe instalar e configurar um distro Linux às suas necessidades e, em seguida, utilizar esse VHD para criar rapidamente máquinas virtuais Azure (VMs).

Este tópico utiliza contas de armazenamento para os VHDs finais, mas também pode fazer estes passos usando [discos geridos](upload-vhd.md). 

## <a name="quick-commands"></a>Comandos rápidos
Se precisar de realizar rapidamente a tarefa, a secção seguinte detalha os comandos base para fazer o upload de um VHD para O Azure. Informações mais detalhadas e contexto para cada passo podem ser encontrados o resto do documento, [a partir](#requirements)daqui.

Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado sessão numa conta Azure utilizando [login az](/cli/azure/reference-index).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Exemplo nomes de `myResourceGroup` `mystorageaccount`parâmetros `mydisks`incluídos, e .

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Crie uma conta de armazenamento para manter os seus discos virtuais com a criação de uma conta de [armazenamento az](/cli/azure/storage/account). O exemplo seguinte cria `mystorageaccount`uma conta de armazenamento chamada:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Enumera as chaves de acesso para a sua conta de armazenamento com a lista de chaves da [conta de armazenamento Az](/cli/azure/storage/account/keys). Tome nota: `key1`

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Crie um recipiente dentro da sua conta de armazenamento utilizando a chave de armazenamento que obteve com o [armazenamento az criar](/cli/azure/storage/container). O exemplo seguinte cria `mydisks` um recipiente chamado `key1`utilizando o valor da chave de armazenamento a partir de:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Finalmente, faça o upload do seu VHD para o recipiente que criou com o upload de [blob de armazenamento az](/cli/azure/storage/blob). Especifique o caminho local `/path/to/disk/mydisk.vhd`para o seu VHD em :

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Especifique o`--image`URI no seu disco ( ) com [az vm criar](/cli/azure/vm). O exemplo seguinte cria `myVM` um VM nomeado utilizando o disco virtual previamente carregado:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

A conta de armazenamento de destino tem de ser a mesma para onde fez o upload do disco virtual. Também precisa especificar, ou responder a solicitações, todos os parâmetros adicionais exigidos pelo **az vm criar** comandos tais como rede virtual, endereço IP público, nome de utilizador e chaves SSH. Pode ler mais sobre os [parâmetros clássicos disponíveis do ClI Resource Manager](../azure-cli-arm-commands.md#virtual-machines).

## <a name="requirements"></a>Requisitos
Para completar os seguintes passos, precisa de:

* **Sistema operativo Linux instalado num ficheiro .vhd** - Instale uma [distribuição Linux apoiada pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (ou consulte [informações para distribuições não endossadas)](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)num disco virtual no formato VHD. Existem múltiplas ferramentas para criar um VM e VHD:
  * Instale e configure [qEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) ou [KVM,](https://www.linux-kvm.org/page/RunningKVM)tendo o cuidado de usar o VHD como formato de imagem. Se necessário, pode converter `qemu-img convert`uma [imagem](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando .
  * Também pode utilizar o Hyper-V [no Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) ou [no Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> O novo formato VHDX não é suportado no Azure. Quando criar um VM, especifique o VHD como formato. Se necessário, pode converter discos VHDX em [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) VHD utilizando ou o [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet PowerShell. Além disso, o Azure não suporta o upload de VHDs dinâmicos, pelo que tem de converter esses discos em VHDs estáticos antes de carregar. Pode utilizar ferramentas como [os Utilitários Azure VHD para a GO](https://github.com/Microsoft/azure-vhd-utils-for-go) converter discos dinâmicos durante o processo de upload para o Azure.
> 
> 

* VMs criados a partir do seu disco personalizado devem residir na mesma conta de armazenamento que o próprio disco
  * Crie uma conta de armazenamento e um recipiente para segurar tanto o seu disco personalizado como os VMs criados
  * Depois de ter criado todos os seus VMs, pode eliminar com segurança o seu disco

Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado sessão numa conta Azure utilizando [login az](/cli/azure/reference-index).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Exemplo nomes de `myResourceGroup` `mystorageaccount`parâmetros `mydisks`incluídos, e .

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Prepare o disco para ser carregado
O Azure suporta várias distribuições linux (ver [Distribuições Endossadas).](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Os seguintes artigos guiam-no através de como preparar as várias distribuições linux que são suportadas no Azure:

* **[Distribuição baseada em CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & abre SUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Outras - Distribuições Não Endossadas](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Consulte também as Notas de **[Instalação Linux](create-upload-generic.md#general-linux-installation-notes)** para obter dicas mais gerais sobre a preparação das imagens linux para o Azure.

> [!NOTE]
> A [plataforma Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) aplica-se aos VMs que executam o Linux apenas quando uma das distribuições endossadas for utilizada com os detalhes de configuração especificados em "Versões Suportadas" em [Linux em Distribuições Apoiadas pelo Azure.](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Os grupos de recursos reúnem logicamente todos os recursos do Azure para apoiar as suas máquinas virtuais, como o networking virtual e o armazenamento. Para mais grupos de recursos de informação, consulte a [visão geral dos grupos](../../azure-resource-manager/management/overview.md)de recursos . Antes de carregar o disco personalizado e criar VMs, primeiro precisa de criar um grupo de recursos com grupo [az criar](/cli/azure/group).

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Criar uma conta do Storage

Crie uma conta de armazenamento para o seu disco personalizado e VMs com a conta de [armazenamento az criar](/cli/azure/storage/account). Quaisquer VMs com discos não geridos que crie a partir do seu disco personalizado precisam estar na mesma conta de armazenamento que esse disco. 

O exemplo seguinte cria `mystorageaccount` uma conta de armazenamento nomeada no grupo de recursos anteriormente criado:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Lista de chaves da conta de armazenamento
O Azure gera duas chaves de acesso de 512 bits para cada conta de armazenamento. Estas chaves de acesso são utilizadas quando autenticam a conta de armazenamento, de modo a realizar operações de escrita. Para obter mais informações sobre as chaves de acesso à conta de [armazenamento,](../../storage/common/storage-account-keys-manage.md)consulte Gerir as chaves de acesso à conta de armazenamento . Vê as chaves de acesso com a lista de chaves da [conta de armazenamento az](/cli/azure/storage/account/keys).

Veja as chaves de acesso da conta de armazenamento que criou:

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

Tome nota `key1` de como irá usá-lo para interagir com a sua conta de armazenamento nos próximos passos.

## <a name="create-a-storage-container"></a>Criar um recipiente de armazenamento
Da mesma forma que cria diferentes diretórios para organizar logicamente o seu sistema de ficheiros local, cria contentores dentro de uma conta de armazenamento para organizar os seus discos. Uma conta de armazenamento pode conter qualquer número de contentores. Criar um recipiente com recipiente de [armazenamento az criar](/cli/azure/storage/container).

O exemplo seguinte cria `mydisks`um recipiente chamado:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Upload VHD
Agora carregue o seu disco personalizado com [upload de blob de armazenamento az](/cli/azure/storage/blob). Faça o upload e guarde o disco personalizado como uma bolha de página.

Especifique a sua chave de acesso, o recipiente que criou no passo anterior e, em seguida, o caminho para o disco personalizado no seu computador local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Crie a VM
Para criar um VM com discos não geridos,`--image`especifique o URI no seu disco ( ) com [az vm criar](/cli/azure/vm). O exemplo seguinte cria `myVM` um VM nomeado utilizando o disco virtual previamente carregado:

Especifica o `--image` parâmetro com [az vm criar](/cli/azure/vm) para apontar para o seu disco personalizado. Certifique-se de que `--storage-account` corresponde à conta de armazenamento onde o disco personalizado está armazenado. Não é preciso utilizar o mesmo recipiente que o disco personalizado para armazenar os seus VMs. Certifique-se de criar quaisquer recipientes adicionais da mesma forma que os passos anteriores antes de carregar o disco personalizado.

O exemplo seguinte cria `myVM` um VM nomeado a partir do seu disco personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Ainda é necessário especificar, ou responder a solicitações, todos os parâmetros adicionais exigidos pelo **az vm criar** comandos tais como username e teclas SSH.


## <a name="resource-manager-template"></a>Modelo do Resource Manager
Os modelos do Gestor de Recursos Azure são ficheiros JavaScript Object Notation (JSON) que definem o ambiente que pretende construir. Os modelos são divididos em diferentes fornecedores de recursos, tais como computação ou rede. Pode usar os modelos existentes ou escrever os seus. Leia mais sobre [a utilização do Gestor de Recursos e modelos](../../azure-resource-manager/management/overview.md).

Dentro `Microsoft.Compute/virtualMachines` do fornecedor do seu `storageProfile` modelo, tem um nó que contém os detalhes de configuração para o seu VM. Os dois principais parâmetros `image` `vhd` para editar são os URIs que apontam para o seu disco personalizado e o disco virtual do seu novo VM. O seguinte mostra um exemplo do JSON para a utilização de um disco personalizado:

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

Você pode usar [este modelo existente para criar um VM a partir de uma imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) ou ler sobre a criação dos seus [próprios modelos de Gestor](../../azure-resource-manager/templates/template-syntax.md)de Recursos Azure . 

Uma vez configurado um modelo, utilize a implementação do [grupo Az](/cli/azure/group/deployment) para criar os seus VMs. Especifique o URI do `--template-uri` seu modelo JSON com o parâmetro:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Se tiver um ficheiro JSON armazenado localmente no `--template-file` seu computador, pode utilizar o parâmetro em vez disso:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Passos seguintes
Depois de ter preparado e carregado o seu disco virtual personalizado, pode ler mais sobre [a utilização do Gestor de Recursos e modelos](../../azure-resource-manager/management/overview.md). Também pode querer [adicionar um disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) de dados aos seus novos VMs. Se tiver aplicações em execução nos seus VMs a que precisa de aceder, certifique-se de [abrir portas e pontos finais.](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


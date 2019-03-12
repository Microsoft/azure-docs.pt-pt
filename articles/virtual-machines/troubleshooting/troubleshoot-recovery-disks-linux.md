---
title: Utilizar uma Linux resolver problemas de VMS com a CLI do Azure | Documentos da Microsoft
description: Saiba como resolver problemas de VM do Linux ao ligar o disco do SO a uma VM de recuperação com a CLI do Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: dfb85b0f9f1dda611c613cb296177cf28391adc0
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776891"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Resolver problemas de uma VM do Linux ao anexar o disco do SO a uma VM com a CLI do Azure de recuperação
Se a sua máquina virtual (VM) do Linux encontra um erro de arranque ou disco, se pretender executar passos de resolução de problemas no disco rígido virtual em si. Um exemplo comum seria uma entrada inválida no `/etc/fstab` que impede que a VM que está a ser capazes de arrancar com êxito. Este artigo fornece detalhes sobre como utilizar a CLI do Azure para ligar o seu disco rígido virtual a outra VM do Linux para corrigir quaisquer erros, em seguida, voltar a criar a VM original. 


## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Elimine a VM se deparar com problemas, mantendo os discos rígidos virtuais.
2. Anexe e Monte o disco rígido virtual a outra VM do Linux para fins de resolução de problemas.
3. Ligue à VM da resolução de problemas. Editar ficheiros ou execute quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
5. Crie uma VM com o disco rígido virtual original.

Para a VM que utiliza o disco gerido, consulte [resolver problemas de uma VM de disco gerido ao anexar um disco de SO novo](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

Para executar estes passos de resolução de problemas, é necessário que a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index).

Nos exemplos a seguir, substitua os nomes de parâmetros com seus próprios valores. Os nomes de parâmetros de exemplo incluem `myResourceGroup`, `mystorageaccount`, e `myVM`.


## <a name="determine-boot-issues"></a>Determinar os problemas de arranque
Examine o resultado serial para determinar por que a VM não é capaz de inicializar corretamente. Um exemplo comum é uma entrada inválida no `/etc/fstab`, ou subjacente disco rígido virtual que está a ser eliminado ou movido.

Obter os registos de arranque com [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics). O exemplo seguinte obtém o resultado de série de VM com o nome `myVM` no grupo de recursos com o nome `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Reveja o resultado serial para determinar por que a VM está a falhar efetuar o arranque. Se a saída de série não fornece qualquer indicação, poderá ter de rever os ficheiros de registo no `/var/log` assim que tiver o disco rígido virtual ligado a uma VM de resolução de problemas.


## <a name="view-existing-virtual-hard-disk-details"></a>Ver detalhes do disco rígido virtual existente
Pode anexar o disco rígido virtual (VHD) a outra VM, terá de identificar o URI do disco do SO. 

Ver informações sobre a VM com [show de vm de az](/cli/azure/vm). Utilize o `--query` sinalizador para extrair o URI para o disco do SO. O exemplo seguinte obtém informações do disco de VM com o nome `myVM` no grupo de recursos com o nome `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

O URI é semelhante à **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd**.

## <a name="delete-existing-vm"></a>Eliminar VM existente
Os discos rígidos virtuais e as VMs são dois recursos diferentes do Azure. Um disco rígido virtual é onde o próprio sistema operacional, aplicativos e configurações são armazenadas. A VM propriamente dito é apenas de metadados que definem o tamanho ou a localização e faz referência a recursos como um disco rígido virtual ou uma placa de interface de rede virtual (NIC). Cada disco rígido virtual tem uma concessão atribuída quando anexado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo se a VM estiver a ser executada, o disco do SO só pode ser desanexado se o recurso da VM for eliminado. A concessão continua a associar o disco do SO a uma VM, mesmo quando a VM está num estado parado e desalocado.

O primeiro passo para recuperar a sua VM é eliminar o próprio recurso da VM. Com a eliminação, os discos rígidos virtuais permanecem na sua conta de armazenamento. Depois da VM é eliminada, é anexar o disco rígido virtual a outra VM para resolver os erros.

Elimine a VM com [az vm eliminar](/cli/azure/vm). O exemplo seguinte elimina a VM com o nome `myVM` do grupo de recursos com o nome `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Aguarde até que a VM tiver concluído a eliminação do antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que associa-a com a VM precisa ser liberada antes de pode anexar o disco rígido virtual a outra VM.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar o disco rígido virtual existente a outra VM
Para os passos seguintes, utilize outra VM para fins de resolução de problemas. Anexar o disco rígido virtual existente para esta VM de resolução de problemas para procurar e editar o conteúdo do disco. Este processo permite-lhe corrigir os eventuais erros de configuração ou analisar o aplicativo adicional ou ficheiros de registo do sistema, por exemplo. Escolha ou crie outra VM para utilizar para fins de resolução de problemas.

Anexar o disco rígido virtual existente com [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk). Ao anexar o disco rígido virtual existente, especificar o URI para o disco de obteve no anterior `az vm show` comando. O exemplo seguinte anexa um disco rígido virtual existente para a VM de resolução de problemas com o nome `myVMRecovery` no grupo de recursos com o nome `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>Monte o disco de dados anexados

> [!NOTE]
> Os exemplos seguintes detalham os passos necessários numa VM do Ubuntu. Se estiver a utilizar uma distribuição Linux diferente, como Red Hat Enterprise Linux ou SUSE, o registo de localizações de ficheiro e `mount` comandos podem ser um pouco diferentes. Consulte a documentação para a sua distribuição específica para as alterações apropriadas nos comandos.

1. SSH para a VM de resolução de problemas usando as credenciais apropriadas. Se este disco é o primeiro disco de dados ligado à sua VM de resolução de problemas, o disco provavelmente está ligado a `/dev/sdc`. Utilize `dmseg` para ver os discos anexados:

    ```bash
    dmesg | grep SCSI
    ```

    O resultado é semelhante ao seguinte exemplo:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    No exemplo anterior, o disco do SO está no `/dev/sda` e o disco temporário fornecido para cada VM é em `/dev/sdb`. Se tivesse vários discos de dados, devem estar no `/dev/sdd`, `/dev/sde`e assim por diante.

2. Crie um diretório para montar o disco rígido virtual existente. O exemplo seguinte cria um diretório chamado `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Se tiver várias partições no disco rígido virtual existente, monte a partição necessária. O exemplo seguinte monta a primeira partição primária em `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > É melhor prática montar discos de dados em VMs no Azure utilizando o identificador exclusivo universalmente (UUID) do disco rígido virtual. Para este cenário de resolução de problemas curto, montar o disco rígido virtual com o UUID não é necessário. No entanto, em utilização normal, edição `/etc/fstab` para montar discos rígidos virtuais com o nome de dispositivo, em vez de UUID pode fazer com que a VM falhe efetuar o arranque.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir erros no disco rígido virtual original
Com o disco rígido virtual existente montado, pode agora efetuar qualquer manutenção e passos de resolução de problemas, conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmonte e desanexe o disco rígido virtual original
Assim que os erros são resolvidos, desmonte e desanexe o disco rígido virtual existente da sua VM de resolução de problemas. Não é possível utilizar o seu disco rígido virtual com qualquer outra VM até que a anexar o disco rígido virtual para a VM de resolução de problemas de concessão será libertada.

1. A partir da sessão SSH à sua VM de resolução de problemas, desmonte o disco rígido virtual existente. Altere o primeiro fora do diretório principal para o ponto de montagem:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O exemplo seguinte desmonta o dispositivo em `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desanexe o disco rígido virtual da VM. Saia da sessão SSH para a VM de resolução de problemas. Listar os discos de dados anexados à sua VM de resolução de problemas com [lista do az vm unmanaged-disk](/cli/azure/vm/unmanaged-disk). O exemplo seguinte lista os discos de dados ligados à VM com o nome `myVMRecovery` no grupo de recursos com o nome `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Tenha em atenção o nome para o seu disco rígido virtual existente. Por exemplo, o nome de um disco com o URI de **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** é **myVHD**. 

    Desanexe o disco de dados da sua VM [az vm unmanaged-disk detach](/cli/azure/vm/unmanaged-disk). O exemplo seguinte desanexa o disco com o nome `myVHD` da VM denominada `myVMRecovery` no `myResourceGroup` grupo de recursos:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Criar VM a partir de disco rígido original
Para criar uma VM a partir do seu disco rígido virtual original, utilize [este modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). O modelo JSON tenha a seguinte hiperligação:

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

O modelo implementa uma VM com o URI do VHD do comando anterior. Implementar o modelo com [criar a implementação do grupo az](/cli/azure/group/deployment). Forneça o URI para o VHD original e, em seguida, especifique o tipo de SO, o tamanho da VM e o nome VM da seguinte forma:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Reativar o diagnóstico de arranque
Quando criar a VM a partir do disco rígido virtual existente, o diagnóstico de arranque pode não ser ativado automaticamente. Ativar diagnósticos de arranque com o [az vm boot-diagnostics ativar](/cli/azure/vm/boot-diagnostics). O exemplo seguinte ativa a extensão de diagnóstico na VM com o nome `myDeployedVM` no grupo de recursos com o nome `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Resolver problemas de uma VM de disco gerido ao anexar um novo disco de SO
1. Pare a VM resultante.
2. [Criar um instantâneo do disco gerido](../linux/snapshot-copy-managed-disk.md) do disco do SO da VM de disco gerido.
3. [Criar um disco gerido a partir do instantâneo](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Anexar o disco gerido como disco de dados da VM](../windows/attach-disk-ps.md).
5. [Alterar o disco de dados do passo 4 para o disco de SO](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Passos Seguintes
Se estiver a ter problemas de ligação à sua VM, veja [resolver problemas de SSH ligações a uma VM do Azure](troubleshoot-ssh-connection.md). Para problemas com o acesso aos aplicativos em execução na sua VM, consulte [resolver problemas de conectividade de aplicações numa VM do Linux](troubleshoot-app-connection.md).


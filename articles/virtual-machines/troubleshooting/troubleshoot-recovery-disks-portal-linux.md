---
title: Utilizar uma Linux VM de resolução de problemas no portal do Azure | Documentos da Microsoft
description: Saiba como resolver problemas de máquina virtual do Linux ao ligar o disco do SO a uma VM com o portal do Azure de recuperação
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: genli
ms.openlocfilehash: 65187c3ef6debfa27c8c4fea62bcd31b857b4171
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776769"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Resolver problemas de uma VM do Linux ao anexar o disco do SO a uma VM de recuperação com o portal do Azure
Se a sua máquina virtual (VM) do Linux encontra um erro de arranque ou disco, se pretender executar passos de resolução de problemas no disco rígido virtual em si. Um exemplo comum seria uma entrada inválida no `/etc/fstab` que impede que a VM que está a ser capazes de arrancar com êxito. Este artigo fornece detalhes sobre como utilizar o portal do Azure para ligar o seu disco rígido virtual a outra VM do Linux para corrigir quaisquer erros, em seguida, voltar a criar a VM original.

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Elimine a VM se deparar com problemas, mantendo os discos rígidos virtuais.
2. Anexe e Monte o disco rígido virtual a outra VM do Linux para fins de resolução de problemas.
3. Ligue à VM da resolução de problemas. Editar ficheiros ou execute quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
5. Crie uma VM com o disco rígido virtual original.

Para a VM que utiliza o disco gerido, consulte [resolver problemas de uma VM de disco gerido ao anexar um disco de SO novo](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

## <a name="determine-boot-issues"></a>Determinar os problemas de arranque
Examine o diagnóstico de arranque e a captura de ecrã VM para determinar por que a VM não é capaz de inicializar corretamente. Um exemplo comum seria uma entrada inválida no `/etc/fstab`, ou um subjacente disco de rígido virtual que está a ser eliminado ou movido.

Selecione a sua VM no portal e, em seguida, desloque para baixo para o **suporte + resolução de problemas** secção. Clique em **diagnósticos de arranque** para ver as mensagens de consola transmitidas a partir da sua VM. Reveja os registos de consola para ver se é possível determinar por que a VM está a experienciar um problema. O exemplo seguinte mostra que uma VM bloqueados no modo de manutenção que requer interação manual:

![VM a ver o diagnóstico de arranque os registos da consola](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Também pode clicar **captura de ecrã** na parte superior do registo de diagnósticos de arranque para transferir uma captura de captura de ecrã da VM.


## <a name="view-existing-virtual-hard-disk-details"></a>Ver detalhes do disco rígido virtual existente
Pode anexar o disco rígido virtual a outra VM, terá de identificar o nome do disco rígido virtual (VHD). 

Selecione o grupo de recursos a partir do portal, em seguida, selecione a sua conta de armazenamento. Clique em **Blobs**, como no exemplo a seguir:

![Selecione os blobs de armazenamento](./media/troubleshoot-recovery-disks-portal-linux/storage-account-overview.png)

Normalmente, tem um contentor denominado **vhds** que armazena os discos rígidos virtuais. Selecione o contentor para ver uma lista de discos rígidos virtuais. Tenha em atenção o nome do seu VHD (o prefixo é normalmente o nome da sua VM):

![Identificar o VHD no contentor de armazenamento](./media/troubleshoot-recovery-disks-portal-linux/storage-container.png)

Selecione o seu disco rígido virtual existente na lista e copie o URL para utilização nos passos seguintes:

![Copie o URL de disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-linux/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Eliminar VM existente
Os discos rígidos virtuais e as VMs são dois recursos diferentes do Azure. Um disco rígido virtual é onde o próprio sistema operacional, aplicativos e configurações são armazenadas. A VM propriamente dito é apenas de metadados que definem o tamanho ou a localização e faz referência a recursos como um disco rígido virtual ou uma placa de interface de rede virtual (NIC). Cada disco rígido virtual tem uma concessão atribuída quando anexado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo se a VM estiver a ser executada, o disco do SO só pode ser desanexado se o recurso da VM for eliminado. A concessão continua a associar o disco do SO a uma VM, mesmo quando a VM está num estado parado e desalocado.

O primeiro passo para recuperar a sua VM é eliminar o próprio recurso da VM. Com a eliminação, os discos rígidos virtuais permanecem na sua conta de armazenamento. Depois da VM é eliminada, é anexar o disco rígido virtual a outra VM para resolver os erros.

Selecione a sua VM no portal, em seguida, clique em **eliminar**:

![Mostrar Erro de arranque VM captura de ecrã de diagnóstico de arranque](./media/troubleshoot-recovery-disks-portal-linux/stop-delete-vm.png)

Aguarde até que a VM tiver concluído a eliminação do antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que associa-a com a VM precisa ser liberada antes de pode anexar o disco rígido virtual a outra VM.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar o disco rígido virtual existente a outra VM
Para os passos seguintes, utilize outra VM para fins de resolução de problemas. Anexar o disco rígido virtual existente para esta VM de resolução de problemas para poder navegar e editar o conteúdo do disco. Este processo permite-lhe corrigir os eventuais erros de configuração ou analisar o aplicativo adicional ou ficheiros de registo do sistema, por exemplo. Escolha ou crie outra VM para utilizar para fins de resolução de problemas.

1. Selecione o grupo de recursos a partir do portal, em seguida, selecione a sua VM de resolução de problemas. Selecione **discos** e, em seguida, clique em **Attach existente**:

    ![Anexar disco existente no portal](./media/troubleshoot-recovery-disks-portal-linux/attach-existing-disk.png)

2. Para selecionar o disco rígido virtual existente, clique em **Ficheiro VHD**:

    ![Procurar o VHD existente](./media/troubleshoot-recovery-disks-portal-linux/select-vhd-location.png)

3. Selecione a conta de armazenamento e o contentor e clique em seu VHD existente. Clique nas **selecione** botão para confirmar a sua escolha:

    ![Selecionar o seu VHD existente](./media/troubleshoot-recovery-disks-portal-linux/select-vhd.png)

4. Com o seu VHD agora selecionado, clique em **OK** para anexar o disco rígido virtual existente:

    ![Confirmar a anexar o disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-linux/attach-disk-confirm.png)

5. Após alguns segundos, o **discos** painel para a sua VM apresenta uma lista de seu disco rígido virtual ligado como um disco de dados:

    ![Disco rígido virtual já existente anexado como disco de dados](./media/troubleshoot-recovery-disks-portal-linux/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Monte o disco de dados anexados

> [!NOTE]
> Os exemplos seguintes detalham os passos necessários numa VM do Ubuntu. Se estiver a utilizar uma distribuição Linux diferente, como Red Hat Enterprise Linux ou SUSE, o registo de localizações de ficheiro e `mount` comandos podem ser um pouco diferentes. Consulte a documentação para a sua distribuição específica para as alterações apropriadas nos comandos.

1. SSH para a VM de resolução de problemas usando as credenciais apropriadas. Se este disco é o primeiro disco de dados ligado à sua VM de resolução de problemas, provavelmente está ligado a `/dev/sdc`. Utilize `dmseg` para listar os discos anexados:

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
Assim que os erros são resolvidos, desanexe o disco rígido virtual existente da sua VM de resolução de problemas. Não é possível utilizar o seu disco rígido virtual com qualquer outra VM até que a anexar o disco rígido virtual para a VM de resolução de problemas de concessão será libertada.

1. A partir da sessão SSH à sua VM de resolução de problemas, desmonte o disco rígido virtual existente. Altere o primeiro fora do diretório principal para o ponto de montagem:

    ```bash
    cd /
    ```

    Agora desmonte o disco rígido virtual existente. O exemplo seguinte desmonta o dispositivo em `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desanexe o disco rígido virtual da VM. Selecione a sua VM no portal e clique em **discos**. Selecione o seu disco rígido virtual existente e, em seguida, clique em **anulação de exposições**:

    ![Desanexar o disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-linux/detach-disk.png)

    Aguarde até que a VM foi desanexado com êxito o disco de dados antes de continuar.

## <a name="create-vm-from-original-hard-disk"></a>Criar VM a partir de disco rígido original
Para criar uma VM a partir do seu disco rígido virtual original, utilize [este modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). O modelo implementa uma VM numa rede virtual existente, utilizando o URL de VHD do comando anterior. Clique nas **implementar no Azure** botão da seguinte forma:

![Implementar a VM a partir de modelo a partir do GitHub](./media/troubleshoot-recovery-disks-portal-linux/deploy-template-from-github.png)

O modelo é carregado no portal do Azure para a implementação. Introduza os nomes para a sua nova VM e os recursos do Azure existentes e cole o URL para o seu disco rígido virtual existente. Para iniciar a implementação, clique em **Compra**:

![Implementar a VM a partir de modelo](./media/troubleshoot-recovery-disks-portal-linux/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Reativar o diagnóstico de arranque
Quando criar a VM a partir do disco rígido virtual existente, o diagnóstico de arranque pode não ser ativado automaticamente. Para verificar o estado do diagnóstico de arranque e ativar se for necessário, selecione a sua VM no portal. Sob **monitorização**, clique em **as definições de diagnóstico**. Certifique-se de que o estado é **nos**e a marca de verificação junto a **diagnósticos de arranque** está selecionada. Se fizer alterações, clique em **guardar**:

![Atualizar as definições de diagnóstico de arranque](./media/troubleshoot-recovery-disks-portal-linux/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Resolver problemas de uma VM de disco gerido ao anexar um novo disco de SO
1. Pare a VM resultante.
2. [Criar um instantâneo do disco gerido](../windows/snapshot-copy-managed-disk.md) do disco do SO da VM de disco gerido.
3. [Criar um disco gerido a partir do instantâneo](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Anexar o disco gerido como disco de dados da VM](../windows/attach-disk-ps.md).
5. [Alterar o disco de dados do passo 4 para o disco de SO](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Passos Seguintes
Se estiver a ter problemas de ligação à sua VM, veja [resolver problemas de SSH ligações a uma VM do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para problemas com o acesso aos aplicativos em execução na sua VM, consulte [resolver problemas de conectividade de aplicações numa VM do Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter mais informações sobre como utilizar o Resource Manager, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

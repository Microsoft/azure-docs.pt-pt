---
title: Utilizar um Windows VM de resolução de problemas no portal do Azure | Documentos da Microsoft
description: Saiba como resolver problemas de máquina virtual do Windows no Azure ao ligar o disco do SO a uma VM com o portal do Azure de recuperação
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/13/2018
ms.author: genli
ms.openlocfilehash: be998ebe972b3b8e243734ad3f08762a5c080758
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671034"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Resolver problemas de uma VM do Windows ao anexar o disco do SO a uma VM de recuperação com o portal do Azure
Se a sua máquina virtual (VM) do Windows no Azure encontra um erro de arranque ou disco, se pretender executar passos de resolução de problemas no disco rígido virtual em si. Um exemplo comum seria uma atualização de aplicação com falhas que impede que a VM que está a ser capazes de arrancar com êxito. Este artigo fornece detalhes sobre como utilizar o portal do Azure para ligar o seu disco rígido virtual a outra VM do Windows para corrigir quaisquer erros, em seguida, voltar a criar a VM original.

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Elimine a VM se deparar com problemas, mantendo os discos rígidos virtuais.
2. Anexe e Monte o disco rígido virtual a outra VM do Windows para fins de resolução de problemas.
3. Ligue à VM da resolução de problemas. Editar ficheiros ou execute quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
5. Crie uma VM com o disco rígido virtual original.

Para a VM esse disco gerido de usos, agora podemos usar Azure PowerShell para alterar o disco do SO para uma VM. Já não precisamos de eliminar e recriar a VM. Para obter mais informações, consulte [resolver problemas de uma VM do Windows ao anexar o disco do SO a uma VM com o Azure PowerShell de recuperação](troubleshoot-recovery-disks-windows.md).

## <a name="determine-boot-issues"></a>Determinar os problemas de arranque
Para determinar por que a VM não é capaz de inicializa corretamente, examine o diagnóstico de arranque captura de ecrã da VM. Um exemplo comum seria ser uma atualização de aplicação que falhou, ou um subjacente disco de rígido virtual que está a ser eliminado ou movido.

Selecione a sua VM no portal e, em seguida, desloque para baixo para o **suporte + resolução de problemas** secção. Clique em **diagnósticos de arranque** para ver a captura de ecrã. Tenha em atenção que quaisquer mensagens de erro específicas ou códigos de erro para ajudar a determinar o motivo pelo qual a VM está a experienciar um problema. 

![VM a ver o diagnóstico de arranque os registos da consola](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

Também pode clicar **transferir a captura de ecrã** para transferir uma captura de captura de ecrã da VM.

## <a name="view-existing-virtual-hard-disk-details"></a>Ver detalhes do disco rígido virtual existente
Pode anexar o disco rígido virtual a outra VM, terá de identificar o nome do disco rígido virtual (VHD). 

Selecione o grupo de recursos a partir do portal, em seguida, selecione a sua conta de armazenamento. Clique em **Blobs**, como no exemplo a seguir:

![Selecione os blobs de armazenamento](./media/troubleshoot-recovery-disks-portal-windows/storage-account-overview.png)

Normalmente, tem um contentor denominado **vhds** que armazena os discos rígidos virtuais. Selecione o contentor para ver uma lista de discos rígidos virtuais. Tenha em atenção o nome do seu VHD (o prefixo é normalmente o nome da sua VM):

![Identificar o VHD no contentor de armazenamento](./media/troubleshoot-recovery-disks-portal-windows/storage-container.png)

Selecione o seu disco rígido virtual existente na lista e copie o URL para utilização nos passos seguintes:

![Copie o URL de disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Eliminar VM existente
Os discos rígidos virtuais e as VMs são dois recursos diferentes do Azure. Um disco rígido virtual é onde o próprio sistema operacional, aplicativos e configurações são armazenadas. A VM propriamente dito é apenas de metadados que definem o tamanho ou a localização e faz referência a recursos como um disco rígido virtual ou uma placa de interface de rede virtual (NIC). Cada disco rígido virtual tem uma concessão atribuída quando anexado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo se a VM estiver a ser executada, o disco do SO só pode ser desanexado se o recurso da VM for eliminado. A concessão continua a associar o disco do SO a uma VM, mesmo quando a VM está num estado parado e desalocado.

O primeiro passo para recuperar a sua VM é eliminar o próprio recurso da VM. Com a eliminação, os discos rígidos virtuais permanecem na sua conta de armazenamento. Depois da VM é eliminada, é anexar o disco rígido virtual a outra VM para resolver os erros.

Selecione a sua VM no portal, em seguida, clique em **eliminar**:

![Mostrar Erro de arranque VM captura de ecrã de diagnóstico de arranque](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Aguarde até que a VM tiver concluído a eliminação do antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que associa-a com a VM precisa ser liberada antes de pode anexar o disco rígido virtual a outra VM.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar o disco rígido virtual existente a outra VM
Para os passos seguintes, utilize outra VM para fins de resolução de problemas. Anexar o disco rígido virtual existente para esta VM de resolução de problemas para poder navegar e editar o conteúdo do disco. Este processo permite-lhe corrigir os eventuais erros de configuração ou analisar o aplicativo adicional ou ficheiros de registo do sistema, por exemplo. Escolha ou crie outra VM para utilizar para fins de resolução de problemas.

1. Selecione o grupo de recursos a partir do portal, em seguida, selecione a sua VM de resolução de problemas. Selecione **discos** e, em seguida, clique em **Attach existente**:

    ![Anexar disco existente no portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Para selecionar o disco rígido virtual existente, clique em **Ficheiro VHD**:

    ![Procurar o VHD existente](./media/troubleshoot-recovery-disks-portal-windows/select-vhd-location.png)

3. Selecione a conta de armazenamento e o contentor e clique em seu VHD existente. Clique nas **selecione** botão para confirmar a sua escolha:

    ![Selecionar o seu VHD existente](./media/troubleshoot-recovery-disks-portal-windows/select-vhd.png)

4. Com o seu VHD agora selecionado, clique em **OK** para anexar o disco rígido virtual existente:

    ![Confirmar a anexar o disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/attach-disk-confirm.png)

5. Após alguns segundos, o **discos** painel para a sua VM apresenta uma lista de seu disco rígido virtual ligado como um disco de dados:

    ![Disco rígido virtual já existente anexado como disco de dados](./media/troubleshoot-recovery-disks-portal-windows/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Monte o disco de dados anexados

1. Abra uma ligação de ambiente de trabalho remoto à sua VM. Selecione a sua VM no portal e clique em **Connect**. Transfira e abra o ficheiro de ligação de RDP. Introduza as suas credenciais para iniciar sessão na sua VM da seguinte forma:

    ![Inicie sessão na sua VM com o ambiente de trabalho remoto](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Open **Gestor de servidores**, em seguida, selecione **File and Storage Services**. 

    ![Selecione o ficheiro e serviços de armazenamento no Gestor de servidor](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. O disco de dados é automaticamente detectado e ligado. Para ver uma lista dos discos ligados, selecione **discos**. Pode selecionar o disco de dados para ver informações de volume, incluindo a letra de unidade. O exemplo seguinte mostra o disco de dados anexados e usando **f:**:

    ![Disco ligado e as informações de volume no Gestor de servidores](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Corrigir erros no disco rígido virtual original
Com o disco rígido virtual existente montado, pode agora efetuar qualquer manutenção e passos de resolução de problemas, conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmonte e desanexe o disco rígido virtual original
Assim que os erros são resolvidos, desanexe o disco rígido virtual existente da sua VM de resolução de problemas. Não é possível utilizar o seu disco rígido virtual com qualquer outra VM até que a anexar o disco rígido virtual para a VM de resolução de problemas de concessão será libertada.

1. A partir da sessão do RDP para a VM, abra **Gestor de servidores**, em seguida, selecione **File and Storage Services**:

    ![Selecione o ficheiro e serviços de armazenamento no Gestor de servidores](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Selecione **discos** e, em seguida, selecione o disco de dados. Com o botão direito no seu disco de dados e selecione **Colocar Offline**:

    ![Definir o disco de dados como offline no Gestor de servidores](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Agora desanexe o disco rígido virtual da VM. Selecione a sua VM no portal do Azure e clique em **discos**. Selecione o seu disco rígido virtual existente e, em seguida, clique em **anulação de exposições**:

    ![Desanexar o disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Aguarde até que a VM foi desanexado com êxito o disco de dados antes de continuar.

## <a name="create-vm-from-original-hard-disk"></a>Criar VM a partir de disco rígido original
Para criar uma VM a partir do seu disco rígido virtual original, utilize [este modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet). O modelo implementa uma VM numa nova ou existente rede virtual, utilizando o URL de VHD do comando anterior. Clique nas **implementar no Azure** botão da seguinte forma:

![Implementar a VM a partir de modelo a partir do GitHub](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

O modelo é carregado no portal do Azure para a implementação. Introduza os nomes para a sua nova VM e os recursos do Azure existentes e cole o URL para o seu disco rígido virtual existente. Para iniciar a implementação, clique em **Compra**:

![Implementar a VM a partir de modelo](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Reativar o diagnóstico de arranque
Quando criar a VM a partir do disco rígido virtual existente, o diagnóstico de arranque pode não ser ativado automaticamente. Para verificar o estado do diagnóstico de arranque e ativar se for necessário, selecione a sua VM no portal. Sob **monitorização**, clique em **as definições de diagnóstico**. Certifique-se de que o estado é **nos**e a marca de verificação junto a **diagnósticos de arranque** está selecionada. Se fizer alterações, clique em **guardar**:

![Atualizar as definições de diagnóstico de arranque](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Passos Seguintes
Se estiver a ter problemas de ligação à sua VM, veja [ligações de resolução de problemas de RDP para uma VM do Azure](troubleshoot-rdp-connection.md). Para problemas com o acesso aos aplicativos em execução na sua VM, consulte [resolver problemas de conectividade de aplicações numa VM do Windows](troubleshoot-app-connection.md).

Para obter mais informações sobre como utilizar o Resource Manager, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).


---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5490bdd3934b438a683ce4271fbec20b3d13735d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770859"
---
Se a sua máquina virtual (VM) no Azure se deparar com um erro de arranque ou disco, poderá ter de realizar alguns passos de resolução de problemas no próprio disco rígido virtual. Um exemplo comum seria uma atualização de aplicação com falha e que impediria a VM de ser arrancada com êxito. Este artigo descreve como utilizar o portal do Azure para ligar o seu disco rígido virtual a outra VM, para corrigir erros e, depois, recriar a VM original.


## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Elimine a VM que está a experienciar os problemas, mas retenha os discos rígidos virtuais.
2. Anexe e monte o disco rígido virtual noutra VM, para resolução de problemas.
3. Ligue à VM da resolução de problemas. Edite os ficheiros ou execute ferramentas para corrigir erros no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
5. Utilize o disco rígido virtual original para criar uma VM.

## <a name="delete-the-original-vm"></a>Eliminar a VM original
Os discos rígidos virtuais e as VMs são dois recursos diferentes do Azure. Um disco rígido virtual é onde são armazenados o sistema operativo, as aplicações e as configurações. A VM são apenas os metadados que definem o tamanho ou a localização e que referenciam os recursos como discos rígidos virtuais ou placas de rede virtuais (NIC). Quando são anexados a uma VM, é atribuída uma concessão a cada disco rígido virtual. Embora os discos de dados possam ser anexados e desanexados mesmo se a VM estiver a ser executada, o disco do SO só pode ser desanexado se o recurso da VM for eliminado. A concessão continua a associar o disco do SO a uma VM, mesmo se esta estiver no estado parado e desalocado.

O primeiro passo para recuperar a VM é eliminar o próprio recurso da VM. Com a eliminação, os discos rígidos virtuais permanecem na sua conta de armazenamento. Quando a VM for eliminada, pode anexar o disco rígido virtual a outra VM para resolver problemas e resolver os erros. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. No menu do lado esquerdo, clique em **Máquinas Virtuais (clássicas)**.
3. Selecione a VM que tem o problema, clique em **Discos** e identifique o nome do disco rígido virtual. 
4. Selecione o disco rígido virtual do SO e verifique a **Localização** para identificar a conta de armazenamento que contém esse disco rígido virtual. No exemplo seguinte, a cadeia imediatamente antes do ".blob.core.windows.net" é o nome da conta de armazenamento.

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![Imagem da localização da VM](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. Clique com o botão direito do rato e selecione **Eliminar**. Quando eliminar a VM, confirme que os discos não estão selecionados.
6. Crie uma VM de recuperação nova. Esta VM tem de estar na mesma região e no mesmo grupo de recursos (Serviço Cloud) que a VM problemática.
7. Selecione a VM de recuperação e selecione **Discos** > **Anexar Existente**.
8. Para selecionar o disco rígido virtual existente, clique em **Ficheiro VHD**:

    ![Procurar o VHD existente](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. Selecione a conta de armazenamento > contentor de VHD > o disco rígido virtual e clique no botão **Selecionar** para confirmar a sua escolha.

    ![Selecionar o seu VHD existente](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. Com o seu VHD agora selecionado, selecione **OK** para anexar o disco rígido virtual já existente.
11. Ao fim de alguns segundos, o painel **Discos** da VM apresenta o disco já existente ligado como o disco de dados:

    ![Disco rígido virtual já existente anexado como disco de dados](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>Corrigir problemas no disco rígido virtual original
Quando o disco rígido virtual já existente for montado, pode então realizar quaisquer passos de manutenção e resolução de problemas, conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Desmontar e desanexar o disco rígido virtual original
Após resolver os erros, desmonte e desanexe o disco rígido virtual já existente da VM de resolução de problemas. Não pode utilizar o seu disco rígido virtual em conjunto com nenhuma outra VM enquanto a concessão que o anexa à VM de resolução de problemas não for libertada.  

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. No menu do lado esquerdo, selecione **Máquinas Virtuais (clássicas)**.
3. Localize a VM de recuperação. Selecione Discos, clique com o botão direito no disco e, em seguida, selecione **Desanexar**.

## <a name="create-a-vm-from-the-original-hard-disk"></a>Criar uma VM a partir do disco rígido original

Para criar uma VM a partir do seu disco rígido virtual original, utilize [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na parte superior esquerda do portal, selecione **criar um recurso** > **computação** > **Máquina Virtual** > **do Galeria**.
3. Na secção **Escolher Imagem**, selecione **Os meus discos** e selecione o disco rígido virtual original. Verifique a informação da localização. Esta é a região em que a VM tem de ser implementada. Selecione o botão “seguinte”.
4. Na secção **Configuração da máquina virtual**, escreva o nome da VM e selecione um tamanho para a mesma.

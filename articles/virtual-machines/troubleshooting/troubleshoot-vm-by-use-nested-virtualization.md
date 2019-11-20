---
title: Solucionar um problema de VM do Azure usando a virtualização aninhada no Azure | Microsoft Docs
description: Como solucionar um problema de VM do Azure usando a virtualização aninhada no Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: 4565eb86727e768ba894d701cbc5e0073c07ee01
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185512"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Solucionar um problema de VM do Azure usando a virtualização aninhada no Azure

Este artigo mostra como criar um ambiente de virtualização aninhado no Microsoft Azure, para que você possa montar o disco da VM com problema no host do Hyper-V (VM de resgate) para fins de solução de problemas.

## <a name="prerequisites"></a>Pré-requisitos

Para montar a VM com problema, a VM de resgate deve atender aos seguintes pré-requisitos:

-   A VM de resgate deve estar no mesmo local que a VM com problema.

-   A VM de resgate deve estar no mesmo grupo de recursos que a VM com problema.

-   A VM de resgate deve usar o mesmo tipo de conta de armazenamento (Standard ou Premium) que a VM com problema.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Etapa 1: criar uma VM de resgate e instalar a função do Hyper-V

1.  Criar uma nova VM de resgate:

    -  Sistema operacional: Windows Server 2016 datacenter

    -  Tamanho: qualquer série V3 com pelo menos dois núcleos que dão suporte à virtualização aninhada. Para obter mais informações, consulte [apresentando os novos tamanhos de VM Dv3 e Ev3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Mesmo local, conta de armazenamento e grupo de recursos que a VM com problema.

    -  Selecione o mesmo tipo de armazenamento que a VM com problema (Standard ou Premium).

2.  Após a criação da VM de resgate, a área de trabalho remota para a VM de resgate.

3.  Em Gerenciador do Servidor, selecione **gerenciar** > **adicionar funções e recursos**.

4.  Na seção **tipo de instalação** , selecione **instalação baseada em função ou recurso**.

5.  Na seção **selecionar servidor de destino** , verifique se a VM de resgate está selecionada.

6.  Selecione a **função Hyper-V** > **Adicionar recursos**.

7.  Selecione **Avançar** na seção **recursos** .

8.  Se um comutador virtual estiver disponível, selecione-o. Caso contrário, selecione **Avançar**.

9.  Na seção **migração** , selecione **Avançar**

10. Na seção **repositórios padrão** , selecione **Avançar**.

11. Marque a caixa para reiniciar o servidor automaticamente, se necessário.

12. Selecione **Instalar**.

13. Permitir que o servidor instale a função Hyper-V. Isso levará alguns minutos e o servidor será reinicializado automaticamente.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Etapa 2: criar a VM com problema no servidor Hyper-V da VM de resgate

1.  [Crie um disco de instantâneo](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) para o disco do sistema operacional da VM que tem o problema e anexe o disco de instantâneo à VM recuse.

2.  Área de trabalho remota para a VM de resgate.

3.  Abra o gerenciamento de disco (diskmgmt. msc). Verifique se o disco da VM com problema está definido como **offline**.

4.  Abra o Gerenciador do Hyper-V: em **Gerenciador do servidor**, selecione a **função Hyper-v**. Clique com o botão direito do mouse no servidor e selecione o **Gerenciador do Hyper-V**.

5.  No Gerenciador do Hyper-V, clique com o botão direito do mouse na VM de resgate e selecione **novo** > **máquina virtual** > **Avançar**.

6.  Digite um nome para a VM e, em seguida, selecione **Avançar**.

7.  Selecione **geração 1**.

8.  Defina a memória de inicialização em 1024 MB ou mais.

9. Se aplicável, selecione o comutador de rede do Hyper-V que foi criado. Caso contrário, vá para a próxima página.

10. Selecione **anexar um disco rígido virtual mais tarde**.

    ![a imagem sobre a opção anexar um disco rígido virtual mais tarde](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Selecione **concluir** quando a VM for criada.

12. Clique com o botão direito do mouse na VM que você criou e selecione **configurações**.

13. Selecione **controlador IDE 0**, selecione **disco rígido**e clique em **Adicionar**.

    ![a imagem sobre adiciona uma nova unidade de disco rígido](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. Em **disco rígido físico**, selecione o disco da VM com problema que você ANEXOU à VM do Azure. Se você não vir discos listados, verifique se o disco está definido como offline usando o gerenciamento de disco.

    ![a imagem sobre a montagem do disco](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Selecione **Apply** (Aplicar) e **OK**.

16. Clique duas vezes na VM e inicie-a.

17. Agora você pode trabalhar na VM como a VM local. Você pode seguir as etapas de solução de problemas necessárias.

## <a name="step-3-replace-the-os-disk-used-by-the-problem-vm"></a>Etapa 3: substituir o disco do sistema operacional usado pela VM com problema

1.  Depois que você colocar a VM novamente online, desligue a VM no Gerenciador do Hyper-V.

2.  [Desmonte e desanexe o disco do sistema operacional reparado](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Substitua o disco do sistema operacional usado pela VM pelo disco do sistema operacional reparado](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Passos seguintes

Se você estiver tendo problemas para se conectar à sua VM, consulte [solucionar problemas de conexões RDP para uma VM do Azure](troubleshoot-rdp-connection.md). Para problemas com o acesso a aplicativos em execução na sua VM, consulte [solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md).

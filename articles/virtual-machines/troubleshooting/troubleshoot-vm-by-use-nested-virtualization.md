---
title: Solucionar um problema de VM do Azure usando a virtualização aninhada no Azure | Microsoft Docs
description: Como solucionar um problema de VM do Azure usando a virtualização aninhada no Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 18d7e9b0ab44dfe18df0dcd7cd36fb708649a4bc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089680"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Solucionar um problema de VM do Azure usando a virtualização aninhada no Azure

Este artigo mostra como criar um ambiente de virtualização aninhado no Microsoft Azure, para que você possa montar o disco da VM com problema no host do Hyper-V (VM de resgate) para fins de solução de problemas.

## <a name="prerequisites"></a>Pré-requisitos

Para montar a VM com problema, a VM de resgate deve atender aos seguintes pré-requisitos:

-   A VM de resgate deve estar no mesmo local que a VM com problema.

-   A VM de resgate deve estar no mesmo grupo de recursos que a VM com problema.

-   A VM de resgate deve usar o mesmo tipo de conta de armazenamento (Standard ou Premium) que a VM com problema.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Passo 1: Criar uma VM de resgate e instalar a função do Hyper-V

1.  Criar uma nova VM de resgate:

    -  Sistema Operacional: Windows Server 2016 Datacenter

    -  Tamanho: Qualquer série V3 com pelo menos dois núcleos que dão suporte à virtualização aninhada. Para obter mais informações, consulte [apresentando os novos tamanhos de VM Dv3 e Ev3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Mesmo local, conta de armazenamento e grupo de recursos que a VM com problema.

    -  Selecione o mesmo tipo de armazenamento que a VM com problema (Standard ou Premium).

2.  Após a criação da VM de resgate, a área de trabalho remota para a VM de resgate.

3.  Em Gerenciador do servidor, selecione **gerenciar** > **adicionar funções e recursos**.

4.  Na seção **tipo de instalação** , selecione **instalação baseada em função ou recurso**.

5.  Na seção **selecionar servidor de destino** , verifique se a VM de resgate está selecionada.

6.  Selecione > a **função Hyper-V** **Adicionar recursos**.

7.  Selecione **Avançar** na seção **recursos** .

8.  Se um comutador virtual estiver disponível, selecione-o. Caso contrário, selecione **Avançar**.

9.  Na seção **migração** , selecione **Avançar**

10. Na seção **repositórios padrão** , selecione **Avançar**.

11. Marque a caixa para reiniciar o servidor automaticamente, se necessário.

12. Selecione **Instalar**.

13. Permitir que o servidor instale a função Hyper-V. Isso levará alguns minutos e o servidor será reinicializado automaticamente.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Passo 2: Criar a VM com problema no servidor Hyper-V da VM de resgate

1.  Registre o nome do disco na VM com problema e, em seguida, exclua a VM com problema. Certifique-se de manter todos os discos anexados. 

2.  Anexe o disco do sistema operacional da VM com problema como um disco de dados da VM de resgate.

    1.  Depois que a VM com problema for excluída, vá para a VM de resgate.

    2.  Selecione **discos**e, em seguida, **adicionar disco de dados**.

    3.  Selecione o disco da VM com problema e, em seguida, selecione **salvar**.

3.  Depois que o disco for anexado com êxito, a área de trabalho remota será realizada na VM de resgate.

4.  Abra o gerenciamento de disco (diskmgmt. msc). Verifique se o disco da VM com problema está definido como **offline**.

5.  Abra o Gerenciador do Hyper-V: Em **Gerenciador do servidor**, selecione a **função Hyper-V**. Clique com o botão direito do mouse no servidor e selecione o **Gerenciador do Hyper-V**.

6.  No Gerenciador do Hyper-V, clique com o botão direito do mouse na VM de resgate e selecione **nova** > **máquina** > virtual**Avançar**.

7.  Digite um nome para a VM e, em seguida, selecione **Avançar**.

8.  Selecione **geração 1**.

9.  Defina a memória de inicialização em 1024 MB ou mais.

10. Se aplicável, selecione o comutador de rede do Hyper-V que foi criado. Caso contrário, vá para a próxima página.

11. Selecione **anexar um disco rígido virtual mais tarde**.

    ![a imagem sobre a opção anexar um disco rígido virtual mais tarde](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Selecione **concluir** quando a VM for criada.

13. Clique com o botão direito do mouse na VM que você criou e selecione **configurações**.

14. Selecione **controlador IDE 0**, selecione **disco rígido**e clique em **Adicionar**.

    ![a imagem sobre adiciona uma nova unidade de disco rígido](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. Em **disco rígido físico**, selecione o disco da VM com problema que você ANEXOU à VM do Azure. Se você não vir discos listados, verifique se o disco está definido como offline usando o gerenciamento de disco.

    ![a imagem sobre a montagem do disco](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Selecione **Apply** (Aplicar) e **OK**.

18. Clique duas vezes na VM e inicie-a.

19. Agora você pode trabalhar na VM como a VM local. Você pode seguir as etapas de solução de problemas necessárias.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Passo 3: Recriar sua VM do Azure no Azure

1.  Depois que você colocar a VM novamente online, desligue a VM no Gerenciador do Hyper-V.

2.  Vá para a [portal do Azure](https://portal.azure.com) e selecione a VM de resgate > discos, copie o nome do disco. Você usará o nome na próxima etapa. Desanexe o disco fixo da VM de resgate.

3.  Vá para **todos os recursos**, procure o nome do disco e, em seguida, selecione o disco.

     ![a imagem sobre pesquisa o disco](media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Clique em **criar VM**.

     ![a imagem sobre cria a VM a partir do disco](media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Você também pode usar Azure PowerShell para criar a VM a partir do disco. Para obter mais informações, consulte [criar a nova VM de um disco existente usando o PowerShell](../windows/create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Passos Seguintes

Se você estiver tendo problemas para se conectar à sua VM, consulte [solucionar problemas de conexões RDP para uma VM do Azure](troubleshoot-rdp-connection.md). Para problemas com o acesso a aplicativos em execução na sua VM, consulte [solucionar problemas de conectividade do aplicativo em uma VM do Windows](troubleshoot-app-connection.md).

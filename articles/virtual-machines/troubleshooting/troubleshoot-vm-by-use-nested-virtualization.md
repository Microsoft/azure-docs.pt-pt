---
title: Resolver um problema de VM do Azure ao utilizar a virtualização aninhada no Azure | Documentos da Microsoft
description: Como resolver um problema de VM do Azure ao utilizar a virtualização aninhada no Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: c84d015da907c8792f09d1d60e6bc8eddb7e2957
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444370"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Resolver um problema de VM do Azure ao utilizar a virtualização aninhada no Azure

Este artigo mostra como criar um ambiente de Virtualização aninhada no Microsoft Azure, para que pode montar o disco do problema VM no anfitrião Hyper-V (em ação VM) para fins de resolução de problemas.

## <a name="prerequisites"></a>Pré-requisitos

Para montar o problema VM, VM entra em ação tem de cumprir os seguintes pré-requisitos:

-   A VM em ação tem de ser na mesma localização que a VM problemática.

-   A VM em ação tem de estar no mesmo grupo de recursos que a VM problemática.

-   A VM em ação tem de utilizar o mesmo tipo de conta de armazenamento (Standard ou Premium) que a VM problemática.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Passo 1: Criar uma VM em ação e instalar a função Hyper-V

1.  Crie uma nova VM entra em ação:

    -  Sistema operativo: Windows Server 2016 Datacenter

    -  Tamanho: Qualquer série V3 com, pelo menos, dois núcleos que suportam a virtualização aninhada. Para obter mais informações, consulte [apresentando novos tamanhos de VM Dv3 e Ev3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Mesma localização, conta de armazenamento e o grupo de recursos que a VM problemática.

    -  Selecione o mesmo tipo de armazenamento como o problema VM (Standard ou Premium).

2.  Após a VM em ação é criado, o ambiente de trabalho remoto à VM entra em ação.

3.  No Gestor de servidor, selecione **Manage** > **adicionar funções e funcionalidades**.

4.  Na **tipo de instalação** secção, selecione **instalação baseada em funções ou baseada em recursos**.

5.  Na **selecionar servidor de destino** secção, certifique-se de que a VM em ação está selecionada.

6.  Selecione o **função Hyper-V** > **adicionar funcionalidades**.

7.  Selecione **próxima** sobre o **funcionalidades** secção.

8.  Se um comutador virtual estiver disponível, selecione-o. Caso contrário, selecione **seguinte**.

9.  Sobre o **migração** secção, selecione **seguinte**

10. Sobre o **arquivos predefinidos** secção, selecione **próxima**.

11. Selecione a caixa reiniciar automaticamente o servidor, se necessário.

12. Selecione **Instalar**.

13. Permitir que o servidor instalar a função Hyper-V. Esta ação demora alguns minutos e o servidor será reiniciado automaticamente.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Passo 2: Criar a VM problemática no servidor de Hyper-V da VM entra em ação

1.  Registe o nome do disco no problema de VM e, em seguida, elimine a VM problemática. Certifique-se de que tenha discos ligados tudo. 

2.  Anexe o disco do SO do seu problema VM como um disco de dados da VM entra em ação.

    1.  Depois do problema a VM é eliminada, vá para a VM em ação.

    2.  Selecione **discos**e, em seguida **adicionar disco de dados**.

    3.  Selecione o disco da VM de problema e, em seguida, selecione **guardar**.

3.  Depois do disco tem com êxito um ambiente de trabalho remoto, anexado à VM entra em ação.

4.  Abra a gestão de discos (Diskmgmt. msc). Certifique-se de que o disco do problema VM está definida como **Offline**.

5.  Abra o Gestor de Hyper-V: Na **Gestor de servidores**, selecione a **função Hyper-V**. O servidor com o botão direito e, em seguida, selecione o **Gestor de Hyper-V**.

6.  No Gestor de Hyper-V, a VM em ação com o botão direito e, em seguida, selecione **New** > **Máquina Virtual** > **seguinte**.

7.  Escreva um nome para a VM e, em seguida, selecione **seguinte**.

8.  Selecione **geração 1**.

9.  Defina a memória de arranque ao 1024 MB ou mais.

10. Se aplicável, selecione o comutador de rede do Hyper-V que foi criado. Outra mudança para a página seguinte.

11. Selecione **anexar um disco rígido virtual mais tarde**.

    ![a imagem sobre a anexar uma opção posteriormente disco rígido Virtual](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Selecione **concluir** quando a VM é criada.

13. Com o botão direito a VM que criou e, em seguida, selecione **definições**.

14. Selecione **controlador IDE 0**, selecione **rígido**e, em seguida, clique em **Add**.

    ![a imagem sobre adiciona o novo disco rígido](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. Na **disco rígido físico**, selecione o disco do problema VM anexado à VM do Azure. Se não vir quaisquer discos listados, verifique se o disco é definido como offline utilizando a gestão de discos.

    ![a imagem sobre monta o disco](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Selecione **Apply** (Aplicar) e **OK**.

18. Faça duplo clique na VM e, em seguida, iniciá-la.

19. Agora pode trabalhar na VM da VM no local. Pode seguir quaisquer passos de resolução de problemas que precisa.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Passo 3: Voltar a criar a VM do Azure no Azure

1.  Depois de obter a VM novamente online, encerre a VM no Gestor de Hyper-V.

2.  Vá para o [portal do Azure](https://portal.azure.com) e selecione a VM em ação > discos, copie o nome do disco. Irá utilizar o nome no próximo passo. Desanexe o disco fixo da VM entra em ação.

3.  Aceda a **todos os recursos**, procure o nome do disco e, em seguida, selecione o disco.

     ![a imagem sobre pesquisa o disco](media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Clique em **criar VM**.

     ![a imagem sobre cria vm a partir do disco](media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Também pode utilizar o Azure PowerShell para criar a VM a partir do disco. Para obter mais informações, consulte [criar a nova VM a partir de um disco existente com o PowerShell](../windows/create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Passos Seguintes

Se estiver a ter problemas de ligação à sua VM, veja [ligações de resolução de problemas de RDP para uma VM do Azure](troubleshoot-rdp-connection.md). Para problemas com o acesso aos aplicativos em execução na sua VM, consulte [resolver problemas de conectividade de aplicações numa VM do Windows](troubleshoot-app-connection.md).

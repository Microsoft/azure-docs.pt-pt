---
title: Resolução de problemas um Azure VM defeituoso usando a virtualização aninhada em Azure Microsoft Docs
description: Como resolver um problema Azure VM usando a virtualização aninhada em Azure
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
ms.openlocfilehash: e1acfc3216ccfaeac035f1ff31e82c7b67c17daf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76119623"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Resolução de problemas um Azure VM defeituoso usando a virtualização aninhada em Azure

Este artigo mostra como criar um ambiente de virtualização aninhado no Microsoft Azure, para que possa montar o disco do VM defeituoso no anfitrião Hiper-V (Rescue VM) para fins de resolução de problemas.

## <a name="prerequisites"></a>Pré-requisitos

Para montar o VM defeituoso, o Rescue VM deve utilizar o mesmo tipo de Conta de Armazenamento (Standard ou Premium) que o VM defeituoso.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Passo 1: Criar um VM de resgate e instalar função Hyper-V

1.  Criar um novo Rescue VM:

    -  Sistema operativo: Windows Server 2016 Datacenter

    -  Tamanho: Qualquer série V3 com pelo menos dois núcleos que suportam a virtualização aninhada. Para obter mais informações, consulte [a introdução dos novos tamanhos Dv3 e Ev3 VM](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Mesma localização, Conta de Armazenamento e Grupo de Recursos como o VM defeituoso.

    -  Selecione o mesmo tipo de armazenamento que o VM defeituoso (Standard ou Premium).

2.  Após a criação do Rescue VM, ambiente de trabalho remoto para o Rescue VM.

3.  No Gestor do Servidor, **selecione Gerir**  >  **Funções e Funcionalidades de Adicionar**.

4.  Na secção **Tipo de Instalação,** selecione **instalação baseada em funções ou baseada em recursos.**

5.  Na secção **de servidor de destino Select,** certifique-se de que o VM de resgate está selecionado.

6.  Selecione o **função Hiper-V**  >  **Adicionar Funcionalidades**.

7.  Selecione **Seguinte** na secção **Funcionalidades.**

8.  Se estiver disponível um interruptor virtual, selecione-o. Caso contrário, selecione **Seguinte**.

9.  Na secção **migração,** selecione **Seguinte**

10. Na secção **Lojas Predefinidos,** selecione **Seguinte**.

11. Verifique a caixa para reiniciar automaticamente o servidor, se necessário.

12. Selecione **Instalar**.

13. Permitir que o servidor instale a função Hyper-V. Isto demora alguns minutos e o servidor reiniciará automaticamente.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>Passo 2: Criar o VM defeituoso no servidor Hiper-V do Rescue VM

1.  [Crie um disco instantâneo](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) para o disco de so do VM que tenha problemas e, em seguida, prenda o disco instantâneo ao VM de recusa.

2.  Ambiente de trabalho remoto para o Rescue VM.

3.  Gestão de Discos Abertos (diskmgmt.msc). Certifique-se de que o disco do VM defeituoso está definido para **offline**.

4.  Open Hyper-V Manager: In **Server Manager**, selecione a **função Hyper-V**. Clique com o botão direito no servidor e, em seguida, selecione o **Hyper-V Manager**.

5.  No Hyper-V Manager, clique à direita no Rescue VM e, em seguida, selecione **New**  >  **Virtual Machine**  >  **Next**.

6.  Digite um nome para o VM e, em seguida, selecione **Seguinte**.

7.  Selecione **Geração 1**.

8.  Desa parte para 1024 MB ou mais.

9. Se aplicável, selecione o Hyper-V Network Switch que foi criado. Senão, passe para a página seguinte.

10. **Selecione Fixe um disco rígido virtual mais tarde**.

    ![a imagem sobre a opção Anexar um Disco Rígido Virtual Mais tarde](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. **Selecione Terminar** quando o VM for criado.

12. Clique com o botão direito no VM que criou e, em seguida, selecione **Definições**.

13. Selecione **o controlador IDE 0**, selecione **Hard Drive**e, em seguida, clique em **Adicionar**.

    ![a imagem sobre adiciona novo disco rígido](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. No **Disco Rígido Físico,** selecione o disco do VM defeituoso que anexou ao Azure VM. Se não vir nenhum disco listado, verifique se o disco está definido para offline utilizando a gestão do disco.

    ![a imagem sobre os montes do disco](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Selecione **Apply** (Aplicar) e **OK**.

16. Clique duas vezes no VM e, em seguida, inicie-o.

17. Agora pode trabalhar no VM como o VM no local. Pode seguir os passos de resolução de problemas que precisar.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>Passo 3: Substitua o disco de so utilizado pelo VM defeituoso

1.  Depois de voltar a pôr o VM on-line, desligue o VM no gestor do Hyper-V.

2.  [Desmonte e retire o disco de OS reparado](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Substitua o disco de so utilizado pelo VM pelo disco oss reparado](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas de ligação ao seu VM, consulte [as ligações RDP de resolução de problemas a um Azure VM](troubleshoot-rdp-connection.md). Para problemas com o acesso a aplicações em execução no seu VM, consulte [problemas de conectividade da aplicação Troubleshoot num VM do Windows](troubleshoot-app-connection.md).

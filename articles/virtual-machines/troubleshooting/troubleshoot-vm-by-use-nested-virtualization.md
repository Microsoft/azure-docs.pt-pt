---
title: Problemas de sucação de um Azure VM defeituoso usando virtualização aninhada em Azure Microsoft Docs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76119623"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Problemas de sucação de um Azure VM defeituoso usando virtualização aninhada em Azure

Este artigo mostra como criar um ambiente de virtualização aninhado no Microsoft Azure, para que possa montar o disco do VM defeituoso no hospedeiro Hyper-V (VM de resgate) para fins de resolução de problemas.

## <a name="prerequisites"></a>Pré-requisitos

Para montar o VM defeituoso, o VM de resgate deve utilizar o mesmo tipo de Conta de Armazenamento (Standard ou Premium) que o VM defeituoso.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Passo 1: Criar um VM de resgate e instalar a função Hyper-V

1.  Crie um novo VM de resgate:

    -  Sistema operativo: Windows Server 2016 Datacenter

    -  Tamanho: Qualquer série V3 com pelo menos dois núcleos que suportam a virtualização aninhada. Para mais informações, consulte [A introdução dos novos tamanhos Dv3 e Ev3 VM](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Mesma localização, Conta de Armazenamento e Grupo de Recursos como o VM defeituoso.

    -  Selecione o mesmo tipo de armazenamento que o VM defeituoso (Standard ou Premium).

2.  Após a criação do VM de resgate, ambiente de trabalho remoto para o VM de resgate.

3.  No Gestor do Servidor, selecione **Gerir** > **funções e funcionalidades**de adicionar .

4.  Na secção Tipo de **Instalação,** selecione **instalação baseada em funções ou baseada em características**.

5.  Na secção **select destino servidor,** certifique-se de que o VM de resgate é selecionado.

6.  Selecione o >  **função Hyper-V****Adicionar Funcionalidades**.

7.  Selecione **Seguinte** na secção **Funcionalidades.**

8.  Se estiver disponível um interruptor virtual, selecione-o. Caso contrário, selecione **Next**.

9.  Na secção **Migração, selecione** **Next**

10. Na secção **'Lojas Predefinidas',** selecione **Seguinte**.

11. Verifique a caixa para reiniciar automaticamente o servidor, se necessário.

12. Selecione **Instalar**.

13. Permita que o servidor instale a função Hyper-V. Isto leva alguns minutos e o servidor irá reiniciar automaticamente.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>Passo 2: Criar o VM defeituoso no servidor Hyper-V da VM de resgate

1.  [Crie um disco instantâneo](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) para o disco OS do VM que tenha problemas e, em seguida, prenda o disco instantâneo ao VM do recuse.

2.  Ambiente de trabalho remoto para o VM de resgate.

3.  Gestão do Disco Aberto (diskmgmt.msc). Certifique-se de que o disco do VM defeituoso está definido para **offline**.

4.  Open Hyper-V Manager: In **Server Manager**, selecione a **função Hyper-V**. Clique no servidor e, em seguida, selecione o **Gestor Hyper-V**.

5.  No Gestor De Hiper-V, clique no VM de resgate e, em seguida, selecione **Nova** > **Máquina** > Virtual**Seguinte**.

6.  Digite um nome para o VM e, em seguida, selecione **Next**.

7.  Selecione **Geração 1**.

8.  Detete a memória de arranque em 1024 MB ou mais.

9. Se aplicável, selecione o Interruptor de Rede Hyper-V que foi criado. Senão passar para a página seguinte.

10. Selecione **anexar um disco rígido virtual mais tarde**.

    ![a imagem sobre a anexação de uma opção Disco Rígido Virtual Posterior](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Selecione **Terminar** quando o VM for criado.

12. Clique no VM que criou e, em seguida, selecione **Definições**.

13. Selecione **IDE Controller 0,** selecione **Hard Drive**, e, em seguida, clique em **Adicionar**.

    ![a imagem sobre adiciona novo disco rígido](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. No **Disco Rígido Físico,** selecione o disco do VM defeituoso que anexou ao VM Azure. Se não vir nenhum disco listado, verifique se o disco está definido para offline utilizando a gestão do Disco.

    ![a imagem sobre monta o disco](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Selecione **Apply** (Aplicar) e **OK**.

16. Clique duas vezes no VM e, em seguida, inicie-o.

17. Agora pode trabalhar no VM como o VM no local. Pode seguir os passos que precisar.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>Passo 3: Substitua o disco OS utilizado pelo VM defeituoso

1.  Depois de voltar a pôr o VM online, desligue o VM do gerente do Hyper-V.

2.  [Desmontar e separar o disco osres reparado](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Substitua o disco OS utilizado pelo VM pelo disco OS reparado](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas de ligação ao seu VM, consulte [ligações RDP de Troubleshoot a um VM Azure](troubleshoot-rdp-connection.md). Para problemas com o acesso a aplicações em execução no seu VM, consulte problemas de conectividade da [aplicação Troubleshoot num VM windows](troubleshoot-app-connection.md).

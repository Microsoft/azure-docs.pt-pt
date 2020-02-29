---
title: Redefinir uma senha local do Windows sem agente Azure  Microsoft Docs
description: Como redefinir a palavra-passe de uma conta de utilizador local do Windows quando o agente convidado do Azure não está instalado ou a funcionar num VM
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: becbf88aeda164f7d916cbc1f1ace89262cc1a3f
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921628"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Redefinir senha local do Windows para Azure VM offline
Pode redefinir a senha local do Windows de um VM em Azure utilizando o [portal Azure ou o Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) desde que o agente convidado Azure esteja instalado. Este método é a principal forma de redefinir uma palavra-passe para um VM Azure. Se encontrar problemas com o agente convidado Do Azure que não responde, ou não conseguir instalar depois de carregar uma imagem personalizada, pode redefinir manualmente uma palavra-passe do Windows. Este artigo detalha como redefinir uma senha de conta local anexando a fonte de disco virtual OS a outro VM. As etapas descritas neste artigo não se aplicam aos controladores de domínio do Windows. 

> [!WARNING]
> Utilize este processo apenas como último recurso. Tente sempre redefinir uma palavra-passe utilizando primeiro o [portal Azure ou o Azure PowerShell.](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="overview-of-the-process"></a>Visão geral do processo
Os passos fundamentais para a realização de uma palavra-passe local reset para um Windows VM em Azure quando não há acesso ao agente convidado Azure é o seguinte:

1. Pare o VM afetado.
1. Crie uma imagem para o disco OS do VM.
1. Crie uma cópia do disco OS a partir do instantâneo.
1. Fixe e monte o disco OS copiado noutro VM do Windows e, em seguida, crie alguns ficheiros de config no disco. Os ficheiros irão ajudá-lo a repor a palavra-passe.
1. Desmonte e retire o disco de SO copiado do VM de resolução de problemas.
1. Troque o disco OS pelo VM afetado.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Passos detalhados para o VM com implantação do Gestor de Recursos

> [!NOTE]
> Os passos não se aplicam aos controladores de domínio do Windows. Funciona apenas num servidor autónomo ou num servidor que seja membro de um domínio.

Tente sempre redefinir uma palavra-passe utilizando o [portal Azure ou o Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de experimentar os seguintes passos. Certifique-se de ter uma cópia de segurança do seu VM antes de começar.

1. Pegue uma foto para o disco OS do VM afetado, crie um disco a partir do instantâneo e, em seguida, prenda o disco a um VM de resolução de problemas. Para mais informações, consulte [Troubleshoot a Windows VM, ligando o disco OS a um VM de recuperação utilizando o portal Azure](troubleshoot-recovery-disks-portal-windows.md).
2. Ligue-se ao VM de resolução de problemas utilizando o Remote Desktop.
3. Crie `gpt.ini` em `\Windows\System32\GroupPolicy` na unidade da VM (se houver gpt.ini, mude o nome para gpt.ini.bak):
   
   > [!WARNING]
   > Certifique-se de que não cria acidentalmente os seguintes ficheiros em C:\Windows, a unidade de SO para o VM de resolução de problemas. Crie os seguintes ficheiros na unidade DE So para o seu VM de origem que está anexado como um disco de dados.
   
   * Adicione as seguintes linhas no ficheiro `gpt.ini` que criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Criar gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. Crie `scripts.ini` em `\Windows\System32\GroupPolicy\Machines\Scripts\`. Certifique-se de que as pastas escondidas são mostradas. Se necessário, crie as pastas `Machine` ou `Scripts`.
   
   * Adicione as seguintes linhas o ficheiro `scripts.ini` que criou:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Criar scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. Crie `FixAzureVM.cmd` em `\Windows\System32` com os seguintes conteúdos, substituindo `<username>` e `<newpassword>` com os seus próprios valores:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Deve cumprir os requisitos de complexidade da palavra-passe configurados para o seu VM ao definir a nova palavra-passe.

6. No portal Azure, retire o disco do VM de resolução de problemas.

7. [Altere o disco OS para o VM afetado](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Depois de o novo VM estar em execução, ligue-se ao VM utilizando o Remote Desktop com a nova palavra-passe especificada no guião `FixAzureVM.cmd`.

9. Da sua sessão remota até ao novo VM, remova os seguintes ficheiros para limpar o ambiente:
    
    * De %windir%\System32
      * remove FixAzureVM.cmd
    * De %windir%\System32\GroupPolicy\Machine\Scripts
      * remover scripts.ini
    * De %windir%\System32\GroupPolicy
      * remover gpt.ini (se gpt.ini existiu antes, e você rebatizou-o para gpt.ini.bak, renomear o ficheiro .bak de volta para gpt.ini)

## <a name="detailed-steps-for-classic-vm"></a>Passos detalhados para VM clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Os passos não se aplicam aos controladores de domínio do Windows. Funciona apenas num servidor autónomo ou num servidor que seja membro de um domínio.

Tente sempre redefinir uma palavra-passe utilizando o [portal Azure ou o Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) antes de experimentar os seguintes passos. Certifique-se de ter uma cópia de segurança do seu VM antes de começar. 

1. Elimine o VM afetado no portal Azure. A eliminação do VM apenas elimina os metadados, a referência do VM dentro do Azure. Os discos virtuais são mantidos quando o VM é eliminado:
   
   * Selecione o VM no portal Azure e, em seguida, clique em *Eliminar:*
     
     ![Eliminar vM existente](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Prenda o disco operativo da VM de origem ao VM de resolução de problemas. O VM de resolução de problemas deve estar na mesma região que o disco operativo da VM de origem (como `West US`):
   
   1. Selecione o VM de resolução de problemas no portal Azure. Clique em *discos* | *anexar os existentes:*
     
      ![Anexar o disco existente](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Selecione *Ficheiro VHD* e, em seguida, selecione a conta de armazenamento que contém o seu VM de origem:
     
      ![Selecionar a conta de armazenamento](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Verifique a caixa marcada Mostrar contas de *armazenamento clássicas*e, em seguida, selecione o recipiente de origem. O recipiente de origem é tipicamente *vhds:*
     
      ![Selecione recipiente de armazenamento](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Selecione recipiente de armazenamento](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Selecione o OS vhd para anexar. Clique em *Selecionar* para completar o processo:
     
      ![Selecione disco virtual de origem](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Clique em Ok para anexar o disco

      ![Anexar o disco existente](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Ligue-se ao VM de resolução de problemas utilizando o Remote Desktop e certifique-se de que o disco operativo de VM de origem está visível:

   1. Selecione o VM de resolução de problemas no portal Azure e clique em *Connect*.

   2. Abra o ficheiro RDP que descarrega. Introduza o nome de utilizador e a palavra-passe do VM de resolução de problemas.

   3. No File Explorer, procure o disco de dados que anexou. Se o VHD da fonte VM for o único disco de dados ligado ao VM de resolução de problemas, deve ser o F: unidade:
     
      ![Ver disco de dados anexado](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Crie `gpt.ini` em `\Windows\System32\GroupPolicy` na unidade da VM (se `gpt.ini` existir, mude o nome para `gpt.ini.bak`):
   
   > [!WARNING]
   > Certifique-se de que não cria acidentalmente os seguintes ficheiros em `C:\Windows`, a unidade de SO para o VM de resolução de problemas. Crie os seguintes ficheiros na unidade DE So para o seu VM de origem que está anexado como um disco de dados.
   
   * Adicione as seguintes linhas no ficheiro `gpt.ini` que criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Criar gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Crie `scripts.ini` em `\Windows\System32\GroupPolicy\Machines\Scripts\`. Certifique-se de que as pastas escondidas são mostradas. Se necessário, crie as pastas `Machine` ou `Scripts`.
   
   * Adicione as seguintes linhas o ficheiro `scripts.ini` que criou:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Criar scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. Crie `FixAzureVM.cmd` em `\Windows\System32` com os seguintes conteúdos, substituindo `<username>` e `<newpassword>` com os seus próprios valores:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Deve cumprir os requisitos de complexidade da palavra-passe configurados para o seu VM ao definir a nova palavra-passe.

7. No portal Azure, retire o disco do VM de resolução de problemas:
   
   1. Selecione o VM de resolução de problemas no portal Azure, clique em *Discos*.
   
   2. Selecione o disco de dados anexado no passo 2, clique em **Desmontar**e, em seguida, clique **EM OK**.

     ![Disquete](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Disquete](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Crie um VM a partir do disco operativo da VM fonte:
   
     ![Criar um VM a partir do modelo](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Criar um VM a partir do modelo](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Criar um VM a partir do modelo](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Complete a experiência da máquina virtual Criar

1. Depois de o novo VM estar em execução, ligue-se ao VM utilizando o Remote Desktop com a nova palavra-passe especificada no guião `FixAzureVM.cmd`.

2. Da sua sessão remota até ao novo VM, remova os seguintes ficheiros para limpar o ambiente:
    
    * De `%windir%\System32`
      * remover `FixAzureVM.cmd`
    * De `%windir%\System32\GroupPolicy\Machine\Scripts`
      * remover `scripts.ini`
    * De `%windir%\System32\GroupPolicy`
      * remover `gpt.ini` (se `gpt.ini` existia antes, e rebatizou-o para `gpt.ini.bak`, rebatizando o ficheiro `.bak` de volta para `gpt.ini`)

## <a name="next-steps"></a>Passos seguintes
Se ainda não conseguir ligar-se utilizando o Remote Desktop, consulte o guia de resolução de [problemas RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). O guia detalhado de resolução de [problemas do RDP analisa](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) métodos de resolução de problemas em vez de passos específicos. Também pode abrir um pedido de [apoio azure](https://azure.microsoft.com/support/options/) para assistência prática.

---
title: Redefinir uma senha local do Windows sem o agente do Azure | Microsoft Docs
description: Como redefinir a senha de uma conta de usuário do Windows local quando o agente convidado do Azure não está instalado ou funcionando em uma VM
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
ms.openlocfilehash: 6faab5bffaddbbd5d8deb9c3834bf3d8fe3e3445
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058649"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Redefinir a senha local do Windows para a VM do Azure offline
Você pode redefinir a senha local do Windows de uma VM no Azure usando o [portal do Azure ou Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) desde que o agente convidado do Azure esteja instalado. Esse método é a principal maneira de redefinir uma senha para uma VM do Azure. Se você encontrar problemas com o agente convidado do Azure não respondendo ou falha ao instalar depois de carregar uma imagem personalizada, você pode redefinir manualmente uma senha do Windows. Este artigo fornece detalhes sobre como redefinir uma senha de conta local anexando o disco virtual do sistema operacional de origem a outra VM. As etapas descritas neste artigo não se aplicam aos controladores de domínio do Windows. 

> [!WARNING]
> Utilize este processo apenas como último recurso. Sempre tente redefinir uma senha usando o [portal do Azure ou Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) primeiro.

## <a name="overview-of-the-process"></a>Visão geral do processo
As etapas principais para executar uma redefinição de senha local para uma VM do Windows no Azure quando não há acesso ao agente convidado do Azure são as seguintes:

1. Pare a VM afetada.
1. Crie um instantâneo para o disco do sistema operacional da VM.
1. Crie uma cópia do disco do sistema operacional a partir do instantâneo.
1. Anexe e monte o disco do sistema operacional copiado em outra VM do Windows e, em seguida, crie alguns arquivos de configuração no disco. Os arquivos irão ajudá-lo a redefinir a senha.
1. Desmonte e desanexe o disco do sistema operacional copiado da VM de solução de problemas.
1. Troque o disco do sistema operacional da VM afetada.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Etapas detalhadas para a VM com a implantação do Gerenciador de recursos

> [!NOTE]
> As etapas não se aplicam aos controladores de domínio do Windows. Ele só funciona no servidor autônomo ou em um servidor que seja membro de um domínio.

Sempre tente redefinir uma senha usando o [portal do Azure ou Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de tentar as etapas a seguir. Verifique se você tem um backup de sua VM antes de começar.

1. Tire um instantâneo do disco do sistema operacional da VM afetada, crie um disco do instantâneo e, em seguida, anexe o disco a uma VM de solução de problemas. Para obter mais informações, consulte [solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure](troubleshoot-recovery-disks-portal-windows.md).
2. Conecte-se à VM de solução de problemas usando Área de Trabalho Remota.
3. Crie `gpt.ini` no`\Windows\System32\GroupPolicy` na unidade da VM de origem (se GPT. ini existir, renomeie para GPT. ini. bak):
   
   > [!WARNING]
   > Certifique-se de não criar acidentalmente os seguintes arquivos em C:\Windows, a unidade do sistema operacional para a VM de solução de problemas. Crie os seguintes arquivos na unidade do sistema operacional para a VM de origem que está anexada como um disco de dados.
   
   * Adicione as seguintes linhas ao `gpt.ini` arquivo que você criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Criar GPT. ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. Criar `scripts.ini` em `\Windows\System32\GroupPolicy\Machines\Scripts\`. Verifique se as pastas ocultas são mostradas. Se necessário, crie as `Machine` pastas `Scripts` ou.
   
   * Adicione as seguintes linhas ao `scripts.ini` arquivo que você criou:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Criar scripts. ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. `FixAzureVM.cmd` `<username>` Crie com o conteúdo a seguir, substituindo `<newpassword>` e pelos seus próprios valores: `\Windows\System32`
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Você deve atender aos requisitos de complexidade de senha configurados para sua VM ao definir a nova senha.

6. Em portal do Azure, desanexe o disco da VM de solução de problemas.

7. [Altere o disco do sistema operacional para a VM afetada](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Depois que a nova VM estiver em execução, conecte-se à VM usando área de trabalho remota com a nova senha que `FixAzureVM.cmd` você especificou no script.

9. Em sua sessão remota para a nova VM, remova os seguintes arquivos para limpar o ambiente:
    
    * De%windir%\System32
      * remove FixAzureVM.cmd
    * De%windir%\System32\GroupPolicy\Machine\Scripts
      * remover scripts. ini
    * De%windir%\System32\GroupPolicy
      * Remova GPT. ini (se o GPT. ini existia antes e você o renomeou para GPT. ini. bak, renomeie o arquivo. bak de volta para GPT. ini)

## <a name="detailed-steps-for-classic-vm"></a>Etapas detalhadas para a VM clássica

> [!NOTE]
> As etapas não se aplicam aos controladores de domínio do Windows. Ele só funciona no servidor autônomo ou em um servidor que seja membro de um domínio.

Sempre tente redefinir uma senha usando o [portal do Azure ou Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) antes de tentar as etapas a seguir. Verifique se você tem um backup de sua VM antes de começar. 

1. Exclua a VM afetada no portal do Azure. Excluir a VM apenas exclui os metadados, a referência da VM no Azure. Os discos virtuais são mantidos quando a VM é excluída:
   
   * Selecione a VM no portal do Azure, em seguida, clique em *excluir*:
     
     ![Excluir VM existente](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Anexe o disco do sistema operacional da VM de origem à VM de solução de problemas. A VM de solução de problemas deve estar na mesma região que o disco do sistema operacional da VM de `West US`origem (como):
   
   1. Selecione a VM de solução de problemas no portal do Azure. Clique em *discos* | *anexar existente*:
     
      ![Anexar disco existente](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Selecione *arquivo VHD* e, em seguida, selecione a conta de armazenamento que contém a VM de origem:
     
      ![Selecionar conta de armazenamento](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Marque a caixa marcada *Mostrar contas de armazenamento clássicas*e, em seguida, selecione o contêiner de origem. O contêiner de origem normalmente é *VHDs*:
     
      ![Selecionar contêiner de armazenamento](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Selecionar contêiner de armazenamento](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Selecione o VHD do sistema operacional a ser anexado. Clique em *selecionar* para concluir o processo:
     
      ![Selecionar disco virtual de origem](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Clique em OK para anexar o disco

      ![Anexar disco existente](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Conecte-se à VM de solução de problemas usando Área de Trabalho Remota e verifique se o disco do sistema operacional da VM de origem está visível:

   1. Selecione a VM de solução de problemas no portal do Azure e clique em *conectar*.

   2. Abra o arquivo RDP que o baixa. Insira o nome de usuário e a senha da VM de solução de problemas.

   3. No explorador de arquivos, procure o disco de dados que você anexou. Se o VHD da VM de origem for o único disco de dados anexado à VM de solução de problemas, ele deverá ser a unidade F::
     
      ![Exibir disco de dados anexado](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Crie `gpt.ini` `gpt.ini` `gpt.ini.bak`no `\Windows\System32\GroupPolicy` na unidade da VM de origem (se existir, renomeie para):
   
   > [!WARNING]
   > Certifique-se de não criar acidentalmente os seguintes arquivos no `C:\Windows`, a unidade do sistema operacional para a VM de solução de problemas. Crie os seguintes arquivos na unidade do sistema operacional para a VM de origem que está anexada como um disco de dados.
   
   * Adicione as seguintes linhas ao `gpt.ini` arquivo que você criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Criar GPT. ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Criar `scripts.ini` em `\Windows\System32\GroupPolicy\Machines\Scripts\`. Verifique se as pastas ocultas são mostradas. Se necessário, crie as `Machine` pastas `Scripts` ou.
   
   * Adicione as seguintes linhas ao `scripts.ini` arquivo que você criou:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Criar scripts. ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. `FixAzureVM.cmd` `<username>` Crie com o conteúdo a seguir, substituindo `<newpassword>` e pelos seus próprios valores: `\Windows\System32`
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Você deve atender aos requisitos de complexidade de senha configurados para sua VM ao definir a nova senha.

7. Em portal do Azure, desanexe o disco da VM de solução de problemas:
   
   1. Selecione a VM de solução de problemas no portal do Azure, clique em *discos*.
   
   2. Selecione o disco de dados anexado na etapa 2, clique em **desanexar**e, em seguida, clique em **OK**.

     ![Desanexar disco](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Desanexar disco](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Crie uma VM do disco do sistema operacional da VM de origem:
   
     ![Criar uma VM com base no modelo](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Criar uma VM com base no modelo](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Criar uma VM com base no modelo](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Concluir a experiência de criação de máquina virtual

1. Depois que a nova VM estiver em execução, conecte-se à VM usando área de trabalho remota com a nova senha que `FixAzureVM.cmd` você especificou no script.

2. Em sua sessão remota para a nova VM, remova os seguintes arquivos para limpar o ambiente:
    
    * De`%windir%\System32`
      * Exclu`FixAzureVM.cmd`
    * De`%windir%\System32\GroupPolicy\Machine\Scripts`
      * Exclu`scripts.ini`
    * De`%windir%\System32\GroupPolicy`
      * Remova `gpt.ini` (se `gpt.ini` existia antes, e você o renomeou `gpt.ini.bak`como, renomeie o `gpt.ini` `.bak` arquivo de volta para)

## <a name="next-steps"></a>Passos seguintes
Se você ainda não conseguir se conectar usando Área de Trabalho Remota, consulte o [Guia de solução de problemas de RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). O [guia detalhado de solução de problemas de RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) analisa os métodos de solução de problemas em vez de etapas específicas. Você também pode [abrir uma solicitação de suporte do Azure](https://azure.microsoft.com/support/options/) para obter assistência prática.

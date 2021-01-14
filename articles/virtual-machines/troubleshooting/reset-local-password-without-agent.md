---
title: Redefinir uma palavra-passe local do Windows sem o agente Azure | Microsoft Docs
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
ms.openlocfilehash: 2cc6ef9b1d9ca8336162b524356ea6e0d1bf5fd2
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197664"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Repor uma palavra-passe do Windows local da VM do Azure offline
Pode redefinir a palavra-passe local do Windows de um VM em Azure utilizando o [portal Azure ou Azure PowerShell](reset-rdp.md) desde que o agente convidado Azure esteja instalado. Este método é a forma primária de redefinir uma palavra-passe para um VM Azure. Se encontrar problemas com o agente convidado Azure que não responde ou que não instala depois de carregar uma imagem personalizada, pode redefinir manualmente uma palavra-passe do Windows. Este artigo detalha como redefinir uma palavra-passe de conta local anexando o disco virtual de origem os a outro VM. Os passos descritos neste artigo não se aplicam aos controladores de domínio do Windows. 

> [!WARNING]
> Utilize este processo apenas como último recurso. Tente sempre redefinir uma palavra-passe utilizando o [portal Azure ou a Azure PowerShell](reset-rdp.md) primeiro.

## <a name="overview-of-the-process"></a>Visão geral do processo
Os passos principais para a realização de uma palavra-passe local reiniciada para um Windows VM em Azure quando não há acesso ao agente convidado Azure são os seguintes:

1. Pare o VM afetado.
1. Crie uma imagem instantânea para o disco de so do VM.
1. Crie uma cópia do disco SO a partir do instantâneo.
1. Prenda e monte o disco de OS copiado para outro VM do Windows e, em seguida, crie alguns ficheiros config no disco. Os ficheiros irão ajudá-lo a redefinir a palavra-passe.
1. Desmonte e retire o disco de OS copiado do VM de resolução de problemas.
1. Troque o disco DE pelo VM afetado.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Passos detalhados para o VM com implantação do Gestor de Recursos

> [!NOTE]
> Os passos não se aplicam aos controladores de domínio do Windows. Funciona apenas em servidor autónomo ou num servidor que é membro de um domínio.

Tente sempre redefinir uma palavra-passe utilizando o [portal Azure ou Azure PowerShell](reset-rdp.md) antes de experimentar os seguintes passos. Certifique-se de que tem uma cópia de segurança do seu VM antes de começar.

1. Tire uma fotografia para o disco de SO do VM afetado, crie um disco a partir do instantâneo e, em seguida, prenda o disco a um VM de resolução de problemas. Para obter mais informações, consulte [Troubleshoot a Windows VM, fixando o disco OS a um VM de recuperação utilizando o portal Azure](troubleshoot-recovery-disks-portal-windows.md).
2. Ligue-se ao VM de resolução de problemas utilizando o Ambiente de Trabalho Remoto.
3. Criar `gpt.ini` `\Windows\System32\GroupPolicy` na unidade da fonte VM (se gpt.ini existir, renomear para gpt.ini.bak):
   
   > [!WARNING]
   > Certifique-se de que não cria acidentalmente os seguintes ficheiros em C:\Windows, a unidade de SO para a resolução de problemas VM. Crie os seguintes ficheiros na unidade so para o seu VM de origem que está anexado como um disco de dados.
   
   * Adicione as seguintes linhas no `gpt.ini` ficheiro que criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini.png" alt-text="Screenshot que mostra as atualizações feitas no ficheiro gpt.ini.":::

4. Criar `scripts.ini` em `\Windows\System32\GroupPolicy\Machine\Scripts\` . Certifique-se de que as pastas ocultas são mostradas. Se necessário, crie as `Machine` pastas ou `Scripts` as pastas. 
   
   * Adicione as seguintes linhas do `scripts.ini` ficheiro que criou:
     
     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-1.png" alt-text="Screenshot que mostra as atualizações feitas no ficheiro script.ini.":::

5. Crie `FixAzureVM.cmd` `\Windows\System32\GroupPolicy\Machine\Scripts\Startup\` com os seguintes conteúdos, substituindo `<username>` e pelos seus `<newpassword>` próprios valores:
   
    ```
    net user <username> <newpassword> /add /Y
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Screenshot que mostra o recém-criado ficheiro FixAzureVM.cmd onde atualiza o nome de utilizador e a palavra-passe.":::
   
    Tem de cumprir os requisitos de complexidade de palavra-passe configurados para o seu VM ao definir a nova palavra-passe.

6. No portal Azure, retire o disco do VM de resolução de problemas.

7. [Altere o disco DE para o VM afetado](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Depois de o novo VM estar em execução, ligue-se ao VM utilizando o Remote Desktop com a nova palavra-passe especificada no `FixAzureVM.cmd` script.

9. Da sua sessão remota para o novo VM, remova os seguintes ficheiros para limpar o ambiente:
    
    * De %windir%\System32\GroupPolicy\Machine\Scripts\Startup
      * remover FixAzureVM.cmd
    * De %windir%\System32\GroupPolicy\Machine\Scripts
      * remover scripts.ini
    * De %windir%\System32\GroupPolicy
      * remova gpt.ini (se gpt.ini existisse antes, e rebatizá-lo para gpt.ini.bak, rebatizar o ficheiro .bak de volta para gpt.ini)

## <a name="detailed-steps-for-classic-vm"></a>Passos detalhados para O VM Clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Os passos não se aplicam aos controladores de domínio do Windows. Funciona apenas em servidor autónomo ou num servidor que é membro de um domínio.

Tente sempre redefinir uma palavra-passe utilizando o [portal Azure ou Azure PowerShell](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) antes de experimentar os seguintes passos. Certifique-se de que tem uma cópia de segurança do seu VM antes de começar. 

1. Elimine o VM afetado no portal Azure. A eliminação do VM apenas elimina os metadados, a referência do VM dentro do Azure. Os discos virtuais são retidos quando o VM é eliminado:
   
   * Selecione o VM no portal Azure e, em seguida, clique em *Eliminar*:
     
     :::image type="content" source="./media/reset-local-password-without-agent/delete-vm-classic.png" alt-text="Eliminar o VM clássico existente":::

2. Fixe o disco de oss de origem da VM ao VM de resolução de problemas. O VM de resolução de problemas deve situar-se na mesma região que o disco de oss da origem VM (tais `West US` como):
   
   1. Selecione o VM de resolução de problemas no portal Azure. Clique *em Discos*  |  *Anexar existentes:*
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-existing-classic.png" alt-text="Anexar o disco existente - Clássico":::
     
   2. Selecione *O Ficheiro VHD* e, em seguida, selecione a conta de armazenamento que contém a sua fonte VM:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-storage-account-classic.png" alt-text="Selecione conta de armazenamento - Clássico":::
     
   3. Verifique a caixa marcada *Mostrar contas de armazenamento clássicas* e, em seguida, selecione o recipiente de origem. O recipiente de origem é tipicamente *vhds:*
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-classic.png" alt-text="Selecione recipiente de armazenamento - Clássico":::

      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png" alt-text="Selecione recipiente de armazenamento - VHD - Clássico":::
     
   4. Selecione o OS vhd para anexar. Clique *em Selecionar* para concluir o processo:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png" alt-text="Selecione disco virtual de origem - Clássico":::

   5. Clique em Ok para anexar o disco

      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-okay-classic.png" alt-text="Anexar o disco existente - DIÁLOGO OK - Clássico":::

3. Ligue-se ao VM de resolução de problemas utilizando o Ambiente de Trabalho Remoto e certifique-se de que o disco oss da fonte VM está visível:

   1. Selecione o VM de resolução de problemas no portal Azure e clique em *Connect*.

   2. Abra o ficheiro RDP que descarrega. Introduza o nome de utilizador e a palavra-passe do VM de resolução de problemas.

   3. No File Explorer, procure o disco de dados que anexou. Se o VHD da fonte vM é o único disco de dados ligado ao VM de resolução de problemas, deve ser o F: unidade:
     
      :::image type="content" source="./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png" alt-text="Ver disco de dados anexado":::

4. Crie `gpt.ini` `\Windows\System32\GroupPolicy` na unidade da fonte VM (se `gpt.ini` existir, renomear `gpt.ini.bak` para):
   
   > [!WARNING]
   > Certifique-se de que não cria acidentalmente os seguintes `C:\Windows` ficheiros, a unidade de SISTEMA para a resolução de problemas em VM. Crie os seguintes ficheiros na unidade so para o seu VM de origem que está anexado como um disco de dados.
   
   * Adicione as seguintes linhas no `gpt.ini` ficheiro que criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini-classic.png" alt-text="Criar gpt.ini - Clássico":::

5. Criar `scripts.ini` em `\Windows\System32\GroupPolicy\Machine\Scripts\` . Certifique-se de que as pastas ocultas são mostradas. Se necessário, crie as `Machine` pastas ou `Scripts` as pastas.
   
   * Adicione as seguintes linhas do `scripts.ini` ficheiro que criou:

     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-classic-1.png" alt-text="Criar scripts.ini - Clássico":::

6. Crie `FixAzureVM.cmd` `\Windows\System32\GroupPolicy\Machine\Scripts\Startup\` com os seguintes conteúdos, substituindo `<username>` e pelos seus `<newpassword>` próprios valores:
   
    ```
    net user <username> <newpassword> /add /Y
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Criar FixAzureVM.cmd - Clássico":::
   
    Tem de cumprir os requisitos de complexidade de palavra-passe configurados para o seu VM ao definir a nova palavra-passe.

7. No portal Azure, retire o disco do VM de resolução de problemas:
   
   1. Selecione o VM de resolução de *problemas* no portal Azure, clique em Discos .
   
   2. Selecione o disco de dados anexado no passo 2, clique em **Retirar** e, em seguida, clique em **OK**.

     :::image type="content" source="./media/reset-local-password-without-agent/data-disks-classic.png" alt-text="Disco de desprendimento - Resolução de Problemas VM - Clássico":::
     
     :::image type="content" source="./media/reset-local-password-without-agent/detach-disk-classic.png" alt-text="Disco de desprendimento - Resolução de Problemas VM - Ok diálogo - Clássico":::

8. Criar um VM a partir do disco oss da fonte VM:
   
     :::image type="content" source="./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png" alt-text="Criar um VM a partir do modelo - Clássico":::

     :::image type="content" source="./media/reset-local-password-without-agent/choose-subscription-classic.png" alt-text="Criar um VM a partir do modelo - Escolha a Subscrição - Clássico":::

     :::image type="content" source="./media/reset-local-password-without-agent/create-vm-classic.png" alt-text="Criar um VM a partir do modelo - Criar VM - Clássico":::

## <a name="complete-the-create-virtual-machine-experience"></a>Complete a experiência de máquina virtual Create

1. Depois de o novo VM estar em execução, ligue-se ao VM utilizando o Remote Desktop com a nova palavra-passe especificada no `FixAzureVM.cmd` script.

2. Da sua sessão remota para o novo VM, remova os seguintes ficheiros para limpar o ambiente:
    
    * De `%windir%\System32\GroupPolicy\Machine\Scripts\Startup\`
      * remover `FixAzureVM.cmd`
    * De `%windir%\System32\GroupPolicy\Machine\Scripts`
      * remover `scripts.ini`
    * De `%windir%\System32\GroupPolicy`
      * remover `gpt.ini` (se `gpt.ini` existisse antes, e renomeou-o `gpt.ini.bak` para, renomear o `.bak` ficheiro de volta para `gpt.ini` )

## <a name="next-steps"></a>Passos seguintes
Se ainda não conseguir ligar-se através do Ambiente de Trabalho Remoto, consulte o [guia de resolução de problemas RDP](troubleshoot-rdp-connection.md). O [guia detalhado de resolução de problemas rdp](detailed-troubleshoot-rdp.md) analisa métodos de resolução de problemas em vez de passos específicos. Também pode abrir um pedido de [apoio ao Azure](https://azure.microsoft.com/support/options/) para assistência prática.

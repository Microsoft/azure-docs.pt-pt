---
title: Redefinir uma senha local do Windows sem agente do Azure | Documentos da Microsoft
description: Como repor a palavra-passe de uma conta de utilizador do Windows local quando o agente convidado do Azure não está instalado ou está a funcionar numa VM
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6b77ceb2ab9abe232cec75254b30ce37c3dbbf60
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105612"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Repor a palavra-passe do Windows local offline para VM do Azure
Pode redefinir a senha do Windows local de uma VM no Azure com o [portal do Azure ou do Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) desde que o agente convidado do Azure está instalado. Esse método é a principal maneira para repor uma palavra-passe de uma VM do Azure. Se ocorrerem problemas com o agente convidado do Azure não está a responder ou deixar de instalar depois de carregar uma imagem personalizada, pode repor manualmente uma palavra-passe do Windows. Este artigo fornece detalhes sobre como repor uma palavra-passe da conta local ao anexar o disco virtual de origem do SO a outra VM. Os passos descritos neste artigo não se aplicam aos controladores de domínio do Windows. 

> [!WARNING]
> Utilize este processo apenas como último recurso. Sempre tenta repor uma palavra-passe através da [portal do Azure ou do Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) primeiro.
> 
> 

## <a name="overview-of-the-process"></a>Descrição geral do processo
As principais etapas para a execução de uma palavra-passe local repor para uma VM do Windows no Azure, quando não é possível aceder ao agente convidado do Azure é o seguinte:

* Elimine a VM de origem. Os discos virtuais são mantidos.
* Anexe o disco do SO da VM de origem para outra VM na mesma localização na sua subscrição do Azure. Esta VM é referida como a VM de resolução de problemas.
* Com a VM de resolução de problemas, crie alguns ficheiros de configuração no disco de SO da VM de origem.
* Desanexe disco de SO da VM da VM de resolução de problemas.
* Utilize um modelo do Resource Manager para criar uma VM, com o disco virtual original.
* Quando é inicializado a nova VM, os ficheiros de configuração que cria atualizar a palavra-passe do utilizador necessária.

> [!NOTE]
> Pode automatizar os processos seguintes:
>
> - Criar a VM de resolução de problemas
> - Anexar o disco do SO
> - Volte a criar a VM original
> 
> Para tal, utilize o [Scripts de recuperação de VM do Azure](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Se optar por utilizar os Scripts de recuperação de VM do Azure, pode utilizar o seguinte processo na secção "Passos detalhados":
> 1. Ignorar os passos 1 e 2 usando os scripts para anexar o disco do SO da VM afetada para uma VM de recuperação.
> 2. Siga os passos 3 a 6 para aplicar as atenuações.
> 3. Ignore os passos 7 a 9 usando os scripts para recriar a VM.
> 4. Siga os passos 10 e 11.

## <a name="detailed-steps"></a>Passos detalhados

> [!NOTE]
> Os passos não se aplicam aos controladores de domínio do Windows. Ele só funciona num servidor autónomo ou um servidor que seja membro de um domínio.
> 
> 

Sempre tenta repor uma palavra-passe através da [portal do Azure ou do Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de tentar os seguintes passos. Certifique-se de que tem uma cópia de segurança da sua VM antes de começar. 

1. Elimine a VM afetada no portal do Azure. A eliminar a VM elimina apenas os metadados, a referência da VM no Azure. Os discos virtuais são mantidos quando a VM é eliminada:
   
   * Selecione a VM no portal do Azure, clique em *eliminar*:
     
     ![Eliminar VM existente](./media/reset-local-password-without-agent/delete_vm.png)
2. Anexe o disco do SO da VM de origem para a VM de resolução de problemas. A VM de resolução de problemas tem de estar na mesma região que o disco do SO da VM de origem (como `West US`):
   
   * Selecione a VM de resolução de problemas no portal do Azure. Clique em *discos* | *Attach existente*:
     
     ![Anexar disco existente](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Selecione *ficheiro VHD* e, em seguida, selecione a conta de armazenamento que contém a VM de origem:
     
     ![Selecionar a conta de armazenamento](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Selecione o contentor de origem. O contentor de origem é normalmente *vhds*:
     
     ![Selecione o contentor de armazenamento](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Selecione o vhd do SO para anexar. Clique em *selecione* para concluir o processo:
     
     ![Selecione o disco virtual de origem](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Ligar à VM de resolução de problemas com o ambiente de trabalho remoto e certifique-se de que o disco de SO da VM de origem está visível:
   
   * Selecione a VM de resolução de problemas no portal do Azure e clique em *Connect*.
   * Abra o ficheiro RDP que transfere. Introduza o nome de utilizador e palavra-passe da VM de resolução de problemas.
   * No Explorador de ficheiros, procure o disco de dados que é anexado. Se a origem de VHD da VM é o disco de dados única anexado à VM de resolução de problemas, deve ser a unidade f::
     
     ![Ver o disco de dados anexados](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Crie `gpt.ini` em `\Windows\System32\GroupPolicy` no disco da VM de origem (se existir o GPT, mudar o nome para gpt.ini.bak):
   
   > [!WARNING]
   > Certifique-se de que acidentalmente cria os seguintes ficheiros em C:\Windows, a unidade do SO para a VM de resolução de problemas. Crie os seguintes ficheiros na unidade de SO para a VM que está anexado como um disco de dados de origem.
   > 
   > 
   
   * Adicione as seguintes linhas para o `gpt.ini` ficheiro que criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Criar GPT](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. Crie `scripts.ini` em `\Windows\System32\GroupPolicy\Machine\Scripts\Startup`. Certifique-se de pastas ocultas são mostradas. Se necessário, crie o `Machine` ou `Scripts` pastas.
   
   * Adicione as seguintes linhas a `scripts.ini` ficheiro que criou:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Criar scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Crie `FixAzureVM.cmd` no `\Windows\System32` com o conteúdo seguinte, substituindo `<username>` e `<newpassword>` pelos seus próprios valores:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Ao definir a nova palavra-passe tem de cumprir os requisitos de complexidade de palavra-passe configurado para a sua VM.
7. No portal do Azure, desanexe o disco da VM de resolução de problemas:
   
   * Selecione a VM de resolução de problemas no portal do Azure, clique em *discos*.
   * Selecione o disco de dados ligado no passo 2, clique em *anulação de exposições*:
     
     ![Desanexar disco](./media/reset-local-password-without-agent/detach_disk.png)
8. Antes de criar uma VM, obtenha o URI para o disco do SO de origem:
   
   * Selecione a conta de armazenamento no portal do Azure, clique em *Blobs*.
   * Selecione o contentor. O contentor de origem é normalmente *vhds*:
     
     ![Selecione o blob da conta de armazenamento](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Selecione a VHD do SO de VM de origem e clique nas *cópia* junto aos *URL* nome:
     
     ![Copie o URI do disco](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Crie uma VM a partir do disco de SO da VM de origem:
   
   * Uso [este modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet) para criar uma VM a partir de um VHD especializado. Clique no `Deploy to Azure` botão para abrir o portal do Azure com os detalhes com modelos preenchidos para si.
   * Se pretende manter todas as definições anteriores para a VM, selecione *Editar modelo* para fornecer a sua VNet existente, a sub-rede, a placa de rede ou o IP público.
   * Na `OSDISKVHDURI` caixa de texto de parâmetro, colar o URI do VHD de origem de se obter no passo anterior:
     
     ![Criar uma VM a partir de modelo](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Depois da nova VM está em execução, ligar-se para a VM com o ambiente de trabalho remoto com a nova palavra-passe que especificou no `FixAzureVM.cmd` script.
11. Da sua sessão remota para a nova VM, remova os seguintes ficheiros para limpar o ambiente:
    
    * De %windir%\System32
      * remove FixAzureVM.cmd
    * De %windir%\System32\GroupPolicy\Machine\
      * Remover scripts.ini
    * De %windir%\System32\GroupPolicy
      * remover GPT (se GPT existido e renomeou gpt.ini.bak, mudar o nome do ficheiro. bak de volta para o GPT)

## <a name="next-steps"></a>Passos Seguintes
Se ainda não é possível estabelecer ligação com o ambiente de trabalho remoto, consulte a [guia de resolução de problemas do RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). O [detalhadas guia de resolução de problemas de RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) analisa a resolução de problemas de métodos em vez de etapas específicas. Também pode [abra um pedido de suporte do Azure](https://azure.microsoft.com/support/options/) para assistência prática.


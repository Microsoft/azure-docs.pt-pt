---
title: Instale o Agente VM Azure em modo offline Microsoft Docs
description: Saiba como instalar o Agente VM Azure no modo offline.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/06/2020
ms.author: genli
ms.openlocfilehash: 456aa225fa8eed47ca794c54e61b77a30c93fa9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002617"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instale o Agente de Máquina Virtual Azure em modo offline 

O Agente de Máquina Virtual Azure (Agente VM) fornece funcionalidades úteis, tais como reset de senha de administrador local e push de script. Este artigo mostra-lhe como instalar o Agente VM para uma máquina virtual offline do Windows (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Quando utilizar o Agente VM em modo offline

Instale o Agente VM no modo offline nos seguintes cenários:

- O Azure VM implantado não tem o Agente VM instalado ou o agente não está a funcionar.
- Esqueceu-se da senha do administrador para o VM ou não pode aceder ao VM.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Como instalar o Agente VM em modo offline

Utilize os seguintes passos para instalar o Agente VM no modo offline.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Passo 1: Fixe o disco de oss do VM a outro VM como disco de dados

1. Tire uma fotografia para o disco de SO do VM afetado, crie um disco a partir do instantâneo e, em seguida, prenda o disco a um VM de resolução de problemas. Para obter mais informações, consulte [Troubleshoot a Windows VM, fixando o disco OS a um VM de recuperação utilizando o portal Azure](troubleshoot-recovery-disks-portal-windows.md). Para o VM clássico, elimine o VM e mantenha o disco oss e, em seguida, prenda o disco de oss ao VM de resolução de problemas.

2.  Ligue-se ao VM da resolução de problemas. Gestão de discos **de gestão de**  >  **computadores abertos.** Confirme que o disco DE está on-line e que as letras de unidade são atribuídas às divisórias de disco.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Passo 2: Modificar o disco DE para instalar o Agente VM Azure

1.  Faça uma ligação de ambiente de trabalho remoto ao VM de resolução de problemas.

2.  No VM de resolução de problemas, navegue no disco OS que anexou, abra a pasta \windows\system32\config. Copie todos os ficheiros desta pasta como uma cópia de segurança, caso seja necessário um reversão.

3.  Inicie o **Editor de Registo** (regedit.exe).

4.  Selecione a **tecla HKEY_LOCAL_MACHINE.** No menu, selecione **File**  >  **Load Hive**:

    ![Carregue a colmeia](./media/install-vm-agent-offline/load-hive.png)

5.  Navegue na pasta \windows\system32\config\SYSTEM no disco OS que anexou. Para o nome da colmeia, **insira BROKENSYSTEM**. A nova colmeia de registo é exibida sob a tecla **HKEY_LOCAL_MACHINE.**

6.  Navegue na pasta \windows\system32\config\SOFTWARE no disco OS que anexou. Para o nome do software de colmeia, **insira BROKENSOFTWARE**.

7. Se o disco OS anexado tiver o agente VM instalado, execute uma cópia de segurança da configuração atual. Se não tiver o agente VM instalado, passe para o passo seguinte.
      
    1. Mude o nome da pasta \windowsazure para \windowsazure.old.

    2. Exportar os seguintes registos:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Utilize os ficheiros existentes no VM de resolução de problemas como repositório para a instalação do Agente VM. Siga os seguintes passos:

    1. A partir do resolução de problemas VM, exporte as seguintes sub-teias em formato de registo (.reg): 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Serviços\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Serviços\RdAgent

          ![Exportar as sub-chaves de registo](./media/install-vm-agent-offline/backup-reg.png)

    2. Edite os ficheiros de registo. Em cada ficheiro, altere o **sistema** de valor de entrada para **BROKENSYSTEM** (como mostrado nas seguintes imagens) e guarde o ficheiro. Lembre-se do **ImagePath** do agente VM atual. Teremos de copiar a pasta correspondente ao disco oss anexado. 

        ![Alterar os valores sub-chave do registo](./media/install-vm-agent-offline/change-reg.png)

    3. Importe os ficheiros de registo no repositório clicando duas vezes em cada ficheiro de registo.

    4. Confirme que os dois sub-atecos seguintes são importados com sucesso para a colmeia **BROKENSYSTEM:**
        - WindowsAzureGuestAgent
        - RdAgent

    5. Copie a pasta de instalação do atual Agente VM para o disco oss anexado: 

        1.  No disco OS que anexou, crie uma pasta chamada WindowsAzure no caminho raiz.

        2.  Vá a C:\WindowsAzure no VM de resolução de problemas, procure qualquer pasta com o nome C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Copie a pasta GuestAgent que tem o número de versão mais recente de C:\WindowsAzure para a pasta WindowsAzure no disco OS anexado. Se não tiver a certeza de qual a pasta que deve ser copiada, copie todas as pastas GuestAgent. A imagem a seguir mostra um exemplo da pasta GuestAgent que é copiada para o disco oss anexado.

             ![Copiar a pasta GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Selecione **BROKENSYSTEM**. No menu, selecione **File**  >  **Unload Hive**.

10.  Selecione **BROKENSOFTWARE**. No menu, selecione **File**  >  **Unload Hive**.

11.  Retire o disco de SO e, em seguida, [altere o disco de SO para o VM afetado](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Para o VM clássico, crie um novo VM utilizando o disco de OS reparado.

12.  Aceda ao VM. Note que o RdAgent está em funcionamento e os registos estão a ser gerados.

Se criou o VM utilizando o modelo de implementação do Gestor de Recursos, está feito.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Utilize a propriedade ProvisionGuestAgent para VMs clássicos

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Se criou o VM utilizando o modelo clássico, utilize o módulo Azure PowerShell para atualizar a propriedade **ProvisionGuestAgent.** A propriedade informa a Azure que o VM tem o Agente VM instalado.

Para definir a propriedade **ProvisionGuestAgent,** executar os seguintes comandos em Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Então executar o `Get-AzureVM` comando. Note que a propriedade **GuestAgentStatus** está agora povoada com dados:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do agente de máquinas virtuais Azure](../extensions/agent-windows.md)
- [Extensões e funcionalidades de máquinas virtuais para Windows](../extensions/features-windows.md)

---
title: Instale o Agente Azure VM em modo offline  Microsoft Docs
description: Aprenda a instalar o Agente Azure VM em modo offline.
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 8ea85b560f35c79b3d5066d794f587345810b5d0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920863"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instale o Agente De Máquina Virtual Azure em modo offline 

O Agente de Máquinas Virtuais Azure (VM Agent) fornece funcionalidades úteis, tais como reset de palavra-passe do administrador local e pressão de script. Este artigo mostra-lhe como instalar o Agente VM para uma máquina virtual offline do Windows (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Quando utilizar o Agente VM em modo offline

Instale o Agente VM em modo offline nos seguintes cenários:

- O Azure VM implantado não tem o Agente VM instalado ou o agente não está a funcionar.
- Esqueceu-se da senha do administrador para o VM ou não consegue aceder ao VM.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Como instalar o Agente VM em modo offline

Utilize os seguintes passos para instalar o Agente VM no modo offline.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Passo 1: Fixe o disco OS do VM a outro VM como disco de dados

1. Pegue uma foto para o disco OS do VM afetado, crie um disco a partir do instantâneo e, em seguida, prenda o disco a um VM de resolução de problemas. Para mais informações, consulte [Troubleshoot a Windows VM, ligando o disco OS a um VM de recuperação utilizando o portal Azure](troubleshoot-recovery-disks-portal-windows.md). Para o VM clássico, elimine o VM e guarde o disco OS e, em seguida, prenda o disco OS ao VM de resolução de problemas.

2.  Ligue-se ao VM de resolução de problemas. Gestão **de computador** aberto > **gestão do disco.** Confirme que o disco DE SO está on-line e que as cartas de acionamento são atribuídas às divisórias do disco.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Passo 2: Modificar o disco OS para instalar o Agente VM Azure

1.  Faça uma ligação remota ao vm de resolução de problemas.

2.  No VM de resolução de problemas, navegue para o disco OS que anexou, abra a pasta \windows\system32\config. Copie todos os ficheiros desta pasta como cópia de segurança, caso seja necessário um reversão.

3.  Inicie o **Editor de Registo** (regedit.exe).

4.  Selecione a tecla **HKEY_LOCAL_MACHINE.** No menu, selecione **File** > **Load Hive:**

    ![Carregue a colmeia](./media/install-vm-agent-offline/load-hive.png)

5.  Navegue na pasta \windows\system32\config\SYSTEM no disco OS que anexou. Para o nome da colmeia, introduza **BROKENSYSTEM**. A nova colmeia de registo é exibida sob a chave **HKEY_LOCAL_MACHINE.**

6.  Navegue na pasta \windows\system32\config\SOFTWARE no disco OS que anexou. Para o nome do software da colmeia, **introduza BROKENSOFTWARE**.

7. Se o disco Os Anexo tiver o agente VM instalado, execute uma cópia de segurança da configuração atual. Se não tiver o agente VM instalado, passe para o próximo passo.
      
    1. Mude o nome da pasta \windowsazure para \windowsazure.old.

    2. Exportar os seguintes registos:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemettryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Utilize os ficheiros existentes no VM de resolução de problemas como um repositório para a instalação do Agente VM. Conclua os seguintes passos:

    1. A partir do Problemático VM, exporte os seguintes subchaves em formato de registo (.reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![Exportar as subchaves do registo](./media/install-vm-agent-offline/backup-reg.png)

    2. Editar os ficheiros do registo. Em cada ficheiro, altere o **SISTEMA** de valor de entrada para **BROKENSYSTEM** (conforme mostrado nas seguintes imagens) e guarde o ficheiro. Lembre-se do **ImagePath** do atual agente VM. Teremos de copiar a pasta correspondente para o disco OS anexo. 

        ![Alterar os valores sub-chave do registo](./media/install-vm-agent-offline/change-reg.png)

    3. Importe os ficheiros de registo no repositório clicando duas vezes em cada ficheiro de registo.

    4. Confirme que as seguintes três subteclas são importadas com êxito para a colmeia **BROKENSYSTEM:**
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. Copie a pasta de instalação do atual Agente VM para o disco OS anexo: 

        1.  No disco OS que anexou, crie uma pasta chamada WindowsAzure no caminho raiz.

        2.  Vá ao C:\WindowsAzure no VM de resolução de problemas, procure qualquer pasta com o nome C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Copie a pasta GuestAgent que tem o mais recente número de versão de C:\WindowsAzure para a pasta WindowsAzure no disco OS anexo. Se não tiver a certeza de qual a pasta deve ser copiada, copie todas as pastas Do GuestAgent. A imagem que se segue mostra um exemplo da pasta GuestAgent que é copiada para o disco OS anexado.

             ![Copiar pasta GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Selecione **BrokenSYSTEM**. No menu, selecione **File** > **Descarregar Colmeia**.

10.  Selecione **brokensoftware**. No menu, selecione **File** > **Descarregar Colmeia**.

11.  Retire o disco OS [e,](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm)em seguida, mude o disco OS para o VM afetado . Para o VM clássico, crie um novo VM utilizando o disco OS reparado.

12.  Aceda ao VM. Note que o Agente Rd está em execução e os registos estão a ser gerados.

Se criou o VM utilizando o modelo de implementação do Gestor de Recursos, está feito.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Utilize a propriedade ProvisionGuestAgent para VMs clássicos

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Se criou o VM utilizando o modelo clássico, utilize o módulo Azure PowerShell para atualizar a propriedade **ProvisionGuestAgent.** A propriedade informa azure que o VM tem o VM Agent instalado.

Para definir a propriedade **ProvisionGuestAgent,** execute os seguintes comandos em Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Em seguida, executar o comando `Get-AzureVM`. Note que a propriedade **GuestAgentStatus** está agora povoada com dados:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do Agente De MáquinaS Virtuais Azure](../extensions/agent-windows.md)
- [Extensões e funcionalidades de máquinavirtual para Windows](../extensions/features-windows.md)

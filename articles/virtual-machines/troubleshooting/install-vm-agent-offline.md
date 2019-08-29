---
title: Instalar o agente de VM do Azure no modo offline | Microsoft Docs
description: Saiba como instalar o agente de VM do Azure no modo offline.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 018f27999c4f69c0868e2a8fde6535346c7d0377
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090368"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instalar o agente de máquina virtual do Azure no modo offline 

O agente de máquina virtual do Azure (agente de VM) fornece recursos úteis, como redefinição de senha de administrador local e envio de script. Este artigo mostra como instalar o agente de VM para uma VM (máquina virtual) do Windows offline. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Quando usar o agente de VM no modo offline

Instale o agente de VM no modo offline nos seguintes cenários:

- A VM do Azure implantada não tem o agente de VM instalado ou o agente não está funcionando.
- Você esqueceu a senha de administrador da VM ou não consegue acessar a VM.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Como instalar o agente de VM no modo offline

Use as etapas a seguir para instalar o agente de VM no modo offline.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Passo 1: Anexar o disco do sistema operacional da VM a outra VM como um disco de dados

1. Tire um instantâneo do disco do sistema operacional da VM afetada, crie um disco do instantâneo e, em seguida, anexe o disco a uma VM de solução de problemas. Para obter mais informações, consulte [solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure](troubleshoot-recovery-disks-portal-windows.md). Para a VM clássica, exclua a VM e mantenha o disco do sistema operacional e anexe o disco do sistema operacional à VM de solução de problemas.

2.  Conecte-se à VM de solução de problemas. Abra gerenciamento de **computador** > gerenciamento de**disco**. Confirme se o disco do sistema operacional está online e se as letras das unidades estão atribuídas às partições de disco.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Passo 2: Modificar o disco do sistema operacional para instalar o agente de VM do Azure

1.  Faça uma conexão de área de trabalho remota com a VM de solução de problemas.

2.  Na VM do solucionador de problemas, navegue até o disco do sistema operacional que você anexou, abra a pasta \Windows\System32\config. Copie todos os arquivos desta pasta como um backup, caso uma reversão seja necessária.

3.  Inicie o **Editor do registro** (regedit. exe).

4.  Selecione a chave **HKEY_LOCAL_MACHINE** . No menu, selecione **arquivo** > **Carregar Hive**:

    ![Carregar o hive](./media/install-vm-agent-offline/load-hive.png)

5.  Navegue até a pasta \windows\system32\config\SYSTEM no disco do sistema operacional que você anexou. Para o nome do hive, insira **BROKENSYSTEM**. O novo hive do registro é exibido sob a chave **HKEY_LOCAL_MACHINE** .

6.  Navegue até a pasta \windows\system32\config\SOFTWARE no disco do sistema operacional que você anexou. Para o nome do software do hive, insira **BROKENSOFTWARE**.

7. Se o disco do sistema operacional anexado tiver o agente de VM instalado, execute um backup da configuração atual. Se ele não tiver o agente de VM instalado, vá para a próxima etapa.
      
    1. Renomeie a pasta \windowsazure para \windowsazure.old.

    2. Exporte os seguintes registros:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Use os arquivos existentes na VM do solucionador de problemas como um repositório para a instalação do agente de VM. Conclua os seguintes passos:

    1. Na VM do solucionador de problemas, exporte as seguintes subchaves no formato de registro (. reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![Exportar as subchaves do registro](./media/install-vm-agent-offline/backup-reg.png)

    2. Edite os arquivos do registro. Em cada arquivo, altere o **sistema** de valor de entrada para **BROKENSYSTEM** (conforme mostrado nas imagens a seguir) e salve o arquivo. Lembre-se do **ImagePath** do agente de VM atual. Será necessário copiar a pasta correspondente para o disco do sistema operacional anexado. 

        ![Alterar os valores da subchave do registro](./media/install-vm-agent-offline/change-reg.png)

    3. Importe os arquivos do registro para o repositório clicando duas vezes em cada arquivo do registro.

    4. Confirme se as três subchaves a seguir foram importadas com êxito para o hive **BROKENSYSTEM** :
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. Copie a pasta de instalação do agente de VM atual para o disco do sistema operacional anexado: 

        1.  No disco do sistema operacional que você anexou, crie uma pasta chamada WindowsAzure no caminho raiz.

        2.  Vá para C:\WindowsAzure na VM do solucionador de problemas, procure qualquer pasta com o nome C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Copie a pasta GuestAgent que tem o número de versão mais recente de C:\WindowsAzure para a pasta WindowsAzure no disco do sistema operacional anexado. Se você não tiver certeza de qual pasta deve ser copiada, copie todas as pastas GuestAgent. A imagem a seguir mostra um exemplo da pasta GuestAgent que é copiada para o disco do sistema operacional anexado.

             ![Copiar pasta GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Selecione **BROKENSYSTEM**. No menu, selecione **arquivo** > **Descarregar Hive**.

10.  Selecione **BROKENSOFTWARE**. No menu, selecione **arquivo** > **Descarregar Hive**.

11.  Desanexe o disco do sistema operacional e, em seguida, [altere o disco do sistema operacional para a VM afetada](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Para a VM clássica, crie uma nova VM usando o disco do sistema operacional reparado.

12.  Acesse a VM. Observe que o RdAgent está em execução e os logs estão sendo gerados.

Se você criou a VM usando o modelo de implantação do Gerenciador de recursos, você concluiu.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Usar a propriedade ProvisionGuestAgent para VMs clássicas

Se você criou a VM usando o modelo clássico, use o módulo Azure PowerShell para atualizar a propriedade **ProvisionGuestAgent** . A propriedade informa ao Azure que a VM tem o agente de VM instalado.

Para definir a propriedade **ProvisionGuestAgent** , execute os seguintes comandos no Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Em seguida, `Get-AzureVM` execute o comando. Observe que a propriedade **GuestAgentStatus** agora está populada com dados:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do agente de máquina virtual do Azure](../extensions/agent-windows.md)
- [Recursos e extensões de máquina virtual para Windows](../extensions/features-windows.md)

---
title: Instale o agente de VM do Azure no modo offline | Documentos da Microsoft
description: Saiba como instalar o agente da VM do Azure no modo offline.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e9fc8351b5e9a4f2274f0906d4071f86dcbcff26
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640671"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Instalar o agente de Máquina Virtual do Azure no modo offline 

O agente da Máquina Virtual do Azure (agente de VM) fornece recursos úteis, como a reposição de palavra-passe de administrador local e script enviar por push. Este artigo mostra-lhe como instalar o agente da VM para uma máquina de virtual offline do Windows (VM). 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Quando utilizar o agente da VM no modo offline

Instale o agente de VM no modo offline nos seguintes cenários:

- VM do Azure implementado não tem instalado o agente de VM ou o agente não está a funcionar.
- Se esqueceu a palavra-passe de administrador para a VM ou não é possível aceder à VM.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Como instalar o agente da VM no modo offline

Utilize os seguintes passos para instalar o agente da VM no modo offline.

> [!NOTE]
> Pode automatizar o processo de instalação do agente de VM no modo offline.
> Para tal, utilize o [Scripts de recuperação de VM do Azure](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Se optar por utilizar os Scripts de recuperação de VM do Azure, pode utilizar o seguinte processo:
> 1. Ignore o passo 1 usando os scripts para anexar o disco do SO da VM afetada para uma VM de recuperação.
> 2. Siga os passos 2 a 10 para aplicar as atenuações.
> 3. Ignore o passo 11 usando os scripts para recriar a VM.
> 4. Siga o passo 12.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Passo 1: Anexar o disco do SO da VM para outra VM como um disco de dados

1.  Elimine a VM. Certifique-se de que selecione os **manter os discos** opção ao eliminar a VM.

2.  Anexar o disco do SO como um disco de dados para outra VM (conhecido como um _resolução de problemas de_ VM). Para obter mais informações, consulte [anexar um disco de dados a uma VM do Windows no portal do Azure](../windows/attach-managed-disk-portal.md).

3.  Ligue-se para a VM de resolução de problemas. Open **gestão de computadores** > **gestão de discos**. Confirme que o disco do SO está online e que as letras de unidade são atribuídas a partições de disco.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Passo 2: Modificar o disco do SO para instalar o agente de VM do Azure

1.  Estabelecer uma ligação de ambiente de trabalho remota para a VM de resolução de problemas.

2.  No disco do SO que anexado, navegue até à pasta de \windows\system32\config. Copie todos os ficheiros nesta pasta como uma cópia de segurança, caso seja necessária uma reversão.

3.  Iniciar o **Editor de registo** (regedit.exe).

4.  Selecione o **HKEY_LOCAL_MACHINE** chave. No menu, selecione **arquivo** > **carregar Hive**:

    ![Carregar o hive](./media/install-vm-agent-offline/load-hive.png)

5.  Navegue até à pasta de \windows\system32\config\SYSTEM no disco do SO que anexados. Para o nome da colmeia, introduza **BROKENSYSTEM**. O novo ramo de registo é apresentado no **HKEY_LOCAL_MACHINE** chave.

6.  Navegue até à pasta de \windows\system32\config\SOFTWARE no disco do SO que anexados. Para o nome do software do hive, introduza **BROKENSOFTWARE**.

7. Se o disco do SO anexado tem instalado o agente VM, execute uma cópia de segurança da configuração atual. Se não tiver instalado o agente da VM, mova para o passo seguinte.
      
    1. Renomeie a pasta de \windowsazure para \windowsazure.old.

    2. Exporte registos seguintes:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Utilize os ficheiros existentes na VM de resolução de problemas como um repositório para a instalação do agente de VM. Conclua os seguintes passos:

    1. De que a resolução de problemas VM, exporte as subchaves seguintes no formato de registo (. reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![Exportar as subchaves de registo](./media/install-vm-agent-offline/backup-reg.png)

    2. Edite os ficheiros de registo. Em cada arquivo, altere o valor de entrada **SYSTEM** ao **BROKENSYSTEM** (conforme mostrado nas imagens seguintes) e guarde o ficheiro. Lembre-se a **ImagePath** do agente VM atual. Será necessário copiar a pasta correspondente para o disco do SO anexado. 

        ![Alterar os valores de subchave de registo](./media/install-vm-agent-offline/change-reg.png)

    3. Importe os ficheiros de registo para o repositório fazendo duplo clique em cada ficheiro de registo.

    4. Confirme se as subchaves de três seguintes são importadas com êxito para o **BROKENSYSTEM** hive:
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. Copie a pasta de instalação do agente de VM atual para o disco do SO anexado: 

        1.  No disco do SO que anexados, crie uma pasta chamada Windows Azure no caminho raiz.

        2.  Ir para C:\WindowsAzure sobre a VM de resolução de problemas, procure qualquer pasta com o nome C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX. Copie a pasta de GuestAgent que tem o número da versão mais recente do C:\WindowsAzure para a pasta do Windows Azure no disco do SO anexado. Se não tiver a certeza de que pasta deve ser copiada, copie todas as pastas de GuestAgent. A imagem seguinte mostra um exemplo da pasta GuestAgent que é copiado para o disco do SO anexado.

             ![Copie a pasta de GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Selecione **BROKENSYSTEM**. No menu, selecione **arquivo** > **descarregar seção**.

10.  Selecione **BROKENSOFTWARE**. No menu, selecione **arquivo** > **descarregar seção**.

11.  Desanexar o disco do SO e, em seguida, recriar a VM ao utilizar o disco do SO.

12.  Aceda à VM. Tenha em atenção que o RdAgent está em execução e os registos estão a ser gerados.

Se criou a VM ao utilizar o modelo de implementação do Resource Manager, está concluído.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Utilize a propriedade de ProvisionGuestAgent para VMs clássicas

Se criou a VM com o modelo clássico, utilize o módulo Azure PowerShell para atualizar o **ProvisionGuestAgent** propriedade. A propriedade informa o Azure que a VM tem o agente da VM instalado.

Para definir o **ProvisionGuestAgent** propriedade, execute os seguintes comandos do Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Em seguida, execute o `Get-AzureVM` comando. Tenha em atenção que o **GuestAgentStatus** propriedade agora é preenchida com dados:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral do agente da Máquina Virtual do Azure](../extensions/agent-windows.md)
- [Extensões de máquina virtual e funcionalidades para Windows](../extensions/features-windows.md)

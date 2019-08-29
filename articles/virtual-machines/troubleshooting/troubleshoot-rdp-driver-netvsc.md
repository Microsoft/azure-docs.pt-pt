---
title: Solucionar problemas de um problema de netvsc. sys ao se conectar remotamente a uma VM do Windows 10 ou Windows Server 2016 no Azure | Microsoft Docs
description: Saiba como solucionar problemas de um problema de RDP relacionado ao netsvc. sys ao se conectar a uma VM do Windows 10 ou Windows Server 2016 no Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 6e68aac07379de142968b85884e7dbd95e73195f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103463"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Não é possível se conectar remotamente a uma VM do Windows 10 ou Windows Server 2016 no Azure devido ao netvsc. sys

Este artigo explica como solucionar um problema no qual não há conexão de rede quando você se conecta a uma VM (máquina virtual) do Windows 10 ou do Windows Server 2016 datacenter em um host do Hyper-V Server 2016.

## <a name="symptoms"></a>Sintomas

Você não pode se conectar a uma VM do Windows 10 ou Windows Server 2016 do Azure usando protocolo RDP (RDP). No [diagnóstico de inicialização](boot-diagnostics.md), a tela mostra uma cruz vermelha sobre a placa de interface de rede (NIC). Isso indica que a VM não tem conectividade após o sistema operacional ser totalmente carregado.

Normalmente, esse problema ocorre no Windows [build 14393](https://support.microsoft.com/help/4093120/) e no [Build 15063](https://support.microsoft.com/help/4015583/). Se a versão do seu sistema operacional for posterior a essas versões, este artigo não se aplicará ao seu cenário. Para verificar a versão do sistema, abra uma sessão CMD no [recurso do console de acesso serial](serial-console-windows.md)e execute **Ver**.

## <a name="cause"></a>Causa

Esse problema pode ocorrer se a versão do arquivo do sistema netvsc. sys instalado for **10.0.14393.594** ou **10.0.15063.0**. Essas versões do netvsc. sys podem impedir que o sistema interaja com a plataforma do Azure.


## <a name="solution"></a>Solução

Antes de seguir estas etapas, [tire um instantâneo do disco do sistema](../windows/snapshot-copy-managed-disk.md) da VM afetada como um backup. Para resolver este problema, utilize a consola de série ou [Repare a VM offline](#repair-the-vm-offline) ao anexar o disco do sistema da VM para uma VM de recuperação.


### <a name="use-the-serial-console"></a>Usar o console serial

Conecte-se ao [console serial, abra uma instância do PowerShell](serial-console-windows.md)e siga estas etapas.

> [!NOTE]
> Se a consola de série não estiver ativada na sua VM, vá para o [Repare a VM offline](#repair-the-vm-offline) secção.

1. Execute o seguinte comando em uma instância do PowerShell para obter a versão do arquivo (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Baixe a atualização apropriada para um disco de dados novo ou existente que esteja anexado a uma VM em funcionamento da mesma região:

   - **10.0.14393.594**: [KB4073562 ou uma atualização](https://support.microsoft.com/help/4073562) posterior
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) ou uma atualização posterior

3. Desanexe o disco do utilitário da VM de trabalho e, em seguida, anexe-o à VM quebrada.

4. Execute o seguinte comando para instalar a atualização na VM:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Reinicie a VM.

### <a name="repair-the-vm-offline"></a>Repare a VM Offline

1. [Anexar o disco do sistema para uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).

2. Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.

3. Certifique-se de que o disco é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída para o disco do sistema anexado.

4. Crie uma cópia da pasta **\Windows\System32\config** caso uma reversão das alterações seja necessária.

5. Na VM de resgate, inicie o editor do registro (regedit. exe).

6. Selecione a chave **HKEY_LOCAL_MACHINE** e, em seguida, selecione **arquivo** > **Carregar Hive** no menu.

7. Localize o arquivo do sistema na pasta **\Windows\System32\config** .

8. Selecione **abrir**, digite **BROKENSYSTEM** para o nome, expanda a chave **HKEY_LOCAL_MACHINE** e localize a chave adicional denominada **BROKENSYSTEM**.

9. Vá para o seguinte local:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Em cada subchave (como 0000), examine o valor de **DriverDesc** que é exibido como **adaptador de rede do Microsoft Hyper-V**.

11. Na subchave, examine o valor **DriverVersion** que é a versão do driver do adaptador de rede da VM.

12. Baixe a atualização apropriada:

    - **10.0.14393.594**: [KB4073562 ou uma atualização](https://support.microsoft.com/help/4073562) posterior
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) ou uma atualização posterior

13. Anexe o disco do sistema como um disco de dados em uma VM de resgate na qual você pode baixar a atualização.

14. Execute o seguinte comando para instalar a atualização na VM:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Execute o seguinte comando para desmontar os hives:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Desanexe o disco do sistema e crie a VM novamente](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte

Se você ainda precisar de ajuda, [entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente.

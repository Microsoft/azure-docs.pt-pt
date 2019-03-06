---
title: Resolver um problema de netvsc.sys ao ligar remotamente a um Windows 10 ou VM do Windows Server 2016 no Azure | Documentos da Microsoft
description: Aprenda a solucionar um RDP relacionadas a netsvc.sys emitir quando ligar a um Windows 10 ou VM do Windows Server 2016 no Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: c6918126c36e1940daf564ee7eae562e31b280c3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449109"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Não é possível ligar remotamente a um Windows 10 ou VM do Windows Server 2016 no Azure, devido a netvsc.sys

Este artigo explica como solucionar um problema no qual não haja nenhuma ligação de rede quando se liga a um Windows 10 ou Windows Server 2016 Datacenter de uma máquina virtual (VM) num anfitrião Hyper-V Server 2016.

## <a name="symptoms"></a>Sintomas

É possível ligar a um Azure Windows 10 ou VM do Windows Server 2016 utilizando o protocolo RDP (Remote Desktop). Na [diagnósticos de arranque](boot-diagnostics.md), o ecrã mostra uma cruz vermelha sobre a placa de interface de rede (NIC). Isto indica que a VM não tem conectividade depois do sistema operacional está totalmente carregado.

Normalmente, este problema ocorre no Windows [criar 14393](http://support.microsoft.com/help/4093120/) e [criar 15063](http://support.microsoft.com/help/4015583/). Se a versão do seu sistema operativo for posterior a essas versões, este artigo não é aplicável ao seu cenário. Para verificar a versão do sistema, abra uma sessão CMD na [a funcionalidade de consola de acesso de série](serial-console-windows.md)e, em seguida, execute **vidor**.

## <a name="cause"></a>Causa

Este problema pode ocorrer se a versão do ficheiro de sistema instalados netvsc.sys for **10.0.14393.594** ou **10.0.15063.0**. Estas versões do netvsc.sys poderão impedir o sistema de interagir com a plataforma do Azure.


## <a name="solution"></a>Solução

Antes de seguir estes passos [tirar um instantâneo do disco de sistema](../windows/snapshot-copy-managed-disk.md) da VM afetada como uma cópia de segurança. Para resolver este problema, utilize a consola de série ou [Repare a VM offline](#repair-the-vm-offline) ao anexar o disco do sistema da VM para uma VM de recuperação.


### <a name="use-the-serial-console"></a>Utilizar a consola de série

Ligar à [da consola de série, abra uma instância de PowerShell](serial-console-windows.md)e, em seguida, siga estes passos.

> [!NOTE]
> Se a consola de série não estiver ativada na sua VM, vá para o [Repare a VM offline](#repair-the-vm-offline) secção.

1. Execute o seguinte comando numa instância do PowerShell para obter a versão do ficheiro (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Transferir a atualização adequada para um disco de dados nova ou existente que está ligado a uma VM em funcionamento da mesma região:

   - **10.0.14393.594**: [KB4073562](http://support.microsoft.com/help/4073562) ou uma atualização posterior
   - **10.0.15063.0**: [KB4016240](http://support.microsoft.com/help/4016240) ou uma atualização posterior

3. Desanexar o disco de utilitário a partir da VM em funcionamento e, em seguida, anexá-lo para a VM quebrada.

4. Execute o seguinte comando para instalar a atualização na VM:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Reinicie a VM.

### <a name="repair-the-vm-offline"></a>Repare a VM Offline

1. [Anexar o disco do sistema para uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).

2. Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.

3. Certifique-se de que o disco é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída para o disco do sistema anexado.

4. Criar uma cópia do **\Windows\System32\config** pasta caso uma reversão sobre as alterações é necessária.

5. Sobre a solução VM, inicie o Editor de registo (regedit.exe).

6. Selecione o **HKEY_LOCAL_MACHINE** da chave e, em seguida, selecione **ficheiro** > **carregar Hive** no menu.

7. Localize o ficheiro de sistema nos **\Windows\System32\config** pasta.

8. Selecione **aberto**, tipo **BROKENSYSTEM** para o nome, expanda o **HKEY_LOCAL_MACHINE** da chave e, em seguida, localize a chave adicional com o nome **BROKENSYSTEM** .

9. Vá para a seguinte localização:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Em cada subchave (como 0000), examinar os **DriverDesc** valor que é apresentado como **adaptador de rede do Microsoft HYPER-V**.

11. Na subchave, examine o **DriverVersion** valor que é a versão do controlador do adaptador de rede da VM.

12. Transferir a atualização adequada:

   - **10.0.14393.594**: [KB4073562](http://support.microsoft.com/help/4073562) ou uma atualização posterior
   - **10.0.15063.0**: [KB4016240](http://support.microsoft.com/help/4016240) ou uma atualização posterior

13. Anexe o disco do sistema como um disco de dados numa VM entra em ação no qual pode baixar a atualização.

14. Execute o seguinte comando para instalar a atualização na VM:

   ```
   dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
   ```

15. Execute o comando seguinte para desmontar o hives:

   ```
   reg unload HKLM\BROKENSYSTEM
   ```

16. [Desanexar o disco do sistema e volte a criar a VM](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, ainda [contacte o suporte Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.

---
title: Problemas de resolução de um problema netvsc.sys quando se conecta remotamente a um Windows 10 ou Windows Server 2016 VM em Azure [ Microsoft Docs
description: Aprenda a resolver um problema relacionado com o RDP relacionado com netsvc.sys quando se conecta a um Windows 10 ou Windows Server 2016 VM em Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 4c10a2dcd55c1605cfafe6c67cfefd9d8a3c5f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057993"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Não é possível ligar remotamente a um Windows 10 ou Windows Server 2016 VM em Azure por causa de netvsc.sys

Este artigo explica como resolver um problema em que não há ligação de rede quando se conecta a uma máquina virtual do Datacenter (VM) do Windows 10 ou do Windows Server 2016 num anfitrião do Servidor Hiper-V 2016.

## <a name="symptoms"></a>Sintomas

Não é possível ligar-se a um VM Azure Windows 10 ou Windows Server 2016 utilizando o Protocolo de Ambiente de Trabalho Remoto (RDP). Nos [diagnósticos boot,](boot-diagnostics.md)o ecrã mostra uma cruz vermelha sobre o cartão de interface de rede (NIC). Isto indica que o VM não tem conectividade depois de o sistema operativo estar totalmente carregado.

Normalmente, este problema ocorre no Windows [construir 14393](https://support.microsoft.com/help/4093120/) e [construir 15063](https://support.microsoft.com/help/4015583/). Se a versão do seu sistema operativo for posterior à destas versões, este artigo não se aplica ao seu cenário. Para verificar a versão do sistema, abra uma sessão CMD na [função Consola](serial-console-windows.md)de Acesso Em Série , e, em seguida, executar **Ver**.

## <a name="cause"></a>Causa

Este problema pode ocorrer se a versão do ficheiro do sistema netvsc.sys instalado for **10.0.14393.594** ou **10.0.15063.0**. Estas versões de netvsc.sys podem impedir que o sistema interaja com a plataforma Azure.


## <a name="solution"></a>Solução

Antes de seguir estes passos, [tire uma foto do disco do sistema](../windows/snapshot-copy-managed-disk.md) do VM afetado como cópia de segurança. Para resolver este problema, utilize a Consola em Série ou [repare o VM offline,](#repair-the-vm-offline) fixando o disco de sistema do VM a um VM de recuperação.


### <a name="use-the-serial-console"></a>Use a Consola Em Série

Ligue-se [à Consola em Série, abra uma instância PowerShell](serial-console-windows.md)e siga estes passos.

> [!NOTE]
> Se a Consola em Série não estiver ativada no seu VM, vá à [reparação da secção de offline VM.](#repair-the-vm-offline)

1. Executar o seguinte comando numa instância PowerShell para obter a versão do ficheiro **(c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Descarregue a atualização adequada para um disco de dados novo ou existente que esteja ligado a um VM em funcionamento da mesma região:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) ou uma atualização posterior
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) ou uma atualização posterior

3. Retire o disco utilitário do VM de funcionamento e, em seguida, prenda-o ao VM partido.

4. Executar o seguinte comando para instalar a atualização no VM:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Reinicie a VM.

### <a name="repair-the-vm-offline"></a>Reparar o VM Offline

1. [Fixe o disco do sistema a um VM](../windows/troubleshoot-recovery-disks-portal.md)de recuperação .

2. Inicie uma ligação remote Desktop ao VM de recuperação.

3. Certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Note a letra de unidade que é atribuída ao disco do sistema anexo.

4. Crie uma cópia da pasta **\Windows\System32\config** no caso de ser necessária uma reversão das alterações.

5. No VM de resgate, inicie registro editor (regedit.exe).

6. Selecione a tecla **HKEY_LOCAL_MACHINE** e, em seguida, selecione **File** > **Load Hive** do menu.

7. Localize o ficheiro SYSTEM na pasta **\Windows\System32\config.**

8. Selecione **Open**, escreva **BROKENSYSTEM** para o nome, expanda a chave **HKEY_LOCAL_MACHINE** e, em seguida, localize a chave adicional que se chama **BROKENSYSTEM**.

9. Vá ao seguinte local:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Em cada subchave (como 0000), examine o valor **driverDesc** que é apresentado como Adaptador de **Rede Microsoft HYPER-V**.

11. Na subchave, examine o valor **DriverVersion** que é a versão condutordo do adaptador de rede do VM.

12. Faça o download da atualização apropriada:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) ou uma atualização posterior
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) ou uma atualização posterior

13. Fixe o disco do sistema como um disco de dados num VM de resgate no qual pode descarregar a atualização.

14. Executar o seguinte comando para instalar a atualização no VM:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Executar o seguinte comando para desmontar as colmeias:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Desmontar o disco do sistema e criar novamente o VM](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, contacte o [Suporte Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

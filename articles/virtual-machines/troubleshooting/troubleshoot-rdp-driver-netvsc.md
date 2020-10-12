---
title: Resolver problemas num problema de netvsc.sys quando se liga remotamente a um VM Windows 10 ou Windows Server 2016 em Azure ; Microsoft Docs
description: Saiba como resolver um problema de RDP relacionado com netsvc.sys quando ligar a um VM windows 10 ou Windows Server 2016 em Azure.
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
ms.openlocfilehash: 0f5a414f00ffa50114f090fc19f37b8a85428547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509006"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Não é possível ligar-se remotamente a um VM Windows 10 ou Windows Server 2016 em Azure por causa de netvsc.sys

Este artigo explica como resolver problemas num problema em que não existe ligação de rede quando se liga a uma máquina virtual do Windows 10 ou do Windows Server 2016 (VM) num anfitrião Hyper-V Server 2016.

## <a name="symptoms"></a>Sintomas

Não é possível ligar-se a um VM Azure Windows 10 ou Windows Server 2016 utilizando o Protocolo de Ambiente de Trabalho Remoto (RDP). Nos [diagnósticos boot,](boot-diagnostics.md)o ecrã mostra uma cruz vermelha sobre o cartão de interface de rede (NIC). Isto indica que o VM não tem conectividade depois de o sistema operativo estar completamente carregado.

Normalmente, este problema ocorre no Windows [construir 14393](https://support.microsoft.com/help/4093120/) e [construir 15063](https://support.microsoft.com/help/4015583/). Se a versão do seu sistema operativo for mais tarde do que estas versões, este artigo não se aplica ao seu cenário. Para verificar a versão do sistema, abra uma sessão CMD na [funcionalidade Consola de Acesso série](serial-console-windows.md)e, em seguida, execute **Ver**.

## <a name="cause"></a>Causa

Este problema pode ocorrer se a versão do ficheiro do sistema netvsc.sys instalado for **10.0.14393.594** ou **10.0.15063.0**. Estas versões de netvsc.sys podem impedir que o sistema interaja com a plataforma Azure.


## <a name="solution"></a>Solução

Antes de seguir estes passos, [tire uma foto do disco](../windows/snapshot-copy-managed-disk.md) do sistema do VM afetado como cópia de segurança. Para resolver este problema, utilize a Consola em Série ou [repare o VM offline](#repair-the-vm-offline) ligando o disco de sistema do VM a um VM de recuperação.


### <a name="use-the-serial-console"></a>Utilize a Consola em Série

[Ligue-se à Consola em Série, abra uma instância PowerShell](serial-console-windows.md)e siga estes passos.

> [!NOTE]
> Se a Consola em Série não estiver ativada no seu VM, vá para a reparação da secção [offline VM.](#repair-the-vm-offline)

1. Executar o seguinte comando num caso PowerShell para obter a versão do ficheiro** (c:\windows\system32\drivers\netvsc.sys): **

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Faça o download da atualização adequada para um disco de dados novo ou existente que esteja ligado a um VM em funcionamento da mesma região:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562)   ou uma atualização posterior
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) ou uma atualização posterior

3. Retire o disco de utilidade do VM de funcionamento e, em seguida, prenda-o ao VM partido.

4. Executar o seguinte comando para instalar a atualização no VM:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Reinicie a VM.

### <a name="repair-the-vm-offline"></a>Reparar o VM Offline

1. [Ligue o disco do sistema a um VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).

2. Inicie uma ligação de ambiente de trabalho remoto ao VM de recuperação.

3. Certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Note a letra de unidade que é atribuída ao disco do sistema anexo.

4. Criar uma cópia da pasta **\Windows\System32\config** no caso de ser necessário um reversão das alterações.

5. No resgate VM, inicie o Editor de Registo (regedit.exe).

6. Selecione a **tecla HKEY_LOCAL_MACHINE** e, em seguida, selecione a Colmeia de Carga de **Ficheiros**  >  **Load Hive** no menu.

7. Localizar o ficheiro SYSTEM na pasta **\Windows\System32\config.**

8. Selecione **Open**, **escreva BROKENSYSTEM** para o nome, expanda a tecla **HKEY_LOCAL_MACHINE** e, em seguida, localize a chave adicional que está chamada **BROKENSYSTEM**.

9. Aceda ao seguinte local:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Em cada sub-chave (como 0000), examine o valor **DriverDesc** que é apresentado como **Adaptador de Rede HYPER-V**da Microsoft .

11. Na sub-chave, examine o valor **DriverVersion** que é a versão condutor do adaptador de rede do VM.

12. Descarregue a atualização apropriada:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562)   ou uma atualização posterior
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) ou uma atualização posterior

13. Anexe o disco do sistema como um disco de dados num VM de resgate no qual pode descarregar a atualização.

14. Executar o seguinte comando para instalar a atualização no VM:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Executar o seguinte comando para desmontar as colmeias:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Retire o disco do sistema e crie novamente o VM](./troubleshoot-recovery-disks-portal-windows.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, contacte o [Suporte Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

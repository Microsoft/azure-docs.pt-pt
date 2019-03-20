---
title: Windows reiniciar loop numa VM do Azure | Documentos da Microsoft
description: Saiba como resolver problemas de ciclo de reinício do Windows | Documentos da Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 032bc1b9c4b1b0e3bf8040ed52bf4db65ba7b6c7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085597"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Ciclo de reinício do Windows numa VM do Azure
Este artigo descreve o ciclo de reinício que pode ocorrer numa máquina Virtual de Windows (VM) no Microsoft Azure.

## <a name="symptom"></a>Sintoma

Quando utiliza [diagnósticos de arranque](./boot-diagnostics.md) para obter as capturas de ecrã de uma VM, é encontrar a máquina virtual é a inicialização, mas o processo de inicialização é obter interrompido e o processo está a ser iniciado ao longo do.

![Ecrã de início 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Causa

O ciclo de reinício ocorre devido às seguintes causas:

### <a name="cause-1"></a>Causa 1

Existe um serviço de terceiros é sinalizado como crítica e que não pode ser iniciado. Isso faz com que o sistema operativo reiniciar o computador.

### <a name="cause-2"></a>Causa 2

Foram feitas algumas alterações para o sistema operativo. Normalmente, estes estão relacionados a uma instalação de atualização, a instalação da aplicação ou uma nova política. Poderá ter de verificar os seguintes registos para obter detalhes adicionais:

- Registos de Eventos
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>Causa 3

Danos no ficheiro de sistema poderiam fazer isso. No entanto, é difícil de diagnosticar e identificar a alteração que faz com que a corrupção do sistema operativo.

## <a name="solution"></a>Solução

Para resolver este problema, [cópia de segurança de disco do SO](../windows/snapshot-copy-managed-disk.md), e [anexar o disco do SO para uma VM de resgate](../windows/troubleshoot-recovery-disks-portal.md)e, em seguida, siga as opções de solução em conformidade ou experimente as soluções individualmente.

### <a name="solution-for-cause-1"></a>Solução para causa 1

1. Assim que o disco do SO está ligado a uma VM em funcionamento, certifique-se de que o disco é sinalizado de forma **Online** na gestão de discos da consola e observe a letra de unidade da partição que contém o **\Windows** pasta.

2. Se o disco estiver definido como **Offline**, em seguida, defina-o como **Online**.

3. Criar uma cópia do **\Windows\System32\config** pasta caso é necessária uma reversão sobre as alterações.

4. No entra em ação VM, abra o Editor de registo (regedit) do Windows.

5. Selecione o **HKEY_LOCAL_MACHINE** da chave e, em seguida, selecione **ficheiro** > **carregar Hive** no menu.

6. Procure o ficheiro de sistema nos **\Windows\System32\config** pasta.

7. Selecione **aberto**, tipo **BROKENSYSTEM** para o nome, expanda o **HKEY_LOCAL_MACHINE** chave e, em seguida, verá uma chave adicional chamada **BROKENSYSTEM** .

8. Verifique que o computador está inicializando a partir de ControlSet. Verá o respetivo número de chave na seguinte chave do Registro.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Verificação de que é a importância do serviço de agente VM através da seguinte chave do Registro.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Se o valor da chave do registo não estiver definido como **2**, em seguida, vá para a atenuação seguinte.

11. Se o valor da chave do registo é definido como **2**, em seguida, altere o valor de **2** para **1**.

12. Se existir alguma das seguintes chaves e têm valor **2** ou **3**e, em seguida, alterar estes valores para **1** em conformidade:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Selecione o **BROKENSYSTEM** da chave e, em seguida, selecione **ficheiro** > **carregar Hive** no menu.

14. Desanexe o disco do SO da VM de resolução de problemas.

15. Remover o disco da VM de resolução de problemas e aguarde cerca de dois minutos para o Azure para este disco de versão.

16. [Criar uma nova VM a partir de disco do SO](../windows/create-vm-specialized.md).

17. Se o problema estiver resolvido, poderá ter de reinstalar o [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Solução para causa 2

Restaurar a VM para a última configuração boa conhecida, siga os passos em [como iniciar a VM do Windows Azure com a última configuração boa conhecida](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Solução para causa 3

1. Assim que o disco está ligado a uma VM de resolução de problemas, certifique-se de que o disco é sinalizado de forma **Online** no console de gerenciamento de disco.

2. Criar uma cópia do **\Windows\System32\config** pasta caso é necessária uma reversão sobre as alterações.

3. Copie os ficheiros nos **\Windows\System32\config\regback** pasta e substituir os ficheiros a **\Windows\System32\config** pasta.

4. Remover o disco da VM de resolução de problemas e aguarde cerca de dois minutos para o Azure para este disco de versão.

5. [Criar uma nova VM a partir de disco do SO](../windows/create-vm-specialized.md).

>[!NOTE]
>O procedimento seguinte só deve ser usado como último recurso. Embora o restauro a partir de regback pode restaurar o acesso à máquina, o sistema operacional não é considerado com as estável, uma vez que não há dados perdidos no Registro entre o carimbo de hora do hive e o dia atual. Precisa criar uma nova VM e a fazer planos para migrar os dados.

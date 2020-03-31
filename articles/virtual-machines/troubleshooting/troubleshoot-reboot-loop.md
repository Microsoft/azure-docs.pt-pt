---
title: Windows reboot loop em um Azure VM [ Microsoft Docs
description: Saiba como resolver o ciclo de reinicialização do Windows / Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 3fd0a8bf6bacfec5e2be6dfa52ca51e46c7025f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443588"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Loop de reinicialização do Windows em um VM Azure
Este artigo descreve o ciclo de reinicialização que pode experimentar numa Máquina Virtual do Windows (VM) no Microsoft Azure.

## <a name="symptom"></a>Sintoma

Quando utiliza [diagnósticos de boot](./boot-diagnostics.md) para obter as imagens de um VM, descobre que a máquina virtual está a arrancar, mas o processo de arranque está a ser interrompido e o processo está a recomeçar.

![Iniciar ecrã 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Causa

O ciclo de reinício ocorre devido às seguintes causas:

### <a name="cause-1"></a>Causa 1

Há um serviço de terceiros que é sinalizado como crítico e não pode ser iniciado. Isto faz com que o sistema operativo reinicie.

### <a name="cause-2"></a>Causa 2

Foram feitas algumas alterações no sistema operativo. Normalmente, estas estão relacionadas com uma instalação de atualização, instalação de aplicações ou uma nova política. Pode ter de verificar os seguintes registos para obter mais detalhes:

- Registos de Eventos
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>Causa 3

A corrupção do sistema de ficheiros pode causar isto. No entanto, é difícil diagnosticar e identificar a mudança que causa a corrupção do sistema operativo.

## <a name="solution"></a>Solução

Para resolver este problema, [volte a colocar o disco OS](../windows/snapshot-copy-managed-disk.md)e fixe o disco OS a um VM de [resgate,](../windows/troubleshoot-recovery-disks-portal.md)e, em seguida, siga as opções de solução em conformidade, ou experimente as soluções uma a uma.

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

1. Uma vez que o disco OS esteja ligado a um VM em funcionamento, certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos e note a letra de unidade da divisória que detém a pasta **\Windows.**

2. Se o disco estiver definido para **Offline,** em seguida, coloque-o para **Online**.

3. Crie uma cópia da pasta **\Windows\System32\config** no caso de ser necessária uma reversão das alterações.

4. No VM de resgate, abra o Editor de Registo do Windows (regedit).

5. Selecione a tecla **HKEY_LOCAL_MACHINE** e, em seguida, selecione **File** > **Load Hive** do menu.

6. Navegue no ficheiro SYSTEM na pasta **\Windows\System32\config.**

7. Selecione **Open**, escreva **BROKENSYSTEM** para o nome, expanda a tecla **HKEY_LOCAL_MACHINE** e, em seguida, verá uma tecla adicional chamada **BROKENSYSTEM**.

8. Verifique de que ControlSet o computador está a arrancar. Verá o seu número-chave na seguinte chave de registo.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Verifique qual é a criticidade do serviço de agente VM através da seguinte chave de registo.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Se o valor da chave de registo não estiver definido para **2,** então vá para a próxima mitigação.

11. Se o valor da chave de registo estiver definido para **2,** então altere o valor de **2** para **1**.

12. Se alguma das seguintes teclas existir e tiver valor **2** ou **3**, e, em seguida, alterar estes valores para **1** em conformidade:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Selecione a tecla **BROKENSYSTEM** e, em seguida, selecione **File** > **Unload Hive** do menu.

14. Retire o disco osso do VM de resolução de problemas.

15. Retire o disco do VM de resolução de problemas e aguarde cerca de 2 minutos para que o Azure liberte este disco.

16. [Crie um novo VM a partir do disco OS](../windows/create-vm-specialized.md).

17. Se o problema for corrigido, poderá ter de reinstalar o [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Restaurar o VM até à última boa configuração conhecida, siga os passos em [Como iniciar o Azure Windows VM com a última configuração conhecida.](https://support.microsoft.com/help/4016731/)

### <a name="solution-for-cause-3"></a>Solução para a causa 3
>[!NOTE]
>O procedimento seguinte só deve ser utilizado como último recurso. Enquanto o restabelecimento do regback pode restaurar o acesso à máquina, o SO não é considerado estável, uma vez que existem dados perdidos no registo entre o carimbo temporal da colmeia e o dia de hoje. Precisa construir um novo VM e fazer planos para migrar dados.

1. Uma vez que o disco esteja ligado a um VM de resolução de problemas, certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos.

2. Crie uma cópia da pasta **\Windows\System32\config** no caso de ser necessária uma reversão das alterações.

3. Copie os ficheiros na pasta **\Windows\System32\config\regback** e substitua os ficheiros na pasta **\Windows\System32\config.**

4. Retire o disco do VM de resolução de problemas e aguarde cerca de 2 minutos para que o Azure liberte este disco.

5. [Crie um novo VM a partir do disco OS](../windows/create-vm-specialized.md).



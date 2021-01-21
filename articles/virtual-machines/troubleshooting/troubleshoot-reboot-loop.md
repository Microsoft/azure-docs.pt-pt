---
title: Windows reinicia loop em um Azure VM | Microsoft Docs
description: Saiba como resolver problemas com o ciclo de reinicialização do Windows | Microsoft Docs
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
ms.openlocfilehash: ad0ed7e9619f0b789bf8949fe398aa27bc36b9e0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629645"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Windows reinicia loop em um Azure VM
Este artigo descreve o loop de reinicialização que pode experimentar numa Máquina Virtual Do Windows (VM) no Microsoft Azure.

## <a name="symptom"></a>Sintoma

Quando utiliza [diagnósticos boot](./boot-diagnostics.md) para obter as imagens de um VM, descobre que a máquina virtual está a arrancar, mas o processo de arranque está a ser interrompido e o processo está a recomeçar.

![Iniciar tela 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Causa

O ciclo de reinicialização ocorre devido às seguintes causas:

### <a name="cause-1"></a>Motivo 1

Há um serviço de terceiros que é sinalizado como crítico e não pode ser iniciado. Isto faz com que o sistema operativo reinicie.

### <a name="cause-2"></a>Motivo 2

Foram feitas algumas alterações ao sistema operativo. Normalmente, estas estão relacionadas com uma instalação de atualização, instalação de aplicações ou uma nova política. Poderá ter de verificar os seguintes registos para obter mais detalhes:

- Registos de Eventos
- CBS.logWindows
- Atualização.log

### <a name="cause-3"></a>Causa 3

Corrupção no sistema de ficheiros pode causar isto. No entanto, é difícil diagnosticar e identificar a mudança que causa a corrupção do sistema operativo.

## <a name="solution"></a>Solução

> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.

Para resolver este problema, [faça uma lavagem ao disco de so](../windows/snapshot-copy-managed-disk.md), e ligue o disco de SO a um [VM de resgate](./troubleshoot-recovery-disks-portal-windows.md), e, em seguida, siga as opções de solução em conformidade, ou experimente as soluções uma a uma.

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

1. Uma vez que o disco OS esteja ligado a um VM em funcionamento, certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos e note a letra de unidade da partição que detém a pasta **\Windows.**

2. Se o disco estiver definido para **Offline,** então desa cos um pouco para **Online**.

3. Crie uma cópia da pasta **\Windows\System32\config** no caso de ser necessário um reversão das alterações.

4. No VM de resgate, abra o Editor de Registo do Windows (regedit).

5. Selecione a **tecla HKEY_LOCAL_MACHINE** e, em seguida, selecione a Colmeia de Carga de **Ficheiros**  >   no menu.

6. Navegue no ficheiro SYSTEM na pasta **\Windows\System32\config.**

7. Selecione **Open**, **escreva BROKENSYSTEM** para o nome, expanda a tecla **HKEY_LOCAL_MACHINE** e, em seguida, verá uma chave adicional chamada **BROKENSYSTEM**.

8. Verifique de que Controlo o computador está a arrancar. Verá o seu número-chave na seguinte chave de registo.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Verifique qual é a criticidade do serviço de agente VM através da seguinte chave de registo.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Se o valor da chave de registo não estiver definido para **2,** então vá para a próxima atenuação.

11. Se o valor da chave de registo for definido para **2,** altere o valor de **2** para **1**.

12. Se existirem alguma das seguintes teclas e tiverem o valor **2** ou **3**, e depois alterar estes valores para **1** em conformidade:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Selecione a **tecla BROKENSYSTEM** e, em seguida, selecione A Colmeia de Descarregamento de   >  **Ficheiros** no menu.

14. Retire o disco de SO do VM de resolução de problemas.

15. Retire o disco do VM de resolução de problemas e aguarde cerca de 2 minutos para que o Azure liberte este disco.

16. [Criar um novo VM a partir do disco oss.](../windows/create-vm-specialized.md)

17. Se o problema for corrigido, poderá ter de reinstalar o [RDAgent](/archive/blogs/mast/install-the-vm-agent-on-an-existing-azure-vm) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Restaurar o VM até à última boa configuração conhecida, siga os passos em [Como iniciar O Azure Windows VM com a última configuração boa conhecida](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Solução para a causa 3
>[!NOTE]
>O procedimento a seguir só deve ser utilizado como último recurso. Ao restaurar o regback pode restaurar o acesso à máquina, o SO não é considerado estável, uma vez que há dados perdidos no registo entre o timetamp da colmeia e o dia atual. É preciso construir um novo VM e fazer planos para migrar dados.

1. Uma vez que o disco esteja ligado a um VM de resolução de problemas, certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos.

2. Crie uma cópia da pasta **\Windows\System32\config** no caso de ser necessário um reversão das alterações.

3. Copie os ficheiros na pasta **\Windows\System32\config\regback** e substitua os ficheiros na pasta **\Windows\System32\config.**

4. Retire o disco do VM de resolução de problemas e aguarde cerca de 2 minutos para que o Azure liberte este disco.

5. [Criar um novo VM a partir do disco oss.](../windows/create-vm-specialized.md)

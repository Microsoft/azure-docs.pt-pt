---
title: Loop de reinicialização do Windows em uma VM do Azure | Microsoft Docs
description: Saiba como solucionar problemas do loop de reinicialização do Windows | Microsoft Docs
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443588"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Loop de reinicialização do Windows em uma VM do Azure
Este artigo descreve o loop de reinicialização que você pode experimentar em uma VM (máquina virtual) do Windows no Microsoft Azure.

## <a name="symptom"></a>Sintoma

Ao usar o [diagnóstico de inicialização](./boot-diagnostics.md) para obter as capturas de tela de uma VM, você descobre que a máquina virtual está inicializando, mas o processo de inicialização está sendo interrompido e o processo está sendo reiniciado.

![Tela inicial 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Causa

O loop de reinicialização ocorre devido às seguintes causas:

### <a name="cause-1"></a>Causa 1

Há um serviço de terceiros sinalizado como crítico e não pode ser iniciado. Isso faz com que o sistema operacional seja reinicializado.

### <a name="cause-2"></a>Causa 2

Algumas alterações foram feitas no sistema operacional. Normalmente, eles estão relacionados a uma instalação de atualização, instalação de aplicativo ou uma nova política. Talvez seja necessário verificar os seguintes logs para obter detalhes adicionais:

- Registos de Eventos
- CBS.logWindows
- Update. log

### <a name="cause-3"></a>Causa 3

A corrupção do sistema de arquivos pode causar isso. No entanto, é difícil diagnosticar e identificar a alteração que causa a corrupção do sistema operacional.

## <a name="solution"></a>Solução

Para resolver esse problema, [faça backup do disco do sistema operacional](../windows/snapshot-copy-managed-disk.md)e [anexe o disco do sistema operacional a uma VM de resgate](../windows/troubleshoot-recovery-disks-portal.md)e siga as opções da solução de forma adequada ou tente as soluções uma a uma.

### <a name="solution-for-cause-1"></a>Solução para o motivo 1

1. Depois que o disco do sistema operacional for anexado a uma VM em funcionamento, verifique se o disco está sinalizado como **online** no console de gerenciamento de disco e anote a letra da unidade da partição que contém a pasta **\Windows** .

2. Se o disco estiver definido como **offline**, defina-o como **online**.

3. Crie uma cópia da pasta **\Windows\System32\config** caso uma reversão das alterações seja necessária.

4. Na VM de resgate, abra o editor do registro do Windows (regedit).

5. Selecione a chave de **HKEY_LOCAL_MACHINE** e, em seguida, selecione **arquivo** > **Carregar Hive** no menu.

6. Navegue até o arquivo do sistema na pasta **\Windows\System32\config**

7. Selecione **abrir**, digite **BROKENSYSTEM** para o nome, expanda a chave **HKEY_LOCAL_MACHINE** e, em seguida, você verá uma chave adicional chamada **BROKENSYSTEM**.

8. Verifique em qual controle o computador está inicializando. Você verá seu número de chave na chave do registro a seguir.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Verifique qual é a importância do serviço do agente de VM por meio da chave do registro a seguir.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Se o valor da chave do registro não estiver definido como **2**, vá para a próxima mitigação.

11. Se o valor da chave do registro for definido como **2**, altere o valor de **2** para **1**.

12. Se qualquer uma das seguintes chaves existir e tiver o valor **2** ou **3**e, em seguida, altere esses valores para **1** de acordo:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Selecione a chave **BROKENSYSTEM** e, em seguida, selecione **arquivo** > **Descarregar Hive** no menu.

14. Desanexe o disco do sistema operacional da VM de solução de problemas.

15. Remova o disco da VM de solução de problemas e aguarde cerca de 2 minutos para que o Azure libere esse disco.

16. [Crie uma nova VM do disco do sistema operacional](../windows/create-vm-specialized.md).

17. Se o problema for corrigido, talvez seja necessário reinstalar o [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent. exe).

### <a name="solution-for-cause-2"></a>Solução para o motivo 2

Restaure a VM para a última configuração válida conhecida, siga as etapas em [como iniciar a VM do Windows Azure com a última configuração válida conhecida](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Solução para a causa 3
>[!NOTE]
>O procedimento a seguir só deve ser usado como último recurso. Durante a restauração do Regback pode restaurar o acesso ao computador, o sistema operacional não é considerado estável, pois há dados perdidos no registro entre o carimbo de data/hora do hive e o dia atual. Você precisa criar uma nova VM e fazer planos para migrar dados.

1. Depois que o disco estiver anexado a uma VM de solução de problemas, verifique se o disco está sinalizado como **online** no console de gerenciamento de disco.

2. Crie uma cópia da pasta **\Windows\System32\config** caso uma reversão das alterações seja necessária.

3. Copie os arquivos na pasta **\Windows\System32\config\regback** e substitua os arquivos na pasta **\Windows\System32\config** .

4. Remova o disco da VM de solução de problemas e aguarde cerca de 2 minutos para que o Azure libere esse disco.

5. [Crie uma nova VM do disco do sistema operacional](../windows/create-vm-specialized.md).



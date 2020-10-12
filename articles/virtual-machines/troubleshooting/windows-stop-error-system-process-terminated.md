---
title: Processo do sistema de estado de erro do Windows stop encerrado
description: Este artigo fornece medidas para resolver problemas em que o sistema operativo encontra o erro de stop 0xC000021A, o que impede o arranque de uma máquina virtual Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: b07033f96402edc24edd51de57661603e57472bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91347767"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Erro de paragem do Windows - 0xC000021A Processo do sistema de estado encerrado

Este artigo fornece medidas para resolver problemas em que o sistema operativo (OS) encontra o erro de stop 0xC000021A, que mantém uma máquina virtual Azure (VM) de arranque.

## <a name="symptom"></a>Sintoma

Quando utiliza [diagnósticos boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a imagem do VM, a imagem mostra a mensagem de que o SO encontrou um erro durante o arranque, com a seguinte mensagem:

**O seu PC teve um problema e precisa de reiniciar. Estamos só a recolher algumas informações de erro, e depois podes recomeçar. (##% completo) Se quiser saber mais, pode pesquisar online mais tarde por este erro: 0xC000021a**.

  ![A Figura 1 apresenta código de erro #0xC000021A com a mensagem "O seu PC teve um problema e precisa de reiniciar. Estamos apenas a recolher algumas informações de erro, e depois podes recomeçar."](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>Causa

Erro 0xC000021A significa **STATUS_SYSTEM_PROCESS_TERMINATED**.

Este erro ocorre quando um processo crítico, como o WinLogon (winlogon.exe) ou o Subsistema Run-Time do Servidor de Clientes (csrss.exe) falha. Uma vez que o núcleo deteta que qualquer um desses serviços parou, aumenta o erro **STOP 0xC00021A.** Este erro pode ter várias causas, incluindo:

- Foram instalados ficheiros de sistema desajustados.
- Uma instalação de atualização de serviços ou KB falhou.
- Um programa de backup que é usado para restaurar um disco rígido não restaurou corretamente ficheiros que possam ter sido utilizados.
- Foi instalado um programa de terceiros incompatível.

## <a name="solution"></a>Solução

### <a name="collect-the-memory-dump-file"></a>Recolher o ficheiro de despejo de memória

Para resolver este problema, o depósito de acidentes terá de ser analisado. Recolha o ficheiro de despejo de memória para o suporte de acidente e contacto. Para recolher o ficheiro de despejo, siga estes passos:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Fixe o disco DE a um novo VM de reparação

1.  Utilize os passos 1-3 dos Comandos de [Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar um VM de reparação.
2.  Utilizando **ligação de ambiente de trabalho remoto,** ligue-se ao VM de reparação.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

1.  Na vM de reparação, vá à pasta do janela no disco oss anexado. Se a carta do controlador que é atribuída ao disco de oss anexado for F, vá para F:\Windows.
2.  Localize o ficheiro memory.dmp e, em seguida, [envie um bilhete de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo de memória.
3.  Se tiver dificuldade em localizar o ficheiro memory.dmp, pode desejar utilizar [chamadas de interrupção não mascarada (NMI) na consola em série.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) Pode seguir o guia para gerar um ficheiro de informação de falha de sistema através de chamadas NMI [aqui](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre resolução de [problemas, consulte os erros comuns de resolução de problemas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-error-troubleshoot) de arranque ou como resolver [problemas num VM do Windows, fixando o disco OS a um VM de recuperação](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-windows). Deve também familiarizar-se com [a utilização de diagnósticos de arranque para resolver problemas com uma máquina virtual.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)
- Para obter mais informações sobre a utilização do Gestor de Recursos, consulte [a visão geral do Gestor de Recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).
- Se não conseguir ligar-se ao seu VM, consulte [as ligações RDP de resolução de problemas a um Azure VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).

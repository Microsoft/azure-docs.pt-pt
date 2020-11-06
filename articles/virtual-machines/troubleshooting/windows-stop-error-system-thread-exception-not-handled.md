---
title: Erro de paragem do Windows - 0x000000007E exceção do fio do sistema não manuseado
description: Este artigo fornece medidas para resolver problemas em que o Guest OS encontra um problema e deseja reiniciar o seu Azure VM. A mensagem dirá que "uma exceção do fio do sistema não foi tratada".
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424227"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Erro de paragem do Windows - 0x000000007E exceção do fio do sistema não manuseado

Este artigo fornece medidas para resolver problemas em que o Guest OS encontra um problema e deseja reiniciar o seu Azure VM. A mensagem dirá que "uma exceção do fio do sistema não foi tratada".

## <a name="symptoms"></a>Sintomas

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem exibe o Windows que precisa de reiniciar com o sistema de sistema de paragem **THREAD EXCEPTION NÃO HANDLED** ou com o código de erro **0x00000007E**.

![A imagem mostra o Windows preso durante o arranque com a mensagem: "O seu PC teve um problema e precisa de reiniciar. Vamos recomeçar por si." Código de paragem: "EXCEÇÃO DO FIO DO SISTEMA NÃO MANUSEADA"](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Causa

A causa não pode ser determinada até que um ficheiro de despejo de memória seja analisado. Continue a recolher o ficheiro de despejo de memória.

## <a name="solution"></a>Solução

### <a name="collect-the-memory-dump-file"></a>Recolher o ficheiro de despejo de memória

Para resolver este problema, primeiro tem de recolher o ficheiro de despejo de memória para o acidente e, em seguida, contactar o suporte com o ficheiro de despejo de memória. Para recolher o ficheiro de despejo, siga estes passos:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Fixe o disco DE a um novo VM de reparação

1. Siga [os passos 1-3 dos Comandos de Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
2. Ligue-se ao VM de reparação utilizando ligação de ambiente de trabalho remoto.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

1. Na vM de reparação, aceda à pasta Windows no disco oss anexado. Se a letra do controlador atribuída ao disco de sos anexado estiver rotulada como *F,* então tem de ir a `F:\Windows` .
2. Localize o ficheiro **memory.dmp** e, em seguida, [envie um bilhete de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo de memória.
3. Se tiver problemas em localizar o ficheiro **memory.dmp,** siga o guia para [gerar um ficheiro de despejo de falhas utilizando chamadas de interrupção não mascarada (NMI).](/windows/client-management/generate-kernel-or-complete-crash-dump)

Para obter mais informações sobre chamadas NMI, consulte as [chamadas de interrupção não mascarada (NMI) no](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) guia do utilizador da consola em série.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Resolução de problemas Azure Erros de Arranque de Máquina Virtual](./boot-error-troubleshoot.md)
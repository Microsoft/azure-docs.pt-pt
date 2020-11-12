---
title: VM não responde enquanto espera pelo gerente da sessão local
description: Este artigo fornece medidas para resolver problemas em que o So convidado está preso à espera que o Gerente de Sessão Local termine o processamento enquanto inicia um VM Azure.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: f2f0177b5fe8bb97773d297319f6c9196d8178d2
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536239"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>VM não responde enquanto espera pelo gerente da sessão local

Este artigo fornece passos para resolver problemas em que o So convidado está preso à espera que o Gerente de Sessão Local termine o processamento enquanto inicia uma máquina virtual Azure (VM).

## <a name="symptoms"></a>Sintomas

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem exibe uma solicitação com a mensagem: *"Por favor, aguarde o Gestor de Sessão Local"*

![A imagem mostra o OS do Hóspede preso com a mensagem "Por favor, aguarde o Gestor de Sessão Local" no Windows Server 2012 R2.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Causa

Pode haver várias razões para uma máquina virtual estar presa à espera do Gerente local de Sessão. Se esperar pelo Gestor de Sessão Local é um problema persistente, terá de recolher um depósito de memória para análise.

## <a name="solution"></a>Solução

Em alguns casos, simplesmente esperar o tempo suficiente para que o processo esteja concluído irá resolver o seu problema. Se o seu VM não estiver a responder e permanecer no ecrã de espera durante mais de uma hora, deverá recolher uma lixeira de memória e, em seguida, contactar o suporte da Microsoft.

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
> [Resolução de problemas Azure Erros de Arranque de Máquina Virtual](boot-error-troubleshoot.md)
---
title: A VM não responde quando se aplica a política de controladores de domínio predefinida
titlesuffix: Azure Virtual Machines
description: Este artigo fornece medidas para resolver problemas em que a Política de Controladores de Domínio Padrão impede o arranque de um VM Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 6c139398182ca9d875de0d3b21c58afe503bd8a5
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632279"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>A VM não responde quando se aplica a política de controladores de domínio predefinida

Este artigo fornece medidas para resolver problemas em que a Política de Controladores de Domínio Padrão impede o arranque de uma Máquina Virtual Azure (VM).

## <a name="symptom"></a>Sintoma

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem mostra o SISTEMA sem resposta enquanto inicia com a mensagem Política de **Controladores de Domínio Padrão**.

  ![A Figura 1 exibe o SISTEMA preso com a mensagem "Política de Controladores de Domínio Predefinidos"](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>Causa

Este problema pode dever-se a alterações recentes feitas à Política de Controladores de Domínio Predefinidos. Caso contrário, será necessário realizar uma análise do ficheiro de despejo de memória para determinar a causa principal.

## <a name="solution"></a>Solução

> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.

Se recentemente tiver feito alterações na Política de Controladores de Domínio Predefinidos, poderá pretender desfazer essas alterações para corrigir o problema. Se não tem a certeza do que está a causar o problema, recolha um depósito de memória e, em seguida, envie um bilhete de apoio.

### <a name="collect-the-memory-dump-file"></a>Recolher o ficheiro de despejo de memória

Para resolver este problema, deve primeiro recolher o ficheiro de despejo de memória para o acidente e, em seguida, contactar o suporte com o ficheiro de despejo de memória. Para recolher o ficheiro de despejo, siga estes passos:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Fixe o disco DE a um novo VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.

1. Utilizando ligação de ambiente de trabalho remoto, ligue-se ao VM de reparação.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

1. Na vM de reparação, aceda à pasta Windows no disco oss anexado. Se a letra de unidade atribuída ao disco do SO anexado for `F`, terá de aceder a `F:\Windows`.

1. Localize o ficheiro .dmp memória e, em seguida, [envie um bilhete de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo de memória.

1. Se tiver dificuldade em localizar o ficheiro .dmp memória, poderá desejar utilizar [chamadas de interrupção não mascarada (NMI) na consola em série.](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) Siga o guia para [gerar um ficheiro de despejo de falhas utilizando chamadas NMI](/windows/client-management/generate-kernel-or-complete-crash-dump).
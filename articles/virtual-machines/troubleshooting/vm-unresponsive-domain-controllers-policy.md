---
title: VM não responde ao aplicar a política de controladores de domínio predefinidos
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
ms.openlocfilehash: 53e1daca47a2917a19cbc30db5348e4fcc06b325
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90039153"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>VM não responde ao aplicar a política de controladores de domínio predefinidos

Este artigo fornece medidas para resolver problemas em que a Política de Controladores de Domínio Padrão impede o arranque de uma Máquina Virtual Azure (VM).

## <a name="symptom"></a>Sintoma

Quando utilizar [diagnósticos boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a imagem do VM, verá que a imagem mostra o SISTEMA sem resposta enquanto inicia com a mensagem Política de **Controladores de Domínio Padrão**.

  ![A Figura 1 exibe o SISTEMA preso com a mensagem "Política de Controladores de Domínio Predefinidos"](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>Causa

Este problema pode dever-se a alterações recentes feitas à Política de Controladores de Domínio Predefinidos. Caso contrário, será necessário realizar uma análise do ficheiro de despejo de memória para determinar a causa principal.

## <a name="solution"></a>Solução

Se recentemente tiver feito alterações na Política de Controladores de Domínio Predefinidos, poderá pretender desfazer essas alterações para corrigir o problema. Se não tem a certeza do que está a causar o problema, recolha um depósito de memória e, em seguida, envie um bilhete de apoio.

### <a name="collect-the-memory-dump-file"></a>Recolher o ficheiro de despejo de memória

Para resolver este problema, deve primeiro recolher o ficheiro de despejo de memória para o acidente e, em seguida, contactar o suporte com o ficheiro de despejo de memória. Para recolher o ficheiro de despejo, siga estes passos:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Fixe o disco DE a um novo VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) para preparar um VM de reparação.

1. Utilizando ligação de ambiente de trabalho remoto, ligue-se ao VM de reparação.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

1. Na vM de reparação, aceda à pasta Windows no disco oss anexado. Se a letra de unidade atribuída ao disco do SO anexado for `F`, terá de aceder a `F:\Windows`.

1. Localize o ficheiro memory.dmp e, em seguida, [envie um bilhete de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo de memória.

1. Se tiver dificuldade em localizar o ficheiro memory.dmp, pode desejar utilizar [chamadas de interrupção não mascarada (NMI) na consola em série.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) Siga o guia para [gerar um ficheiro de despejo de falhas utilizando chamadas NMI](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).

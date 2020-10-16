---
title: A Azure VM não responde ao aplicar a Política de Segurança ao sistema
description: Este artigo fornece medidas para resolver problemas em que o ecrã de carga está preso quando o VM não responde enquanto aplica a política de segurança ao sistema num VM Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 6b50bffd1a44c0cf53f15650f5ff4d938f45df4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84908194"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>A Azure VM não responde ao aplicar a Política de Segurança ao sistema

Este artigo fornece medidas para resolver problemas em que o SO está pendurado e fica sem resposta enquanto aplica uma política de segurança num VM Azure.

## <a name="symptoms"></a>Sintomas

Quando utilizar [diagnósticos boot](boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem mostra o SISTEMA preso enquanto inicia com a mensagem:

> "Aplicar a política de segurança ao sistema".

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="A imagem do ecrã de arranque R2012 R2 do Windows Server está presa.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="A imagem do ecrã de arranque R2012 R2 do Windows Server está presa.":::

## <a name="cause"></a>Causa

Há uma panóplia de potenciais causas desta questão. Não poderá saber a fonte até que uma análise do despejo de memória seja realizada.

## <a name="resolution"></a>Resolução

### <a name="process-overview"></a>Visão geral do processo

1. [Criar e Aceder a um VM de reparação](#create-and-access-a-repair-vm)
2. [Ativar a recolha de consolas em série e de despejo de memória](#enable-serial-console-and-memory-dump-collection)
3. [Reconstruir o VM](#rebuild-the-vm)
4. [Recolher o ficheiro de despejo de memória](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>Criar e Aceder a um VM de reparação

1. Utilize [os passos 1-3 dos Comandos de Reparação VM](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para preparar um VM de reparação.
2. Utilize ligação de ambiente de trabalho remoto à VM de reparação.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Ativar a recolha de consolas em série e de despejo de memória

Para ativar a recolha de lixo de memória e a Consola em Série, execute este script:

1. Abra uma sessão de solicitação de comando elevada (Executar como administrador).
2. Enuda os dados da loja BCD e determine o identificador de carregador de arranque, que utilizará no passo seguinte.

     1. Para uma Geração 1 VM, insira o seguinte comando e note o identificador listado:

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        No comando, \<BOOT PARTITON> substitua-a pela letra da partição no disco anexo que contém a pasta de arranque.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="A imagem do ecrã de arranque R2012 R2 do Windows Server está presa." /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Descarregar disco de SO quebrado:

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>Reconstruir o VM

Utilize [o passo 5 dos Comandos de Reparação VM](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para remontar o VM.

### <a name="collect-the-memory-dump-file"></a>Recolher o ficheiro de despejo de memória

Para resolver este problema, seria necessário primeiro recolher o ficheiro de despejo de memória para o acidente e o suporte de contacto com o ficheiro de despejo de memória. Para recolher o ficheiro de despejo, siga estes passos:

1. Fixe o disco OS a um novo VM de reparação:

    - Utilize [os passos 1-3 dos Comandos de Reparação VM](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para preparar um novo VM de reparação.
    - Utilize ligação de ambiente de trabalho remoto à VM de reparação.

2. Localize o ficheiro de informação de falha de sistema e envie um pedido de suporte:

    - Na vM de reparação, vá à pasta do janela no disco oss anexado. Se a letra de unidade atribuída ao disco do SO anexado for `F`, terá de aceder a `F:\Windows`.
    - Localize o ficheiro memory.dmp e, em seguida, [envie um bilhete de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo de memória.
    - Se tiver dificuldade em localizar o ficheiro memory.dmp, pode desejar utilizar [chamadas de interrupção não mascarada (NMI) na consola em série.](serial-console-windows.md#use-the-serial-console-for-nmi-calls) Pode seguir o guia para [gerar um ficheiro de despejo de falhas utilizando chamadas NMI](/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas quando aplicar a política de Utilizadores e Grupos Locais, ver [que o VM não responde ao aplicar a política de utilizadores e grupos locais de política](unresponsive-vm-apply-group-policy.md) de grupos
---
title: Problemas com um disco de SISTEMA completo numa máquina virtual Linux
description: Como resolver problemas com um disco de SISTEMA completo numa máquina virtual Linux
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523532"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Problemas com um disco de SISTEMA completo numa máquina virtual Linux

Quando o disco DE de uma máquina virtual Linux (VM) fica cheio, isto pode causar problemas com o bom funcionamento do VM.

## <a name="symptom"></a>Sintoma

Quando tenta criar um novo ficheiro, recebe esta mensagem:

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

Vários daemons indicam então que não são capazes de criar ficheiros temporários durante a sessão de arranque.

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>Causa

Existem várias razões pelas quais esta mensagem de erro pode ocorrer:

1. O disco pode estar cheio.
1. O sistema de ficheiros pode ter ficado sem iNodes.
1. Um disco de dados pode ser montado sobre um diretório existente, fazendo com que os ficheiros sejam escondidos.
1. Ficheiros que são abertos por um processo e depois apagados.

## <a name="solution"></a>Solução

### <a name="process-overview"></a>Visão geral do processo

1. Criar e aceder a um VM de reparação.
1. Espaço livre no disco.
1. Reconstruir o VM.

> [!NOTE]
> Ao encontrar este erro, o SO convidado não está operacional. Resolver problemas em modo offline para resolver este problema.

### <a name="create-and-access-a-repair-vm"></a>Criar e aceder a um VM de reparação

1. Utilize os passos 1-3 dos Comandos de [Reparação VM](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md) para preparar um VM de reparação.
1. Utilizando sSH, ligue-se ao VM de reparação.

### <a name="free-up-space-on-the-disk"></a>Liberte espaço no disco

Para resolver o problema:

- Redimensione o disco até 1 TB se ainda não estiver no tamanho máximo de 1 TB.
- Liberte o espaço do disco.

1. Verifique se o disco está cheio. Se o tamanho do disco for inferior a 1 TB, expanda-o até um máximo de 1 TB [utilizando O Azure CLI](../linux/expand-disks.md).
1. Se o disco já estiver em 1 TB, terá de libertar espaço em disco.
1. Uma vez terminado o redimensionamento e a limpeza, proceda à reconstrução do VM.

### <a name="rebuild-the-vm"></a>Reconstruir o VM

Utilize [o passo 5 dos Comandos de Reparação VM](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) para reconstruir o VM.

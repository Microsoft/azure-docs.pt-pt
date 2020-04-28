---
title: azcopy jobs show [ Microsoft Docs
description: Este artigo fornece informações de referência para os trabalhos de azcopy mostrar comando.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034126"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Mostra informações detalhadas para a identificação do trabalho.

## <a name="synopsis"></a>Sinopse

Se apenas a identificação do trabalho for fornecida sem uma bandeira, então o resumo do progresso do trabalho é devolvido.

O byte conta e por cento completo que aparece quando executa este comando reflete apenas os ficheiros que estão concluídos no trabalho. Não refletem ficheiros parcialmente concluídos.

Se `with-status` a bandeira estiver definida, a lista de transferências no trabalho com o valor dado será mostrada.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, --ajuda|Mostra conteúdo de ajuda para o comando do show.|
|-com cadeia de estatuto|Apenas enumerar as transferências de emprego com este estatuto, valores disponíveis: Iniciado, Sucesso, Falhado|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Cobre a taxa de transferência, em megabits por segundo. A entrada momentânea pode variar ligeiramente a partir da tampa. Se esta opção estiver definida para zero, ou for omitida, a entrada não está limitada.|
|--cadeia tipo saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|

## <a name="see-also"></a>Consulte também

- [azcopy jobs](storage-ref-azcopy-jobs.md)

---
title: empregos de azcopia limpos / Microsoft Docs
description: Este artigo fornece informações de referência para o comando limpo de trabalhos de azcopia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: a06e428908777c526602166f127a28304b595ba0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220088"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Remova todos os registos e planos para todos os trabalhos

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Dados de transferência com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opções

**-h, -- ajuda**                Ajuda para limpar.

**--com o status** string Apenas remova os postos de trabalho com este estatuto, valores disponíveis: Cancelado, Concluído, Falhado, InProgress, Todos (predefinido "Todos")

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

**--cap-mbps uint32**      Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.

**...-tipo de saída** formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é 'texto'. ("texto" predefinido)

**--cadeia de sufixos fidedignos-microsoft-sufixos** Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.

## <a name="see-also"></a>Veja também

- [azcopy jobs](storage-ref-azcopy-jobs.md)

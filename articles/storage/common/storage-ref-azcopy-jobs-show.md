---
title: empregos azcopia mostram - Microsoft Docs
description: Este artigo fornece informações de referência para os trabalhos de azcopia mostram comando.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ffb16936ec96f30ad7d8e05babbadb85e01a71e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87281944"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Mostra informações detalhadas para a identificação do trabalho.

## <a name="synopsis"></a>Sinopse

Se apenas a identificação do trabalho for fornecida sem uma bandeira, então o resumo do trabalho é devolvido.

As contagens byte e por cento completas que aparecem quando executam este comando refletem apenas ficheiros que estão concluídos no trabalho. Não refletem a sua mente.

Se a `with-status` bandeira for definida, então a lista de transferências no trabalho com o valor dado será mostrada.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Dados de transferência com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, -- ajuda|Mostra conteúdo de ajuda para o comando do show.|
|--com corda de estado|Apenas enumerar as transferências de emprego com este estatuto, valores disponíveis: Iniciado, Sucesso, Falhado|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|---cap-mbps flutuar|Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.|
|--cadeia do tipo de saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|
|--cadeia de sufixos de confiança da Microsoft-Sufixes   |Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.|

## <a name="see-also"></a>Consulte também

- [azcopy jobs](storage-ref-azcopy-jobs.md)

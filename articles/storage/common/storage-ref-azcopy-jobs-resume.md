---
title: empregos azcopia currículo / Microsoft Docs
description: Este artigo fornece informações de referência para os trabalhos de azcopia retomar o comando.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e143a5e82b817aaba37750a8cce08e3f74f0abc8
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220029"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Retoma o trabalho existente com a identificação de emprego dada.

## <a name="synopsis"></a>Sinopse

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Dados de transferência com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|--destination-sas string|Destino SAS do destino para jobid dado.|
|--excluir cadeia|Filtro: Exclua estas transferências falhadas ao retomar o trabalho. Os ficheiros devem ser separados por ';'.|
|-h, -- ajuda|Mostrar conteúdo de ajuda para o comando do currículo.|
|--incluir corda|Filtro: inclua apenas estas transferências falhadas ao retomar o trabalho. Os ficheiros devem ser separados por ';'.|
|--cadeia fonte-sas |fonte SAS da fonte para jobid dado.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.|
|--cadeia do tipo de saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|
|--cadeia de sufixos de confiança da Microsoft-Sufixes   |Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.|

## <a name="see-also"></a>Ver também

- [azcopy jobs](storage-ref-azcopy-jobs.md)

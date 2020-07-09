---
title: lista de azcopias Microsoft Docs
description: Este artigo fornece informações de referência para o comando da lista de azcopia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: db11a71cccee897a3b66e9ca95ded2dab62337b2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84219981"
---
# <a name="azcopy-list"></a>azcopy list

Lista as entidades num dado recurso.

## <a name="synopsis"></a>Sinopse

Apenas os recipientes Blob são suportados na libertação atual.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Dados de transferência com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, -- ajuda|Mostrar conteúdo de ajuda para o comando da lista.|
|--máquina-legível|Lista os tamanhos dos ficheiros em bytes.|
|--mega-unidades|Exibe unidades por encomendas de 1000, não 1024.|
|--running-tally|Conta o número total de ficheiros e os seus tamanhos.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.|
|--cadeia do tipo de saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|
|--cadeia de sufixos de confiança da Microsoft-Sufixes   |Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.|

## <a name="see-also"></a>Veja também

- [azcopia](storage-ref-azcopy.md)

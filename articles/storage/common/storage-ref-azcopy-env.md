---
title: azcopy env / Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy env.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 197d8ee1ea1d389a26548fa65adee83c6a92cf96
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87275977"
---
# <a name="azcopy-env"></a>azcopy env

Mostra as variáveis ambientais que podem configurar o comportamento da AzCopy.

## <a name="synopsis"></a>Sinopse

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Se definir uma variável ambiental utilizando a linha de comando, essa variável será legível no histórico da sua linha de comando. Considere limpar variáveis que contenham credenciais do histórico da sua linha de comando. Para evitar que as variáveis apareçam na sua história, pode utilizar um script para solicitar ao utilizador as suas credenciais e definir a variável ambiental.

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Dados de transferência com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, -- ajuda|Mostra o conteúdo da ajuda para o comando env. |
|--show-sensível|Mostra variáveis ambientais sensíveis/secretas.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|---cap-mbps flutuar|Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.|
|--cadeia do tipo de saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|
|--cadeia de sufixos de confiança da Microsoft-Sufixes  | Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.|

## <a name="see-also"></a>Veja também

- [azcopy](storage-ref-azcopy.md)

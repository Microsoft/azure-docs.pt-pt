---
title: azcopy env [ Microsoft Docs
description: Este artigo fornece informações de referência para o comando env da azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033752"
---
# <a name="azcopy-env"></a>azcopy env

Mostra as variáveis ambientais que podem configurar o comportamento da AzCopy.

## <a name="synopsis"></a>Sinopse

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Se definir uma variável ambiental utilizando a linha de comando, essa variável será legível no histórico da sua linha de comando. Considere limpar variáveis que contenham credenciais do histórico da sua linha de comando. Para evitar que as variáveis apareçam na sua história, pode utilizar um script para solicitar ao utilizador as suas credenciais e definir a variável ambiental.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, --ajuda|Mostra conteúdo de ajuda para o comando env. |
|--show-sensível|Apresenta variáveis ambientais sensíveis/secretas.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Cobre a taxa de transferência, em megabits por segundo. A entrada momentânea pode variar ligeiramente a partir da tampa. Se esta opção estiver definida para zero, ou for omitida, a entrada não está limitada.|
|--cadeia tipo saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)

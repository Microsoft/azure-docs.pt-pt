---
title: clfs de carga azcopia | Microsoft Docs
titleSuffix: Azure Storage
description: Este artigo fornece informações de referência para o comando de azcopy load clfs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b36ea25180c31fef199aaacb10e46b3caa20f807
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878380"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Transfere dados locais para um Contentor e armazena-os no formato Avere Cloud FileSystem (CLFS) da Microsoft.

## <a name="synopsis"></a>Sinopse

O comando de carga copia dados em recipientes de armazenamento Azure Blob e, em seguida, armazena esses dados no formato Avere Cloud FileSystem (CLFS) da Microsoft. O formato CLFS proprietário é utilizado pela Cache Azure HPC e Avere vFXT para produtos Azure.

Para alavancar este comando, instale a extensão necessária através: pip3 instale clfsload~=1.0.23. Certifique-se de CLFSLoad.py está no seu CAMINHO. Para mais informações sobre este passo, [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) visite.

Este comando é uma opção simples para mover os dados existentes para armazenamento em nuvem para uso com produtos específicos de cache de computação de alto desempenho da Microsoft. 

Como estes produtos utilizam um formato de sistema de ficheiros em nuvem proprietário para gerir dados, esses dados não podem ser carregados através do comando de cópia nativa. 

Em vez disso, os dados devem ser carregados através do próprio produto cache ou através deste comando de carga, que utiliza o formato proprietário correto.
Este comando permite transferir dados sem utilizar o cache. Por exemplo, para pré-povoar o armazenamento ou para adicionar ficheiros a um conjunto de trabalho sem aumentar a carga de cache.

O destino é um recipiente de armazenamento Azure vazio. Quando a transferência estiver concluída, o recipiente de destino pode ser utilizado com uma instância cache Azure HPC ou Avere vFXT para cluster Azure.

> [!NOTE] 
> Esta é uma versão de pré-visualização do comando de carga. Por favor, informe qualquer problema sobre o repo AzCopy Github.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Dados de transferência com armazenamento AzCopy e Blob](./storage-use-azcopy-v10.md#transfer-data)
- [Transferir dados com o AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

Carregue um diretório inteiro num recipiente com um SAS em formato CLFS:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Opções

**--cadeia do tipo de compressão** especificar o tipo de compressão a utilizar para as transferências. Os valores disponíveis são: `DISABLED` `LZ4` . . (predefinição) `LZ4`

**...ajuda**    para o `azcopy load clfs` comando.

**--cadeia de nível de log** Define a verbosidade do registo para o ficheiro de registo, níveis disponíveis: `DEBUG` , , `INFO` `WARNING` . `ERROR` (predefinição) `INFO`

**--erros máximos** uint32 Especifique o número máximo de falhas de transferência a tolerar. Se ocorrerem erros suficientes, pare imediatamente o trabalho.

**--nova sessão**   Iniciar um novo emprego em vez de continuar um existente cuja informação de rastreio é mantida em `--state-path` . (verdade padrão)

**---conserva-hardlinks**    Preservar relações de ligação rígida.

**...-cadeia de caminho do estado** Requeriu caminho para um diretório local para rastreio de estado de trabalho. O caminho deve apontar para um diretório existente para retomar um trabalho. Deve estar vazio para um novo emprego.

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|---cap-mbps flutuar|Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.|
|--cadeia do tipo de saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|
|--cadeia de sufixos de confiança da Microsoft-Sufixes   | Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.|

## <a name="see-also"></a>Veja também

- [azcopia](storage-ref-azcopy.md)

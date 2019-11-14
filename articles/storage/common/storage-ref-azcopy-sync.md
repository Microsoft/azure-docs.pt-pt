---
title: sincronização de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9bfe88c34c78d18f2f5aefb8ae6946b9786030ad
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023503"
---
# <a name="azcopy-sync"></a>azcopy sync

Replica o local de origem para o local de destino.

## <a name="synopsis"></a>Resumo

As horas da última modificação são usadas para comparação. O arquivo será ignorado se a hora da última modificação no destino for mais recente.

Os pares com suporte são:

- < local-> blob do Azure (a autenticação SAS ou OAuth pode ser usada)
- Blob do Azure <-> blob do Azure (a origem deve incluir uma SAS ou está acessível publicamente; a autenticação SAS ou OAuth pode ser usada para o destino)
- Arquivo do Azure <-> arquivo do Azure (a origem deve incluir uma SAS ou pode ser acessado publicamente; A autenticação SAS deve ser usada para o destino)

O comando de sincronização difere do comando de cópia de várias maneiras:

1. Por padrão, o sinalizador recursivo é true e a sincronização copia todos os subdiretórios. Sincronizar somente copiará os arquivos de nível superior dentro de um diretório se o sinalizador recursivo for false.
2. Ao sincronizar entre diretórios virtuais, adicione uma barra à direita no caminho (consulte exemplos) se houver um blob com o mesmo nome de um dos diretórios virtuais.
3. Se o sinalizador ' deleteDestination ' for definido como true ou prompt, a sincronização excluirá arquivos e blobs no destino que não estão presentes na origem.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o armazenamento de arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Avançado

Se você não especificar uma extensão de arquivo, o AzCopy detectará automaticamente o tipo de conteúdo dos arquivos ao carregar do disco local, com base na extensão de arquivo ou no conteúdo (se nenhuma extensão for especificada).

A tabela de pesquisa interna é pequena, mas no UNIX, ela é aumentada pelos arquivos MIME. Types do sistema local, se disponíveis em um ou mais desses nomes:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

No Windows, os tipos de MIME são extraídos do registro.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Exemplos

Sincronizar um único arquivo:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

O mesmo que acima, mas desta vez, também calcula o hash MD5 do conteúdo do arquivo e o salva como a propriedade Content-MD5 do blob:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Sincronize um diretório inteiro incluindo seus subdiretórios (Observe que recursivo é ativado por padrão):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

ou

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Sincronizar somente os arquivos principais dentro de um diretório, mas não seus subdiretórios:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Sincronizar um subconjunto de arquivos em um diretório (por exemplo: somente arquivos jpg e PDF, ou se o nome do arquivo for "exactname"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Sincronize um diretório inteiro, mas exclua determinados arquivos do escopo (por exemplo: cada arquivo que começa com foo ou termina com bar):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Sincronizar um único blob:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

Sync a virtual directory:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Sincronizar um diretório virtual que tem o mesmo nome de um blob (adicione uma barra à direita no caminho para eliminar a ambiguidade):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Sincronizar um diretório de arquivos do Azure (mesma sintaxe que BLOB):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Se os sinalizadores de inclusão/exclusão forem usados juntos, somente os arquivos correspondentes aos padrões de inclusão serão examinados, mas aqueles que correspondem aos padrões de exclusão seriam sempre ignorados.

## <a name="options"></a>Opções

**--Block-size-MB** float Use esse tamanho de bloco (especificado na MIB) ao carregar no armazenamento do Azure ou baixar do armazenamento do Azure. O padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas (por exemplo: 0,25).

--a cadeia de caracteres **de verificação MD5** especifica como os hashes MD5 estritamente devem ser validados durante o download. Essa opção só está disponível durante o download. Os valores disponíveis incluem: NOCHECK, LogOn, FailIfDifferent, FailIfDifferentOrMissing. (padrão ' FailIfDifferent '). (padrão "FailIfDifferent")

**--delete –** a cadeia de caracteres de destino define se é para excluir arquivos extras do destino que não estão presentes na origem. Pode ser definido como true, false ou prompt. Se definido como prompt, o usuário receberá uma pergunta antes de agendar arquivos e BLOBs para exclusão. (padrão ' false '). (padrão "false")

**--Exclude-atributos** String (somente Windows) exclua os arquivos cujos atributos correspondem à lista de atributos. Por exemplo: A; & D

**--Exclude-Pattern** cadeia de caracteres excluir arquivos onde o nome corresponde à lista padrão. Por exemplo: *. jpg;* . PDF; exatoname

**-h,--** ajuda da ajuda para sincronização

**--include-** a cadeia de caracteres de atributos (somente Windows) inclui apenas arquivos cujos atributos correspondem à lista de atributos. Por exemplo: A; & D

**--include-** a cadeia de caracteres de padrão inclui apenas arquivos em que o nome corresponde à lista de padrões. Por exemplo: *. jpg;* . PDF; exatoname

**--** cadeia de caracteres de nível de log defina o detalhamento de log para o arquivo de log, níveis disponíveis: informações (todas as solicitações e respostas), aviso (respostas lentas), erro (somente solicitações com falha) e nenhum (nenhum log de saída). (informações padrão). (padrão "INFO")

**--Put-MD5**                     Crie um hash MD5 de cada arquivo e salve o hash como a propriedade Content-MD5 do BLOB ou arquivo de destino. (Por padrão, o hash não é criado.) Disponível somente ao carregar.

**--recursivo**                   True por padrão, examinar subdiretórios recursivamente ao sincronizar entre diretórios. (padrão true). (padrão true)

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)

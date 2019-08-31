---
title: sincronização de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy Sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195766"
---
# <a name="azcopy-sync"></a>sincronização de azcopy

Replica o local de origem para o local de destino.

## <a name="synopsis"></a>Resumo

As horas da última modificação são usadas para comparação. O arquivo será ignorado se a hora da última modificação no destino for mais recente.

Os pares com suporte são:

- < local-> blob do Azure (a autenticação SAS ou OAuth pode ser usada)

O comando de sincronização difere do comando de cópia de várias maneiras:

  1. O sinalizador recursivo está ativado por padrão.
  2. A origem e o destino não devem conter padrões (como * ou?).
  3. Os sinalizadores de inclusão e exclusão podem ser uma lista de padrões correspondentes aos nomes de arquivo. Veja a seção de exemplo para ilustração.
  4. Se houver arquivos ou BLOBs no destino que não estão presentes na origem, o usuário será solicitado a excluí-los.

     Esse aviso pode ser silenciado usando os sinalizadores correspondentes para responder automaticamente à pergunta de exclusão.

### <a name="advanced"></a>Avançadas

O AzCopy detecta automaticamente o tipo de conteúdo dos arquivos ao carregar do disco local, com base na extensão de arquivo ou no conteúdo (se nenhuma extensão for especificada).

A tabela de pesquisa interna é pequena, mas no UNIX, ela é aumentada pelos arquivos MIME. Types do sistema local, se disponíveis em um ou mais desses nomes:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

No Windows, os tipos de MIME são extraídos do registro.

```azcopy
azcopy sync [flags]
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

> [!NOTE]
> Se os sinalizadores de inclusão/exclusão forem usados juntos, somente os arquivos correspondentes aos padrões de inclusão serão examinados, mas aqueles que correspondem aos padrões de exclusão seriam sempre ignorados.

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|--bloco-tamanho-MB float|Use esse tamanho de bloco (especificado na MiB) ao carregar para o armazenamento do Azure ou baixar do armazenamento do Azure. O padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas (por exemplo: 0,25).|
|--verificação-cadeia de caracteres MD5|Especifica como os hashes MD5 estritamente devem ser validados durante o download. Essa opção só está disponível durante o download. Os valores disponíveis incluem: NOCHECK, LogOn, FailIfDifferent, FailIfDifferentOrMissing. (padrão "FailIfDifferent").|
|--Delete-cadeia de caracteres de destino|define se é para excluir arquivos extras do destino que não estão presentes na origem. Pode ser definido como true, false ou prompt. Se definido como prompt, o usuário receberá uma pergunta antes de agendar arquivos e BLOBs para exclusão. (padrão "false").|
|--excluir cadeia de caracteres|Exclua os arquivos onde o nome corresponde à lista padrão. Por exemplo: *. jpg;* . PDF; exatoname.|
|-h,--ajuda|Mostra o conteúdo da ajuda para o comando de sincronização.|
|--incluir Cadeia de caracteres|Inclua somente os arquivos em que o nome corresponde à lista padrão. Por exemplo: *. jpg;* . PDF; exatoname.|
|--Cadeia de caracteres de nível de log|Defina o detalhamento de log para o arquivo de log, níveis disponíveis: INFORMAÇÕES (todas as solicitações/respostas), aviso (respostas lentas), erro (somente solicitações com falha) e nenhum (nenhum log de saída). (padrão "INFO").|
|--Put-MD5|Crie um hash MD5 de cada arquivo e salve o hash como a propriedade Content-MD5 do BLOB ou arquivo de destino. (Por padrão, o hash não é criado.) Disponível somente ao carregar.|
|--recursivo|True por padrão, examinar subdiretórios recursivamente ao sincronizar entre diretórios. (padrão true).|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)

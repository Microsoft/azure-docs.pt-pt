---
title: sincronização de azcopy [ Microsoft Docs
description: Este artigo fornece informações de referência para o comando de sincronização da azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d855019be7f357a35a26d14e68ba3d427d984e17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086033"
---
# <a name="azcopy-sync"></a>azcopy sync

Replica a localização de origem para o local de destino.

## <a name="synopsis"></a>Sinopse

Os últimos tempos modificados são usados para comparação. O ficheiro é ignorado se a última hora modificada no destino for mais recente.

Os pares suportados são:

- <-> local (pode ser utilizada a autenticação SAS ou OAuth)
- Azure Blob <-> Azure Blob (Fonte deve incluir um SAS ou é acessível ao público; ou a autenticação SAS ou OAuth pode ser usada para o destino)
- Ficheiro Azure <-> Ficheiro Azure (Fonte deve incluir um SAS ou acessível ao público; A autenticação SAS deve ser utilizada para o destino)

O comando de sincronização difere do comando de cópia de várias maneiras:

1. Por padrão, a bandeira recursiva é verdadeira e sincroniza todos os subdiretórios. Sync só copia os ficheiros de alto nível dentro de um diretório se a bandeira recursiva for falsa.
2. Ao sincronizar entre diretórios virtuais, adicione um corte de rasto sintetizador no caminho (consulte exemplos) se houver uma bolha com o mesmo nome que um dos diretórios virtuais.
3. Se a bandeira 'deleteDestination' estiver definida como verdadeira ou pronta, então a sincronização eliminará ficheiros e bolhas no destino que não estão presentes na fonte.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Avançado

Se não especificar uma extensão de ficheiro, a AzCopy deteta automaticamente o tipo de conteúdo dos ficheiros ao enviar a partir do disco local, com base na extensão ou conteúdo do ficheiro (se não for especificada nenhuma extensão).

A mesa de lookup incorporada é pequena, mas no Unix, é aumentada pelos ficheiros mímicas do sistema local, se disponível sob um ou mais destes nomes:

- /etc/mímica.tipos
- /etc/apache2/mime.types
- /etc/apache/mime.type

No Windows, os tipos MIME são extraídos do registo.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Exemplos

Sincronizar um único ficheiro:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

> [!NOTE]
> A bolha de destino *deve* existir. Utilize `azcopy copy` para copiar um único ficheiro que ainda não exista no destino. Caso contrário, ocorre o `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container`seguinte erro: .

O mesmo que acima, mas desta vez, também calcule o haxixe MD5 do conteúdo do ficheiro e guarde-o como propriedade content-MD5 da bolha:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Sincronizar um diretório inteiro, incluindo os seus subdirectivos (nota de que a recursiva está em predefinição):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

ou

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Sincronizar apenas os ficheiros superiores dentro de um diretório, mas não os seus subdiretórios:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Sincronizar um subconjunto de ficheiros num diretório (por exemplo: apenas ficheiros JPG e pdf, ou se o nome do ficheiro for "exatidão"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Sincronizar um diretório inteiro, mas excluir certos ficheiros do âmbito (por exemplo: cada ficheiro que comece com foo ou termina com barra):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Sincronizar uma única bolha:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Sincronizar um diretório virtual:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Sincronizar um diretório virtual que tenha o mesmo nome que uma bolha (adicione um corte de rasto no caminho para desambiguar):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Sync an Azure File diretório (a mesma sintaxe que Blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Se incluir/excluir bandeiras são usadas em conjunto, apenas os ficheiros correspondentes aos padrões incluem, mas aqueles que correspondem aos padrões de exclusão seriam sempre ignorados.

## <a name="options"></a>Opções

**--bloco-tamanho mb** flutuante Utilize este tamanho de bloco (especificado no MiB) ao fazer o upload para o Armazenamento Azure ou o download do Azure Storage. A predefinição é automaticamente calculada com base no tamanho do ficheiro. São permitidas frações decimais (por exemplo: 0,25).

**-check-md5** string Especifica como os hashes MD5 devem ser validados estritamente ao descarregar. Esta opção só está disponível no download. Os valores disponíveis incluem: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (predefinido 'FailIfDifferent'). (padrão "FailIfDifferent")

**-excluir a** cadeia de destino Define se deve eliminar ficheiros extra do destino que não estão presentes na fonte. Pode ser definido para verdade, falso ou rápido. Se for programado para ser solicitado, o utilizador será questionado antes de agendar ficheiros e bolhas para eliminação. (predefinido 'falso'). ("falso" predefinido)

**--excluir-atribui** a cadeia (apenas ao Windows) Excluir ficheiros cujos atributos correspondem à lista de atributos. Por exemplo: A; S; R

**-excluir-caminho** de corda Exclua estes caminhos ao copiar. Esta opção não suporta caracteres wildcard (*). Verifica prefixo relativo do caminho (por exemplo: myFolder;myFolder/subDirName/file.pdf). Quando utilizados em combinação com a transversala de conta, os caminhos não incluem o nome do recipiente.

**-excluir a** cadeia de padrão Exclua ficheiros onde o nome corresponde à lista de padrões. Por exemplo: \*.jpg; \*.pdf;exactName

**-h, --ajuda** para sincronizar

**--incluir acadeia -apenas** com o Windows) Incluir apenas ficheiros cujos atributos correspondem à lista de atributos. Por exemplo: A; S; R

**-incluir** a cadeia de padrões Inclua apenas ficheiros onde o nome corresponde à lista de padrões. Por exemplo: \*.jpg; \*.pdf;exactName

**--cadeia de log-level** Defina a verbosidade do registo, níveis disponíveis: INFO (todos os pedidos e respostas), AVISO (respostas lentas), ERROR (apenas pedidos falhados) e NENHUM (sem registos de saída). (INFORMAÇÃO predefinida). ("INFO" padrão)

**-put-md5**                     Crie um haxixe MD5 de cada ficheiro e guarde o haxixe como propriedade Content-MD5 da bolha de destino ou ficheiro. (Por padrão, o hash NÃO é criado.) Só disponível no momento do upload.

**-recursivo**                   É verdade por defeito, procure sub-directórios recursivamente ao sincronizar entre diretórios. (padrão verdadeiro). (por defeito verdadeiro)

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Cobre a taxa de transferência, em megabits por segundo. A entrada momentânea pode variar ligeiramente a partir da tampa. Se esta opção estiver definida para zero, ou for omitida, a entrada não está limitada.|
|--cadeia tipo saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)

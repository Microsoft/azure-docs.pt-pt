---
title: cópia de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy Copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c15d188e333bea5e74fa65d2bbdf38ae7fadc246
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195740"
---
# <a name="azcopy-copy"></a>cópia azcopy

Copia os dados de origem para um local de destino.

## <a name="synopsis"></a>Resumo

As instruções com suporte são:

- < local-> blob do Azure (autenticação SAS ou OAuth)
- < local-> arquivo do Azure (autenticação SAS de compartilhamento/diretório)
- < local-> ADLS Gen 2 (autenticação SAS, OAuth ou SharedKey)
- Blob do Azure (SAS ou público) <-> blob do Azure (autenticação SAS ou OAuth)
- Arquivo do Azure (SAS)-> blob de blocos do Azure (autenticação SAS ou OAuth)
- AWS S3 (chave de acesso)-> blob de blocos do Azure (autenticação SAS ou OAuth)

Consulte os exemplos para obter mais informações.

### <a name="advanced"></a>Avançadas

O AzCopy detecta automaticamente o tipo de conteúdo dos arquivos ao carregar do disco local, com base na extensão de arquivo ou no conteúdo (se nenhuma extensão for especificada).

A tabela de pesquisa interna é pequena, mas no UNIX, ela é aumentada pelos arquivos MIME. Types do sistema local, se disponíveis em um ou mais desses nomes:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

No Windows, os tipos de MIME são extraídos do registro. Esse recurso pode ser desativado com a ajuda de um sinalizador. Veja a seção sinalizador.

> [!IMPORTANT]
> Se você definir uma variável de ambiente usando a linha de comando, essa variável será legível no seu histórico de linha de comando. Considere limpar as variáveis que contêm credenciais do seu histórico de linha de comando. Para impedir que as variáveis apareçam no seu histórico, você pode usar um script para solicitar ao usuário suas credenciais e definir a variável de ambiente.

```azcopy
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exemplos

Carregar um único arquivo usando a autenticação OAuth.

Se você ainda não fez logon no AzCopy, use `azcopy login` o comando antes de executar o comando a seguir.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

O mesmo que acima, mas desta vez também calcula o hash MD5 do conteúdo do arquivo e o salva como a propriedade Content-MD5 do blob:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Carregar um único arquivo com uma SAS:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Carregar um único arquivo com uma SAS usando o encanamento (somente blobs de blocos):

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Carregar um diretório inteiro com uma SAS:

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

ou

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5
```

Carregar um conjunto de arquivos com uma SAS usando caracteres curinga:

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Carregar arquivos e diretórios com uma SAS usando caracteres curinga:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Baixe um único arquivo usando a autenticação OAuth.

Se você ainda não fez logon no AzCopy, use `azcopy login` o comando antes de executar o comando a seguir.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Baixar um único arquivo com uma SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Baixar um único arquivo com uma SAS usando o encanamento (somente blobs de blocos):

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

Baixar um diretório inteiro com uma SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

Baixe um conjunto de arquivos com uma SAS usando caracteres curinga:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir"
```

Baixar arquivos e diretórios com uma SAS usando caracteres curinga:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir" --recursive=true
```

Copiar um único blob com SAS para outro blob com SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copie um único blob com SAS para outro blob com o token OAuth.

Se você ainda não fez logon no AzCopy, use `azcopy login` o comando antes de executar o comando a seguir. O token OAuth é usado para acessar a conta de armazenamento de destino.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Copie um diretório inteiro do diretório virtual de blob com SAS para outro diretório virtual de blob com SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Copie os dados de uma conta inteira da conta de blob com SAS para outra conta de blob com SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Copiar um único objeto do S3 com a chave de acesso para o blob com SAS:

Defina a variável de ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a origem S3.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copie um diretório inteiro do S3 com a chave de acesso para o diretório virtual do blob com SAS:

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Consulte https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html para saber mais sobre o que [Folder] significa para S3. Defina a variável de ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a origem S3.

Copie todos os buckets no serviço S3 com a chave de acesso para a conta de blob com SAS:

Defina a variável de ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a origem S3.

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Copie todos os buckets em uma região S3 com a chave de acesso para a conta de blob com SAS:

```azcopy
azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

Defina a variável de ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a origem S3.

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|--blob-tipo de cadeia de caracteres|Define o tipo de blob no destino. Isso é usado para carregar BLOBs e ao copiar entre contas (o padrão é "None").|
|--bloco-blob-cadeia de caracteres da camada|Carregar blob de blocos no armazenamento do Azure usando esta camada de BLOB. (padrão "None").|
|--bloco-tamanho-MB float |Use esse tamanho de bloco (especificado em MiB) ao carregar para o armazenamento do Azure e baixar do armazenamento do Azure. O valor padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas (por exemplo: 0,25).|
|--Cadeia de controle de cache|Defina o cabeçalho Cache-Control. Retornado no download.|
|--verificação-cadeia de caracteres MD5|Especifica como os hashes MD5 estritamente devem ser validados durante o download. Disponível somente ao baixar. Opções disponíveis: NOCHECK, LogOn, FailIfDifferent, FailIfDifferentOrMissing. (padrão "FailIfDifferent")|
|--Cadeia de caracteres de disposição de conteúdo|Defina o cabeçalho de disposição de conteúdo. Retornado no download.|
|--Cadeia de caracteres de codificação de conteúdo|Defina o cabeçalho de codificação de conteúdo. Retornado no download.|
|--Cadeia de caracteres de linguagem de conteúdo|Defina o cabeçalho de linguagem de conteúdo. Retornado no download.|
|--Content-Type cadeia de caracteres |Especifica o tipo de conteúdo do arquivo. Implica no tipo não-palpite-MIME. Retornado no download.|
|--excluir cadeia de caracteres|Exclua esses arquivos ao copiar. Suporte ao uso de *.|
|--Exclude-blob-tipo de cadeia de caracteres|Opcionalmente, especifica o tipo de BLOB (BlockBlob/PageBlob/AppendBlob) a ser excluído ao copiar BLOBs do contêiner ou da conta. O uso desse sinalizador não é aplicável para copiar dados de não Azure-Service para o serviço. Mais de um blob deve ser separado por '; '.|
|--Follow-symlinks|Siga links simbólicos ao carregar do sistema de arquivos local.|
|--Cadeia de caracteres de-para|Opcionalmente, especifica a combinação de destino de origem. Por exemplo: LocalBlob, BlobLocal, LocalBlobFS.|
|-h,--ajuda|Mostra o conteúdo da ajuda para o comando de cópia. |
|--Cadeia de caracteres de nível de log|Defina o detalhamento de log para o arquivo de log, níveis disponíveis: INFORMAÇÕES (todas as solicitações/respostas), aviso (respostas lentas), erro (somente solicitações com falha) e nenhum (nenhum log de saída). (padrão "INFO").|
|--Cadeia de caracteres de metadados|Carregue no armazenamento do Azure com esses pares de chave-valor como metadados.|
|--no-palpite-tipo MIME|Impede que o AzCopy detecte o tipo de conteúdo com base na extensão ou no conteúdo do arquivo.|
|--substituir|Substitua os arquivos/BLOBs conflitantes no destino se esse sinalizador for definido como true. (padrão true).|
|--página – cadeia de caracteres de camada de BLOB |Carregue o blob de páginas no armazenamento do Azure usando essa camada de BLOB. (padrão "None").|
|--preservar-último-modificado-hora|Disponível somente quando o destino é sistema de arquivos.|
|--Put-MD5|Crie um hash MD5 de cada arquivo e salve o hash como a propriedade Content-MD5 do BLOB ou arquivo de destino. (Por padrão, o hash não é criado.) Disponível somente ao carregar.|
|--recursivo|Examine os subdiretórios recursivamente ao carregar do sistema de arquivos local.|
|--S2S-Detect-origem-alterado|Verifique se a origem foi alterada após a enumeração. Para a cópia S2S, como a origem é um recurso remoto, validar se a origem foi alterada precisa de custos de solicitação adicionais.|
|--S2S-Handle-inválida-cadeia de caracteres de metadados |Especifica como as chaves de metadados inválidas são tratadas. Opções disponíveis: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (padrão "ExcludeIfInvalid").|
|--S2S-preserve-acesso-camada|Preserve a camada de acesso durante a cópia de serviço para serviço. Consulte armazenamento de [BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](../blobs/storage-blob-storage-tiers.md) para garantir que a conta de armazenamento de destino dê suporte à configuração da camada de acesso. Nos casos em que não há suporte para a configuração da camada de acesso, use s2sPreserveAccessTier = false para ignorar a cópia da camada de acesso.  (padrão true).|
|--S2S-preserve-Propriedades|Preserve as propriedades completas durante a cópia de serviço para serviço. Para S3 e fonte de arquivo não único de arquivo do Azure, como a operação de lista não retorna propriedades completas de objetos e arquivos, para preservar as propriedades completas, o AzCopy precisa enviar uma solicitação adicional por objeto e arquivo. (padrão true).|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)

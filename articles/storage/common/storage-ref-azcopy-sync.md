---
title: sincronização de azcopia Microsoft Docs
description: Este artigo fornece informações de referência para o comando de sincronização de azcopia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: de97640ab462ac8ea7342d235d0fad802c232179
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783373"
---
# <a name="azcopy-sync"></a>azcopy sync

Replica a localização da fonte para o local do destino.

## <a name="synopsis"></a>Sinopse

Os últimos tempos modificados são utilizados para comparação. O ficheiro é ignorado se o último tempo modificado no destino for mais recente.

Os pares suportados são:

- local <-> Azure Blob (pode ser utilizada a autenticação SAS ou OAuth)
- Azure Blob <-> Azure Blob (A fonte deve incluir um SAS ou é acessível ao público; ou a autenticação SAS ou OAuth pode ser usada para o destino)
- Ficheiro Azure <-> Ficheiro Azure (A fonte deve incluir um SAS ou é acessível ao público; A autenticação SAS deve ser utilizada para o destino)

O comando de sincronização difere do comando de cópia de várias maneiras:

1. Por predefinição, a bandeira recursiva é verdadeira e sincroniza todas as subdireições. Sincronizar apenas os ficheiros de nível superior dentro de um diretório se a bandeira recursiva for falsa.
2. Ao sincronizar entre diretórios virtuais, adicione um corte de fuga ao caminho (consulte exemplos) se houver uma bolha com o mesmo nome que um dos diretórios virtuais.
3. Se a `deleteDestination` bandeira for definida como verdadeira ou pronta, a sincronização apagará ficheiros e bolhas no destino que não estão presentes na fonte.

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Dados de transferência com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Avançado

Se não especificar uma extensão de ficheiro, o AzCopy deteta automaticamente o tipo de conteúdo dos ficheiros ao carregar a partir do disco local, com base na extensão do ficheiro ou no conteúdo (se não for especificada nenhuma extensão).

A tabela de procuração incorporada é pequena, mas no Unix, é aumentada pelos ficheiros mime.types do sistema local, se disponível sob um ou mais destes nomes:

- /etc/mime.tipos
- /etc/apache2/mime.tipos
- /etc/apache/mime.tipos

No Windows, os tipos de MIME são extraídos do registo.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Exemplos

Sincronizar um único ficheiro:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

O mesmo que acima, mas também calcular um hash MD5 do conteúdo do ficheiro, e, em seguida, guardar esse haxixe MD5 como propriedade content-MD5 da bolha. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Sincronize todo um diretório, incluindo as suas subdireições (note que a recursiva está por defeito em):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

ou

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Sincronizar apenas os ficheiros dentro de um diretório, mas não subdiretórios ou ficheiros dentro de subdiretórios:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Sincronia um subconjunto de ficheiros num diretório (por exemplo: apenas ficheiros JPG e pdf, ou se o nome do ficheiro `exactName` for):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Sincronizar um diretório inteiro mas excluir certos ficheiros do âmbito (por exemplo: cada ficheiro que comece com foo ou termine com barra):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

Sincronia uma única bolha:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Sincronizar um diretório virtual:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Sincronize um diretório virtual com o mesmo nome de uma bolha (adicione um corte de fuga ao caminho para desambiguar):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Sincronizar um diretório de ficheiros Azure (mesma sintaxe que blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Se forem utilizadas bandeiras de inclusão/exclusão, apenas os ficheiros correspondentes aos padrões incluídos seriam analisados, mas aqueles que correspondem aos padrões de exclusão seriam sempre ignorados.

## <a name="options"></a>Opções

**--block-size-mb** float Use this block size (especificado em MiB) ao carregar para Azure Storage ou descarregar a partir do Azure Storage. O predefinimento é calculado automaticamente com base no tamanho do ficheiro. São permitidas frações decimais (por exemplo: `0.25` ).

**---cadeia de verificação-md5** Especifica como os hashes MD5 devem ser validados estritamente ao descarregar. Esta opção só está disponível ao descarregar. Os valores disponíveis incluem: `NoCheck` , , , . `LogOnly` `FailIfDifferent` `FailIfDifferentOrMissing` . (predefinição). `FailIfDifferent` (predefinição) `FailIfDifferent`

**--eliminar** a cadeia de destino Define se deve eliminar ficheiros extra do destino que não estão presentes na fonte. Pode ser definido para `true` `false` , ou `prompt` . Se estiver `prompt` definido, o utilizador será questionado antes de agendar ficheiros e bolhas para eliminação. (predefinição). `false` (predefinição) `false`

**--excluir-atributos** cadeia (apenas Windows) Exclui ficheiros cujos atributos correspondem à lista de atributos. Por exemplo: `A;S;R`

**--excluir-cadeia de caminhos** Excluir estes caminhos ao comparar a fonte com o destino. Esta opção não suporta caracteres wildcard (*). Verifica o prefixo relativo do caminho (Por exemplo: `myFolder;myFolder/subDirName/file.pdf` ).

**--excluir-padrão** excluir ficheiros onde o nome corresponde à lista de padrões. Por exemplo: `*.jpg;*.pdf;exactName`

**...ajuda**    para sincronizar.

**--incluir-atributos (apenas** Windows) Inclui apenas ficheiros cujos atributos correspondem à lista de atributos. Por exemplo: `A;S;R`

**-- incluir** a cadeia de padrão Inclua apenas ficheiros onde o nome corresponde à lista de padrões. Por exemplo: `*.jpg;*.pdf;exactName`

**--cadeia de nível de log** Define a verbosidade do registo para o ficheiro de registo, níveis disponíveis: `INFO` (todos os pedidos e respostas), `WARNING` (respostas lentas), `ERROR` (apenas pedidos falhados) e `NONE` (sem registos de saída). (predefinição). `INFO` 

**--preservar-smb-info**   Falso por defeito. Preserva a informação de propriedade SMB (última hora de escrita, tempo de criação, atributo bits) entre recursos conscientes de SMB (Windows e Azure Files). Esta bandeira aplica-se tanto a ficheiros como a pastas, a menos que seja especificado um filtro apenas de ficheiro (por exemplo, incluir o padrão). A informação transferida para pastas é a mesma que para ficheiros, com exceção da Última Hora de Escrita que não está preservada para pastas.

**---conserva-smb-permissões**   Falso por defeito. Preserva acls SMB entre recursos conscientes (Windows e Azure Files). Esta bandeira aplica-se tanto a ficheiros como a pastas, a menos que seja especificado um filtro apenas para ficheiros (por exemplo, `include-pattern` ).

**--put-md5**     Crie um haxixe MD5 de cada ficheiro e guarde o haxixe como propriedade do Conteúdo-MD5 da bolha de destino ou ficheiro. (Por defeito, o haxixe NÃO é criado.) Só disponível no upload.

**--recursivo** `True` por padrão, procure subdisposições de forma recorrente ao sincronizar entre diretórios.     (predefinição). `True` 

**--s2s-preserve-access-tier**  Preservar o nível de acesso durante o serviço à cópia de serviço. Consulte o [armazenamento do Azure Blob: níveis de acesso quentes, frescos e de arquivo](../blobs/storage-blob-storage-tiers.md) para garantir que a conta de armazenamento de destino suporta a definição do nível de acesso. Nos casos em que a definição do nível de acesso não seja suportada, utilize o s2sPreserveAccessTier=falso para contornar o nível de acesso de cópia. (predefinição). `true` 

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.|
|--cadeia do tipo de saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|
|--cadeia de sufixos de confiança da Microsoft-Sufixes   |Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é ' *.core.windows.net;* . core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.|

## <a name="see-also"></a>Ver também

- [azcopia](storage-ref-azcopy.md)
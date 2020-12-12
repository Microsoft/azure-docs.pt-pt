---
title: cópia azcopia . Microsoft Docs
description: Este artigo fornece informações de referência para o comando de cópia de azcopia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 6390aafca4937a480e4d92ff04003a294b9c0e20
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356179"
---
# <a name="azcopy-copy"></a>azcopy copy

Copia dados de origem para um local de destino.

## <a name="synopsis"></a>Sinopse

Copia dados de origem para um local de destino. As direções apoiadas são:

  - <-> local Azure Blob (autenticação SAS ou OAuth)
  - arquivos Azure <-> locais (autenticação SAS de partilha/diretório)
  - local <-> Azure Data Lake Storage Gen 2 (SAS, OAuth ou autenticação de chaves partilhadas)
  - Azure Blob (SAS ou público) -> Azure Blob (autenticação SAS ou OAuth)
  - Azure Blob (SAS ou público) -> Ficheiros Azure (SAS)
  - Ficheiros Azure (SAS) -> Ficheiros Azure (SAS)
  - Azure Files (SAS) -> Azure Blob (autenticação SAS ou OAuth)
  - Amazon Web Services (AWS) S3 (Chave de Acesso) -> Azure Block Blob (autenticação SAS ou OAuth)

Para mais informações, consulte a secção de exemplos deste artigo.

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Dados de transferência com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Avançado

O AzCopy deteta automaticamente o tipo de conteúdo dos ficheiros quando os envia a partir do disco local. O AzCopy deteta o tipo de conteúdo com base na extensão ou conteúdo do ficheiro (se não for especificada nenhuma extensão).

A tabela de procuração incorporada é pequena, mas no Unix, é aumentada pelos ficheiros do sistema local `mime.types` se estiverem disponíveis sob um ou mais destes nomes:

- /etc/mime.tipos
- /etc/apache2/mime.tipos
- /etc/apache/mime.tipos

No Windows, os tipos de MIME são extraídos do registo. Esta função pode ser desligada com a ajuda de uma bandeira. Para mais informações, consulte a secção de bandeira deste artigo.

Se definir uma variável ambiental utilizando a linha de comando, essa variável será legível no histórico da sua linha de comando. Considere limpar variáveis que contenham credenciais do seu histórico de linha de comando. Para evitar que as variáveis apareçam na sua história, pode utilizar um script para solicitar ao utilizador as suas credenciais e definir a variável ambiental.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exemplos

Faça o upload de um único ficheiro utilizando a autenticação OAuth. Se ainda não tiver iniciado sessão no AzCopy, execute o `azcopy login` comando antes de executar o seguinte comando.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
O mesmo que acima, mas desta vez, também calcular o haxixe MD5 do conteúdo do ficheiro e guardá-lo como propriedade content-MD5 da bolha:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Faça o upload de um único ficheiro utilizando um token SAS:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Faça o upload de um único ficheiro utilizando um token SAS e tubagens (apenas bolhas de bloco):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Faça o upload de um diretório inteiro utilizando um token SAS:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

ou

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Faça o upload de um conjunto de ficheiros utilizando um token SAS e caracteres wildcard (*):
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Faça upload de ficheiros e diretórios utilizando um token SAS e caracteres wildcard (*):

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Faça upload de ficheiros e diretórios para a conta de Armazenamento Azure e descreva as etiquetas codificadas de cadeia de consulta na bolha. 

- Para definir tags {key = "bla bla", val = "foo"} e {key = "bla bla 2", val = "bar"}, utilize a seguinte sintaxe : `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- As chaves e os valores são codificados por URL e os pares de valor-chave são separados por um ampersand ('&')

- Ao definir tags nas bolhas, existem permissões adicionais ('t' para tags) em SAS sem as quais o serviço devolverá o erro de autorização.

Faça o download de um único ficheiro utilizando a autenticação OAuth. Se ainda não tiver iniciado sessão no AzCopy, execute o `azcopy login` comando antes de executar o seguinte comando.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Descarregue um único ficheiro utilizando um token SAS:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Faça o download de um único ficheiro utilizando um token SAS e, em seguida, canalizando a saída para um ficheiro (apenas bolhas de bloco):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Faça o download de um diretório inteiro utilizando um token SAS:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Uma nota sobre a utilização de um personagem wildcard (*) em URLs:

Só há duas formas suportadas de usar um personagem wildcard numa URL. 

- Pode utilizar um logo após o corte dianteiro final (/) de um URL. Esta utilização do personagem wildcard copia todos os ficheiros num diretório diretamente para o destino sem os colocar numa subdiretório. 

- Também pode ser um personagem wildcard em nome de um recipiente, desde que o URL se refira apenas a um recipiente e não a uma bolha. Pode utilizar esta abordagem para obter ficheiros a partir de um subconjunto de contentores. 

Descarregue o conteúdo de um diretório sem copiar o próprio diretório.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Faça o download de uma conta de armazenamento inteira.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Faça o download de um subconjunto de contentores dentro de uma conta de armazenamento utilizando um símbolo wildcard (*) no nome do recipiente.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Copie uma única bolha para outra bolha utilizando um token SAS.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copie uma única bolha para outra bolha utilizando um token SAS e um token Auth. Você tem que usar um token SAS no final do URL da conta de origem, mas a conta de destino não precisa de uma se você iniciar sessão no AzCopy usando o `azcopy login` comando. 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Copie um diretório virtual blob para outro utilizando um token SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Copie todos os recipientes blob, diretórios e bolhas da conta de armazenamento para outra, utilizando um token SAS:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copie um único objeto para Blob Storage da Amazon Web Services (AWS) S3 usando uma chave de acesso e um token SAS. Em primeiro lugar, descreva a variável ambiental `AWS_ACCESS_KEY_ID` e para a fonte `AWS_SECRET_ACCESS_KEY` AWS S3.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Copie um diretório inteiro para Blob Storage a partir de AWS S3 utilizando uma chave de acesso e um token SAS. Em primeiro lugar, descreva a variável ambiental `AWS_ACCESS_KEY_ID` e para a fonte `AWS_SECRET_ACCESS_KEY` AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Consulte https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html para entender melhor o espaço reservado [pasta].

Copie todos os baldes para o Blob Storage da Amazon Web Services (AWS) utilizando uma chave de acesso e um token SAS. Em primeiro lugar, descreva a variável ambiental `AWS_ACCESS_KEY_ID` e para a fonte `AWS_SECRET_ACCESS_KEY` AWS S3.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copie todos os baldes para o Blob Storage a partir de uma região da Amazon Web Services (AWS) usando uma chave de acesso e um token SAS. Em primeiro lugar, descreva a variável ambiental `AWS_ACCESS_KEY_ID` e para a fonte `AWS_SECRET_ACCESS_KEY` AWS S3.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Copie um subconjunto de baldes utilizando um símbolo wildcard (*) no nome do balde. Tal como os exemplos anteriores, vai precisar de uma chave de acesso e de um token SAS. Certifique-se de que define a variável ambiente `AWS_ACCESS_KEY_ID` e para a fonte `AWS_SECRET_ACCESS_KEY` AWS S3.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Transfira ficheiros e diretórios para a conta de Armazenamento Azure e descreva as etiquetas codificadas de cadeia de consulta na bolha. 

- Para definir tags {key = "bla bla", val = "foo"} e {key = "bla bla 2", val = "bar"}, utilize a seguinte sintaxe : `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- As chaves e os valores são codificados por URL e os pares de valor-chave são separados por um ampersand ('&')
    
- Ao definir tags nas bolhas, existem permissões adicionais ('t' para tags) em SAS sem as quais o serviço devolverá o erro de autorização.

## <a name="options"></a>Opções

**--backup** Ativa o SeBackupPrivilege do Windows para uploads, ou SeRestorePrivilege para downloads, para permitir que o AzCopy veja e leia todos os ficheiros, independentemente das permissões do sistema de ficheiros, e para restaurar todas as permissões. Requer que a conta que executa a AzCopy já tenha estas permissões (por exemplo, tem direitos de administrador ou é membro do `Backup Operators` grupo). Esta bandeira ativa privilégios que a conta já tem.

**--blob-tags conjunto** tags em bolhas para categorizar dados na sua conta de armazenamento.

**---tip** string tipo blob Define o tipo de bolha no destino. Isto é utilizado para o upload de bolhas e ao copiar entre contas `Detect` (padrão). Valores válidos `Detect` `BlockBlob` incluem, , e `PageBlob` `AppendBlob` . Ao copiar entre contas, um valor de `Detect` faz com que a AzCopy utilize o tipo de bolha de origem para determinar o tipo de bolha de destino. Ao carregar um ficheiro, `Detect` determina se o ficheiro é um VHD ou um ficheiro VHDX baseado na extensão do ficheiro. Se o ficheiro for um ficheiro VHD ou VHDX, a AzCopy trata o ficheiro como uma bolha de página. (predefinição "Detetar")

**--bloco-blob-nível** Bloco Carregar bloco blob para Azure Storage usando este nível blob. (predefinição "Nenhum")

**--block-size-mb** float Use this block size (especificado em MiB) ao carregar para Azure Storage, e descarregar a partir do Azure Storage. O valor predefinido é calculado automaticamente com base no tamanho do ficheiro. São permitidas frações decimais (Por exemplo: 0,25).

**--cadeia de controlo de cache** Coloque o cabeçalho de controlo de cache. Devolvido no download.

**--check-length** Verifique a duração de um ficheiro no destino após a transferência. Se houver um desfasamento entre a fonte e o destino, a transferência é marcada como falhada. (o valor predefinido `true` é)

**---cadeia de verificação-md5** Especifica como os hashes MD5 devem ser validados estritamente ao descarregar. Disponível apenas ao descarregar. Opções disponíveis: `NoCheck` `LogOnly` . `FailIfDifferent` `FailIfDifferentOrMissing` . (predefinição) `FailIfDifferent` (predefinição "FailIfDifferent")

**--cadeia de disposição de conteúdo** Definir o cabeçalho de disposição de conteúdo. Devolvido no download.

**--cadeia de codificação de conteúdo** Definir o cabeçalho codificante de conteúdo. Devolvido no download.

**--cadeia de linguagem de conteúdo** Definir o cabeçalho de linguagem de conteúdo. Devolvido no download.

**--cadeia do tipo de conteúdo** Especifica o tipo de conteúdo do ficheiro. Implica nenhum tipo de mime. Devolvido no download.

**--descompressão** Descomprimir automaticamente os ficheiros ao descarregar, se a codificação de conteúdos indicar que estão comprimidos. Os valores de codificação de conteúdo suportado são `gzip` e `deflate` . Extensões de ficheiros `.gz` / `.gzip` de ou não `.zz` são necessárias, mas serão removidas se estiverem presentes.

**--excluir-atributos** cadeia (apenas Windows) Exclui ficheiros cujos atributos correspondem à lista de atributos. Por exemplo: A; S; R

**--excluir-blob-string** Especifica opcionalmente o tipo de bolha `BlockBlob` /  `PageBlob` /  `AppendBlob` () para excluir ao copiar bolhas do recipiente ou da conta. A utilização deste pavilhão não é aplicável para copiar dados do serviço não-Azure para o serviço. Mais de uma bolha deve ser separada por `;` . 

**...-excluir** a cadeia de caminhos Excluir estes caminhos ao copiar. Esta opção não suporta caracteres wildcard (*). Verifica o prefixo relativo do caminho (Por exemplo: `myFolder;myFolder/subDirName/file.pdf` ). Quando utilizados em combinação com a transversal de conta, os caminhos não incluem o nome do recipiente.

**--excluir** a cadeia de padrão Excluir estes ficheiros ao copiar. Esta opção suporta caracteres wildcard (*).

**--follow-symlinks**  Siga links simbólicos ao carregar a partir do sistema de ficheiros local.

**--força-se-ler-apenas** Ao sobrepor um ficheiro existente no Windows ou em Azure Files, force o excesso de trabalho mesmo que o ficheiro existente tenha o seu conjunto de atributos apenas de leitura.

**-de-para** cadeia Opcionalmente especifica a combinação de destino de origem. Por exemplo: `LocalBlob` `BlobLocal` . `LocalBlobFS`

**...ajuda**  para a cópia.

**--incluir-incluir-depois** da cadeia Inclua apenas os ficheiros modificados na ou após a data/hora dada. O valor deve estar no formato ISO8601. Se não for especificado o timezone, presume-se que o valor está no timezone local da máquina que executa a AzCopy. por exemplo, `2020-08-19T15:04:00Z` para uma hora UTC, ou `2020-08-19` para a meia-noite (00:00) no fuso horário local. Tal como no AzCopy 10.5, esta bandeira aplica-se apenas a ficheiros, não a pastas, pelo que as propriedades das pastas não serão copiadas quando utilizarem esta bandeira com `--preserve-smb-info` ou `--preserve-smb-permissions` .

 **--incluir antes da** cadeia Inclua apenas os ficheiros modificados antes ou na data/hora dada. O valor deve estar no formato ISO8601. Se não for especificado o timezone, presume-se que o valor está no timezone local da máquina que executa a AzCopy. Por exemplo, `2020-08-19T15:04:00Z` por um tempo UTC, ou `2020-08-19` para a meia-noite (00:00) no fuso horário local. A partir do AzCopy 10.7, esta bandeira aplica-se apenas a ficheiros, não a pastas, pelo que as propriedades das pastas não serão copiadas quando utilizarem esta bandeira com `--preserve-smb-info` ou `--preserve-smb-permissions` .

**--incluir-atributos (apenas** Windows) Inclui ficheiros cujos atributos correspondem à lista de atributos. Por exemplo: A; S; R

**--incluir** a corda do caminho Inclua apenas estes caminhos ao copiar. Esta opção não suporta caracteres wildcard (*). Verifica o prefixo relativo do caminho (Por exemplo: `myFolder;myFolder/subDirName/file.pdf` ).

**-- incluir** a cadeia de padrão Inclua apenas estes ficheiros ao copiar. Esta opção suporta caracteres wildcard (*). Separar ficheiros utilizando um `;` .

**--lista de versões** cadeia Especifica um ficheiro em que cada iD de versão está listado numa linha separada. Certifique-se de que a fonte deve apontar para uma única bolha e todos os IDs de versão especificados no ficheiro que utilizam esta bandeira devem pertencer apenas à bolha de origem. O AzCopy irá descarregar as versões especificadas na pasta de destino fornecida. Para obter mais informações, consulte [Baixar as versões anteriores de uma bolha.](storage-use-azcopy-blobs.md#download-previous-versions-of-a-blob)

**--cadeia de nível de log** Define a verbosidade do registo para o ficheiro de registo, níveis disponíveis: INFO (todos os pedidos/respostas), ADVERTÊNCIA (respostas lentas), ERRO (apenas pedidos falhados) e NENHUM (sem registos de saída). (predefinição). `INFO` 

**--cadeia de metadados** Carregar para Azure Storage com estes pares de valor-chave como metadados.

**--no-guess-mime-type**  Impede que a AzCopy detete o tipo de conteúdo com base na extensão ou conteúdo do ficheiro.

**...-overwrite** string Overwrite os ficheiros e bolhas conflituosas no destino se esta bandeira for definida como verdadeira. (predefinição) `true` Os valores possíveis `true` `false` incluem, `prompt` `ifSourceNewer` e. Para os destinos que suportam pastas, as propriedades de nível de pasta conflituosas serão substituídas esta bandeira `true` ou se for dada uma resposta positiva ao pedido. (padrão "verdadeiro")

**--page-blob-tier** Upload page blob para Azure Storage usando este nível blob. (predefinição). `None` (predefinição "Nenhum")

**--preservar-último-tempo modificado**  Só disponível quando o destino é o sistema de ficheiros.

**--preserve-proprietário**    Só tem um efeito nos downloads, e apenas quando `--preserve-smb-permissions` é usado. Se for verdade (o padrão), o proprietário do ficheiro e o Grupo são preservados em downloads. Se definido como falso, `--preserve-smb-permissions` continuará a preservar ACLs, mas o Proprietário e o Grupo serão baseados no utilizador que executa a AzCopy (padrão verdadeiro)

**--preservar-smb-info**   Falso por defeito. Preserva a informação de propriedade SMB (última hora de escrita, tempo de criação, atributo bits) entre recursos conscientes de SMB (Windows e Azure Files). Apenas os bits de atributo suportados pela Azure Files serão transferidos; qualquer outro será ignorado. Esta bandeira aplica-se tanto a ficheiros como a pastas, a menos que seja especificado um filtro apenas de ficheiro (por exemplo, incluir o padrão). A informação transferida para pastas é a mesma que para ficheiros, com exceção da Última Hora de Escrita que nunca é preservada para pastas.

**---conserva-smb-permissões**   Falso por defeito. Preserva acls SMB entre recursos conscientes (Windows e Azure Files). Para downloads, também necessitará da `--backup` bandeira para restaurar permissões onde o novo Proprietário não será o utilizador que executa o AzCopy. Esta bandeira aplica-se tanto a ficheiros como a pastas, a menos que seja especificado um filtro apenas para ficheiros (por exemplo, `include-pattern` ).

**--put-md5**    Crie um haxixe MD5 de cada ficheiro e guarde o haxixe como propriedade do Conteúdo-MD5 da bolha de destino ou ficheiro. (Por defeito, o haxixe NÃO é criado.) Só disponível no upload.

**--recursivo**    Procure sub-diretivas de forma recorrente ao carregar a partir do sistema de ficheiros local.

**--s2s-detect-source-changed**   Detete se o ficheiro de origem/bolha muda enquanto estiver a ser lido. (Este parâmetro aplica-se apenas às cópias de serviço-a-serviço, uma vez que a verificação correspondente está ativada permanentemente para uploads e downloads.)

**--s-handle-invalid-metadados-metadados** especifica como as chaves de metadados inválidas são manuseadas. Opções disponíveis: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (predefinição). `ExcludeIfInvalid` (predefinição "Excluir NãoInvalide")

**--s2s-preserve-access-tier**   Preservar o nível de acesso durante o serviço à cópia de serviço. Consulte o [armazenamento do Azure Blob: níveis de acesso quentes, frescos e de arquivo](../blobs/storage-blob-storage-tiers.md) para garantir que a conta de armazenamento de destino suporta a definição do nível de acesso. Nos casos em que a definição do nível de acesso não seja suportada, utilize s2sPreserveAccessTier=falso para contornar o nível de acesso de cópia. (predefinição). `true`  (padrão "verdadeiro")

**--s2-conserva-propriedades**   Preservar propriedades completas durante o serviço à cópia de serviço. Para a AWS S3 e Azure File não é única fonte de ficheiro, a operação da lista não devolve propriedades completas de objetos e ficheiros. Para preservar propriedades completas, a AzCopy precisa de enviar um pedido adicional por objeto ou ficheiro. (verdade padrão)

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

**---cap-mbps flutuar**   Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.

**...-tipo de saída** formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é `text`. ("texto" predefinido)

**--cadeia de sufixos fidedignos-microsoft-sufixos** Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  A predefinição é `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.

## <a name="see-also"></a>Ver também

- [azcopy](storage-ref-azcopy.md)

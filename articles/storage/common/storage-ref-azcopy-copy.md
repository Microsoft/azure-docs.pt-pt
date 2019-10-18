---
title: cópia de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy Copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e7f08c175972826a8b226d7e80f563ac71ba23db
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514770"
---
# <a name="azcopy-copy"></a>azcopy copy

Copia os dados de origem para um local de destino.

## <a name="synopsis"></a>Resumo

Copia os dados de origem para um local de destino. As instruções com suporte são:

  - < local-> blob do Azure (autenticação SAS ou OAuth)
  - < locais-> arquivos do Azure (autenticação SAS de compartilhamento/diretório)
  - < local-> ADLS Gen 2 (autenticação SAS, OAuth ou SharedKey)
  - Blob do Azure (SAS ou público)-> blob do Azure (autenticação SAS ou OAuth)
  - Blob do Azure (SAS ou público)-> arquivos do Azure (SAS)
  - Arquivos do Azure (SAS)-> arquivos do Azure (SAS)
  - Arquivos do Azure (SAS)-> blob do Azure (autenticação SAS ou OAuth)
  - AWS S3 (chave de acesso)-> blob de blocos do Azure (autenticação SAS ou OAuth)

Consulte os exemplos para obter mais informações.

## <a name="advanced"></a>Segurança

O AzCopy detecta automaticamente o tipo de conteúdo dos arquivos ao carregar do disco local, com base na extensão de arquivo ou no conteúdo (se nenhuma extensão for especificada).

A tabela de pesquisa interna é pequena, mas no UNIX, ela é aumentada pelos arquivos MIME. Types do sistema local, se disponíveis em um ou mais desses nomes:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

No Windows, os tipos de MIME são extraídos do registro. Esse recurso pode ser desativado com a ajuda de um sinalizador. Veja a seção sinalizador.

Se você definir uma variável de ambiente usando a linha de comando, essa variável será legível no seu histórico de linha de comando. Considere limpar as variáveis que contêm credenciais do seu histórico de linha de comando. Para impedir que as variáveis apareçam no seu histórico, você pode usar um script para solicitar ao usuário suas credenciais e definir a variável de ambiente.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exemplos

Carregar um único arquivo usando a autenticação OAuth. Se você ainda não fez logon no AzCopy, execute o comando de logon AzCopy antes de executar o comando a seguir.

- azcopy CP "/Path/to/file.txt" "https://[conta]. blob. Core. Windows. net/[contêiner]/[caminho/para/blob]"

O mesmo que acima, mas desta vez também calcula o hash MD5 do conteúdo do arquivo e o salva como a propriedade Content-MD5 do blob:

- azcopy CP "/Path/to/file.txt" "https://[conta]. blob. Core. Windows. net/[contêiner]/[caminho/para/blob]"--Put-MD5

Carregar um único arquivo usando um token SAS:

- azcopy CP "/Path/to/file.txt" "https://[conta]. blob. Core. Windows. net/[contêiner]/[caminho/para/blob]? [SAS] "

Carregar um único arquivo usando um token e um pipe SAS (somente blobs de blocos):
  
- gato "/Path/to/file.txt" | azcopy CP "https://[conta]. blob. Core. Windows. net/[contêiner]/[caminho/para/blob]? [SAS] "

Carregar um diretório inteiro usando um token SAS:
  
- azcopy CP "/Path/to/dir" "https://[conta]. blob. Core. Windows. net/[contêiner]/[caminho/para/diretório]? [SAS] "--recursivo = true

ou

- azcopy CP "/Path/to/dir" "https://[conta]. blob. Core. Windows. net/[contêiner]/[caminho/para/diretório]? [SAS] "--recursivo = true--Put-MD5

Carregue um conjunto de arquivos usando um token SAS e caracteres curinga (*):

- azcopy CP "/Path/*foo/* bar/*. pdf" "https://[conta]. blob. Core. Windows. net/[Container]/[caminho/para/diretório]? [SAS] "

Carregar arquivos e diretórios usando um token SAS e caracteres curinga (*):

- azcopy CP "/Path/*foo/* bar *" "https://[conta]. blob. Core. Windows. net/[contêiner]/[caminho/para/diretório]? [SAS] "--recursivo = true

Baixe um único arquivo usando a autenticação OAuth. Se você ainda não fez logon no AzCopy, execute o comando de logon AzCopy antes de executar o comando a seguir.

- azcopy CP "https://[conta]. blob. Core. Windows. net/[contêiner]/[caminho/para/blob]" "/Path/to/file.txt"

Baixar um único arquivo usando um token SAS:

- azcopy CP "https://[conta]. blob. Core. Windows. net/[contêiner]/[caminho/para/blob]? [SAS] ""/Path/to/file.txt "

Baixar um único arquivo usando um token SAS e, em seguida, canalizando a saída para um arquivo (somente blobs de blocos):
  
- azcopy CP "https://[conta]. blob. Core. Windows. net/[contêiner]/[caminho/para/blob]? [SAS] ">"/Path/to/file.txt "

Baixe um diretório inteiro usando um token SAS:
  
- azcopy CP "https://[conta]. blob. Core. Windows. net/[contêiner]/[caminho/para/diretório]? [SAS] ""/Path/to/dir "--recursivo = true

Uma observação sobre como usar um caractere curinga (*) em URLs:

Há apenas duas maneiras suportadas de usar um caractere curinga em uma URL. 

- Você pode usar um logo após a barra final (/) de uma URL. Isso copia todos os arquivos em um diretório diretamente para o destino, sem colocá-los em um subdiretório.

- Você também pode usar um no nome de um contêiner, desde que a URL se refira apenas a um contêiner e não a um blob. Você pode usar essa abordagem para obter arquivos de um subconjunto de contêineres.

Baixe o conteúdo de um diretório sem copiar o próprio diretório recipiente.

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[caminho/para/pasta]/*? [SAS] ""/Path/to/dir "

Baixe uma conta de armazenamento inteira.

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/" "/Path/to/dir"--recursivo

Baixe um subconjunto de contêineres em uma conta de armazenamento usando um símbolo curinga (*) no nome do contêiner.

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/[contêiner * Name]" "/Path/to/dir"--recursivo

Copiar um único blob para outro blob usando um token SAS.

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[caminho/para/blob]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[Container]/[caminho/para/blob]? [SAS] "

Copie um único blob para outro blob usando um token SAS e um token OAuth. Você precisa usar um token SAS no final da URL da conta de origem, mas a conta de destino não precisará de uma se você fizer logon no AzCopy usando o comando de logon AzCopy. 

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[caminho/para/blob]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[Container]/[caminho/para/blob] "

Copie um diretório virtual de BLOB para outro usando um token SAS:

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net/[Container]/[caminho/para/diretório]? [SAS] "" https://[destaccount]. blob. Core. Windows. net/[Container]/[caminho/para/diretório]? [SAS] "--recursivo = true

Copie todos os contêineres de BLOB, diretórios e blobs da conta de armazenamento para outra usando um token SAS:

- azcopy CP "https://[srcaccount]. blob. Core. Windows. net? [SAS] "" https://[destaccount]. blob. Core. Windows. net? [SAS] "--recursivo = true

Copie um único objeto para o armazenamento de blobs de Amazon Web Services (AWS) S3 usando uma chave de acesso e um token SAS. Primeiro, defina a variável de ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a origem S3 AWS.
  
- azcopy CP "https://s3.amazonaws.com/ [Bucket]/[Object]" "https://[destaccount]. blob. Core. Windows. net/[Container]/[caminho/para/blob]? [SAS] "

Copie um diretório inteiro para o armazenamento de BLOBs do AWS S3 usando uma chave de acesso e um token SAS. Primeiro, defina a variável de ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a origem S3 AWS.

- azcopy CP "https://s3.amazonaws.com/ [Bucket]/[pasta]" "https://[destaccount]. blob. Core. Windows. net/[Container]/[caminho/para/diretório]? [SAS] "--recursivo = true

Consulte https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html para entender melhor o espaço reservado [pasta].

Copie todos os buckets para o armazenamento de blobs de Amazon Web Services (AWS) usando uma chave de acesso e um token SAS. Primeiro, defina a variável de ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a origem S3 AWS.

- azcopy CP "https://s3.amazonaws.com/" "https://[destaccount]. blob. Core. Windows. net? [SAS] "--recursivo = true

Copie todos os buckets para o armazenamento de blobs de uma região Amazon Web Services (AWS) usando uma chave de acesso e um token SAS. Primeiro, defina a variável de ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a origem S3 AWS.

- azcopy CP "https://s3- [Region]. amazonaws. com/" "https://[destaccount]. blob. Core. Windows. net? [SAS] "--recursivo = true

Copie um subconjunto de buckets usando um símbolo curinga (*) no nome do Bucket. Como nos exemplos anteriores, você precisará de uma chave de acesso e um token SAS. Certifique-se de definir a variável de ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a origem S3 AWS.

- azcopy CP "https://s3.amazonaws.com/ [Bucket * Name]/" "https://[destaccount]. blob. Core. Windows. net? [SAS] "--recursivo = true

## <a name="options"></a>Opções

**--blob-Type** cadeia de caracteres define o tipo de blob no destino. Isso é usado para carregar BLOBs e ao copiar entre contas (padrão ' detect '). Os valores válidos incluem ' detect ', ' BlockBlob ', ' PageBlob ' e ' AppendBlob '. Ao copiar entre contas, um valor de "detectar" faz com que o AzCopy use o tipo de blob de origem para determinar o tipo do blob de destino. Ao carregar um arquivo, ' detect ' determina se o arquivo é um VHD ou um arquivo VHDX com base na extensão de arquivo. Se o arquivo for ether a um VHD ou arquivo VHDX, AzCopy tratará o arquivo como um blob de páginas. (padrão "detectar")

**--Block-blob-camada** carregar blob de blocos para o armazenamento do Azure usando esta camada de BLOB. (padrão "None")

**--Block-size-MB** float Use esse tamanho de bloco (especificado na MIB) ao carregar no armazenamento do Azure e baixar do armazenamento do Azure. O valor padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas (por exemplo: 0,25).

**--Cache-Control** String defina o cabeçalho Cache-Control. Retornado no download.

**--comprimento da verificação**                         Verifique o comprimento de um arquivo no destino após a transferência. Se houver uma incompatibilidade entre a origem e o destino, a transferência será marcada como com falha. (padrão true)

--a cadeia de caracteres **de verificação MD5** especifica como os hashes MD5 estritamente devem ser validados durante o download. Disponível somente ao baixar. Opções disponíveis: NOCHECK, LogOn, FailIfDifferent, FailIfDifferentOrMissing. (padrão ' FailIfDifferent ') (padrão "FailIfDifferent")

**--** cadeia de caracteres de disposição de conteúdo defina o cabeçalho de disposição de conteúdo. Retornado no download.

**--** cadeia de codificação de conteúdo defina o cabeçalho Content-Encoding. Retornado no download.

**--** cadeia de caracteres de linguagem de conteúdo defina o cabeçalho de linguagem de conteúdo. Retornado no download.

**--Content-Type** String especifica o tipo de conteúdo do arquivo. Implica no tipo não-palpite-MIME. Retornado no download.

**--descompactar**                           Descompacte automaticamente os arquivos durante o download, se sua codificação de conteúdo indicar que eles estão compactados. Os valores de codificação de conteúdo com suporte são ' gzip ' e ' deflate '. As extensões de arquivo '. gz '/'. gzip ' ou '. ZZ ' não são necessárias, mas serão removidas se estiverem presentes.

**--Exclude-atributos** String (somente Windows) exclua os arquivos cujos atributos correspondem à lista de atributos. Por exemplo: A; & D

**--Exclude-blob-tipo** de cadeia de caracteres especifica opcionalmente o tipo de BLOB (BlockBlob/PageBlob/AppendBlob) a ser excluído ao copiar BLOBs do contêiner ou da conta. O uso desse sinalizador não é aplicável para copiar dados de não Azure-Service para o serviço. Mais de um blob deve ser separado por '; '.

**--Exclude-cadeia de** caracteres de caminho exclua esses caminhos ao copiar. Essa opção não dá suporte a caracteres curinga (*). Verifica o prefixo de caminho relativo (por exemplo: MyFolder; MyFolder/subDirName/File. pdf). Quando usado em combinação com passagem de conta, os caminhos não incluem o nome do contêiner.

**--a cadeia de caracteres de exclusão-padrão** exclui esses arquivos ao copiar. Esta opção dá suporte a caracteres curinga (*)

**--follow-symlinks**                      Siga links simbólicos ao carregar do sistema de arquivos local.

**--a cadeia de caracteres de-para** especifica opcionalmente a combinação de destino de origem. Por exemplo: LocalBlob, BlobLocal, LocalBlobFS.

**-h,-** ajuda ajuda para cópia

**--include-Attributes** (somente Windows) inclua arquivos cujos atributos correspondam à lista de atributos. Por exemplo: A; & D

--a cadeia de caracteres **include-path** inclui apenas esses caminhos ao copiar. Essa opção não dá suporte a caracteres curinga (*). Verifica o prefixo de caminho relativo (por exemplo: MyFolder; MyFolder/subDirName/File. pdf).

**--include-a cadeia de caracteres de padrão** inclui apenas esses arquivos ao copiar. Essa opção dá suporte a caracteres curinga (*). Separar arquivos usando um '; '.

**--** cadeia de caracteres de nível de log defina o detalhamento de log para o arquivo de log, níveis disponíveis: informações (todas as solicitações/respostas), aviso (respostas lentas), erro (somente solicitações com falha) e nenhum (nenhum log de saída). (padrão ' INFO '). (padrão "INFO")

**--** carregamento da cadeia de caracteres de metadados para o armazenamento do Azure com esses pares de chave-valor como metadados.

**--no-palpite-tipo MIME**                   Impede que o AzCopy detecte o tipo de conteúdo com base na extensão ou no conteúdo do arquivo.

**--substituir** cadeia de caracteres substituirá os arquivos conflitantes e os BLOBs no destino se esse sinalizador for definido como true. (padrão ' true ') Os valores possíveis incluem ' true ', ' false ' e ' prompt '. (padrão "true")

**--Page-blob-camada – blob de** página de carregamento de cadeia de caracteres para o armazenamento do Azure usando essa camada de BLOB. (padrão ' none '). (padrão "None")

**--preservar-último-modificado-hora**          Disponível somente quando o destino é sistema de arquivos.

**--Put-MD5**                             Crie um hash MD5 de cada arquivo e salve o hash como a propriedade Content-MD5 do BLOB ou arquivo de destino. (Por padrão, o hash não é criado.) Disponível somente ao carregar.

**--recursivo**                            Examine os subdiretórios recursivamente ao carregar do sistema de arquivos local.

**--S2S-Detect-origem-alterado**           Verifique se a origem foi alterada após a enumeração.

**--S2S-Handle-inválida-a cadeia de caracteres de metadados** especifica como as chaves de metadados inválidas são tratadas. Opções disponíveis: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (padrão ' ExcludeIfInvalid '). (padrão "ExcludeIfInvalid")

**--S2S-preserve-acesso-camada**             Preserve a camada de acesso durante a cópia de serviço para serviço. Consulte armazenamento de [BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) para garantir que a conta de armazenamento de destino dê suporte à configuração da camada de acesso. Nos casos em que não há suporte para a configuração da camada de acesso, use s2sPreserveAccessTier = false para ignorar a cópia da camada de acesso. (padrão true).  (padrão true)

**--S2S-preserve-Propriedades**              Preserve as propriedades completas durante a cópia de serviço para serviço. Para AWS S3 e fonte de arquivo não único de arquivo do Azure, a operação de lista não retorna propriedades completas de objetos e arquivos. Para preservar as propriedades completas, o AzCopy precisa enviar uma solicitação adicional por objeto ou arquivo. (padrão true)

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

**--Cap-Mbps UInt32**      Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.

**--** formato da cadeia de caracteres do tipo de saída da saída do comando. As opções incluem: Text, JSON. O valor padrão é ' Text '. (padrão "texto")

## <a name="see-also"></a>Ver também

- [azcopy](storage-ref-azcopy.md)

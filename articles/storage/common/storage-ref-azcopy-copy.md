---
title: cópia azcopy[ Microsoft Docs
description: Este artigo fornece informações de referência para o comando de cópia da azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 431372b930269c3dfa6bdc6e8b2fe4d291a8162e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933791"
---
# <a name="azcopy-copy"></a>azcopy copy

Cópias dados de origem para um local de destino.

## <a name="synopsis"></a>Sinopse

Cópias dados de origem para um local de destino. As instruções suportadas são:

  - local <-> Azure Blob (SAS ou Autenticação OAuth)
  - arquivos azure <-> locais (autenticação Share/diretório SAS)
  - < aadLS gen 2 > local (SAS, OAuth ou SharedKey)
  - Azure Blob (SAS ou público) -> Azure Blob (SAS ou Autenticação OAuth)
  - Azure Blob (SAS ou público) -> Ficheiros Azure (SAS)
  - Ficheiros Azure (SAS) -> Ficheiros Azure (SAS)
  - Ficheiros Azure (SAS) -> Azure Blob (SAS ou autenticação OAuth)
  - AWS S3 (Chave de Acesso) -> Bloco Azure Blob (SAS ou autenticação OAuth)

Consulte os exemplos para mais informações.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Avançado

A AzCopy deteta automaticamente o tipo de conteúdo dos ficheiros ao ser carregado a partir do disco local, com base na extensão ou conteúdo do ficheiro (se não for especificada nenhuma extensão).

A mesa de lookup incorporada é pequena, mas no Unix, é aumentada pelos ficheiros mímicas do sistema local, se disponível sob um ou mais destes nomes:

- /etc/mímica.tipos
- /etc/apache2/mime.types
- /etc/apache/mime.type

No Windows, os tipos MIME são extraídos do registo. Esta característica pode ser desligada com a ajuda de uma bandeira. Por favor, consulte a secção da bandeira.

Se definir uma variável ambiental utilizando a linha de comando, essa variável será legível no histórico da sua linha de comando. Considere limpar variáveis que contenham credenciais do histórico da sua linha de comando. Para evitar que as variáveis apareçam na sua história, pode utilizar um script para solicitar ao utilizador as suas credenciais e definir a variável ambiental.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exemplos

Faça upload de um único ficheiro utilizando a autenticação OAuth. Se ainda não tiver iniciado sessão no AzCopy, por favor, faça o comando de login de azcopia antes de executar o seguinte comando.

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

O mesmo que acima, mas desta vez também calcule o haxixe MD5 do conteúdo do ficheiro e guarde-o como propriedade Content-MD5 da bolha:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[contentor]/[path/to/blob]" --put-md5

Faça upload de um único ficheiro utilizando um token SAS:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Faça upload de um único ficheiro utilizando uma ficha SAS e tubagens (apenas bolhas de bloco):
  
- gato "/path/to/file.txt" [ azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Faça upload de um diretório inteiro usando um token SAS:
  
- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/diretório]? [SAS]" --recursivo=verdadeiro

ou

- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/diretório]? [SAS]" --recursivo=verdadeiro --put-md5

Faça upload de um conjunto de ficheiros utilizando um token SAS e caracteres wildcard (*):

- azcopy cp "/path/*foo/* bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/diretório]? [SAS]"

Faça upload de ficheiros e diretórios utilizando um token SAS e caracteres wildcard (*):

- azcopy cp "/path/*foo/* bar*" "https://[account].blob.core.windows.net/[container]/[path/to/diretório]? [SAS]" --recursivo=verdadeiro

Faça o download de um único ficheiro utilizando a autenticação OAuth. Se ainda não tiver iniciado sessão no AzCopy, por favor, faça o comando de login de azcopia antes de executar o seguinte comando.

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"

Faça o download de um único ficheiro utilizando uma ficha SAS:

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "/path/to/file.txt"

Faça o download de um único ficheiro utilizando uma ficha SAS e, em seguida, canalizando a saída para um ficheiro (apenas blocos de bolhas):
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" > "/path/to/file.txt"

Faça o download de um diretório inteiro utilizando um token SAS:
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/diretório]? [SAS]" "/path/to/dir" --recursive=true

Uma nota sobre a utilização de um personagem wildcard (*) em URLs:

Só há duas formas suportadas de usar um personagem wildcard numa URL. 

- Pode utilizar um logo após a barra final para a frente (/) de um URL. Isto copia todos os ficheiros num diretório diretamente para o destino sem os colocar num subdiretório.

- Também pode utilizar um em nome de um recipiente, desde que o URL se refira apenas a um recipiente e não a uma bolha. Pode utilizar esta abordagem para obter ficheiros a partir de um subconjunto de contentores.

Descarregue o conteúdo de um diretório sem copiar o próprio diretório.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*? [SAS]" "/path/to/dir"

Faça o download de uma conta de armazenamento inteira.

- azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive

Descarregue um subconjunto de contentores dentro de uma conta de armazenamento utilizando um símbolo wildcard (*) no nome do recipiente.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursivo

Copie uma única bolha para outra bolha utilizando um token SAS.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[contentor]/[path/to/blob]? [SAS]"

Copie uma única bolha para outra bolha usando um token SAS e um token OAuth. Tem de utilizar um token SAS no final do URL da conta fonte, mas a conta de destino não precisa de uma se iniciar sessão no AzCopy utilizando o comando de login azcopy. 

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[contentor]/[path/to/blob]"

Copie um diretório virtual blob para outro usando um símbolo SAS:

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/diretório]? [SAS]" "https://[destaccount].blob.core.windows.net/[contentor]/[path/to/diretório]? [SAS]" --recursivo=verdadeiro

Copie todos os recipientes, diretórios e blobs blobs da conta de armazenamento para outra utilizando um símbolo SAS:

- azcopy cp "https://[srcaccount].blob.core.windows.net? [SAS]" "https://[destaccount].blob.core.windows.net? [SAS]" --recursivo=verdadeiro

Copie um único objeto para blob Storage da Amazon Web Services (AWS) S3 usando uma chave de acesso e um token SAS. Em primeiro lugar, definir a variável ambiental AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.
  
- azcopy cphttps://s3.amazonaws.com/" [balde]/[objeto]" "https://[destaccount].blob.core.windows.net/[contentor]/[path/to/blob]? [SAS]"

Copie um diretório inteiro para blob Storage da AWS S3 utilizando uma chave de acesso e um token SAS. Em primeiro lugar, definir a variável ambiental AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cphttps://s3.amazonaws.com/" [balde]/[pasta]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/diretório]? [SAS]" --recursivo=verdadeiro

Por favor, consulte https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html para entender melhor o espaço reservado [pasta].

Copie todos os baldes para Blob Storage da Amazon Web Services (AWS) utilizando uma chave de acesso e um token SAS. Em primeiro lugar, definir a variável ambiental AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cphttps://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net? [SAS]" --recursivo=verdadeiro

Copie todos os baldes para Blob Storage de uma região da Amazon Web Services (AWS) utilizando uma chave de acesso e um token SAS. Em primeiro lugar, definir a variável ambiental AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cphttps://s3-" [região].amazonaws.com/" "https://[destaccount].blob.core.windows.net? [SAS]" --recursivo=verdadeiro

Copie um subconjunto de baldes utilizando um símbolo wildcard (*) no nome do balde. Tal como os exemplos anteriores, vai precisar de uma chave de acesso e um símbolo SAS. Certifique-se de que a variável ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cphttps://s3.amazonaws.com/" [bucket*name]/" "https://[destaccount].blob.core.windows.net? [SAS]" --recursivo=verdadeiro

## <a name="options"></a>Opções

**--cadeia do tipo blob** Define o tipo de bolha no destino. Isto é utilizado para o upload de bolhas e para copiar entre contas (predefinido 'Detecte'). Os valores válidos incluem 'Detect', 'BlockBlob', 'PageBlob' e 'AppendBlob'. Ao copiar entre contas, um valor de 'Detect' faz com que a AzCopy utilize o tipo de bolha de origem para determinar o tipo de bolha de destino. Ao carregar um ficheiro, 'Detect' determina se o ficheiro é um VHD ou um ficheiro VHDX baseado na extensão do ficheiro. Se o ficheiro for éter um ficheiro VHD ou VHDX, a AzCopy trata o ficheiro como uma bolha de página. (padrão "Detetar")

**-block-blob-tier** string Upload block blobs diretamente para o [nível de acesso](../blobs/storage-blob-storage-tiers.md) à sua escolha. (padrão 'Nenhum'). Os valores válidos incluem 'Nenhum', 'Hot', 'Cool' e 'Archive'. Se 'Nenhum' ou nenhum nível for passado, a bolha herdará o nível da conta de armazenamento.

**--bloco-tamanho mb** flutuante Utilize este tamanho de bloco (especificado no MiB) ao fazer o upload para o Armazenamento Azure e descarregar a partir do Armazenamento Azure. O valor predefinido é calculado automaticamente com base no tamanho do ficheiro. São permitidas frações decimais (por exemplo: 0,25).

**--cadeia de controlo de cache** Derem o cabeçalho do controlo de cache. Devolvido no download.

**-check-length**                         Verifique o comprimento de um ficheiro no destino após a transferência. Se houver um desfasamento entre fonte e destino, a transferência é marcada como falhada. (por defeito verdadeiro)

**-check-md5** string Especifica como os hashes MD5 devem ser validados estritamente ao descarregar. Só disponível no momento do download. Opções disponíveis: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (padrão "FailIfDifferent")

**--** cadeia de disposição de conteúdo Definir o cabeçalho de disposição do conteúdo. Devolvido no download.

**--** cadeia de codificação de conteúdo Definir o cabeçalho codificador de conteúdo. Devolvido no download.

**--** string de linguagem de conteúdo Definir o cabeçalho em linguagem de conteúdo. Devolvido no download.

**--** a cadeia do tipo conteúdo especifica o tipo de conteúdo do ficheiro. Implica não adivinhar-mímica. Devolvido no download.

**--descomprimir**                           Descomprime automaticamente os ficheiros ao descarregar, se a codificação de conteúdos indicar que estão comprimidos. Os valores suportados de codificação de conteúdos são "gzip" e "esvaziar". Não são necessárias extensões de ficheiros de '.gz'/'.gzip' ou '.zz', mas serão removidas se estiverem presentes.

**--excluir-atribui** a cadeia (apenas ao Windows) Excluir ficheiros cujos atributos correspondem à lista de atributos. Por exemplo: A; S; R

**-excluir-blob-tipo** cadeia Opcionalmente especifica o tipo de blob (BlockBlob/ PageBlob/ AppendBlob) para excluir ao copiar bolhas do recipiente ou da conta. A utilização desta bandeira não é aplicável à cópia de dados do serviço não azure ao serviço. Mais de uma bolha deve ser separada por ";".

**-excluir-caminho** de corda Exclua estes caminhos ao copiar. Esta opção não suporta caracteres wildcard (*). Verifica prefixo relativo do caminho (por exemplo: myFolder;myFolder/subDirName/file.pdf). Quando utilizados em combinação com a transversala de conta, os caminhos não incluem o nome do recipiente.

**-excluir a** cadeia de padrão Exclua estes ficheiros ao copiar. Esta opção suporta caracteres wildcard (*)

**--follow-symlinks**                      Siga as ligações simbólicas ao fazer o upload do sistema de ficheiros local.

**-- de** cadeia Opcionalmente especifica a combinação de destino fonte. Por exemplo: LocalBlob, BlobLocal, LocalBlobFS.

**-h, -ajuda** para copiar

**--incluir alinha-atributos** (apenas windows) Inclua ficheiros cujos atributos correspondem à lista de atributos. Por exemplo: A; S; R

**--incluir a** corda do caminho Inclua apenas estes caminhos ao copiar. Esta opção não suporta caracteres wildcard (*). Verifica prefixo relativo do caminho (por exemplo: myFolder;myFolder/subDirName/file.pdf).

**-incluir** a cadeia de padrões Inclua apenas estes ficheiros ao copiar. Esta opção suporta caracteres wildcard (*). Separe os ficheiros utilizando um ';'.

**--cadeia de log-level** Defina a verbosidade do registo, níveis disponíveis: INFO (todos os pedidos/respostas), AVISO (respostas lentas), ERROR (apenas pedidos falhados) e NENHUM (sem registos de saída). ("INFO" padrão)

**--linha de metadados** Upload para Armazenamento Azure com estes pares de valor-chave como metadados.

**-- sem palpite-mímica-tipo**                   Impede que a AzCopy detete o tipo de conteúdo com base na extensão ou conteúdo do ficheiro.

**-sobrepor** a corda Sobrepreite os ficheiros e bolhas conflituosos no destino se esta bandeira for verdadeira. Os valores possíveis incluem "verdadeiro", "falso", "ifSourceNewer" e "prompt". (padrão "verdadeiro")

**-página-blob-tier** Enviar página blob para Armazenamento Azure usando este nível de blob. (padrão "Nenhum")

**--preservar-último tempo modificado**          Só disponível quando o destino é o sistema de ficheiros.

**-put-md5**                             Crie um haxixe MD5 de cada ficheiro e guarde o haxixe como propriedade Content-MD5 da bolha de destino ou ficheiro. (Por padrão, o hash NÃO é criado.) Só disponível no momento do upload.

**-recursivo**                            Procure sub-directórios de forma recorrente ao carregar do sistema de ficheiros local.

**-s2s-detect-source-change**           Verifique se a fonte mudou após a enumeração.

**-s2s-handle-invalid-metadata** string Especifica como as chaves de metadados inválidas são manuseadas. Opções disponíveis: ExcluiIfInválido, FailIfInvalid, RenameIfInvalid. (predefinido "ExcluirIfInválido")

**-s2s-preserve-access-tier**             Preservar o nível de acesso durante o serviço à cópia de serviço. Consulte o [armazenamento Azure Blob: níveis de acesso quentes, frescos e de arquivo](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) para garantir que a conta de armazenamento de destino suporta a definição do nível de acesso. Nos casos em que o nível de acesso de definição não é suportado, utilize o s2sPreserveAccessTier=falso para contornar o nível de acesso de cópia. (por defeito verdadeiro)

**-s2s-preserve-properties**              Preservar propriedades completas durante o serviço à cópia de serviço. Para a fonte de ficheiros não única da AWS S3 e Azure File, a operação da lista não devolve propriedades completas de objetos e ficheiros. Para preservar propriedades completas, a AzCopy precisa enviar um pedido adicional por objeto ou ficheiro. (por defeito verdadeiro)

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

**--cap-mbps uint32**      Cobre a taxa de transferência, em megabits por segundo. A entrada momentânea pode variar ligeiramente a partir da tampa. Se esta opção estiver definida para zero, ou for omitida, a entrada não está limitada.

**-formato** de cadeia do tipo de saída da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto". ("texto por defeito")

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)

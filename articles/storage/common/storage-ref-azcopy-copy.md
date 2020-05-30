---
title: cópia azcopia . Microsoft Docs
description: Este artigo fornece informações de referência para o comando de cópia de azcopia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7f55b22938bd6f18bae1576a0c64e673996d38bf
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220123"
---
# <a name="azcopy-copy"></a>azcopy copy

Copia dados de origem para um local de destino.

## <a name="synopsis"></a>Sinopse

Copia dados de origem para um local de destino. As direções apoiadas são:

  - <-> local Azure Blob (autenticação SAS ou OAuth)
  - arquivos Azure <-> locais (autenticação SAS de partilha/diretório)
  - <-> ADLS Gen 2 (autenticação SAS, OAuth ou SharedKey)
  - Azure Blob (SAS ou público) -> Azure Blob (autenticação SAS ou OAuth)
  - Azure Blob (SAS ou público) -> Ficheiros Azure (SAS)
  - Ficheiros Azure (SAS) -> Ficheiros Azure (SAS)
  - Azure Files (SAS) -> Azure Blob (autenticação SAS ou OAuth)
  - AWS S3 (Chave de Acesso) -> Blob do Bloco Azure (autenticação SAS ou OAuth)

Consulte os exemplos para mais informações.

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Dados de transferência com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Avançado

O AzCopy deteta automaticamente o tipo de conteúdo dos ficheiros ao carregar a partir do disco local, com base na extensão ou conteúdo do ficheiro (se não for especificada nenhuma extensão).

A tabela de procuração incorporada é pequena, mas no Unix, é aumentada pelos ficheiros mime.types do sistema local, se disponível sob um ou mais destes nomes:

- /etc/mime.tipos
- /etc/apache2/mime.tipos
- /etc/apache/mime.tipos

No Windows, os tipos de MIME são extraídos do registo. Esta função pode ser desligada com a ajuda de uma bandeira. Por favor, consulte a secção da bandeira.

Se definir uma variável ambiental utilizando a linha de comando, essa variável será legível no histórico da sua linha de comando. Considere limpar variáveis que contenham credenciais do histórico da sua linha de comando. Para evitar que as variáveis apareçam na sua história, pode utilizar um script para solicitar ao utilizador as suas credenciais e definir a variável ambiental.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exemplos

Faça o upload de um único ficheiro utilizando a autenticação OAuth. Se ainda não tiver iniciado sessão no AzCopy, por favor, execute o comando de login da azcopia antes de executar o seguinte comando.

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[caminho/para/blob]"

O mesmo que acima, mas desta vez também calcular o haxixe MD5 do conteúdo do ficheiro e guardá-lo como propriedade content-MD5 da bolha:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[caminho/para/blob]" -put-md5

Faça o upload de um único ficheiro utilizando um token SAS:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[caminho/para/blob]? [SAS]"

Faça o upload de um único ficheiro utilizando um token SAS e tubagens (apenas bolhas de bloco):
  
- gato "/path/to/file.txt" azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Faça o upload de um diretório inteiro utilizando um token SAS:
  
- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[caminho/para/diretório]? [SAS]" --recursivo=verdadeiro

ou

- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[caminho/para/diretório]? [SAS]" --recursivo=verdadeiro --put-md5

Faça o upload de um conjunto de ficheiros utilizando um token SAS e caracteres wildcard (*):

- azcopy cp "/path/*foo/* bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[caminho/para/diretório]? [SAS]"

Faça upload de ficheiros e diretórios utilizando um token SAS e caracteres wildcard (*):

- azcopy cp "/path/*foo/* bar*" "https://[account].blob.core.windows.net/[container]/[caminho/para/diretório]? [SAS]" --recursivo=verdadeiro

Faça o download de um único ficheiro utilizando a autenticação OAuth. Se ainda não tiver iniciado sessão no AzCopy, por favor, execute o comando de login da azcopia antes de executar o seguinte comando.

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"

Descarregue um único ficheiro utilizando um token SAS:

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "/path/to/file.txt"

Faça o download de um único ficheiro utilizando um token SAS e, em seguida, canalizando a saída para um ficheiro (apenas bolhas de bloco):
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" > "/path/to/file.txt"

Faça o download de um diretório inteiro utilizando um token SAS:
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directy]? [SAS]" "/path/to/dir" --recursive=true

Uma nota sobre a utilização de um personagem wildcard (*) em URLs:

Só há duas formas suportadas de usar um personagem wildcard numa URL. 

- Pode utilizar um logo após o corte dianteiro final (/) de um URL. Isto copia todos os ficheiros de um diretório diretamente para o destino sem colocá-los numa subdiretório.

- Também pode utilizar um em nome de um recipiente, desde que o URL se refira apenas a um recipiente e não a uma bolha. Pode utilizar esta abordagem para obter ficheiros a partir de um subconjunto de contentores.

Descarregue o conteúdo de um diretório sem copiar o próprio diretório.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[caminho/para/pasta]/*? [SAS]" "/caminho/para/dir"

Faça o download de uma conta de armazenamento inteira.

- azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive

Faça o download de um subconjunto de contentores dentro de uma conta de armazenamento utilizando um símbolo wildcard (*) no nome do recipiente.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive

Copie uma única bolha para outra bolha utilizando um token SAS.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[caminho/para/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[caminho/para/blob]? [SAS]"

Copie uma única bolha para outra bolha utilizando um token SAS e um token OAuth. Você tem que usar um token SAS no final do URL da conta de origem, mas a conta de destino não precisa de uma se você iniciar sessão no AzCopy usando o comando de login de azcopy. 

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[caminho/para/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[caminho/para/blob]"

Copie um diretório virtual blob para outro utilizando um token SAS:

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[caminho/para/diretório]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[caminho/para/diretório]? [SAS]" --recursivo=verdadeiro

Copie todos os recipientes blob, diretórios e bolhas da conta de armazenamento para outra, utilizando um token SAS:

- azcopy cp "https://[srcaccount].blob.core.windows.net? [SAS]" "https://[destaccount].blob.core.windows.net? [SAS]" --recursivo=verdadeiro

Copie um único objeto para Blob Storage da Amazon Web Services (AWS) S3 usando uma chave de acesso e um token SAS. Em primeiro lugar, dedibre a variável ambiental AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.
  
- azcopy cp " https://s3.amazonaws.com/ [balde]/[objeto]" "https://[destaccount].blob.core.windows.net/[container]/[caminho/para/blob]?[ SAS]"

Copie um diretório inteiro para Blob Storage a partir de AWS S3 utilizando uma chave de acesso e um token SAS. Em primeiro lugar, dedibre a variável ambiental AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cp " https://s3.amazonaws.com/ [balde]/[pasta]" "https://[destaccount].blob.core.windows.net/[container]/[caminho/para/diretório]?[ SAS]" --recursivo=verdadeiro

Por favor, consulte https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html para entender melhor o espaço reservado [pasta].

Copie todos os baldes para o Blob Storage da Amazon Web Services (AWS) utilizando uma chave de acesso e um token SAS. Em primeiro lugar, dedibre a variável ambiental AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cp " https://s3.amazonaws.com/ " https://[destaccount].blob.core.windows.net?[ SAS]" --recursivo=verdadeiro

Copie todos os baldes para o Blob Storage a partir de uma região da Amazon Web Services (AWS) usando uma chave de acesso e um token SAS. Em primeiro lugar, dedibre a variável ambiental AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cp " https://s3- [região].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[ SAS]" --recursivo=verdadeiro

Copie um subconjunto de baldes utilizando um símbolo wildcard (*) no nome do balde. Tal como os exemplos anteriores, vai precisar de uma chave de acesso e de um token SAS. Certifique-se de que define a variável ambiental AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cp " https://s3.amazonaws.com/ [bucket*name]/" "https://[destaccount].blob.core.windows.net?[ SAS]" --recursivo=verdadeiro

## <a name="options"></a>Opções

**--backup**                               Ativa o SeBackupPrivilege do Windows para uploads, ou SeRestorePrivilege para downloads, para permitir que o AzCopy veja ler todos os ficheiros, independentemente das permissões do sistema de ficheiros, e para restaurar todas as permissões. Requer que a conta em execução AzCopy já tenha estas permissões (por exemplo, tem direitos de administrador ou é membro do grupo 'Operadores de Reserva'). Tudo o que esta bandeira faz é ativar privilégios que a conta já tem.

**---tip** string tipo blob Define o tipo de bolha no destino. Isto é utilizado para o upload de bolhas e ao copiar entre contas (predefinido 'Detectar'). Os valores válidos incluem 'Detectar', 'BlockBlob', 'PageBlob' e 'AppendBlob'. Ao copiar entre contas, um valor de 'Detect' faz com que a AzCopy utilize o tipo de bolha de origem para determinar o tipo de bolha de destino. Ao carregar um ficheiro, 'Detect' determina se o ficheiro é um VHD ou um ficheiro VHDX baseado na extensão do ficheiro. Se o ficheiro for um ficheiro VHD ou VHDX, a AzCopy trata o ficheiro como uma bolha de página. (predefinição "Detetar")

**--bloco-blob-tier** bloco As bolhas de blocos de carregamento diretamente para o [nível](../blobs/storage-blob-storage-tiers.md) de acesso à sua escolha. (predefinição 'Nenhum'). Valores válidos incluem 'Nenhum', 'Quente', 'Cool' e 'Archive'. Se 'Nenhum' ou nenhum nível for passado, a bolha herdará o nível da conta de armazenamento.

**--block-size-mb** float Use this block size (especificado em MiB) ao carregar para Azure Storage, e descarregar a partir do Azure Storage. O valor predefinido é calculado automaticamente com base no tamanho do ficheiro. São permitidas frações decimais (Por exemplo: 0,25).

**--cadeia de controlo de cache** Coloque o cabeçalho de controlo de cache. Devolvido no download.

**--check-length**                         Verifique a duração de um ficheiro no destino após a transferência. Se houver um desfasamento entre a fonte e o destino, a transferência é marcada como falhada. (verdade padrão)

**---cadeia de verificação-md5** Especifica como os hashes MD5 devem ser validados estritamente ao descarregar. Disponível apenas ao descarregar. Opções disponíveis: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (predefinição "FailIfDifferent")

**--cadeia de disposição de conteúdo** Definir o cabeçalho de disposição de conteúdo. Devolvido no download.

**--cadeia de codificação de conteúdo** Definir o cabeçalho codificante de conteúdo. Devolvido no download.

**--cadeia de linguagem de conteúdo** Definir o cabeçalho de linguagem de conteúdo. Devolvido no download.

**--cadeia do tipo de conteúdo** Especifica o tipo de conteúdo do ficheiro. Implica nenhum tipo de mime. Devolvido no download.

**--descompressão**                           Descomprimir automaticamente os ficheiros ao descarregar, se a codificação de conteúdos indicar que estão comprimidos. Os valores de codificação de conteúdo suportados são 'gzip' e 'deflate'. Não são necessárias extensões de ficheiros de '.gz'/'.gzip' ou '.zz', mas serão removidas se estiverem presentes.

**--excluir-atributos** cadeia (apenas Windows) Excluir ficheiros cujos atributos correspondam à lista de atributos. Por exemplo: A; S; R

**--excluir-blob-string** Opcionalmente especifica o tipo de bolha (BlockBlob/ PageBlob/ AppendBlob) para excluir ao copiar bolhas do recipiente ou da conta. A utilização deste pavilhão não é aplicável para copiar dados de non azure-service para o serviço. Mais de uma bolha deve ser separada por ';'.

**...-excluir** a cadeia de caminhos Excluir estes caminhos ao copiar. Esta opção não suporta caracteres wildcard (*). Verifica o prefixo relativo do caminho (Por exemplo: myFolder;myFolder/subDirName/file.pdf). Quando utilizados em combinação com a transversal de conta, os caminhos não incluem o nome do recipiente.

**--excluir** a cadeia de padrão Excluir estes ficheiros ao copiar. Esta opção suporta caracteres wildcard (*)

**--follow-symlinks**                      Siga links simbólicos ao carregar a partir do sistema de ficheiros local.

**-de-para** cadeia Opcionalmente especifica a combinação de destino de origem. Por exemplo: LocalBlob, BlobLocal, LocalBlobfs.

**-h, ajuda** para a cópia

**--incluir** a cadeia de atributos (apenas windows) Incluir ficheiros cujos atributos correspondem à lista de atributos. Por exemplo: A; S; R

**--incluir** a corda do caminho Inclua apenas estes caminhos ao copiar. Esta opção não suporta caracteres wildcard (*). Verifica o prefixo relativo do caminho (Por exemplo: myFolder;myFolder/subDirName/file.pdf).

**-- incluir** a cadeia de padrão Inclua apenas estes ficheiros ao copiar. Esta opção suporta caracteres wildcard (*). Separe os ficheiros utilizando um ';'.

**--cadeia de nível de log** Define a verbosidade do registo para o ficheiro de registo, níveis disponíveis: INFO (todos os pedidos/respostas), ADVERTÊNCIA (respostas lentas), ERRO (apenas pedidos falhados) e NENHUM (sem registos de saída). (predefinição "INFO")

**--cadeia de metadados** Carregar para Azure Storage com estes pares de valor-chave como metadados.

**--no-guess-mime-type**                   Impede que a AzCopy detete o tipo de conteúdo com base na extensão ou conteúdo do ficheiro.

**...-overwrite** string Overwrite os ficheiros e bolhas conflituosas no destino se esta bandeira for definida como verdadeira. Os valores possíveis incluem "verdadeiro", "falso", "ifSourceNewer" e "prompt". (padrão "verdadeiro")

**--page-blob-tier** Upload page blob para Azure Storage usando este nível blob. (predefinição "Nenhum")

**--preservar-último-tempo modificado**          Só disponível quando o destino é o sistema de ficheiros.

**--preservar-smb-permissões** cadeia Falso por padrão. Preserva acls SMB entre recursos conscientes (Windows e Azure Files). Para downloads, também terá de usar a `--backup` bandeira para restaurar permissões onde o novo Proprietário não será o utilizador que está a executar o AzCopy. Esta bandeira aplica-se tanto aos ficheiros como às pastas, a menos que seja especificado um filtro apenas para ficheiros (por `include-pattern` exemplo).

**--preservar-smb-info** cadeia falso por padrão. Preserva a informação de propriedade SMB (última hora de escrita, tempo de criação, atributo bits) entre recursos conscientes de SMB (Windows e Azure Files). Apenas os bits de atributo suportados pela Azure Files serão transferidos; qualquer outro será ignorado. Esta bandeira aplica-se tanto aos ficheiros como às pastas, a menos que seja especificado um filtro apenas de ficheiro (por exemplo, padrão incluído). As informações transferidas para pastas são as mesmas para os ficheiros, exceto para a Última Hora de Escrita, que nunca é preservada para pastas.

**--preserve-proprietário**                       Só tem um efeito no descarregamento de dados, e apenas quando `--preserve-smb-permissions` os utilizados são utilizados. Se for verdade (o padrão), o proprietário do ficheiro e o Grupo são preservados em downloads. Se esta bandeira for definida como falsa, continuará a `--preserve-smb-permissions` preservar ACLs, mas o Proprietário e o Grupo serão baseados no utilizador que está a executar a AzCopy.

**--put-md5**                             Crie um haxixe MD5 de cada ficheiro e guarde o haxixe como propriedade do Conteúdo-MD5 da bolha de destino ou ficheiro. (Por defeito, o haxixe NÃO é criado.) Só disponível no upload.

**--recursivo**                            Procure sub-directórios recursivamente ao carregar do sistema de ficheiros local.

**--s2s-detect-source-changed**           Verifique se a fonte mudou após a enumeração.

**--s-handle-invalid-metadados-metadados** especifica como as chaves de metadados inválidas são manuseadas. Opções disponíveis: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (predefinição "Excluir NãoInvalide")

**--s2s-preserve-access-tier**             Preservar o nível de acesso durante o serviço à cópia de serviço. Consulte o [armazenamento do Azure Blob: níveis de acesso quentes, frescos e de arquivo](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) para garantir que a conta de armazenamento de destino suporta a definição do nível de acesso. Nos casos em que a definição do nível de acesso não seja suportada, utilize o s2sPreserveAccessTier=falso para contornar o nível de acesso de cópia. (verdade padrão)

**--s2-conserva-propriedades**              Preservar propriedades completas durante o serviço à cópia de serviço. Para a AWS S3 e Azure File não é única fonte de ficheiro, a operação da lista não devolve propriedades completas de objetos e ficheiros. Para preservar propriedades completas, a AzCopy precisa de enviar um pedido adicional por objeto ou ficheiro. (verdade padrão)

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

**--cap-mbps uint32**      Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.

**...-tipo de saída** formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é 'texto'. ("texto" predefinido)

**--cadeia de sufixos fidedignos-microsoft-sufixos** Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.

## <a name="see-also"></a>Ver também

- [azcopia](storage-ref-azcopy.md)

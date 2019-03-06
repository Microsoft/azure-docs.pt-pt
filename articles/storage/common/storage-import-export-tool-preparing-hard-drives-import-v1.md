---
title: Preparar as unidades de disco rígidas para uma tarefa de importação de importar/exportar do Azure - v1 | Documentos da Microsoft
description: Saiba como preparar as unidades de disco rígido usando a ferramenta de v1 WAImportExport para criar uma tarefa de importação para o serviço importar/exportar do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 5ea510d8335437cb43f3e8824ec73175c35dcd03
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452373"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Preparar as unidades de disco rígido para uma tarefa de importação
Para preparar um ou mais unidades de disco rígido para uma tarefa de importação, siga estes passos:

-   Identificar os dados para importar para o serviço de BLOBs

-   Identificar os diretórios virtuais de destino e os blobs no serviço de BLOBs

-   Determinar quantas unidades, terá de

-   Copiar os dados para cada uma das suas unidades de disco rígido

 Para um fluxo de trabalho de exemplo, consulte [fluxo de trabalho de exemplo para preparar os discos rígidos para uma tarefa de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Identificar os dados a serem importados
 O primeiro passo para criar uma tarefa de importação é determinar a quais diretórios e arquivos que pretende importar. Isso pode ser uma lista de diretórios, uma lista de ficheiros exclusivos ou uma combinação dessas duas. Quando um diretório está incluído, todos os ficheiros no diretório e respetivos subdiretórios vão fazer parte da tarefa de importação.

> [!NOTE]
>  Uma vez que os subdiretórios são incluído recursivamente, quando um diretório principal está incluído, especifique apenas o diretório principal. Não também especifique qualquer um dos seus subdiretórios.
>
>  Atualmente, a ferramenta de importação/exportação do Microsoft Azure tem a limitação do seguinte: se um diretório contém mais dados do que pode conter um disco rígido, em seguida, o diretório tem de ser dividido em diretórios menores. Por exemplo, se um diretório contém 2,5 TB de dados e a capacidade do disco rígido é apenas de 2TB, em seguida, terá de dividir o diretório de 2,5 TB em diretórios menores. Esta limitação será resolvida numa versão posterior da ferramenta.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Identificar as localizações de destino no serviço blob
 Para cada diretório ou ficheiro que irá ser importado, terá de identificar um diretório virtual de destino ou os BLOBs no serviço Blob do Azure. Usará esses destinos como entradas para a ferramenta de importação/exportação do Azure. Tenha em atenção que os diretórios devem ser delimitados com o caráter de barra "/".

 A tabela seguinte mostra alguns exemplos de destinos de blob:

|Ficheiro de origem ou diretório|Blob de destino ou diretório virtual|
|------------------------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https://mystorageaccount.blob.core.windows.net/music|

## <a name="determine-how-many-drives-are-needed"></a>Determinar quantas unidades são necessários
 Em seguida, terá de determinar:

-   O número de unidades de disco rígido necessário para armazenar os dados.

-   Os diretórios e/ou arquivos autônomos que vão ser copiados para cada disco rígido.

 Certifique-se de que tem o número de unidades de disco rígido, que terá de armazenar os dados que estiver a transferir.

## <a name="copy-data-to-your-hard-drive"></a>Copiar dados para o seu disco rígido
 Esta secção descreve como chamar a ferramenta de importação/exportação do Azure para copiar os dados para um ou mais unidades de disco rígido. Sempre que chamar a ferramenta de importação/exportação do Azure, criar um novo *copiar sessão*. Criar sessão, pelo menos, uma cópia para cada unidade para o qual copiar dados. em alguns casos, poderá ter mais do que uma sessão de cópia para copiar todos os seus dados para uma única unidade. Aqui estão algumas razões que poderá ter várias sessões de cópia:

-   Tem de criar uma sessão de cópia separada para cada unidade que copia para.

-   Uma sessão de cópia pode copiar um único diretório ou um único blob para a unidade. Se estiver a copiar vários diretórios, vários blobs ou uma combinação de ambos, terá de criar várias sessões de cópia.

-   Pode especificar propriedades e metadados que serão definidos nos blobs importados como parte de uma tarefa de importação. As propriedades ou metadados que especificou para uma sessão de cópia serão aplicada a todos os blobs especificados por essa sessão de cópia. Se pretender especificar diferentes propriedades ou metadados para alguns blobs, terá de criar uma sessão de cópia separada. Ver [definição de propriedades e metadados durante o processo de importação](storage-import-export-tool-setting-properties-metadata-import-v1.md)para obter mais informações.

> [!NOTE]
>  Se tiver vários computadores que cumprem os requisitos descritos na [definição de cópia de segurança do Azure ferramenta importar/exportar](storage-import-export-tool-setup-v1.md), pode copiar dados para várias unidades de disco rígido em paralelo ao executar uma instância dessa ferramenta em cada máquina.

 Para cada unidade de disco rígido que se preparar com a ferramenta de importação/exportação do Azure, a ferramenta irá criar um ficheiro de diário único. Terá dos ficheiros de diário de todos os das suas unidades para criar a tarefa de importação. O ficheiro de diário também pode ser utilizado para retomar a preparação da unidade, se a ferramenta é interrompida.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Sintaxe da ferramenta de importação/exportação do Azure para uma tarefa de importação
 Para preparar unidades para uma tarefa de importação, chame a ferramenta de importação/exportação do Azure com o **PrepImport** comando. Quais parâmetros incluir depende se esta for a primeira sessão de cópia ou uma sessão de cópia subsequentes.

 A primeira sessão de cópia para uma unidade requer alguns parâmetros adicionais para especificar a chave de conta de armazenamento; a letra de unidade de destino; Se o disco tem de ser formatado; Se a unidade tem de estar encriptada e nesse caso, a chave do BitLocker; e o diretório de registo. Esta é a sintaxe para uma sessão de cópia inicial copiar um diretório ou de um único ficheiro:

 **Primeiro, copie a sessão para copiar um único diretório**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Primeiro de copiar a sessão para copiar um ficheiro único**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 Sessões de cópia subsequentes, não é necessário especificar os parâmetros iniciais. Esta é a sintaxe para uma sessão subsequente de cópia copiar um diretório ou de um único ficheiro:

 **Sessões subseqüentes de cópia para copiar um único diretório**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Sessões subseqüentes de cópia para copiar um ficheiro único**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parâmetros para a primeira sessão de cópia para um disco rígido
 Sempre que executar a ferramenta de importação/exportação do Azure para copiar ficheiros para o disco rígido, a ferramenta cria uma sessão de cópia. Cada sessão de cópia copia um único diretório ou de um único ficheiro para um disco rígido. O estado da sessão de cópia é escrito no ficheiro de diário. Se uma sessão de cópia for interrompida (por exemplo, devido a uma queda de energia do sistema), pode ser retomada a executar a ferramenta novamente e especificando o ficheiro de diário na linha de comandos.

> [!WARNING]
>  Se especificar a **/Formatar** parâmetro para a primeira sessão de cópia, o disco irá ser formatado e todos os dados na unidade serão eliminados. Recomenda-se que utilize discos em branco apenas para a sua sessão de cópia.

 O comando utilizado para a primeira sessão de cópia para cada unidade requer parâmetros diferentes que os comandos para sessões de cópia subsequentes. A tabela seguinte lista os parâmetros adicionais que estão disponíveis para a primeira sessão de cópia:

|Parâmetro da linha de comandos|Descrição|
|-----------------------------|-----------------|
|**/sk:**<StorageAccountKey\>|`Optional.` A chave de conta de armazenamento para a conta de armazenamento para o qual os dados serão importados. Tem de incluir qualquer um **/sk:**< StorageAccountKey\> ou **/csas:**< ContainerSas\> no comando.|
|**/csas:**<ContainerSas\>|`Optional`. O contentor de SAS para utilizar para importar dados para a conta de armazenamento. Tem de incluir qualquer um **/sk:**< StorageAccountKey\> ou **/csas:**< ContainerSas\> no comando.<br /><br /> O valor para este parâmetro tem de começar com o nome do contentor, seguido de um ponto de interrogação (?) e o token SAS. Por exemplo:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> As permissões, se especificado no URL ou de uma política de acesso armazenadas, tem de incluir leitura, escrita e eliminação para tarefas de importação e leitura, escrita e lista para tarefas de exportação.<br /><br /> Quando este parâmetro for especificado, todos os blobs para ser importado ou exportado tem de estar dentro do contentor especificado na assinatura de acesso partilhado.|
|**/t:**<TargetDriveLetter\>|`Required.` A letra de unidade de disco de rígido de destino para a sessão de cópia atual, sem os dois pontos à direita.|
|**/format**|`Optional.` Especificar este parâmetro quando a unidade tem de ser formatado; caso contrário, omita. Antes da ferramenta formata o disco, irá solicitar a confirmação da consola. Para suprimir a confirmação, especifique o parâmetro de /silentmode.|
|**/silentmode**|`Optional.` Especifica este parâmetro para suprimir a confirmação para formatar a unidade de destino.|
|**/encrypt**|`Optional.` Especificar este parâmetro quando a unidade não tenha sido criptografada com BitLocker e tem de ser encriptados pela ferramenta. Se a unidade já foi criptografada com BitLocker, em seguida, se omitir este parâmetro e especificar o `/bk` parâmetro, fornecendo a chave do BitLocker existente.<br /><br /> Se especificar a `/format` parâmetro, em seguida, também tem de especificar o `/encrypt` parâmetro.|
|**/bk:**<BitLockerKey\>|`Optional.` Se `/encrypt` é especificado, omitir este parâmetro. Se `/encrypt` é omitido, é necessário ter já encriptou a unidade com o BitLocker. Utilize este parâmetro para especificar a chave do BitLocker. Criptografia de disco BitLocker é necessária para todos os discos rígidos para tarefas de importação.|
|**/logdir:**<LogDirectory\>|`Optional.` O diretório de registo Especifica um diretório a ser utilizado para armazenar registos verbosos, bem como arquivos de manifesto temporários. Se não for especificado, será utilizado o diretório atual como o diretório de registo.|

### <a name="parameters-required-for-all-copy-sessions"></a>Parâmetros necessários para todas as sessões de cópia
 O ficheiro de diário contém o estado de todas as sessões de cópia para um disco rígido. Também contém as informações necessárias para criar a tarefa de importação. Tem de especificar sempre um ficheiro de diário ao executar a ferramenta de importação/exportação do Azure, bem como um ID de sessão de cópia:

|||
|-|-|
|Parâmetro de linha de comandos|Descrição|
|**/j:**<JournalFile\>|`Required.` O caminho para o ficheiro de diário. Cada unidade tem de ter exatamente um ficheiro de diário. Tenha em atenção que o ficheiro de diário não tem de residir na unidade de destino. A extensão de ficheiro de diário é `.jrn`.|
|**/id:**<SessionId\>|`Required.` O ID de sessão identifica uma sessão de cópia. É utilizado para garantir a recuperação precisa de uma sessão de cópia interrompida. Ficheiros que são copiados numa sessão de cópia são armazenados num diretório chamado depois do ID de sessão na unidade de destino.|

### <a name="parameters-for-copying-a-single-directory"></a>Parâmetros para copiar um único diretório
 Quando copiar um único diretório, aplicam-se os seguintes parâmetros obrigatórios e opcionais:

|Parâmetro de linha de comandos|Descrição|
|----------------------------|-----------------|
|**/srcdir:**<SourceDirectory\>|`Required.` O diretório de origem que contém os arquivos sejam copiados para a unidade de destino. O caminho do diretório tem de ser um caminho absoluto (não um caminho relativo).|
|**/dstdir:**<DestinationBlobVirtualDirectory\>|`Required.` O caminho para o diretório virtual de destino na sua conta de armazenamento do Windows Azure. O diretório virtual pode ou não pode já existir.<br /><br /> Pode especificar um contentor ou como um prefixo de blob `music/70s/`. O diretório de destino tem de começar com o nome do contentor, seguido de uma barra "/" e, opcionalmente, pode incluir um diretório virtual blob que termina com "/".<br /><br /> Quando o contentor de destino é o contentor de raiz, deve especificar explicitamente o contêiner raiz, incluindo a barra, como `$root/`. Uma vez que não podem incluir blobs no contentor de raiz "/" nos respetivos nomes, não serão copiados todos os subdiretórios no diretório de origem quando o diretório de destino é o contêiner raiz.<br /><br /> Certifique-se de que utilizar nomes de contentor válido ao especificar os diretórios virtuais de destino ou os blobs. Tenha em atenção que os nomes dos contentores têm de estar em minúsculos. Para regras de nomenclatura de contentor, consulte [nomenclatura e referenciação de contentores, Blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/ Disposição:**< mudar o nome&#124;não-substituir&#124;substituir >|`Optional.` Especifica o comportamento quando um blob com o endereço especificado já existe. Os valores válidos para este parâmetro são: `rename`, `no-overwrite` e `overwrite`. Tenha em atenção que estes valores diferenciam maiúsculas de minúsculas. Se for especificado nenhum valor, a predefinição é `rename`.<br /><br /> O valor especificado para este parâmetro afeta todos os ficheiros no diretório especificado pelo `/srcdir` parâmetro.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Especifica o tipo de blob para os blobs de destino. Os valores válidos são: `BlockBlob` e `PageBlob`. Tenha em atenção que estes valores diferenciam maiúsculas de minúsculas. Se for especificado nenhum valor, a predefinição é `BlockBlob`.<br /><br /> Na maioria dos casos, `BlockBlob` é recomendado. Se especificar `PageBlob`, o comprimento de cada ficheiro no diretório tem de ser um múltiplo de 512, o tamanho de uma página para blobs de páginas.|
|**/PropertyFile:**<PropertyFile\>|`Optional.` Caminho para o ficheiro de propriedade para os blobs de destino. Ver [metadados e o formato de arquivo de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações.|
|**/MetadataFile:**<MetadataFile\>|`Optional.` Caminho para o ficheiro de metadados para os blobs de destino. Ver [metadados e o formato de arquivo de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações.|

### <a name="parameters-for-copying-a-single-file"></a>Parâmetros para copiar um ficheiro único
 Quando copiar um único arquivo, aplicam-se os seguintes parâmetros obrigatórios e opcionais:

|Parâmetro de linha de comandos|Descrição|
|----------------------------|-----------------|
|**/srcfile:**<SourceFile\>|`Required.` O caminho completo para o arquivo seja copiado. O caminho do diretório tem de ser um caminho absoluto (não um caminho relativo).|
|**/dstblob:**<DestinationBlobPath\>|`Required.` O caminho para o blob de destino na sua conta de armazenamento do Windows Azure. O blob poderá ou não pode já existir.<br /><br /> Especifica o início do nome de blob com o nome do contentor. O nome do blob não pode começar com "/" ou o nome da conta de armazenamento. Para regras de nomenclatura de BLOBs, veja [nomenclatura e referenciação de contentores, Blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> Quando o contentor de destino é o contentor de raiz, deve especificar explicitamente `$root` como o contêiner, tais como `$root/sample.txt`. Tenha em atenção que os blobs no contentor de raiz não pode incluir "/" nos respetivos nomes.|
|**/ Disposição:**< mudar o nome&#124;não-substituir&#124;substituir >|`Optional.` Especifica o comportamento quando um blob com o endereço especificado já existe. Os valores válidos para este parâmetro são: `rename`, `no-overwrite` e `overwrite`. Tenha em atenção que estes valores diferenciam maiúsculas de minúsculas. Se for especificado nenhum valor, a predefinição é `rename`.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Especifica o tipo de blob para os blobs de destino. Os valores válidos são: `BlockBlob` e `PageBlob`. Tenha em atenção que estes valores diferenciam maiúsculas de minúsculas. Se for especificado nenhum valor, a predefinição é `BlockBlob`.<br /><br /> Na maioria dos casos, `BlockBlob` é recomendado. Se especificar `PageBlob`, o comprimento de cada ficheiro no diretório tem de ser um múltiplo de 512, o tamanho de uma página para blobs de páginas.|
|**/PropertyFile:**<PropertyFile\>|`Optional.` Caminho para o ficheiro de propriedade para os blobs de destino. Ver [metadados e o formato de arquivo de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações.|
|**/MetadataFile:**<MetadataFile\>|`Optional.` Caminho para o ficheiro de metadados para os blobs de destino. Ver [metadados e o formato de arquivo de propriedades de serviço de importação/exportação](../storage-import-export-file-format-metadata-and-properties.md) para obter mais informações.|

### <a name="resuming-an-interrupted-copy-session"></a>A retomar uma sessão de cópia interrompida
 Se uma sessão de cópia é interrompida por qualquer motivo, pode continuá-lo ao executar a ferramenta com apenas o ficheiro de diário especificado:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Apenas a cópia sessão mais recente se terminou anormalmente, pode ser retomada.

> [!IMPORTANT]
>  Quando retomar uma sessão de cópia, não modifique os ficheiros de origem de dados e diretórios adicionando ou removendo arquivos.

### <a name="aborting-an-interrupted-copy-session"></a>Operação de abortar a uma sessão de cópia interrompida
 Se uma sessão de cópia é interrompida e não é possível retomar (por exemplo, se um diretório de origem se mostrou inacessível), tem de anular a sessão atual para que podem ser revertida e novas sessões de cópia podem ser iniciados:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Apenas a última sessão de cópia, se terminou anormalmente, pode ser anulada. Tenha em atenção que não é possível anular a primeira sessão de cópia para uma unidade. Em vez disso, tem de reiniciar a sessão de cópia com um novo ficheiro de diário.

## <a name="next-steps"></a>Passos Seguintes

* [Configurar a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)
* [Definir propriedades e metadados durante o processo de importação](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Referência rápida para comandos utilizados com frequência](storage-import-export-tool-quick-reference-v1.md) 
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparação de uma tarefa de importação](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparação de uma tarefa de exportação](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
